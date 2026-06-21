---
layout: post
title: "Cosine Similarity & Vector Embedding"
subtitle: '코사인 유사도 & 벡터 임베딩 실행 메모'
author: "Mihyun"
header-style: text
tags:
  - math
  - ai
  - cosine similarity
  - vector
  - embedding
---

# 코사인 유사도 & 벡터 임베딩 실행 메모

## 1. 개념 요약

### 1.1. 코사인 유사도

두 벡터 \(A, B\) 사이의 코사인 유사도:

![](https://github.com/MilenaLee/milenalee.github.io/blob/a8af12685d8b87cae247a8d139d1d47a1e2a3ad7/img/in-post/Pasted%20image%2020260621233827.png?raw=true)

![](https://github.com/MilenaLee/milenalee.github.io/blob/a8af12685d8b87cae247a8d139d1d47a1e2a3ad7/img/in-post/Pasted%20image%2020260621233841.png?raw=true)

값 해석:

- 1   : 완전히 같은 방향 → 매우 유사
- 0   : 직각(orthogonal) → 관련성 없음에 가깝다
- -1  : 완전히 반대 방향 → 정반대 경향

> - 참고: https://phet.colorado.edu/sims/html/trig-tour/latest/trig-tour_all.html?locale=ko
---

## 2. 순수 파이썬으로 코사인 유사도 구현

### 2.1. 벡터 연산 헬퍼 함수

```python
# cosine_demo.py

import math

def dot(a, b):
    """두 벡터 a, b의 내적 계산"""
    return sum(x * y for x, y in zip(a, b))

def norm(a):
    """벡터 a의 유클리드 노름(길이) 계산"""
    return math.sqrt(sum(x * x for x in a))

def cosine_similarity(a, b):
    """코사인 유사도 계산"""
    na = norm(a)
    nb = norm(b)
    if na == 0 or nb == 0:
        # 길이가 0인 벡터는 방향이 정의되지 않으므로 처리 필요
        return 0.0
    return dot(a, b) / (na * nb)
```

### 2.2. 간단한 예제 1: 같은 방향, 직각, 반대 방향

```python
def demo_basic_vectors():
    # 같은 방향
    v1 = (1, 0)
    v2 = (2, 0)   # v1을 2배한 벡터 (방향 동일, 길이만 다름)

    # 직각
    v3 = (0, 1)   # v1과 직각 방향

    # 반대 방향
    v4 = (-1, 0)  # v1과 정확히 반대 방향

    print("cos(v1, v2) (같은 방향):      ", cosine_similarity(v1, v2))
    print("cos(v1, v3) (직각):          ", cosine_similarity(v1, v3))
    print("cos(v1, v4) (정반대 방향):   ", cosine_similarity(v1, v4))


if __name__ == "__main__":
    demo_basic_vectors()
```

실행 예시(이론적으로):

- cos(v1, v2) ≈ 1
- cos(v1, v3) = 0
- cos(v1, v4) = -1

---

## 3. 텍스트를 “단어 빈도 벡터”로 간단히 임베딩해서 코사인 유사도 보기

실제 NLP 임베딩(BERT 등) 대신, 개념 이해용으로 **아주 단순한 벡터화**를 해보자.  
문서에서 특정 단어들의 등장 횟수를 세어서 벡터로 만든다.

### 3.1. 단어 카운트 기반 벡터화

```python
from collections import Counter

def tokenize(text):
    """아주 단순한 토큰화: 공백 기준 split + 소문자화 정도만"""
    # 실제로는 형태소 분석, 정규화 등이 필요하지만 여기서는 개념용
    return text.lower().replace(",", " ").replace(".", " ").split()

def build_vocab(texts):
    """여러 문서에서 단어 사전(vocab) 생성"""
    vocab = {}
    idx = 0
    for text in texts:
        for token in set(tokenize(text)):  # set으로 중복 제거 후 사전에 추가
            if token not in vocab:
                vocab[token] = idx
                idx += 1
    return vocab

def text_to_vector(text, vocab):
    """
    단어 카운트를 이용해 벡터 생성.
    vocab 순서에 따라 각 단어의 등장 횟수를 배열에 담는다.
    """
    tokens = tokenize(text)
    counts = Counter(tokens)
    vec = [0] * len(vocab)
    for token, cnt in counts.items():
        if token in vocab:
            vec[vocab[token]] = cnt
    return vec
```

### 3.2. 예시: 고양이 vs 축구 vs 고양이 긍정/부정

```python
def demo_text_cosine():
    doc_cat_pos = "고양이는 정말 귀엽고 사랑스러운 동물이다"
    doc_cat_neg = "고양이는 시끄럽고 성가신 동물이라서 싫다"
    doc_dog     = "강아지는 귀엽고 활발한 동물이다"
    doc_soccer  = "축구 경기는 선수들이 공을 차서 골을 넣는 스포츠이다"

    texts = [doc_cat_pos, doc_cat_neg, doc_dog, doc_soccer]
    vocab = build_vocab(texts)

    v_cat_pos = text_to_vector(doc_cat_pos, vocab)
    v_cat_neg = text_to_vector(doc_cat_neg, vocab)
    v_dog     = text_to_vector(doc_dog, vocab)
    v_soccer  = text_to_vector(doc_soccer, vocab)

    print("\n=== 텍스트 코사인 유사도 예시 ===")
    print("고양이 긍정 vs 고양이 부정:", cosine_similarity(v_cat_pos, v_cat_neg))
    print("고양이 긍정 vs 강아지:    ", cosine_similarity(v_cat_pos, v_dog))
    print("고양이 긍정 vs 축구:      ", cosine_similarity(v_cat_pos, v_soccer))
    print("강아지 vs 축구:           ", cosine_similarity(v_dog, v_soccer))


if __name__ == "__main__":
    demo_basic_vectors()
    demo_text_cosine()
```

이 예시에서 기대할 수 있는 경향:

- “고양이 긍정 vs 고양이 부정”  
  - 단어들은 일부 겹치지만, 긍정/부정 단어가 달라서 완전 1은 안 나옴
- “고양이 긍정 vs 강아지”  
  - 동물/귀엽다 같은 단어가 겹쳐서 어느 정도 높은 값
- “고양이 긍정 vs 축구” / “강아지 vs 축구”  
  - 거의 단어가 안 겹치니 0에 더 가까운 작은 값

※ 여기서는 “태도(긍·부정)”까지 제대로 수치로 나오는 건 아니고,  
그냥 “공통 단어 패턴” 정도만 보는 매우 단순한 예제다.  
실제 의미/태도 임베딩은 BERT·Word2Vec·FastText 같은 모델이 필요하다.

---

## 4. 실제 임베딩 라이브러리로 의미/유사도 확인 (선택)

파이썬 환경에서 인터넷/설치가 가능하다면,  
`sentence-transformers`를 쓰면 문장 수준 의미 유사도도 쉽게 볼 수 있다.

### 4.1. 설치

```bash
pip install sentence-transformers
```

### 4.2. 코드 예시

```python
# semantic_demo.py

from sentence_transformers import SentenceTransformer
from sklearn.metrics.pairwise import cosine_similarity as sk_cosine_similarity

def demo_semantic():
    model = SentenceTransformer("sentence-transformers/all-MiniLM-L6-v2")

    sentences = [
        "고양이는 정말 귀엽고 사랑스러운 동물이다.",
        "고양이는 시끄럽고 성가신 동물이라서 싫다.",
        "강아지는 귀엽고 사람을 잘 따른다.",
        "축구 경기는 선수들이 공을 차서 골을 넣는 스포츠이다."
    ]

    embeddings = model.encode(sentences)

    def cos(a, b):
        return float(sk_cosine_similarity([a], [b])[0][0])

    print("고양이 긍정 vs 고양이 부정:",
          cos(embeddings[0], embeddings[1]))
    print("고양이 긍정 vs 강아지:",
          cos(embeddings[0], embeddings[2]))
    print("고양이 긍정 vs 축구:",
          cos(embeddings[0], embeddings[3]))
    print("강아지 vs 축구:",
          cos(embeddings[2], embeddings[3]))

if __name__ == "__main__":
    demo_semantic()
```

여기서 관찰 포인트:

- “고양이 긍정 vs 강아지”  
  → 꽤 높은 유사도 (둘 다 반려동물, 귀여움 쪽)
- “고양이 긍정 vs 고양이 부정”  
  → 주제는 같지만 태도가 다르므로,  
    “강아지” 문장과의 유사도보다 낮게 나올 수도 있음
- “축구” 관련 문장과 나머지  
  → 상대적으로 낮은 유사도

이런 값들이 실제로 어떻게 나오는지 확인하면  
“의미/태도까지 어느 정도 벡터에 담긴다”는 감각을 가질 수 있다.

---

## 5. 요약

1. `cosine_demo.py`  
   - 순수 파이썬으로 내적, 노름, 코사인 유사도 직접 구현  
   - 아주 단순한 “단어 카운트 기반” 텍스트 벡터로 코사인 유사도 실험

2. `semantic_demo.py` (선택)  
   - `sentence-transformers` 모델 사용  
   - 실제 의미/태도까지 어느 정도 반영된 문장 임베딩으로  
     코사인 유사도 확인
