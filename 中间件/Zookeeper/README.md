# 常用的zk java客户端

1.zk原生api
2.zkclient
3.Apache curator

# zk原生api的不足之处

 1.超时重连，不支持自动，需要手动操作
 2.Watch注册一次后会失效
 3.不支持递归创建节点

# Apache curator

 1.Apache的开源项目
 2.解决watcher的注册一次就失败
 3.Api更加简单易用
 4.提供更多解决方案并且实现简单：比如 分布式锁
 5.提供常用的ZooKeeper工具类
 6.编程分格更爽

          
           同步创建zk示例，原生api是异步的
           
           curator链接zookeeper的策略:ExponentialBackoffRetry
           baseSleepTimeMs：初始sleep的时间
           maxRetries：最大重试次数
           maxSleepMs：最大重试时间
           
      		RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 5);

          
           curator链接zookeeper的策略:RetryNTimes
           n：重试的次数
           sleepMsBetweenRetries：每次重试间隔的时间
           
          RetryPolicy retryPolicy = new RetryNTimes(3, 5000);

     curator链接zookeeper的策略:RetryOneTime
     sleepMsBetweenRetry:每次重试间隔的时间
     
    		RetryPolicy retryPolicy2 = new RetryOneTime(3000);

    
     永远重试，不推荐使用
     
    		RetryPolicy retryPolicy3 = new RetryForever(retryIntervalMs)

    
     curator链接zookeeper的策略:RetryUntilElapsed
     maxElapsedTimeMs:最大重试时间
     sleepMsBetweenRetries:每次重试间隔
     重试时间超过maxElapsedTimeMs后，就不再重试
     
    		RetryPolicy retryPolicy4 = new RetryUntilElapsed(2000, 3000);

# 客户端Curator使用
## 1.会员连接与关闭
## 2.节点的增删改查

实例化

        CuratorOperator cto = new CuratorOperator();
        boolean isZkCuratorStarted = cto.client.isStarted();
        System.out.println("当前客户的状态：" + (isZkCuratorStarted ? "连接中" : "已关闭"));
		
## 创建节点

        String nodePath = "/super/imooc";
    	byte[] data = "superme".getBytes();
    		cto.client.create().creatingParentsIfNeeded()
    			.withMode(CreateMode.PERSISTENT)
    			.withACL(Ids.OPEN_ACL_UNSAFE)
    			.forPath(nodePath, data);

##  更新节点数据

    		byte[] newData = "batman".getBytes();
    		cto.client.setData().withVersion(0).forPath(nodePath, newData);
		
##  删除节点

    		cto.client.delete()
    				  .guaranteed()					// 如果删除失败，那么在后端还是继续会删除，直到成功
    				  .deletingChildrenIfNeeded()	// 如果有子节点，就删除
    				  .withVersion(0)
    				  .forPath(nodePath);
		
		
		
##  读取节点数据

    		Stat stat = new Stat();
    		byte[] data = cto.client.getData().storingStatIn(stat).forPath(nodePath);
    		System.out.println("节点" + nodePath + "的数据为: " + new String(data));
    		System.out.println("该节点的版本号为: " + stat.getVersion());
		
		
##  查询子节点

    		List<String> childNodes = cto.client.getChildren()
    											.forPath(nodePath);
    		System.out.println("开始打印子节点：");
    		for (String s : childNodes) {
    			System.out.println(s);
    		}

				
##  判断节点是否存在,如果不存在则为空

    		Stat statExist = cto.client.checkExists().forPath(nodePath + "/abc");
    		System.out.println(statExist);

		
##  watcher 事件  当使用usingWatcher的时候，监听只会触发一次，监听完毕后就销毁

    		cto.client.getData().usingWatcher(new MyCuratorWatcher()).forPath(nodePath);
    		cto.client.getData().usingWatcher(new MyWatcher()).forPath(nodePath);

##  为节点添加watcher

         NodeCache: 监听数据节点的变更，会触发事件
    		final NodeCache nodeCache = new NodeCache(cto.client, nodePath);
    		buildInitial : 初始化的时候获取node的值并且缓存
    		nodeCache.start(true);
    		if (nodeCache.getCurrentData() != null) {
    			System.out.println("节点初始化数据为：" + new String(nodeCache.getCurrentData().getData()));
    		} else {
    			System.out.println("节点初始化数据为空...");
    		}
    		nodeCache.getListenable().addListener(new NodeCacheListener() {
    			public void nodeChanged() throws Exception {
    				if (nodeCache.getCurrentData() == null) {
    					System.out.println("空");
    					return;
    				}
    				String data = new String(nodeCache.getCurrentData().getData());
    				System.out.println("节点路径：" + nodeCache.getCurrentData().getPath() + "数据：" + data);
    			}
    		});
		
		
##  为子节点添加watcher

       PathChildrenCache: 监听数据节点的增删改，会触发事件
      String childNodePathCache =  nodePath;
       cacheData: 设置缓存节点的数据状态
      final PathChildrenCache childrenCache = new PathChildrenCache(cto.client, childNodePathCache, true);
      
       StartMode: 初始化方式
       POST_INITIALIZED_EVENT：异步初始化，初始化之后会触发事件
       NORMAL：异步初始化
       BUILD_INITIAL_CACHE：同步初始化
       
      childrenCache.start(StartMode.POST_INITIALIZED_EVENT);

      List<ChildData> childDataList = childrenCache.getCurrentData();
      System.out.println("当前数据节点的子节点数据列表：");
      for (ChildData cd : childDataList) {
        String childData = new String(cd.getData());
        System.out.println(childData);
      }

      childrenCache.getListenable().addListener(new PathChildrenCacheListener() {
        public void childEvent(CuratorFramework client, PathChildrenCacheEvent event) throws Exception {
          if(event.getType().equals(PathChildrenCacheEvent.Type.INITIALIZED)){
            System.out.println("子节点初始化ok...");
          }

          else if(event.getType().equals(PathChildrenCacheEvent.Type.CHILD_ADDED)){
            String path = event.getData().getPath();
            if (path.equals(ADD_PATH)) {
              System.out.println("添加子节点:" + event.getData().getPath());
              System.out.println("子节点数据:" + new String(event.getData().getData()));
            } else if (path.equals("/super/imooc/e")) {
              System.out.println("添加不正确...");
            }

          }else if(event.getType().equals(PathChildrenCacheEvent.Type.CHILD_REMOVED)){
            System.out.println("删除子节点:" + event.getData().getPath());
          }else if(event.getType().equals(PathChildrenCacheEvent.Type.CHILD_UPDATED)){
            System.out.println("修改子节点路径:" + event.getData().getPath());
            System.out.println("修改子节点数据:" + new String(event.getData().getData()));
          }
        }
      });

      Thread.sleep(100000);

      cto.closeZKClient();
      boolean isZkCuratorStarted2 = cto.client.isStarted();
      System.out.println("当前客户的状态：" + (isZkCuratorStarted2 ? "连接中" : "已关闭"));

