---
title: "[DS] Bloom filter"
date: 2021-07-09 16:21:13
category: 'Algorithm'
draft: false
---

## What is Bloom Filter?
수많은 양의 정보가 저장된 데이터베이스에서 해당 데이터가 존재하는지 확인하는 작업에는 부하가 뒤따릅니다. 이를 위해 선형탐색을 사용한다면 너무나 오랜 시간이 소요될 것입니다. 이진 탐색도 좋은 방법이지만 더 나은 방법이 있을 것 같습니다.

이렇게 어떤 집합에 특정원소가 있는지 확인하는 작업을 `Membership Testing`이라고 합니다. 이러한 Membership Test를 위한 자료구조에는 Bloom filter, AVL, red-black tree등이 있습니다. 이중 Bloom filter는 확률적 자료구조로 에러를 허용하는 대신 공간복잡도를 낮추었습니다.

Bloom Filter는 Burtoon H. Bloom이 1970년의 논문 [Space/Time Trade-offs in Hash Coding with Allowable Errors](https://doi.org/10.1145/362686.362692)에서 제안한 확률적 자료구조입니다.

## Working

Bloom Filter는 다음과 같이 m개의 비트로 구성된 bit 배열 자료구조입니다.
![image](https://user-images.githubusercontent.com/28651727/125014874-6d226e80-e0a9-11eb-8069-1ef794123ea3.png)

### Insert
주어진 input값(test, )에 대해 k개의 hash function(h1,h2,h3,...,hk)을 통해 hash를 계산합니다.(3개의 hash function을 사용한다 가정하겠습니다.)

```cpp
// input : test
h1(test) % 10 = 0
h2(test) % 10 = 3
h3(test) % 10 = 7
// input  : monkey 
h1(monkey) % 10 = 3
h2(monkey) % 10 = 4
h3(monkey) % 10 = 5
```
![image](https://user-images.githubusercontent.com/28651727/125014940-8b886a00-e0a9-11eb-9cc4-b9ec22a9f59b.png)

![image](https://user-images.githubusercontent.com/28651727/125015064-bd013580-e0a9-11eb-89a4-b61b4c2b3240.png)

### Lookup(Membership Test)

Membership Test는 동일한 input을 주어 필터에 해당하는 해시값이 모두 1로 설정되어있다면 해당 input값이 존재할 것이라고 판단할 수 있습니다. 

### False Positive

**거짓양성(False Positive)**는 값이 존재하지 않음에도 불구하고 해당값이 존재할것이라 판단하는것을 말합니다.

```cpp
// input  : bloom
h1(bloom) % 10 = 3
h2(bloom) % 10 = 5
h3(bloom) % 10 = 7
```

![image](https://user-images.githubusercontent.com/28651727/125017529-4b77b600-e0ae-11eb-8a21-b0f655c2204b.png)

예를들어 위와 같이 'bloom'이라는 input을 조회했을때 해시값이 모두 1이므로 해당 값이 존재할 것이라 판단하게 됩니다. 하지만 bloom은 존재하지 않기때문에 이를 **거짓양성(False positive)**라고 합니다. 

이러한 거짓양성의 발생률은 Bloom Filter의 크기를 조절하여 조정할 수 있습니다. 당연히 Bloom Filter를 위해 더많은 공간(hash function, bit array)을 할당할수록 거짓양성률이 
낮아집니다. 

최적의 hash function의 개수 $k$는 $m$이 bit array의 크기, $n$이 삽입될 원소의 개수라고 한다면 다음과 같습니다.

<div align=center>
$k = \frac{m}{n}ln2$
</div>

### Space Efficiency
값을 저장하는 배열, 연결리스트, 해시맵, 트리와 같은 자료구조는 실제 값을 저장해야 하지만, Bloom Filter는 실제값을 저장하지 않고 bit array를 통해 표현하기 때문에 공간 복잡도가 낮습니다. 다만 이는 `hash collision`을 유발합니다.

> **Hash Collision(해시 충돌)**  
> Hash table의 크기가 충분히 크지 못해 서로다른 입력값이 동일한 hash값을 가지는 상황을 의미합니다.

## Bloom Filter의 종류(Classification)

- **Standard Bloom Filter(SBF)** : 전통적인 블룸필터로 거짓 양성과 거짓음성의 문제를 가지고 있습니다. 거짓양성은 비트 배열의 크기가 충분히 크기 못할때 발생하며, 거짓음성은 원소를 삭제할때 일어납니다. 이러한 문제때문에 SBF는 원소를 삭제할 수 없습니다. 
- **Counting Bloom Filter(CBF)** : SBF의 확장성 문제를 해결하기 위해 등장하였으며, 비트배열에 카운터를 포함합니다. 삽입연산이 발생하면 카운터를 증가시키고 삭제연산이 발생하면 카운터를 감소시켜 삽입/삭제가 가능하도록 구현하였습니다. SBF에 비해 거짓 양성의 비율이 매우 높고, 삭제연산으로 인해 거짓 음성이 발생할 수 있습니다.
- **Fingerprint Bloom Filter** : CBF기반으로 만들어 졌으며 binary bit를 사용하는 대신 작은 크기의 hash값을 사용하여 CBF의 높은 거짓 양성률을 해결하였습니다. 하지만 이로인해 CBF에 비해 높은 공간복잡도를 보입니다.
- **Hierachical Bloom Filter** : 트리구조의 Bloom filter로 높은 정확도, 확정성, 그리고 낮은 거짓 양성률을 보입니다. 대표적으로 Forest Structured Bloom filter, Bloom Store가 있습니다. 
- **Multidimensional Bloom Filter(MDBF)** : 다차원의 Bloom filter 배열을 사용합니다.
- **Compressed Bloom Filter** : 공간 효율적인 Bloom filter이지만 압축률이 높아질수록 거짓양성률이 매우 높아집니다.

## 활용

<div align=center>
  <img src="https://user-images.githubusercontent.com/28651727/125037029-a3beb000-e0ce-11eb-888b-f1be9063af19.png">
  <b>그림 출처:</b> <a href="https://en.wikipedia.org/wiki/Bloom_filter">위키피디아 블룸필터 문서</a>
</div>

Bloom Filter는 위의 그림과 같이 Filter는 메모리에 두고 실제 데이터는 Storage에 저장하여 디스크 접근시간으로 인한 성능저하를 개선할 수 있습니다. 하지만 이경우에도 거짓 양성으로인한 불필요한 디스크 접근이 발생할 수 있습니다. 