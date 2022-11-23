# bigdata
### [MNIST](https://drive.google.com/file/d/1YAO_JI1bIScLM-phiQIsZCo3JCATLyMO/view?usp=sharing)
### WordCloud & SNA
```python
# 패키지 설치
install.packages('NLP')
install.packages('tm')
install.packages('sna')
install.packages('SnowballC')
install.packages("wordcloud")
install.packages("RColorBrewer")
```
```python
# 패키지 불러오기
library(NLP)
library(tm)
library(sna)
library(SnowballC)
library(wordcloud)
library(RColorBrewer)
```
#### Wordcloud 구현
```python
# 데이터 불러오기
file_path = "C:/Temp/patent.csv"
data = as.matrix(read.csv(file_path, header=TRUE, sep=",", stringsAsFactors=FALSE))
```
```python
# 전처리
dataCorpus = Corpus(VectorSource(data))
dataCorpus = tm_map(dataCorpus, stripWhitespace)
dataCorpus = tm_map(dataCorpus, tolower)
dataCorpus = tm_map(dataCorpus, removeNumbers)
dataCorpus = tm_map(dataCorpus, removePunctuation)
dataCorpus = tm_map(dataCorpus, removeWords, stopwords("english"))
```
```python
# 단어x문서 행렬 만들기
tdm = TermDocumentMatrix(dataCorpus)
tdm_matrix = as.matrix(tdm)
```
```python
# 단어 빈도
wordFreq = sort(rowSums(tdm_matrix), decreasing=TRUE)
```
```python
# 워드클라우드 그리기
pal = brewer.pal(8, "Dark2")
wordcloud(words=names(wordFreq), freq=wordFreq, scale=c(3, 0.8),
          min.freq=20, colors=pal, random.order=FALSE)
```
#### SNA 구현
```python
# 데이터 불러오기
file_path = "C:/Temp/keyword_network.csv"
data_file <- read.csv(file_path, header = TRUE, sep = ',', stringsAsFactors = FALSE)
names(data_file) <- c("title", "abstract")
head(data_file, 1)
```
```python
# 전처리
text_data <- as.matrix(paste(data_file$title, data_file$abstract))
text_data <- VCorpus(VectorSource(text_data))
text_data <- tm_map(text_data, content_transformer(tolower))
text_data <- tm_map(text_data, content_transformer(removePunctuation))
text_data <- tm_map(text_data, content_transformer(removeNumbers))
text_data <- tm_map(text_data, removeWords, stopwords("english"))
text_data <- tm_map(text_data, content_transformer(stemDocument))
text_data <- tm_map(text_data, content_transformer(stripWhitespace))

# You can add stopwords by yourself
text_data = tm_map(text_data,removeWords,c("also","can","may","well","even","just",
                                           "will","look","available","using","like","though","now","ufffdufffd","last",
                                           "make","yet","get","much","might","google","still","said","neededundo","googles",
                                           "far","told", "view gallery", "bgrcom", "new york", "first time", "HEADLINE:",
                                           "BODY:", "headline apple", "per cent", "body apple", "body apples" ,"san francisco",
                                           "apple inc", "steve jobs", "mr jobs", "chief executive", "tim cook", "mr cook",
                                           "headline apples", "including", "wherein", "based", "etc", "according", "define",
                                           "least", "includes", "include", "whereby", "whenever", "whether", "whose", "via",
                                           "totle", "therefore", "therefor", "thereby", "thus"))
```
```python
# 단어x문서 행렬 만들기
tdm <- as.matrix(TermDocumentMatrix(text_data))
tdm_order <- order(rowSums(tdm), decreasing = TRUE)
```
```python
# 상위 단어만 추출하기
core_keyword <- tdm[tdm_order[c(1:10, 700:720)],]
core_keyword <- tdm[tdm_order[1:15],]
comatrix <- core_keyword %*% t(core_keyword)
```
```python
# SNA 그리기
# Reference: http://melissaclarkson.com/resources/R_guides/documents/gplot_Ver2.pdf
gplot(comatrix, displaylabels = TRUE, vertex.cex = .6, label.cex = .9, arrowhead.cex = .3,
      mode = "fruchtermanreingold")
```
```python
# 다양한 모양으로 그려보기

# mode=circle
gplot(comatrix, displaylabels = TRUE, vertex.cex = .6, label.cex = .9, arrowhead.cex = .3,
      mode = "circle")

# mode=spring
gplot(comatrix, displaylabels = TRUE, vertex.cex = .6, label.cex = .9, arrowhead.cex = .3,
      mode = "spring")

# mode=springrepulse
gplot(comatrix, displaylabels = TRUE, vertex.cex = .6, label.cex = .9, arrowhead.cex = .3,
      mode = "springrepulse")

# mode=target
gplot(comatrix, displaylabels = TRUE, vertex.cex = .6, label.cex = .9, arrowhead.cex = .3,
      mode = "target")
```
