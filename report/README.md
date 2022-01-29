# Categorizing Chinese News Articles from the Web

<p style="font-size: 20px;">2021 Introduction to Machine Learning Final Project Report</p>

**Author:** Chia-Hao Chang (張家豪)
**Student ID:** 0716021
**Year:** 3

# I. Introduction

In this project, we explore the machine learning pipeline and 3 different methods to do the following task: Categorize Chinese news articles from the web (given the title and content) into one of the following categories:

1. 科技 (Technology)
2. 產經 (Business and Economy)
3. 娛樂 (Entertainment)
4. 運動 (Sports)
5. 社會 (Society)
6. 政治 (Politics)

## Motivation

Reading news from multiple sources is quite a pain, and would like to go to a single website and read articles from all of the sources all at once. At the same time, I also want the articles to have categoric labels so that I can locate the news articles I want easily (by selecting the specific category). As we know, every news website may have its categorizing schemes, for example:

![](https://i.imgur.com/MqR5kt8.png)
![](https://i.imgur.com/OJAxCPo.png)
![](https://i.imgur.com/xNoMPQ4.png)

Although the categories are quite specific for each source, this becomes a problem when we keep all of the articles in the same place (e.g. a database). This is because there may be duplicate labels or labels that are too similar and can be put in the same category. Therefore, I would like to find out a generalized way to categorize/classify news articles given the title and content.

# II. Data Collection

## A. News Source Selection

Initially, I considered gathering news articles from 3 to 4 news sources including: The Liberty Times [自由時報](https://www.ltn.com.tw/), China Times [中國時報](https://www.chinatimes.com/?chdtv), United Daily [聯合新聞網](https://udn.com/news/index), and Central News Agency CNA [中央通訊社](https://www.cna.com.tw/). But after some observation, I've realized that since CNA is a *news agency* (通訊社) rather than a *newspaper*, many newspapers may contain articles directly obtained from CNA, which would make creating a dataset difficult (considering the fact that the training set and test set would contain identical data).

An example of identical articles is shown here:
* [旅客確診者多 中國取消自美入境航班遭華府批評 （CNA）](https://www.cna.com.tw/news/aopl/202201130033.aspx)
* [旅客確診者多 中國取消自美入境航班遭華府批評 （聯合新聞網）](https://udn.com/news/story/121707/6029914)

In order to prevent from identical articles appearing in the test set, I have decided to obtain news articles from The Central News Agency CNA (中央通訊社) only. I will then use the categories defined in the CNA website as the label to predict.

## B. Data Crawling

In order to obtain articles, I have written a python script that uses BeautifulSoup to crawl all news articles from CNA's website and stores the data into MongoDB.

After selecting relevant information, the info of the obtained dataset is as follows:

* Number of articles in total: 19826
* Categories: 6 (科技, 產經, 娛樂, 政治, 社會, 運動)
* Fields:
    * Title
    * Content
    * Category (Target to predict)
* News date range:
    * 2021 Februrary ~ 2022 January 10th

A single article is shown here:

```json=
{
    '_id': {'$oid': '602b59c9df6290789c381d06'},
    'title': '修憲工程春節後啟動 柯建銘：絕不走極端',
    'content':
    [
    '（中央社記者溫貴香、王揚宇台北16日電）力拚2022憲改公投綁大選，民進黨團總召柯建銘今天表示，春節連假過後將啟動修憲工程，會負責任提出黨版修憲案，並強調任何題目都可以談且都會去思考，但民進黨絕不會走極端。',
    '民進黨立法院黨團總召柯建銘受訪表示，修憲應由總統主導，涉及國家政府體制的建立，這是總統無法迴避的責任，總統也不會迴避，要尊重由總統主導。民進黨是執政黨，要主導國會修憲。',
      '他說，2019年12月的美麗島事件40周年、世界人權日，立法院會三讀通過監察院國家人權委員會組織法，監察院下設國家人權委員會，由監察院長擔任主任委員。同時，修正通過考試院組織法部分條文，考試委員名額從19人改為7人至9人，考試院長、副院長及考試委員任期從6年改為4年，與總統任期一致。',
      '柯建銘說，18歲公民權已是普世價值，過去民進黨一直強烈主張，自創黨以來，民進黨中央從來沒有停止過修憲的討論，這次也是一樣；民進黨執政必須尊重由總統主導修憲，包括中央政府體制、五權變三權、不分區席次票票不等值、閣揆同意權等，民進黨一定會提出一個版本。',
      '柯建銘表示，修憲必須要有黨版，哪些問題可能會成功，哪些問題屬於理念闡述，都必須仔細評估，最後一定會有黨版。他強調，修憲提案沒有併大選絕對不會成功，即使併大選要成功闖關，也必須朝野有高度共識，否則會一事無成。',
      '他表示，修憲提案要併2022年直轄市暨縣市長選舉，必須先往回推算，約大選日9個月前要送出修憲提案；因為修憲提案必須先公告半年加上3個月後投票，總計需9個月時間，前置作業又包括何時開公聽會、要拋出什麼議題等，必須先行設定期程。',
      '至於修憲併2022年大選時間是否太趕，柯建銘表示，「不會」，修憲案若不併2022年大選根本是自殺式修憲，下修18歲公民權的修憲案，若做民調並不是壓倒性的勝利，必須各政黨高度動員與合作才有可能超過965萬票，這絕對不是朝野政黨比賽搶功勞、爭功諉過的題目。',
      '柯建銘表示，這次修憲有很多題目，不管是個別立委提案或民間團體倡議，例如環境權、人權入憲、勞動權等，都可以談且都會去思考；另涉及變更領土、國號等敏感性議題，民進黨不會走極端，因為面對世界大變局，兩岸局勢多變，不能有任何挑釁。',
      '他表示，965萬票是高門檻，修憲不能變成政治舞台表演，把修憲意義破壞掉；2月16日春節過後要開始進行，包括設定議題、舉辦公聽會，民進黨有一定的步驟。',
      '民進黨立法院黨團修憲小組由柯建銘親自領軍並設有雙召委機制，由資深立委管碧玲、具法律專業背景立委周春米，在總統主導下，府院黨加上黨團協力共同推動；總統府則由副秘書長李俊俋、民進黨秘書長林錫耀、行政院政務委員羅秉成，加上立法院黨團三長，最後提出黨版修憲案。',
      '柯建銘表示，2022年若18歲公民權修憲案沒有通過，這說不過去，這是「我們這一代政治人物的責任」，因為過關是高門檻，朝野必須高度動員。',
      '憲法增修條文第12條規定，「憲法之修改，須經立法院立法委員1/4之提議，3/4之出席，及出席委員3/4之決議，提出憲法修正案，並於公告半年後，經中華民國自由地區選舉人投票複決，有效同意票過選舉人總額之半數，即通過之。」換句話說，以2020年總統大選的選舉人數換算，要超過965萬票才算通過，外界視為超高門檻。（編輯：林克倫）1100216'
    ],
    'category': '政治'
}
```

The distribution of the categories is shown as follows:

![](https://i.imgur.com/BtE1t5W.png)


# III. Preprocessing

## A. Data Cleaning

We would like to remove data that may affect our model while learning, for example, the reporter at the beginning of the article: `（中央社記者溫貴香、王揚宇台北16日電）` and the editor, date information in the end of the article: `（編輯：林克倫）1100216`.

This is because it is possible that `溫貴香` or `林克倫` always write news articles in a certain category (e.g. `政治`), which would let the model learn irrelevant information. Since we want our model to be more general and can classify articles from other sources (where the editor or reporter does not belong to), we remove the respective words.

The resulting dataset looks as follows:

![](https://i.imgur.com/cJW46Ab.png)

The `category` field is what we want to predict.

## B. Text Segmentation and Tokenization



Chinese and English differ a lot in the sense that English is naturally segmented by spaces, but we have to manually separate words in Chinese.

To do so, we make use of the tool [Jieba 中文分詞](https://github.com/fxsjy/jieba), which performs text segmentation on Chinese text.

Before performing segmentation, we concatenate the title and content so that the text forms a single corpus. Then as we separate the we remove the punctuations to make the data even cleaner.

The result looks as follows:

| Before | After Segmentation |
| -------- | -------- |
|李登輝逝世週年 日台協會設文庫專區追思 前總統李登輝逝世滿週年，日本... | 李登輝 逝世 週年 日台 協會 設 文庫 專區 追思 前總統 李登輝 逝世 滿週年 日本...     |
|男子捷運月台性騷擾女乘客 北院判拘役40天 一名楊姓男子去年8月間2度... | 男子 捷運 月台 性騷擾 女 乘客 北院 判 拘役 天 一名 楊姓 男子 去年 月間 度... |

(I've also removed numbers on purpose since further processing may take numbers into account.)

## C. Splitting the Train, Validation, Test set

Since the further tf-idf preprocessing stage takes the whole dataset into consideration (which would make us ***see*** the testing data if we did it first), we split the dataset into train, test, validation here.

We first split the data set into a training dataset and a testing dataset in a ratio of 7:3, the testing set will be used to evaulate the performance of the three models later.

Then, we split the training set further using holdout validation with ratio 7:3, where the new training holds 7/10 of the orginal training set and the validation set holds 3/10.


## D. TF-IDF term weighting

After separating a piece corpus into segments of words, we want to encode the words in an article into features of a *document* (a single row in the dataframe). 

Here, we use the term weighting scheme **TF-IDF (Term Frequency-Inverse Document Frequency)** to encode our text. TF-IDF in short, gives a word that appears frequently in one document (a high term-frequency (tf)), but not as frequent in all other documents (a high **inverse** document frequency (idf)) a higher weight, which can be thought of as *more important*. Then, we choose the top 10000 words with the highest frequencies as the features.

Scikit Learn provides a package function `tfidfVectorizer` to count the word frequencies then encode each article into a vector of 10000 features for us. Thus, by calling the package, it helps us to map each document in the training set to 10000 features.

In order to make sure that the mapping makes sense, we map the 10000 features to 2 dimensions using the t-SNE method, and plot the training set and its classes on it:


![](https://i.imgur.com/kA74WUY.jpg)


We can see that each category has a rough boundary that can be distinguished.

## E. Over-sampling the minority classes

Since the categories are quite unbalanced, we over-sample the minority classes to prevent mispredicting.

Originally, the testing set contains the following number of documents of each category:

| Category | Count    |
| -------- | -------- |
| 政治     | 2717     |
| 產經     | 2306     |
| 社會     | 2029     |
| 運動     | 1949     |
| 娛樂     | 470      |
| 科技     | 243      |

We over-sample the two classes `科技` and `娛樂` to 1000 samples.

| Category | Count    |
| -------- | -------- |
| 政治     | 2717     |
| 產經     | 2306     |
| 社會     | 2029     |
| 運動     | 1949     |
| 娛樂     | **1000**     |
| 科技     | **1000**     |

(After some experiments, the oversampling has improved about up to 50% of the recall score for the `科技` category in the Naive Bayes model, which shows that an imbalanced dataset may affect the performance a lot)

# IV. Models

We choose the three most common models to perform text classification:
* Logistic Regression
* Naive Bayes
* Artificial Neural Networks (Multilayer Perceptron)

For all models, we use off-the-shelf models provided by the sckit-learn package and train it on the encoded and upsampled training set above.

Note that the validation set is encoded separately from the training set (by `tfidfVectorizer.transform`), so that we won't take the words in the validation set into consideration while counting frequencies in tfidf.

## A. Logistic Regression

For the logistic regression model, we use the module `LogisticRegression` out of the box and train it on the augmented training set.

Then, we predict on the validation set, the obtained results are shown below:

![](https://i.imgur.com/VwXMM0S.png)


**Validation accuracy: 0.9551**

| Category | Precision| Recall   | 
| -------- | -------- | -------- |
| 政治     | 0.9499   | 0.9371   |
| 產經     | 0.9400   | 0.9483   |
| 社會     | 0.9614   | 0.9724   |
| 運動     | 0.9806   | 0.9829   |
| 娛樂     | 0.9512   | 0.9466   |
| 科技     | 0.8879   | 0.8636   |

A thing worth to note is that, before oversampling the minority classes 科技 and 娛樂, the recall score was 0.53 and 0.89 respectively, which shows that most of the articles belonging to 科技 was categorized to other classes (產經 in this case). Oversampling has prevented this situation from happening, and showed a 33% of improvement in the recall score of the 科技 class. The accuracy of the model also improved from 94.52% to 95.51% for the prediction of the validation.

## B. Naive Bayes Classifier

For the Naive Bayes model, I have chose to use the `MultinomialNB` module in sckit-learn, since it is one of the most common naive bayes model used for text classification.

The prediction results are shown below:

![](https://i.imgur.com/cBWnGMl.png)

**Validation accuracy: 0.9352**

| Category | Precision| Recall   | 
| -------- | -------- | -------- |
| 政治     | 0.9039   | 0.9229   |
| 產經     | 0.9211   | 0.9252   |
| 社會     | 0.9519   | 0.9552   |
| 運動     | 0.9815   | 0.9680   |
| 娛樂     | 0.9208   | 0.9029   |
| 科技     | 0.9255   | 0.7909   |

Similar to logistic regression, oversampling also showed a huge improvement in the recall score of the 科技 category, which raised from 21.48% to 79.09% (58% improvement). The overall accuracy has also improved from 92.11% to 93.52%.

## C. Artificial Neural Networks

Due to the results from the previous model, I have decided to just create a simple neural network, since a decent result could be obtained just by simple models.

The multi-layer perceptron consists of 3 layers, the input layer, output layer, and one hidden layer which includes 1024 neurons.

I have chosen adam as the optimizer with $\alpha=10^{-5}$, the results are shown below:

![](https://i.imgur.com/IvYdwIO.png)


**Validation accuracy: 0.9537**

| Category | Precision| Recall   | 
| -------- | -------- | -------- |
| 政治     | 0.9524   | 0.9388   |
| 產經     | 0.9270   | 0.9505   |
| 社會     | 0.9679   | 0.9679   |
| 運動     | 0.9818   | 0.9874   |
| 娛樂     | 0.9420   | 0.9466   |
| 科技     | 0.8673   | 0.7727   |

Surprisingly, oversampling shows almost no difference when predicting using the neural network.

# V. Results

The results of the final predictions (categorizations) on the testing set of each model are shown below.

## Logistic Regression
![](https://i.imgur.com/kBGj0PR.png)

**Accuracy: 0.9568**

| Category | Precision| Recall   | 
| -------- | -------- | -------- |
| 政治     | 0.9519   | 0.9468   |
| 產經     | 0.9438   | 0.9466   |
| 社會     | 0.9666   | 0.9712   |
| 運動     | 0.9832   | 0.9849   |
| 娛樂     | 0.9580   | 0.9580   |
| 科技     | 0.8523   | 0.8380   |

## Multinomial Naive Bayes
![](https://i.imgur.com/48EK5H1.png)

**Accuracy: 0.9375**

| Category | Precision| Recall   | 
| -------- | -------- | -------- |
| 政治     | 0.9062   | 0.9360   |
| 產經     | 0.9385   | 0.9159   |
| 社會     | 0.9560   | 0.9545   |
| 運動     | 0.9772   | 0.9698   |
| 娛樂     | 0.9368   | 0.9336   |
| 科技     | 0.8343   | 0.7877   |

## Artificial Neural Network

![](https://i.imgur.com/BjLv7Oi.png)

**Accuracy: 0.9593**

| Category | Precision| Recall   | 
| -------- | -------- | -------- |
| 政治     | 0.9532   | 0.9498   |
| 產經     | 0.9384   | 0.9576   |
| 社會     | 0.9720   | 0.9720   |
| 運動     | 0.9865   | 0.9849   |
| 娛樂     | 0.9719   | 0.9685   |
| 科技     | 0.8812   | 0.7877   |


# VI. Conclusion

As long as the preprocessing is done correctly and having a correctly labelled dataset, using any of the models proposed should do a decent job in classifying the news articles, thus, I think our goal can be satisfied.

In the following subsections, we look at the model prections on real world data, then we discuss the possible applications on this topic.

## A. Model Prediction on Real-World Unseen Data: Categorization Examples
Here, we fetch a few news articles as of January 13, 2022 from other news sources, and take a look at what the models will predict.

1. [自由時報：台北電玩展「手遊」躍升主角！多間家機、一線遊戲公司缺席不參加 01/12/2022](https://3c.ltn.com.tw/news/47364)
> 台北電玩展（TGS）往年是台灣玩家必定朝聖的一大盛會，各大品牌都會在活動釋出最新情報、遊戲試玩，雖然受惠於台灣防疫有成，2021、2022 年都能舉辦實體活動，陣容卻仍受到嚴重打擊。
2022 年台北電玩展將於 22 日開跑，今日主辦單位台北電腦公會正式公開活動內容與參展陣容，前一年被許多網友笑稱是「手遊展」，今年趨勢似乎更加明確，同時陣容更受到打擊，不僅 Sony、微軟等家機遊戲無緣展出，包含萬代、SEGA、Ubisoft 等一線遊戲公司也都沒有參加實體活動。...



| Model              | Logistic Regression | Naive Bayes | Artificial Neural Network  |
| --------           | ------------------- | ----------- | -------------------------- |
| Predicted Category | 科技                | 產經         |  科技                       |


2. [自由時報：證交法將修正 大股東持股5％要「全都露」01/12/2022](https://ec.ltn.com.tw/article/breakingnews/3797993)
> 金管會昨天（11日）宣布，將啟動修正「證券交易法」共有2大修正內容，第1是強化我國公司持股透明化，讓藏鏡人無所遁形、大股東全都露，持股「申報」及「公告」門檻，從原本規定10%修訂為5%。第2是提高裁罰門檻，若證券商等相關機構，未建立落實內稽內控等重大缺失，罰鍰上限也將由現行480萬元拉高到600萬元。
證交法最近一次修正為去年元月27日，而金管會今年要再度啟動修法。...

| Model              | Logistic Regression | Naive Bayes | Artificial Neural Network  |
| --------           | ------------------- | ----------- | -------------------------- |
| Predicted Category | 產經                | 產經         |  產經                       |

3. [中國時報：國民黨中常委提案改黨名 洪秀柱怒批：沒出息 01/13/2022](https://www.chinatimes.com/realtimenews/20220113005093-260407?ctrack=pc_politic_headl_p01&chdtv)
> 從四項公投到補選、罷免，國民黨在一個月內經歷了3場挫敗。國民黨有中常委提案，要修改國民黨的黨名為「台灣國民黨」。國民黨前主席洪秀柱今天（13日）對此怒斥，那是一個非常沒有出息的想法跟做法，如果黨內還是有人這樣去做，就離開這個政黨吧。洪秀柱13日受訪表示，民黨是個百年政黨，是創建中華民國的政黨，它的名字就叫中國國民黨...

| Model              | Logistic Regression | Naive Bayes | Artificial Neural Network  |
| --------           | ------------------- | ----------- | -------------------------- |
| Predicted Category | 政治                | 政治         |  政治                       |

We can see that most results follow what we assumed the categories of the articles should be, where the first article is 科技, second is 產經, and the third being 政治.

## B. Applications

> A tool to classify a set of news that come from several different sources

As I have mentioned in the introduction section, I would like to find out a generalized way to categorize/classify news articles given the title and content.

In this case, the classifier that we have trained can be used to provide a more general categorizing scheme. Therefore, if we had a database where we store news articles from many different sources, we can use any of the models to find out the category of the articles.
