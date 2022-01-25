---
layout: single
title: "Stop Words"
date: "2021-11-26 09:18:00 +0900"
last_modified_at: "2021-11-26 13:45:00 +0900"
---

# Stop Words

# 불용어
갖고 있는 데이터에서 유의미한 단어 토큰만을 선별하기 위해서는 큰 의미가 없는 단어 토큰을 제거하는 작업이 필요합니다. 여기서 큰 의미가 없다라는 것은 자주 등장하지만 분석을 하는 것에 있어서는 큰 도움이 되지 않는 단어들을 말합니다. 예를 들면, I, my, me, over, 조사, 접미사 같은 단어들은 문장에서는 자주 등장하지만 실제 의미 분석을 하는데는 거의 기여하는 바가 없는 경우가 있습니다. 이러한 단어들을 불용어(stopword)라고 하며, NLTK에서는 위와 같은 100여개 이상의 영어 단어들을 불용어로 패키지 내에서 미리 정의하고 있습니다.

a  an the always

# spacy 라이브러리로 불용어 제거


```python
import spacy
nlp = spacy.load('en_core_web_sm')
```


```python
print(nlp.Defaults.stop_words)
```

    {'without', 'over', 'also', 'whatever', '‘m', 'anyhow', 'as', 'someone', 'seeming', 'its', 'moreover', 'do', 'one', "'ve", 'own', 'our', 'n‘t', 'further', 'third', 'really', 'everyone', 'against', 'rather', 'seemed', 'before', 'among', 'hence', 'i', 'across', 'now', 'did', 'quite', 'many', 'does', 'nowhere', 'those', 'two', 'became', 'behind', 'enough', 'more', 'any', 'twelve', 'beforehand', 'their', 'everything', 'you', 'should', 'when', 'within', '‘d', 'amongst', 'by', 'herself', 'however', 'whereafter', 'put', 'have', 'myself', 'in', 'become', 'anyway', 'first', 'up', 'give', 'this', 'a', 'others', 'so', 'using', 'alone', 'serious', 'where', "'re", 'eleven', 'forty', 'seem', 'hereafter', 'perhaps', 'sometime', 'them', 'until', 'three', 'call', 'thereupon', 'go', 'otherwise', 'whose', 'is', 'or', 'during', 'thru', 'either', 'ever', 'cannot', 'take', 'bottom', 'although', 'themselves', 'whereas', 'towards', 'and', '’ve', 'eight', 'ourselves', '’ll', 'yet', 'always', 'had', 'out', 'yourself', 'becoming', 'most', 'hundred', 'after', 'please', 'about', 'his', 'through', 'next', 'every', 'what', 'would', 'but', 'yours', "'d", 'doing', 'formerly', 'an', 'whence', 'toward', 'via', 'who', 'he', 'ours', '’re', 'latter', 'make', 'four', 'mine', 'well', 'several', 'it', 'which', 'along', 'again', 'must', 'they', 'used', 'meanwhile', 'nobody', 'between', '‘ve', 'say', 'since', 'beside', "'ll", 'done', 'not', 'wherein', 'has', 'her', 'us', 'hereby', 'never', 'much', 'another', 'of', 're', 'due', 'how', 'five', '‘ll', 'itself', 'under', 'anyone', 'then', 'here', 'can', 'whom', 'nor', 'show', 'will', 'various', 'seems', 'somehow', 'why', 'both', 'sixty', 'neither', 'elsewhere', '‘re', 'regarding', 'she', 'once', 'could', 'might', 'other', 'wherever', 'back', 'noone', 'himself', 'together', 'whether', 'been', 'no', 'less', 'there', 'keep', 'side', 'that', 'per', "n't", 'except', 'thus', 'below', 'herein', 'becomes', 'from', 'somewhere', 'too', 'with', 'such', 'whereupon', 'indeed', 'just', 'are', 'to', 'sometimes', 'these', 'none', 'therein', 'twenty', 'thereafter', 'thence', "'m", 'into', 'amount', 'besides', 'whenever', 'yourselves', 'part', 'very', 'off', 'nevertheless', 'last', 'your', 'see', 'may', '’d', 'few', 'for', 'therefore', 'am', 'onto', '’m', 'everywhere', 'nine', 'be', 'thereby', 'down', 'fifty', 'around', 'move', 'than', 'the', 'were', 'him', 'throughout', 'while', 'hers', 'being', 'whoever', 'on', 'if', 'some', 'front', 'hereupon', 'unless', 'made', 'all', 'same', 'upon', 'me', 'former', "'s", 'latterly', 'we', 'often', 'though', 'n’t', 'at', 'even', 'whereby', 'already', 'top', 'beyond', 'full', 'above', 'six', 'whole', 'was', 'afterwards', 'mostly', 'almost', 'else', 'something', 'my', 'nothing', 'only', 'still', 'empty', 'name', '‘s', '’s', 'whither', 'anything', 'ten', 'get', 'anywhere', 'each', 'because', 'ca', 'namely', 'fifteen', 'least'}



```python
len(nlp.Defaults.stop_words)
```




    326




```python
# is stop: Is the token part of a stop list, i.e. the most common words of the language?
nlp.vocab['always'].is_stop
```




    True




```python
nlp.vocab['finance'].is_stop
```




    False




```python
nlp.vocab['asdf'].is_stop
```




    False




```python
nlp.Defaults.stop_words.add('asdf') #불용어 추가됨
```


```python
nlp.vocab['asdf'].is_stop = True
```


```python
nlp.vocab['asdf'].is_stop #불용어 추가 되었나 true이면 추가되었음
```




    True




```python
len(nlp.Defaults.stop_words)
```




    327




```python
nlp.vocab['no'].is_stop
```




    True




```python
nlp.vocab['no'].is_stop = False
```


```python
nlp.vocab['no'].is_stop # no 제거안됨
```




    False




```python
print(nlp.Defaults.stop_words)
```

    {'without', 'over', 'also', 'whatever', '‘m', 'anyhow', 'as', 'someone', 'seeming', 'its', 'moreover', 'do', 'one', "'ve", 'own', 'our', 'n‘t', 'further', 'third', 'really', 'everyone', 'against', 'rather', 'seemed', 'before', 'among', 'hence', 'i', 'across', 'now', 'did', 'quite', 'many', 'does', 'nowhere', 'those', 'two', 'became', 'behind', 'enough', 'more', 'any', 'twelve', 'beforehand', 'their', 'everything', 'you', 'should', 'when', 'within', '‘d', 'amongst', 'by', 'herself', 'however', 'whereafter', 'put', 'have', 'myself', 'in', 'become', 'anyway', 'first', 'up', 'give', 'this', 'a', 'others', 'so', 'using', 'alone', 'serious', 'where', "'re", 'eleven', 'forty', 'seem', 'hereafter', 'perhaps', 'sometime', 'them', 'until', 'three', 'asdf', 'call', 'thereupon', 'go', 'otherwise', 'whose', 'is', 'or', 'during', 'thru', 'either', 'ever', 'cannot', 'take', 'bottom', 'although', 'themselves', 'whereas', 'towards', 'and', '’ve', 'eight', 'ourselves', '’ll', 'yet', 'always', 'had', 'out', 'yourself', 'becoming', 'most', 'hundred', 'after', 'please', 'about', 'his', 'through', 'next', 'every', 'what', 'would', 'but', 'yours', "'d", 'doing', 'formerly', 'an', 'whence', 'toward', 'via', 'who', 'he', 'ours', '’re', 'latter', 'make', 'four', 'mine', 'well', 'several', 'it', 'which', 'along', 'again', 'must', 'they', 'used', 'meanwhile', 'nobody', 'between', '‘ve', 'say', 'since', 'beside', "'ll", 'done', 'not', 'wherein', 'has', 'her', 'us', 'hereby', 'never', 'much', 'another', 'of', 're', 'due', 'how', 'five', '‘ll', 'itself', 'under', 'anyone', 'then', 'here', 'can', 'whom', 'nor', 'show', 'will', 'various', 'seems', 'somehow', 'why', 'both', 'sixty', 'neither', 'elsewhere', '‘re', 'regarding', 'she', 'once', 'could', 'might', 'other', 'wherever', 'back', 'noone', 'himself', 'together', 'whether', 'been', 'no', 'less', 'there', 'keep', 'side', 'that', 'per', "n't", 'except', 'thus', 'below', 'herein', 'becomes', 'from', 'somewhere', 'too', 'with', 'such', 'whereupon', 'indeed', 'just', 'are', 'to', 'sometimes', 'these', 'none', 'therein', 'twenty', 'thereafter', 'thence', "'m", 'into', 'amount', 'besides', 'whenever', 'yourselves', 'part', 'very', 'off', 'nevertheless', 'last', 'your', 'see', 'may', '’d', 'few', 'for', 'therefore', 'am', 'onto', '’m', 'everywhere', 'nine', 'be', 'thereby', 'down', 'fifty', 'around', 'move', 'than', 'the', 'were', 'him', 'throughout', 'while', 'hers', 'being', 'whoever', 'on', 'if', 'some', 'front', 'hereupon', 'unless', 'made', 'all', 'same', 'upon', 'me', 'former', "'s", 'latterly', 'we', 'often', 'though', 'n’t', 'at', 'even', 'whereby', 'already', 'top', 'beyond', 'full', 'above', 'six', 'whole', 'was', 'afterwards', 'mostly', 'almost', 'else', 'something', 'my', 'nothing', 'only', 'still', 'empty', 'name', '‘s', '’s', 'whither', 'anything', 'ten', 'get', 'anywhere', 'each', 'because', 'ca', 'namely', 'fifteen', 'least'}



```python
nlp.Defaults.stop_words.remove('no') #no는 remove으로 제거할 수 있음
```


```python
nlp.vocab['no'].is_stop = False
```


```python
nlp.vocab['no'].is_stop
```




    False




```python
print(nlp.Defaults.stop_words)
```

    {'without', 'over', 'also', 'whatever', '‘m', 'anyhow', 'as', 'someone', 'seeming', 'its', 'moreover', 'do', 'one', "'ve", 'own', 'our', 'n‘t', 'further', 'third', 'really', 'everyone', 'against', 'rather', 'seemed', 'before', 'among', 'hence', 'i', 'across', 'now', 'did', 'quite', 'many', 'does', 'nowhere', 'those', 'two', 'became', 'behind', 'enough', 'more', 'any', 'twelve', 'beforehand', 'their', 'everything', 'you', 'should', 'when', 'within', '‘d', 'amongst', 'by', 'herself', 'however', 'whereafter', 'put', 'have', 'myself', 'in', 'become', 'anyway', 'first', 'up', 'give', 'this', 'a', 'others', 'so', 'using', 'alone', 'serious', 'where', "'re", 'eleven', 'forty', 'seem', 'hereafter', 'perhaps', 'sometime', 'them', 'until', 'three', 'asdf', 'call', 'thereupon', 'go', 'otherwise', 'whose', 'is', 'or', 'during', 'thru', 'either', 'ever', 'cannot', 'take', 'bottom', 'although', 'themselves', 'whereas', 'towards', 'and', '’ve', 'eight', 'ourselves', '’ll', 'yet', 'always', 'had', 'out', 'yourself', 'becoming', 'most', 'hundred', 'after', 'please', 'about', 'his', 'through', 'next', 'every', 'what', 'would', 'but', 'yours', "'d", 'doing', 'formerly', 'an', 'whence', 'toward', 'via', 'who', 'he', 'ours', '’re', 'latter', 'make', 'four', 'mine', 'well', 'several', 'it', 'which', 'along', 'again', 'must', 'they', 'used', 'meanwhile', 'nobody', 'between', '‘ve', 'say', 'since', 'beside', "'ll", 'done', 'not', 'wherein', 'has', 'her', 'us', 'hereby', 'never', 'much', 'another', 'of', 're', 'due', 'how', 'five', '‘ll', 'itself', 'under', 'anyone', 'then', 'here', 'can', 'whom', 'nor', 'show', 'will', 'various', 'seems', 'somehow', 'why', 'both', 'sixty', 'neither', 'elsewhere', '‘re', 'regarding', 'she', 'once', 'could', 'might', 'other', 'wherever', 'back', 'noone', 'himself', 'together', 'whether', 'been', 'less', 'there', 'keep', 'side', 'that', 'per', "n't", 'except', 'thus', 'below', 'herein', 'becomes', 'from', 'somewhere', 'too', 'with', 'such', 'whereupon', 'indeed', 'just', 'are', 'to', 'sometimes', 'these', 'none', 'therein', 'twenty', 'thereafter', 'thence', "'m", 'into', 'amount', 'besides', 'whenever', 'yourselves', 'part', 'very', 'off', 'nevertheless', 'last', 'your', 'see', 'may', '’d', 'few', 'for', 'therefore', 'am', 'onto', '’m', 'everywhere', 'nine', 'be', 'thereby', 'down', 'fifty', 'around', 'move', 'than', 'the', 'were', 'him', 'throughout', 'while', 'hers', 'being', 'whoever', 'on', 'if', 'some', 'front', 'hereupon', 'unless', 'made', 'all', 'same', 'upon', 'me', 'former', "'s", 'latterly', 'we', 'often', 'though', 'n’t', 'at', 'even', 'whereby', 'already', 'top', 'beyond', 'full', 'above', 'six', 'whole', 'was', 'afterwards', 'mostly', 'almost', 'else', 'something', 'my', 'nothing', 'only', 'still', 'empty', 'name', '‘s', '’s', 'whither', 'anything', 'ten', 'get', 'anywhere', 'each', 'because', 'ca', 'namely', 'fifteen', 'least'}


# nltk 라이브러리로 불용어 제거



```python
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize

```


```python
word = "불용어란 자주 등장 하지만 데이터를 분석하는데 큰 의미를 갖지 않는 단어들을 뜻합니다"
```


```python
stop = "자주,종종,가끔,많이"
```


```python
stop_list = stop.split(',')
```


```python
tok = word_tokenize(word)
```


```python
def stopword(word_tokenize): # 불용어 제거 함수
    result = []

    for w in word_tokenize:
        if w not in stop_list:
            result.append(w)

    return result
```


```python
print("토큰화한 문장은"+ str(tok)+ "입니다.")
```

    토큰화한 문장은['불용어란', '자주', '등장', '하지만', '데이터를', '분석하는데', '큰', '의미를', '갖지', '않는', '단어들을', '뜻합니다']입니다.



```python
print("토큰화한 문장은"+ str(stopword(tok))+ "입니다.")
```

    토큰화한 문장은['불용어란', '등장', '하지만', '데이터를', '분석하는데', '큰', '의미를', '갖지', '않는', '단어들을', '뜻합니다']입니다.


