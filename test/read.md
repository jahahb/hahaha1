# **代码核心功能介绍**
## **github11代码截图**
<img src=https://github.com/jahahb/hahaha1/blob/main/test/github11.png  width = "800" />

### 1.文本预处理（包括过滤无效字符和过滤长度为1的词）

    def get_words(filename):
        words = []
        with open(filename, 'r', encoding='utf-8') as fr:
            for line in fr:
                line = line.strip()
                line = re.sub(r'[.【】0-9、——。，！~\*]', '', line)  # 过滤无效字符
                line = cut(line)  # jieba分词
                line = filter(lambda word: len(word) > 1, line)  # 过滤长度为1的词
                words.extend(line)
        return words

从指定的文件中读取文本，进行预处理（过滤无效字符和过滤短词），并返回处理后的单词列表；
初始化一个空列表 words，用于存储最终处理后的单词；
使用 with open 打开文件，确保文件在读取完成后自动关闭；
for line in fr 逐行读取文件内容；line.strip() 去除行首和行尾的空白字符（如换行符、空格等）；
使用正则表达式 re.sub 过滤掉文本中的无效字符；
使用 jieba.cut() 方法对文本进行分词处理；
使用 filter 和 lambda 函数过滤掉长度为1的单词；
将当前行处理后的单词列表追加到 words 中。

### 2.提取邮件文件中的单词，统计它们的出现次数，并返回最常见的单词列表
    def get_top_words(top_num):
        filename_list = ['邮件_files/{}.txt'.format(i) for i in range(151)]
        for filename in filename_list:
            all_words.append(get_words(filename))  # 遍历所有邮件生成词库
        freq = Counter(chain(*all_words))  # 统计词频
        return [i[0] for i in freq.most_common(top_num)]  # 返回前 top_num 个高频词

从一组邮件文件中提取单词，统计每个单词的出现次数，并返回出现次数最多的 top_num 个单词;
遍历 filename_list 中的每个文件名;
调用 get_words(filename) 函数，从文件中提取单词列表，并将结果追加到 all_words 中;
使用 itertools.chain() 将 all_words 中的所有列表组合成一个大列表;
使用 collections.Counter() 统计每个单词的出现次数;
使用 most_common(top_num) 方法获取出现次数最多的 top_num 个单词及其出现次数;
使用列表推导式提取单词（忽略出现次数），返回一个包含单词的列表。

### 3.将文本数据转换为数值化的词频向量，便于后续的数据分析任务
    vector = []
    for words in all_words:
        word_map = list(map(lambda word: words.count(word), top_words))  # 统计每个特征词的词频
        vector.append(word_map)
    vector = np.array(vector)  # 转换为 NumPy 数组

初始化一个空列表 vector，用于存储每个文本的词频向量;
all_words 是一个包含多个文本单词列表的列表（例如，每个文本对应一个单词列表）,遍历 all_words 中的每个单词列表
map 函数将 lambda 函数应用于 top_words 中的每个单词;lambda word: words.count(word) 是一个匿名函数，表示统计当前文本（words）中每个特征词（word）的出现次数;list(map(...)) 将结果转换为一个列表，表示当前文本中每个特征词的词频;
将当前文本的词频向量 word_map 追加到 vector 列表中;

### 4.模型训练（使用多项式朴素贝叶斯模型（Multinomial Naive Bayes）对文本数据进行分类训练）
    model = MultinomialNB()  
    model.fit(vector, labels)  

多项式朴素贝叶斯模型适用于处理多项分布的数据，例如词频向量。它假设特征是独立的，并且每个特征的出现次数服从多项分布;
使用 vector 和 labels 训练模型，学习数据中的模式和规律。

### 5.提取高频词特征
    count_features, count_feature_names = feature_extraction(documents, method='count')
    print("高频词特征矩阵：")
    print(count_features.toarray())
    print("特征名：", count_feature_names)

feature_extraction函数是一个自定义函数,用于从文本数据中提取特征;
documents输入文本数据,通常是一个包含多个文本的列表;
method='count'指定使用词频统计（CountVectorizer）作为特征提取方法。

### 6.提取TF-IDF特征
    tfidf_features, tfidf_feature_names = feature_extraction(documents, method='tfidf')
    print(r"\nTF-IDF特征矩阵：")
    print(tfidf_features.toarray())
    print("特征名：", tfidf_feature_names)

feature_extraction函数:这是一个自定义函数，用于从文本数据中提取特征;
documents输入的文本数据,通常是一个包含多个文本的列表;
method='tfidf'指定使用TF-IDF方法作为特征提取方法。
tfidf_features.toarray()将特征矩阵从稀疏矩阵格式转换为密集矩阵（NumPy 数组,便于查看, 输出每个文本的 TF-IDF 特征矩阵，矩阵中的每个元素表示对应特征词在文本中的 TF-IDF 值。

## **平衡处理样品**
<img src=https://github.com/jahahb/hahaha1/blob/main/test/sample%20balancing%20treatment.png  width = "800" />

## **添加模型评估度量**
<img src=https://github.com/jahahb/hahaha1/blob/main/test/add%20model%20evaluation%20metrics.png  width = "800" />
