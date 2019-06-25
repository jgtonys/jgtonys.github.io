---
layout: post
title:  "텍스트 마이닝 프로젝트 : 중고거래 분석 (중고나라)"
category: BigData
tags: [textmining, python, html]
---


## **지역별, 시간대별 중고거래 분석(중고나라)**

중고나라의 거래 자료들을 크롤링하여 데이터를 쌓고 그 데이터를 가지고 분석을 진행하였다. 데이터를 전처리하고 토큰화하여 명사 단위로 인식할 수 있도록 처리하였고 단어 출현 빈도수와 동시출현 네트워크를 형성했다. 결론적으로 LDA를 활용하여 토픽모델링을 하고 시각화하여 중고 거래의 트랜드를 분석하였다.

<!-- more -->

---

## **프로젝트 진행 순서**

![C62280DB-9D11-4AA8-965E-5CE5DFBEE904](https://user-images.githubusercontent.com/33674947/60061368-e1428180-972f-11e9-8934-9f2eac494338.png)

1. 먼저 중고나라 페이지의 거래 데이터들을 크롤링한다

2. 모아진 데이터를 전처리하고 토큰화한다.

3. 처리된 데이터의 단어 빈도수를 통해 중요 단어에 대한 단서를 파악하고 동시출현 네트워크를 통해 등장하는 단어들의 관계를 파악한다.

4. 토픽모델링과 시각화를 통해 지역별,시간대별로 가장 거래가 활발한 단어(주제)를 파악한다.

5. 프로젝트의 결과를 분석한다

<br>

위와 같은 순서로 프로젝트가 진행되었으며, 모든 처리는 python 의 모듈들을 사용하여 처리하였다. 상세 구현 방법은 다음과 같다.


<br>

---

## **Crawling**

중고나라 데이터를 크롤링하는 과정에서 문제점을 발견했다. 기존에 쉽게 크롤링할 수 있는 request 모듈을 사용하려 했으나 동적으로 생성되는 웹 페이지의 내용은 가져올 수 없었다. **웹이 로드되고 나서 내용이 동적으로 생성되기 때문이다(dynamic DOM)**

중고나라의 경우에는 iframe 을 사용하여 게시판의 내용이 형성되는데 이 정보들을 가져오기 위해 가상 테스팅 툴인 Selenium 을 사용하였다.

Selenium 의 Chrome Driver 를 사용하면 실제로 사람이 클릭하는 결과를 크롤링 할 수 있다.

<br>

크롤러는 크롤러를 제어하는 driver.py 파일과 실제로 크롤링을 수행하는 selenium_crawler.py 로 이루어진다. 이렇게 나누게 된 이유는 여러 메뉴와 페이지를 돌며 크롤링을 수행할 때 그 수집된 데이터를 따로 정해진 위치와 이름으로 저장하기 위해서다.

selenium 의 사용법은 여기에서 확인할 수 있다.

{% reference https://www.javatpoint.com/selenium-tutorial %}

<br>

*driver.py*

```python
from selenium import webdriver
from selenium_crawler import main as crawler
import json

"""
Default selenium webdriver setting

variables:
headless_flag   -- selenium chrome browser headless or not
options         -- selenium chrome browser options
"""

headless_flag = True

if headless_flag:
    options = webdriver.ChromeOptions()
    options.add_argument('headless')
    options.add_argument('window-size=1920x1080')
    options.add_argument("disable-gpu")
    driver = webdriver.Chrome('chromedriver', options=options)
else:
    driver = webdriver.Chrome('chromedriver')

# Wait until driver load resources
driver.implicitly_wait(3)

"""
Main crawling task

file:
selenium_crawler.py

module:
main(location, link, filename, driver)    -- main crawler

main arguments:
location    -- korean location name
link        -- browser menu id
filename    -- path result stored
driver      -- selenium driver
"""

with open('source/location_data.json') as loca_data:
    ld = json.load(loca_data)

for d in ld:
    crawler(d['loca'], d['link'], d['filename'], driver)
```

위의 코드에서 주목해야 할 부분은 webdriver 에 ChromeOptions 를 주어 headless(웹 페이지가 보이지 않게 하는 옵션)를 설정하거나 gpu 가속을 줄 수 있다. 또한 implicitly_wait 은 webdriver 가 로드될 때까지 기다리는 역할을 한다(안정성).

크롤러를 제어하는 부분은 `source/location_data.json` 파일에 담겨있는 크롤링 대상 지역,링크,저장될 데이터 파일이름 등을 인식하고 메인크롤러에 인자로 넘겨주어 실행한다. 다음은 `location_data.json` 의 모습이다.

*location_data.json 일부*

```json
[
  {
    "loca" : "서울 중고나라",
    "link" : "menuLink1710",
    "filename" : "outputs/서울/중구"
  },
  {
    "loca" : "서울 중고나라",
    "link" : "menuLink1711",
    "filename" : "outputs/서울/용산구"
  },
  {
    "loca" : "서울 중고나라",
    "link" : "menuLink1712",
    "filename" : "outputs/서울/성동구"
  },
  {
    "loca" : "서울 중고나라",
    "link" : "menuLink1713",
    "filename" : "outputs/서울/광진구"
  },
]
```

<br>

메인크롤러는 다음과 같다.

```python
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import codecs

def main(location, link, filename, driver):
    driver.get('http://cafe.naver.com/joonggonara')
    wait = WebDriverWait(driver, 30)
    wait.until(EC.visibility_of_element_located((By.CSS_SELECTOR, "a.gm-tcol-c")))

    location_group = driver.find_element_by_css_selector("a[title*='" + location + "']")
    driver.execute_script("arguments[0].scrollIntoView();", location_group)
    location_group.click();

    elm = driver.find_element_by_id(link)
    driver.execute_script("arguments[0].scrollIntoView();", elm)
    elm.click()

    iframe_element = driver.find_element_by_css_selector("iframe#cafe_main")
    driver.switch_to.frame(iframe_element)

    first = 0
    end = 11
    page_10th = 99

    f = codecs.open(filename+"(2019).txt", 'w', encoding='utf-8')
    fo = codecs.open(filename + "(~2019).txt", 'w', encoding='utf-8')

    file_switch_flag = False

    notice_hidden = driver.find_element_by_css_selector("label[for*='notice_hidden']")
    driver.execute_script("arguments[0].scrollIntoView();", notice_hidden)
    notice_hidden.click()

    for ten in range(page_10th):
        for i in range(first, end):
            page_button = driver.find_element_by_class_name("prev-next")
            driver.execute_script("arguments[0].scrollIntoView();", page_button)
            page_buttons = driver.find_elements_by_css_selector("div.prev-next a")

            if (ten is not 0) and (len(page_buttons) <= 11) and (i is len(page_buttons)):
                debug_last_date = driver.find_elements_by_css_selector("td.td_date")
                if file_switch_flag:
                    fo.write("last date : " + debug_last_date[0].text)
                else:
                    f.write("last date : " + debug_last_date[0].text)

                print("last date : " + debug_last_date[0].text)
                print("DEBUG TERMINATION")
                return 0;

            page_buttons[i].click()

            if i is end-1:
                first = 1
                end = 12
            else:
                print("------------" + filename + " " + str(10 * ten + i) + "page-----------")
                articles = driver.find_elements_by_css_selector("td.td_article")
                date = driver.find_elements_by_css_selector("td.td_date")[0].text

                if date[:4] == "2018":
                    file_switch_flag = True

                for article in articles:
                    title_text = article.find_element_by_css_selector('a.article').text
                    if file_switch_flag:
                        fo.write(title_text+"\n")
                    else:
                        f.write(title_text+"\n")
```

이전의 driver.py 에서 인식한 json 파일의 내용을 인자로 받아 크롤러가 실행된다. 중고나라 페이지에 들어가서 `wait.until(EC.visibility_of_element_located((By.CSS_SELECTOR, "a.gm-tcol-c")))` 를 통해 크롤링하고자 하는 내용의 element 가 로드될 때까지 대기한다.

또한 selenium webdriver 는 실제 웹페이지가 시각적으로 윈도우 내에 보여야만 데이터를 가져올 수 있다. 이를 임의로 조정해 주기 위해 `driver.execute_script("arguments[0].scrollIntoView();", location_group)` 를 통해 특정 element 로 스크롤을 이동시켜 주었다.

중고나라는 내용이 iframe 안에 담겨있기 때문에 iframe 으로 이동하는 것은 `dirver.switch_to.frame(iframe_element)` 로 구현하였다.

그 이후의 상세한 크롤링은 pagination 을 돌며 게시글의 제목을 가져와서 텍스트파일에 저장하는 것이다. 텍스트 파일이 두개로 나누어진 이유는, 2019년도(올해) 와 그 이전의 데이터를 따로 분리해서 저장하기 위함이다. 저장되는 filename 은 인자로 받은 이름으로 사용된다. 더 이상 pagination 이 존재하지 않으면 terminate 한다.

*크롤링 후 저장된 파일의 모습*

![stored_documents](https://user-images.githubusercontent.com/33674947/60062634-0ab1dc00-9735-11e9-9145-e2fddbabbaed.jpg)

![stored_documents2](https://user-images.githubusercontent.com/33674947/60062638-0eddf980-9735-11e9-8ea7-8405a9e8fda4.jpg)


<br>

분석을 위해 **서울 - 중구**, **서울 - 종로구**,  **서울 - 용산구**, **서울 - 성동구** 데이터만을 사용하였다.

전체 수집된 서울 전 지역 약 18만건의 데이터 중, 분석으로 사용된 데이터는 총 **27085건**이다.

![898AD8AB-CC07-46BB-A950-2F8E9CF3EF7D](https://user-images.githubusercontent.com/33674947/60062803-a80d1000-9735-11e9-9125-d8c1671ca8a1.png)


<br>

---

## **Data Preprocessing**

크롤링된 실제 데이터에는 불필요한 정보들이 담겨있다. 프로젝트에서 필요한 정보는 주된 명사(단어) 이고, 이것을 제외한 다른 내용들을 전처리 해야만 했다.

한국어로 나타난 정보이기 때문에 Konlpy(twitter) 를 사용하여 처리하였다.

{% reference http://konlpy.org/en/latest/ %}

먼저 konlpy 의 original twitter 를 사용하여 데이터를 전처리 해 본 결과, 특정 고유명사(제품명, 영화제목, 줄임말) 등을 인식할 수 없는 단점이 존재했다. 따라서 많이 나타나는 고유명사를 파악하여 수동으로 twitter dictionary 를 추가하였다. 추가한 모습은 다음과 같다.

```python
new_nouns = []

with open('preprocess/dictionary.txt', encoding='utf8') as fd:
    for line in fd:
        new_nouns.append(line.strip('\n'))

twitter.add_dictionary(new_nouns, 'Noun')
```

미리 정의된 `preprocess/dictionary.txt` 파일에 정의된 고유명사를 twitter 에 추가하였다. 추가한 단어의 모습이다.

![image](https://user-images.githubusercontent.com/33674947/60065732-d7287f00-973f-11e9-9c14-169ea345904b.png)

<br>

조사나 쓸모없는 단어들(예를들어 미개봉, 최고 같은 단어들)을 불용어(stopwords)로 처리하였는데, 그 과정은 다음과 같다.

먼저 Stopwords의 처리와 n-gram인식 및 문자열 대체를 위해 Customize Konlpy(ckonlpy)를 사용하여 pos-tagging을 진행하였다.

> Stopwords(불용어) : 조사나 쓸모없는 단어들(예를들어 미개봉, 최고 같은 단어들)

> n-gram : 두개 이상의 단어가 합쳐진 형태

> pos-tagging : 품사를 구별하여 명시하는 것

```python
from ckonlpy.tag import Postprocessor
from ckonlpy.tag import Twitter
from ckonlpy.utils import load_wordset
from ckonlpy.utils import load_replace_wordpair
from ckonlpy.utils import load_ngram

passwords = load_wordset('postprocess/passwords.txt')
stopwords = load_wordset('postprocess/stopwords.txt')
replace = load_replace_wordpair('postprocess/replace.txt')
ngrams = load_ngram('postprocess/ngrams.txt')

twitter = Twitter()
new_nouns = []

with open('preprocess/dictionary.txt', encoding='utf8') as fd:
    for line in fd:
        new_nouns.append(line.strip('\n'))

twitter.add_dictionary(new_nouns, 'Noun')

passtags = {'Noun'}

postprocessor = Postprocessor(
    base_tagger = twitter,
    stopwords = stopwords,
    passtags = passtags,
    replace = replace,
    ngrams = ngrams
)
```

Base_tagger는 앞서 생성된 단어가 추가된 twitter를 사용한다. Stopwords, passtags, replace, ngram처리는 미리 정의된 txt파일을 불러와 processor를 생성한다. 이 processor는 사전추가된 twitter를 base_tagger 로 토큰화 하여 불용어와 n-gram 을 처리하여 명사만을 추출한다.

*불용어 처리의 모습*

![image](https://user-images.githubusercontent.com/33674947/60066502-50c16c80-9742-11e9-8dbc-5e9d35bbe568.png)

Tokenizing 파일의 경로로부터 ‘postprocess/stopwords.txt’ 에 불용어 리스트를 넣어둔다. 텍스트 파일의 단어는 줄단위로 추가된다. 분석에 용이하게 결과가 나타날 수 있도록 불용어를 처리하고 ngram(마찬가지로 ngram.txt 에 기입)을 추가하여 토큰화 하는 작업이다.

<br>

이렇게 만들어진 processor 로 크롤링 된 원본 데이터를 처리하면 다음과 같이 나타난다.

*서울 - 종로구(2019)*

![image](https://user-images.githubusercontent.com/33674947/60066648-c0cff280-9742-11e9-9eae-8c44d54db662.png)

추가된 고유명사를 인식하고 원본 텍스트에서 명사만 추출한 것을 확인할 수 있다.

실제 vectorize.py 코드를 실행하면 원본 텍스트 옆에 preprocessed 가 붙어 전처리된 데이터가 생성된다.

<br>

---

## **Word Counting**

이제 처리된 데이터를 가지고 어떤 단어가 얼마나 높은 빈도수로 나타났는지를 측정한다. 간단하게 단어들의 수를 측정하면 된다.

```python
filename = '성동구(~2019)'

with open('outputs/서울/' + filename + '.txt', 'r') as f:
    lines = f.read()
    pre_nouns = postprocessor.pos(lines)

for w in pre_nouns:
    nouns.append(w[0])

count = Counter(nouns)

pprint(count)

f = codecs.open('wordclouds/서울/' + filename+"_wordCounting.txt", 'w', encoding='utf-8')
f.write(str(count))

tagbox = count.most_common(40)
taglist = pytagcloud.make_tags(tagbox, maxsize=80)

pytagcloud.create_tag_image(taglist, 'wordclouds/서울/' + filename + '.jpg', size=(900, 600), fontname='korean', rectangular=False)
```

Filename 에 해당하는 파일을 읽고(크롤링된 원본 데이터), Word Counting 결과를 텍스트로 저장한다(wordclouds/지역/세부지역). 가장 상위 빈도 단어들(여기서는 상위 40개 단어)을 추출하고 pytagcloud를 사용하여 wordcloud로 시각화한다(wordclouds/지역/세부지역).

<br>

*서울 - 강서구(2019) WordCloud 모습*

![image](https://user-images.githubusercontent.com/33674947/60066876-6d11d900-9743-11e9-9507-e5949dcb8656.png)

<br>

---

## **Co-Occurrence Network**

이제 연관된 단어가 어떻게 이어져 있는지 확인할 수 있는 동시출현 네트워크(co-occurrence network)를 만든다. 동시출현 네트워크를 만들기 위해서는 먼저 동시출현 매트릭스(co-occurrence matrix)가 생성되어야 한다. 과정은 다음과 같다.

<br>

1. sklearn의 countVectorizer를 사용하여 co-occurrence matrix를 생성하기 위해 기존의 processor를 알맞은 format으로 변환한다.

2. 적절한 format으로 변환하기 위해 새로운 formatting class로 token을 생성한다.(MyTokenizer class)

3. countVectorizer로 벡터화 한 후 vectorizer.fit_transform을 통해 matrix를 생성한다.

4. Xc = (X.T * X) 를 통해 co-occurrence matrix를 생성한다.

5. Xc.setdiag(0)을 통해 같은 단어의 동시출현을 0으로 설정한다.

<br>

*Co-occurrence network 생성 코드*

```python
class MyTokenizer:
    def __init__(self, tagger):
        self.tagger = tagger
    def __call__(self, sent):
        pos = self.tagger.pos(sent)
        pos = ['{}/{}'.format(word,tag) for word, tag in pos]
        return pos


filename = '서울/종로구(2019)'

with open('outputs/' + filename + '.txt', 'r') as ft:
    text = ft.readlines()


my_tokenizer = MyTokenizer(postprocessor)
vectorizer = CountVectorizer(tokenizer = my_tokenizer)
X = vectorizer.fit_transform(text)

Xc = (X.T * X)
Xc.setdiag(0)
print(Xc.todense())

# pandas 를 사용하여 gephi 에서 보여줄 word-word co-occurrence matrix csv 파일을 생성
names = vectorizer.get_feature_names() # 엔티티 이름들을 보여준다
df = pd.DataFrame(data = Xc.toarray(), columns = names, index = names)
df.to_csv('gephi/' + filename + '.csv', sep = ',')
```

마지막에는 생성된 matrix 를 pandas 를 사용하여 column 과 index 를 설정하고 csv 파일로 저장한다. 이는 gephi 에서 사용될 자료를 만드는 과정이다.

<br>

생성된 csv 파일을 gephi 에서 불러와 시각화하면 다음과 같다.

> Layout : ForceAtlas 2 (default settings)

> Modularity : Node & Egde Color Grouping

*서울 - 종로구(2019) Co-occurrence Network*

![image](https://user-images.githubusercontent.com/33674947/60067254-e4943800-9744-11e9-8caa-375acc567f41.png)

확대하면 다음과 같다

![image](https://user-images.githubusercontent.com/33674947/60067281-042b6080-9745-11e9-859b-5a8182f46681.png)

이렇게 굉장히 많은 node 와 egde 가 나타난다.

<br>

좀 더 시각화를 용이하게 하기 위해 gephi 에서 modularity 를 분석하고 그 modularity 대로 색을 입혔다. 같은 문맥(클러스터)에 존재하는 단어들은 같은 색으로 표현되도록 한 것이다.

또한 너무 많은 정보가 얽혀있어 degree 가 낮은, 상대적으로 덜 중요한 노드들을 제거하고 *(Degree Range : 13~259 (30% visible)degree range 는 noise 에 따라 다르게 설정한다)* text size 를 PageRank 에 따라 부여하였다.

<br>

이렇게 설정한 결과 다음과 같이 나타난다.

![image](https://user-images.githubusercontent.com/33674947/60067473-ac412980-9745-11e9-905d-1bd28cb0f15d.png)

가전제품, 주방기구, 의류 등 각각의 품목끼리 클러스터를 지어(같은색으로) 존재하고, 또 그 제품군들에서 중심을 차지하거나 높은 관련성을 보이는 단어들이 크게 나타났다.

<br>

---

## **Topic Modeling**

이제 각 데이터 문서별로 주제가 되는 단어를 뽑아내는 토픽 모델링을 구현한다. 이 과정에서 결과로 나온 가장 유력한 주제는 그 시간대, 그 지역의 가장 거래가 활발한 제품으로 판단할 수 있다.

토픽모델링을 구현하기 위해서는 먼저 Term - Document Matrix 를 생성해야 한다. 생성하는 과정은 다음과 같다.

```python
id2word = corpora.Dictionary(token)
totalCorpus = token

## 출현단어 2개 이하는 무시하고 dictionary 생성

min_count = 3
word_counter = Counter((word for words in token for word in words))
removal_word_idxs = {
    id2word.token2id[word] for word, count in word_counter.items()
    if count < min_count
}

id2word.filter_tokens(removal_word_idxs)
id2word.compactify()
print('dictionary size : %d' % len(id2word))

## Term - Document Matrix 생성
corpus = [id2word.doc2bow(text) for text in totalCorpus]
```

여기서는 token 의 출현이 3회 미만인 단어들을 제외하고 dictionary 를 생성하였다.

<br>

이제 생성된 Term - Document Matrix 를 가지고 LDA를 적용한다. 이 때 LDA는  gensim.model의 ldaModel을 사용하는 것보다 mallet lda 를 적용하는 것이 더 좋았다(일관성 점수 기준). 구현은 다음과 같다.

```python
"""
lda_model = models.ldamodel.LdaModel(corpus=corpus,
id2word=id2word,
num_topics=20,
random_state=100,
update_every=1,
chunksize=100,
passes=10,
alpha='auto',
per_word_topics=True)
"""

# mallet lda 적용
mallet_path = 'source/mallet-2.0.8/bin/mallet'
lda_model = models.wrappers.LdaMallet(mallet_path, corpus=corpus, num_topics=20, id2word=id2word)

pprint(lda_model.print_topics())

# Compute Coherence Score
coherence_model_lda = models.CoherenceModel(model=lda_model, texts=totalCorpus, dictionary=id2word, coherence='c_v')
coherence_lda = coherence_model_lda.get_coherence()
print('\nCoherence Score: ', coherence_lda)
```

여기서 일관성 점수는 생성된 LDA 모델이 얼마나 잘 만들어졌는가를 판단하는 척도가 된다.

출력된 LDA 토픽은 상위 20개까지 나타나고 관련된 단어들이 출력되게 된다. 이것을 시각화하여 표현하기 위해 추가적으로 pyLDAvis 를 사용하였다. 그 결과 다음과 같이 나타난다.

![image](https://user-images.githubusercontent.com/33674947/60068218-c92b2c00-9748-11e9-9538-387161ae7321.png)

여기에는 가장 주제가 될법한 단어들을 선정하고 해당 단어가 다른 어떤 단어들과의 연관성을 가지는지 보여준다. LDA 의 결과 생성된 주제 단어는 그 문서의 주제 단어가 된다는 가정 하에 결과를 분석하였다.

<br>

---

## **Result Analysis**

![AFB2D91E-C341-4C1F-A5DF-DCD1FB7E3DC2](https://user-images.githubusercontent.com/33674947/60068334-317a0d80-9749-11e9-8b37-0678b4527c4e.png)

![2BADCDD3-058F-4809-B82D-1D1EC9B1E4D4](https://user-images.githubusercontent.com/33674947/60068340-3dfe6600-9749-11e9-8ea0-475b82c06a77.png)

### **4개 지역 단어 빈도수 분석 결과**

**전체적으로 휴대폰 및 차량의 거래가 활발하다**

서울 중구 : 다른 지역과 달리 의류의 거래가 활발하게 나타난다

서울 종로구 : 중고 차량의 거래가 활발하게 나타난다

서울 용산구 : 전자기기와 가전제품의 거래가 활발하게 나타난다

서울 성동구 : 서적 및 쿠폰의 거래가 가장 활발하게 나타난다

<br>

![24CA3A7B-C771-4808-BA97-0DE0F89FFBE8](https://user-images.githubusercontent.com/33674947/60068385-7e5de400-9749-11e9-8d50-1430b56bbbff.png)

![639C27ED-A4C2-443E-8DE2-701BFA636B2C](https://user-images.githubusercontent.com/33674947/60068411-95043b00-9749-11e9-83c6-f3fe35ae18bb.png)

![147E1C57-96E7-4EBF-BF12-B12BD4B79105](https://user-images.githubusercontent.com/33674947/60068447-b49b6380-9749-11e9-8269-2637799df78f.png)

![247E851F-EDA0-40C9-BE27-67D6DCF53665](https://user-images.githubusercontent.com/33674947/60068453-bf55f880-9749-11e9-8e5d-895c7ec88f7c.png)

![7A4A02D5-9C75-4C86-9CA1-A2C322E56B78](https://user-images.githubusercontent.com/33674947/60068474-d694e600-9749-11e9-9849-bd3b197723a8.png)

![7D0455B9-7E56-4B99-A53D-6B1112B0E039](https://user-images.githubusercontent.com/33674947/60068480-deed2100-9749-11e9-87dd-26971bc2a13f.png)

![79A6037D-B86D-4FB9-8BCE-BB6A43EE9B33](https://user-images.githubusercontent.com/33674947/60068489-ef9d9700-9749-11e9-9cb6-b8fbbc450f94.png)

![25ED0DB4-7AF6-4CB1-B5A3-A9C51314B133](https://user-images.githubusercontent.com/33674947/60068497-f75d3b80-9749-11e9-9e00-4c5f0873af93.png)

![151E7F5E-B3A3-42AF-9271-0A502679E3F7](https://user-images.githubusercontent.com/33674947/60068507-093ede80-974a-11e9-80e0-852d454fe01f.png)

![E31013AE-4494-49EC-8950-476DE33B968F](https://user-images.githubusercontent.com/33674947/60068515-11971980-974a-11e9-841a-f24623594b66.png)

![64C2CBCB-4A1B-40CE-BA74-80773A95989D](https://user-images.githubusercontent.com/33674947/60068536-24a9e980-974a-11e9-9753-02645f41c4d8.png)


<br>

---

## **Executable Code**

{% reference https://github.com/jgtonys/joonggonara_textmining %}
