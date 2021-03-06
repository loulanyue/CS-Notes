
机器学习产品PAI：

	阿里云机器学习平台PAI：构建在阿里云MaxCompute计算平台之上，集数据处理、建模、离线预测、在线预测为一体的机器学习平台。
	
	为算法开发者提供了丰富的MPI、PS、BSP等编程框架和数据存储接口，同时提供了基于WEB的可视化控制台，降低了使用门槛。
	
	上手简单	算法丰富	一站式体验	深度学习

PAI架构：

	业务应用层：包含金融、医疗、教育、交通、安全等领域
	
	模型算法层：包含数据预处理、特征工程、机器学习算法等基本组件
	
	计算框架层：包括MapReduce SQL MPI等计算方式，分布式计算架构主要执行并行化计算分发任务
	
	基础设施层：CPU计算集群
	
	
PAI功能特性：

	具有超大规模的数据处理能力和分布式的存储能力，同时整个模型支持超大规模的建模以及计算。
	
		Web UI界面
		
		机器学习算法层
		
		MAXCOMPUTE平台层

PAI的可视化：

	可视化建模	数据可视化	模型可视化
	


PAI支持的算法：

	PAI包含特征工程、数据预处理组件、统计分析、常用机器学习算法、深度学习框架、垂直应用相关的文本分析、搜索推荐、图像处理、网络分析等多种算法
	


支持深度学习：（建立模拟人脑进行学习的神经网络）

	TensorFlow	Caffe	MXNet	M40型号GPU卡
	

基本概念：

	表：机器学习的表存储在MaxCompute中
	
	生命周期：参数为lifecycle
	
	稀疏数据：信息量不够全面，需要通过合适的方法挖掘出有用的信息。如果样本中某个特征的数据为稀疏格式，可将稀疏格式的数据转换成libsvm格式，设置k:v稀疏数据格式。
	
	特征：每个表的一列就是该数据集的一个特征
	
	降维：维是指描述一个数据集的特征

PAI的在线预测、离线调度

	PAI除了提供模型训练功能，还提供了在线预测以及离线调度功能，让机器学习训练结果和业务可以无缝衔接
	
	在线部署通常是以API的方式提供服务，通过调用API发送待处理请求，并获得调用结果。API的优势是用户可以简单的使用服务，而不必了解源码或者其内部构造。
	
	离线部署：对于数据量较大且对时效性要求不强的场景，对数据进行批量处理。批量处理通常是预先知道调用的条件，定义成按时触发的任务，可以根据资源情况合理安排调度窗口。
	

PAI应用场景：

	营销类场景：商品推荐、用户群体画像、广告精准投放
	
	金融类场景：贷款发放预测、金融风险控制、股票走势预测、黄金价格预测
	
	SNS关系挖掘：微博粉丝领袖分析、社交关系链分析
	
	文本类场景：新闻分类、关键词提起、文章摘要、文本内容分析
	
	非结构化数据处理场景：图片分类、图片文本内容提取OCR
	
	其它各类预测场景：降雨预测、足球比赛结果预测
	
	
	
机器学习PAI的应用流程：

	首先明确任务、目标、并且掌握数据实际情况下，开始机器学习的实施过程：
	
	1）数据预处理
	
		数据采集、过滤、合并、转换
		
	2）选择特征
	
		样本特征的评估、变换生成、选择
		
	3）选择模型进行数据训练
	
		统计分析和数据可视化展现
		
	4）模型评估
	
	5）应用部署及再学习、再训练
	
