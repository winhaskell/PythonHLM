用Python读红楼
-----------
最近受到这篇文章[用Python读金庸武侠](https://zhuanlan.zhihu.com/p/21428792)的启发。作为古典文（zhuang）学（bi）青年，一定要用Python来试试我读了20年的《红楼梦》。

首先从网上找到了红楼梦.txt（不清楚版本，可能是程甲版）和对应的人物表。

把人名加入词库，然后用结巴分词切分整个文章（我没有区分前八十回和后四十回），加入gensim的训练中

    def tranning():
	    with open('hlm.txt') as f:
	        data = [line.strip()
	                for line in f.readlines()
	                if line.strip()]
	
	    sentences = []
	    for line in data:
	        words = list(jieba.cut(line))
	        sentences.append(words)
	    model = gensim.models.Word2Vec(sentences,
	                                   size=100,
	                                   window=5,
	                                   min_count=5,
	                                   workers=4)
	    return model

训练生成一个以词向量表示词的模型，下面就用代码来找找和宝钗相似的人物吧。

        for k, s in model.most_similar(positive=["宝钗"]):
	        print('{}:{}'.format(k, s))

 

探春:0.9595787525177002
紫鹃:0.9429049491882324
黛玉:0.9325058460235596
林黛玉:0.9230709075927734
李纨:0.9200501441955566
湘云:0.9067497253417969
袭人:0.9052997827529907
薛姨妈:0.9024999737739563
贾琏:0.899409294128418
宝玉:0.8973875045776367

电脑认为探春和宝钗的相似度最高，回想起第五十六回《敏探春兴利除宿弊 贤宝钗小惠全大体》，都是治家的好手，算是有点道理。
但是这个第二相似的是紫鹃，为什么不是袭人？看来还是没读懂？
第三名是黛玉，这个不奇怪，所谓“钗黛一体”，红学家论述很多。（这里说明一下，因为目前还没法把黛玉和林黛玉这两个名字合为一个人，可能结果有些偏差）

下面用相似关系的来试试

    find_relationship(model, '黛玉', '紫鹃', '宝玉')
    给定“黛玉”与“紫鹃”，“宝玉”和“平儿”有类似的关系

我本意是晴雯或者袭人吧，不过这里平儿乱入，是不是说明了宝玉的一些小心思，呵呵，耐人寻味。

下面用K-Means算法，来聚类试试



	类别1：
	贾敬, 贾珠, 贾宝玉, 贾琮, 贾菌, 贾芹, 贾菖, 贾菱, 巧姐儿, 王子腾, 
	王仁, 王熙凤, 薛宝钗, 林妹妹, 贾蓉之妻, 甄宝玉, 绮霰, 檀云, 茜雪, 春燕, 
	坠儿, 四儿, 佳蕙, 待书, 入画, 彩屏, 翠墨, 莲花儿, 珍珠, 玻璃, 
	翡翠, 小红, 金钏, 玉钏, 彩霞, 素云, 翠缕, 宝珠, 小螺, 傻大姐, 
	焦大, 李贵, 锄药, 墨雨, 来旺, 隆儿, 昭儿, 喜儿, 王信, 龄官, 
	蕊官, 藕官, 宝官, 文官, 葵官, 空空道人, 甄士隐, 小童, 神瑛侍者, 警幻仙子, 
	贾雨村, 封肃, 冷子兴, 林如海, 李嬷嬷, 门子, 冯渊, 拐子, 刘氏, 板儿, 
	青儿, 周瑞, 智能, 詹光, 吴新登, 秦业, 金氏, 戴权, 张材家的, 水溶, 
	净虚, 赵嬷嬷, 程日兴, 花自芳, 多浑虫, 卜世仁, 倪二, 马道婆, 周姨娘, 蒋玉菡, 
	云儿, 张道士, 傅试, 宋嬷嬷, 鲍二家的, 金文翔, 柳湘莲, 赖尚荣, 邢岫烟, 李婶娘, 
	李纹, 李绮, 胡君荣, 乌进孝, 女先儿, 夏婆子, 柳家的, 佩凤, 尤三姐, 尤老娘, 
	张华, 俞禄, 秋桐, 喜鸾, 王善保家的, 邢德全, 孙绍祖, 王一贴, 
	类别2：
	黛玉, 宝钗, 贾政, 贾琏, 贾母, 宝玉, 王夫人, 凤姐, 薛姨妈, 邢夫人, 
	尤氏, 李纨, 刘姥姥, 袭人, 晴雯, 麝月, 紫鹃, 鸳鸯, 平儿, 
	类别3：
	贾赦, 贾珍, 贾环, 贾瑞, 贾蓉, 贾兰, 贾蔷, 贾芸, 史湘云, 薛蟠, 
	薛蝌, 林黛玉, 香菱, 妙玉, 赵姨娘, 秋纹, 司棋, 雪雁, 琥珀, 莺儿, 
	丰儿, 彩云, 宝蟾, 茗烟, 焙茗, 兴儿, 芳官, 周瑞家的, 秦钟, 冯紫英, 
	赖大, 林之孝, 林之孝家的, 尤二姐, 


看见类别2都是主角，类别3是配角，类别1就是跑龙套的了

再采用层级聚类的方式
得到如下的图
![Alt text](./figure_1.png)
这幅图绿色的线都是主角，有些聚类比较准确，比如贾政、贾赦属于兄弟俩。
藕官、小红、春燕、坠儿都在丫头的分类里面。
有些比较搞笑，薛姨妈和李纨归为一类，难道因为都是寡妇的缘故吗（:-D）？
薛蟠和妙玉分在一类，这是什么鬼？难道预示了妙玉"无瑕白玉遭泥陷"的结局吗？
以上就是我的分析, gensim都是初学,就一个小时，一个新的红学家就诞生了，😄

严肃的来说，因为红楼梦属于明清小说，行文有很多和现代文不一样的地方，结巴分词不能很好的切词，导致学习效果比较差。
以上分析纯属逗乐，不能当真。