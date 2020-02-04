# DataScienceEpidemic
武汉疫情数据调研分析

材料收集：
  1. 武汉疫情近期相关研究论文
  2. 其他流行病相关论文
  3. 全国疫情数据（目前已经收集湖北各地市）
  4. 疫情期间政策以及事件
  5. 更具流传病动力学建模（待做）


# Early dynamics of transmission and control of 2019-nCoV: a mathematical modelling study
模型部分：
在模型中把个体分为4类传染病类别：

1. 易感人群（susceptible）： 暴露在感染环境中但尚未感染人群
2. 暴露人群（exposed）： 接触感染源但暂未感染（潜伏期人群）
3. 感染人群（infectious）： 患有感染病并且可作为传染源
4. 隔离人群（removed）： 包含被隔离/康复/以及其他不再传染疾病的患者

模型解释了症状发现的延迟和观测的不确定性现象。潜伏期（接触到发病）通常认为服从平均值为5.2的埃尔朗分布，而从发病到隔离的延迟服从平均值为2.9的埃尔朗分布。而发病到报道认为服从平均值为6.1天的几何分布。一小部分群体接触感染源后进行国际旅行，并且我们认为武汉传播疾病到其他国家的概率取决于：

1. 武汉的病例数量
2. 出境游客数量（假设在出行限制日期1月23日之前每天3300人而之后为0。）
3. 不同国家与武汉的联系强度
4. 不同国家报道病例的概率

我们选择了20个最有可能接收武汉感染者的国家进行分析。使用几何随机游走过程模拟传播，并且使用连续蒙特卡洛方法（SMC）来推断随时间的传播速度，使用同样的方法计算病例数量和传染速度R（一个典型感染病例平均每天感染的人数），模型有两个未知量： 

1. 传播过程中大量的不可靠因素
2. 武汉内确证病例的报道概率与从武汉去往其他国家的确诊病例的报道概率的区别。

我们假设病毒起源于2019年12月2号的一起感染源并且所有人立即成为潜在感染对象。估算出R后，我们使用服从负二项分布（a negative binomial offspring distribution）的分支过程来计算一个感染病例引起的大爆发。更相信的方法和计算细节提供在附件中。

###附件材料：
###传播模型
我们使用随机SEIR模型并且每6小时使用欧拉-丸山算法（Euler-Maruyama algorithm）按照符合传播速度的随机布朗运动模式进行传播，这个模型如图一所示：

![疫情传播示意图](https://github.com/axuanwu/DataScienceEpidemic/blob/master/%E5%9F%BA%E4%BA%8E%E6%AD%A6%E6%B1%89%E7%96%AB%E6%83%85%E7%9A%84%E7%9B%B8%E5%85%B3%E6%96%87%E7%AB%A0/1580805132582.jpg?raw=true)

详见 [完整公式](https://app.yinxiang.com/fx/2ea02bb5-a2f0-43da-9627-c42a8d12ac8b) 

$S(t+1)=S(t)-\beta(t)/N S(t)[I_{1w}(t)+I_{2w} (t)]$
$E_{1w}(t+1)=E_{1w}(t)+(1-f)\beta(t)S(t)[I_{1w}(t)+I_{2w} (t)]-2\sigma E_{1w}(t)$
$E_{2w}(t+1)=E_{2w}(t)+2\sigma E_{1w}(t) -2\sigma E_{2w}(t)$
$I_{1w}(t+1)=I_{1w}(t)+2\sigma E_{2w}(t)-2\gamma I_{1w}(t)$
$I_{2w}(t+1)=I_{2w}(t)+2\gamma I_{1w}(t) - 2\gamma I_{2w}(t)$
$Q_w(t+1)=Q_w(t)+2\sigma E_{2w}(t) - \kappa Q_w(t)$
$D_w(t+1)=D_w(t)+2\sigma E_{2w}(t)$
$C_w(t+1)=C_w(t)+\kappa Q_w(t)$

其中：

1. $S(t)$ 是t时刻武汉的易感人群数量
2. $E_{1w}(t)$和$E_{2w} (t)$是t时刻武汉服从埃尔朗分布的潜伏期一期和二期暴露人群人数。
3. $I_{1w}(t)$和$I_{2w} (t)$是t时刻武汉服从埃尔朗分布的潜伏期一期和二期感染人群人数。
4. . $Q_w(t)$是t时刻武汉需要报道的感染人数（已发病为报道）
5. $D_w(t)$是t时刻武汉积累的感染人数
6. $​C​_w​(t)$是t时刻武汉积累的确诊人数
7.  $\beta (t)$是t时刻的传播率
8. $\sigma $是发病概率（比如 1/潜伏时长）
9. $\gamma$是隔离率 (比如 1/从发病到去医院就医时间长度)
10. $\kappa$ 是报道率（比如 1/发病到确诊时间）
11. $N$ 是武汉人口数量
12. $f$ 是武汉离开武汉的概率
并且我们假设一旦变为感染者则必定会有症状。

目前 公式中的潜伏一期和二期。感染一期和二期具体含义尚未足够明确，需要去了解埃尔朗分布。
