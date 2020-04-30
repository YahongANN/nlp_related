## 目录
[2. 词典分词](#2-词典分词)
-    [2.1 什么是词](#2-1什么是词)
-    [2.2 词典](#2-2词典)
-    [2.3 切分算法](#2-3切分算法)
-    2.4 字典树
-    2.5 基于字典树的其它算法
-    2.6 HanLP的字典分词实现

## 2. 词典分词
- 中文分词：指的是将一段文字拆分为一系列单词的过程，这些单词顺序拼接后等于原文。
- 中文分词算法大致分为基于词典规则与基于起学习两大派

### 2.1 什么是词
- 在基于词典的中文分词中，词的定义要显示的多：词典中的字符串就是词。
- 词的性质--齐夫定律：一个单词的词频与它的词频排名成反比。

### 2.2 词典
开源的词库例如：[sougow15万个词条](https://www.sogou.com/labs/resource/w.php)，清华大学开放中文词库 [THUOCL](http://thuocl.thunlp.org), HanLP词库(千万级词条)

### 2.3 切分算法
首先，加载词典

```
def load_dictionary():
    dic = set()

    # 按行读取字典文件，每行第一个空格之前的字符串提取出来。
    for line in open("CoreNatureDictionary.mini.txt","r"):
        dic.add(line[0:line.find('	')])
    
    return dic
```
1. 完全切分
指的是，找出一段文本中的所有单词

```
def fully_segment(text, dic):
    word_list = []
    for i in range(len(text)):                  # i 从 0 到text的最后一个字的下标遍历
        for j in range(i + 1, len(text) + 1):   # j 遍历[i + 1, len(text)]区间
            word = text[i:j]                    # 取出连续区间[i, j]对应的字符串
            if word in dic:                     # 如果在词典中，则认为是一个词
                word_list.append(word)
    return word_list
  
dic = load_dictionary()
print(fully_segment('就读北京大学', dic))
```
输出

```
['就', '就读', '读', '北', '北京', '北京大学', '京', '大', '大学', '学']
```
输出了所有可能的单词。由于词库中含有单字，所以结果中也出现了一些单字。
2. 正向最长匹配

```
def forward_segment(text, dic):
    word_list = []
    i = 0
    while i < len(text):
        longest_word = text[i]                      # 当前扫描位置的单字
        for j in range(i + 1, len(text) + 1):       # 所有可能的结尾
            word = text[i:j]                        # 从当前位置到结尾的连续字符串
            if word in dic:                         # 在词典中
                if len(word) > len(longest_word):   # 并且更长
                    longest_word = word             # 则更优先输出
        word_list.append(longest_word)              # 输出最长词
        i += len(longest_word)                      # 正向扫描
    return word_list

dic = load_dictionary()
print(forward_segment('就读北京大学', dic))
print(forward_segment('研究生命起源', dic))
```


