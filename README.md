# BERT-embedding
A simple wrapper class for extracting features(embedding) and comparing them using BERT

## How to Use

### Installation
```bash
git clone https://github.com/seriousmac/BERT-embedding.git
cd BERT-embedding
pip install -r requirements.txt
wget https://storage.googleapis.com/bert_models/2018_11_23/multi_cased_L-12_H-768_A-12.zip
unzip multi_cased_L-12_H-768_A-12.zip -d bert/
```

### Run Test
```bash
python bert_embedding.py
```

### input and output
- bert.extracts(sentences)
  - input: list of string
  - output: list of dict
    - 'features': 입력한 문장 내 토큰 갯수 만큼의 list
      - 'token': 토큰 값
      - 'layers': list of layer dict
        - 'index': layer 번호
        - 'values': 768길이의 float값 list =extracting features(embedding)

### Example - 한 문장에서 embedding 추출하기
```python
from bert_embedding import BERT

bert = BERT()
bert.init()

sentence = "[OBS 독특한 연예뉴스 조연수 기자] 가수 겸 배우 수지가 '국민' 타이틀을 거머쥔 스타로 꼽혔다."
result = bert.extracet(sentence)
```

### Example 2 - 여러 문장에서 embedding 추출하기
```python
from bert_embedding import BERT

bert = BERT()
bert.init()

sentences = ['‘세계의 공장’으로 막대한 달러를 쓸어담으며 경제력을 키웠던 중국의 좋은 시절도 오래가지 않을 듯>하다.',
   '자본 유출과 서비스 수지 적자 폭이 커지며 경상수지 적자를 향해 빠르게 다가가고 있어서다.',
   "[OBS 독특한 연예뉴스 조연수 기자] 가수 겸 배우 수지가 '국민' 타이틀을 거머쥔 스타로 꼽혔다.",
   "OBS '독특한 연예뉴스'(기획·연출·감수 윤경철, 작가 박은경·김현선)가 '국민 신드롬'을 일으킨 첫사랑의 아이콘 >김연아, 수지, 설현의 근황을 살펴봤다."]
results = bert.extracts(sentences)
```


### Example 3 - 적은 수의 문장들에서 distance가 가장 가까운 문장 찾기
```python
from bert_embedding import BERT

bert = BERT()  
bert.init()

sentences = ['‘세계의 공장’으로 막대한 달러를 쓸어담으며 경제력을 키웠던 중국의 좋은 시절도 오래가지 않을 듯>하다.',
             '자본 유출과 서비스 수지 적자 폭이 커지며 경상수지 적자를 향해 빠르게 다가가고 있어서다.',
             '[OBS 독특한 연예뉴스 조연수 기자] 가수 겸 배우 수지가 국민 타이틀을 거머쥔 스타로 꼽혔다.',
             'OBS 독특한 연예뉴스(기획·연출·감수 윤경철, 작가 박은경·김현선)가 국민 신드롬을 일으킨 첫사랑의 아이콘 >김연아, 수지, 설현의 근황을 살펴봤다.',
             '오늘은 날씨가 좋습니다. 맛집을 찾아 가볼까요? 아이들이 좋아하더라구요.',
             '보쌈집에서는 보쌈을 맛있게 하면 그만입니다.ㅋㅋ']

results = bert.extracts(sentences)

distances = []
for i in range(len(results)):
  distance = []
  for j in range(len(results)):
    if i == j:
      distance.append(99999)
    else:
      distance.append(bert.cal_dif_cls(results[i], results[j]))
  distances.append(distance)

for idx in range(len(sentences)):
  print(sentences[idx])
  print(sentences[distances[idx].index(min(distances[idx]))])
  print()
```

출력 결과
```
‘세계의 공장’으로 막대한 달러를 쓸어담으며 경제력을 키웠던 중국의 좋은 시절도 오래가지 않을 듯>하다.
자본 유출과 서비스 수지 적자 폭이 커지며 경상수지 적자를 향해 빠르게 다가가고 있어서다.

자본 유출과 서비스 수지 적자 폭이 커지며 경상수지 적자를 향해 빠르게 다가가고 있어서다.
‘세계의 공장’으로 막대한 달러를 쓸어담으며 경제력을 키웠던 중국의 좋은 시절도 오래가지 않을 듯>하다.

[OBS 독특한 연예뉴스 조연수 기자] 가수 겸 배우 수지가 국민 타이틀을 거머쥔 스타로 꼽혔다.
OBS 독특한 연예뉴스(기획·연출·감수 윤경철, 작가 박은경·김현선)가 국민 신드롬을 일으킨 첫사랑의 아이콘 >김연아, 수지, 설현의 근황을 살펴봤다.

OBS 독특한 연예뉴스(기획·연출·감수 윤경철, 작가 박은경·김현선)가 국민 신드롬을 일으킨 첫사랑의 아이콘 >김연아, 수지, 설현의 근황을 살펴봤다.
[OBS 독특한 연예뉴스 조연수 기자] 가수 겸 배우 수지가 국민 타이틀을 거머쥔 스타로 꼽혔다.

오늘은 날씨가 좋습니다. 맛집을 찾아 가볼까요? 아이들이 좋아하더라구요.
보쌈집에서는 보쌈을 맛있게 하면 그만입니다.ㅋㅋ

보쌈집에서는 보쌈을 맛있게 하면 그만입니다.ㅋㅋ
오늘은 날씨가 좋습니다. 맛집을 찾아 가볼까요? 아이들이 좋아하더라구요.
```