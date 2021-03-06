# 第一届腾讯社交广告-高校算法设计大赛

计算广告是互联网最重要的商业模式之一，广告投放效果通常通过曝光、点击和转化各环节来衡量，大多数广告系统受广告效果数据回流的限制只能通过曝光或点击作为投放效果的衡量标准开展优化。腾讯社交广告( http://ads.tencent.com )发挥特有的用户识别和转化跟踪数据能力，帮助广告主跟踪广告投放后的转化效果，基于广告转化数据训练转化率预估模型(pCVR，Predicted Conversion Rate)，在广告排序中引入pCVR因子优化广告投放效果，提升ROI。 本题目以移动App广告为研究对象，预测App广告点击后被激活的概率：pCVR=P(conversion=1 | Ad,User,Context)，即给定广告、用户和上下文情况下广告被点击后发生激活的概率。


# 安装过的包

换源：-i https://pypi.tuna.tsinghua.edu.cn/simple

- keras
- tensorflow（ tensorflow-gpu）
- numpy
- scipy
- h5py
- matplotlib
- scikit-learn 
- scikit-image
- xgboost

# 一些指令
- ps -ef| grep `pid`  找到这个pid的用户
- nvidia-smi 查看显存使用情况
- watch -n 1 nvidia-smi 每一秒输出一次后面的指令结果

# 结果统计 
- 总数：`3749528`
- label_1: `93262`
- label_0: `3656266`

显然是一个不平衡的数据集

# 特征工程

希望最终都能转为one-hot喜欢的格式，所以连续数值请按分布取值

###  user
- 年龄
    - 取值：0的，10以下、10~15、15~18、18~24、24~30、30~35、35~40、40~50、50以上
- 性别
    - 取值：男、女、未知
- 教育
    - 取值：小学，初中，高中，专科，本科，硕士，博士，未知
    - 统计：TODO
- 婚姻状况
    - 取值：单身，新婚，已婚，未知
- 宝宝状况
    - 取值：孕育中，宝宝0~6个月，宝宝6~12个月，宝宝1~2岁，宝宝2~3岁，育儿但宝宝年龄未知，未知
- 籍贯的沿海or内陆城市（外部数据嫌隙）
- 籍贯的南方or北方（外部数据嫌隙）
- 常居住地的省（外部数据嫌隙）
- 常居住地的沿海or内陆城市（外部数据嫌隙）
- 常居住地的南方or北方（外部数据嫌隙）
- 用户app安装个数分布
    - 取值划分：TODO
    - 统计：TODO
- app安装个数超过平均值
    - 统计：TODO
- 用户app安装时间近1天安装个数：
    - 划分：TODO
- 用户app安装时间近2天安装个数：
    - 划分：TODO
- 用户app安装时间近3天安装个数：
    - 划分：TODO
- 用户app安装时间近5天安装个数：
    - 划分：TODO
- 用户app安装时间近10天安装个数：
    - 划分：TODO
- 用户app安装时间近20天安装个数：
    - 划分：TODO
- 用户app安装时间近25天安装个数：
    - 划分：TODO 
- 用户聚类（根据安装相同app的分布来聚类）
    - 类别划分：根据聚类结果，打标签
- 连续3天安装app
     
### ad

几个id之间的关系 ：

账户 -&gt; 推广计划 -&gt; 广告 -&gt; 素材  

- app平台
    - 取值：Android，iOS，未知
- app大类别
    - 取值：百位数，0~9，0为未知
- app小类别
    - 取值：0~99（实际上在训练数据中都是0~9）
- 账户ID(advertiserID)
    - 统计：91个
    - 取值：想不到好的，感觉这个特征没用，暂时不用
- 推广计划ID(campaignID)
    - 统计：720个
    - 取值：想不到好的，感觉这个特征没用，暂时不用
- 该素材(creativeID)转化概率
- 该素材(creativeID)多次点击次数分布
    - 统计：1：0,2~10：1，10~50：2，50~100：3，100~300：4，300~500：5，500~1000：6，1000~2000：7，2000~6000：8，6000以上：9
- 该素材(creativeID)所在的广告(adID)转化概率
- 该素材(creativeID)所在的广告(adID)被点击的次数分布
    - 统计：30以下：0,30~100：1，100~200：2，200~500：3，500~1000：4，1000~2000：5，2000~6000：6，6000以上：7
- 该素材(creativeID)所在的推广计划(campaignID)转化概率
- 该素材(creativeID)所在的推广计划(campaignID)被点击的次数分布
    - 统计：30以下：0,30~100：1，100~200：2，200~500：3，500~1000：4，1000~2000：5，2000~6000：6，6000以上：7
- 该素材(creativeID)所在的账户(advertiserID)转化概率
- 该素材(creativeID)所在的账户(advertiserID)被点击的次数分布
    - 统计：30以下：0,30~100：1，100~200：2，200~500：3，500~1000：4，1000~2000：5，2000~5000：6，5000~10000：7，10000以上：8
- 该素材(creativeID)所在的**大**分类(appCategory)转化概率
- 该素材(creativeID)所在的**大**分类(appCategory)是否热门
    - 统计：0~1000:1,1000~10000:2,10000~50000:3,>50000:4
- 该素材(creativeID)所在的**小**分类(appCategory)转化概率
- 该素材(creativeID)所在的**小**分类(appCategory)是否热门
    - 统计：0~1000:1,1000~5000:2,5000~10000:3,10000~30000:4,>30000:5

### Context features（上下文特征）
- 联网方式(connectionType)转化概率
- 联网方式(connectionType)
    - 取值：2G，3G，4G，WIFI，未知
- 运营商(telecomsOperator)的转化概率
- 运营商(telecomsOperator)
    - 取值：中国移动，中国联通，中国电信，未知
- 广告位类型(positionType)的转化概率
- 广告位类型(positionType)
    - 取值：0,1,2,3,4,5
- 广告位ID(positionID)的转化概率
- 广告位ID(positionID)
    - 统计取值：1~10:1,10~50,：2,50~200:3,200~500:4,500~1000:5，1000~2000:6,2000~5000:7，5000以上：8
- 站点集合ID(sitesetID，多个广告位的聚合，如QQ空间)的转化概率
- 站点集合ID(sitesetID，多个广告位的聚合，如QQ空间)
    - 取值：0,1,2


# data文件含义
- get_data_v1 仅仅是训练数据的id，没有具体的获得数据
- get_data_v2 增加关联的数据（目前还没有app记录，以后版本可以考虑用户社团发现）
- get_data_v3 解决数据不平衡问题：采用复制数据方法，来提高数据平衡性（效果不佳，暂时舍弃）
- get_data_v4.1 xgb需要的数据，没有one-hot
- get_data_v4.2 xgb需要的数据，并拥有特征工程1.0版本
- get_data_v4.3 xgb需要的数据，去掉float等数据

# 模型
注意：请使用对应版本的数据
- dense  深度学习
- lstm  RNN
- xgb算法 迭代决策树的高级版本

# 经验
- 损失函数用binary_crossentropy（又叫做logloss）
- 加入 `BatchNormalization` 和 `dropout` 解决了loss直接收敛的问题
- 考虑直接在数据层归一化。
- sgd:0.01效果好点

# 结果
- lstm_v2_bak.py 的在迭代一千次（5小时）的情况下，达到了loss：0.084，并且还没有完全收敛，改天可以继续迭代。后来经过腾讯的测试，仅有0.16，效果不好
- xgboost_v1 在仅仅迭代50次，CPU下运行时间才1分钟（你可知道深度学习1000次迭代在GPU下要5个小时）的情况下，达到了历史最高线上0.109，线下0.08
- xgb调整参数，在上一版基础上，提升到0.1034
- xgb，拥有分布+概率特征工程，效果线上0.107205，线下:0.225293。很诡异
