---
title: pytorch 헷갈리는 개념 정리1
description:
author: 박성호
date: 2025-04-08 17:10:00 +09:00
categories: [Study, AI]
tags: [AI, pytorch]
---

인공지능 공부를 막 시작해서 pytorch도 다뤄보게 되었는데, pytorch 모듈 중 기억해둘 만한 구문들을 나열식으로 정리하려고 포스팅합니다. 기록용 포스트라 pytorch를 공부하고자 하는 분들에게 큰 도움이 되지는 않을 것 같습니다. 공부하면서 pytorch 공식 사이트에 도움이 될만한 내용이 많다고 느껴서 참고해주시면 좋을 것 같습니다.

우선 pytorch에서는 다차원 array들을 tensor로 다루는데, 이는 numpy array와 같은 형태지만 torch 라이브러리의 모듈은 tensor 형태에만 적용 가능하기에 필요시 변환해주어야 합니다.

## torch.nn 모듈

---

이 모듈 내의 기능들을 이용하려면 우선
`import torch`와 `import torch.nn as nn` 구문이 선행되어야 합니다.

### nn.module

이 상위 클래스 하에서 각각의 layer들이 선언됨,` __init__()`과 `forward()` 메서드는 무조건 필요

### nn.sequential

`__init__()` 메서드에서 연속된 layer들을 묶어서 선언하는데 이용

### nn.functional.cross_entropy

log_softmax와 nll_loss(negative log likelihood loss) 함수의 기능을 합한 것. cross entropy 및 NLLLoss 함수는 classification에 주로 사용된다.

#### nn.NLLLoss()

negative log likelihood -> -log(likelihood) likelihood 형태. 분류 문제에서 주로 사용됨. 인스턴스(텐서,라벨) 로 입력하면 loss가 계산된다. 이 때, 정답 라벨만 주면 알아서 one hot 확률분포로 고려하여 해당 라벨에 대한 예측 로그 확률(-)을 구하는 방식으로 계산한다.

### nn.MSELoss()

mean square error로 계산된 loss. 주로 regression 문제에서 사용된다.

## 그 외 + MNIST 분류를 위한 nn모듈 정의

---

### tensor.max(dim=n)

n번째 dim에서 가장 큰 값과 그 index를 반환한다. a,b = tensor.max(dim=1) 사용 시 a=최대값, b=최대값의 인덱스 순서로 반환된다. MNIST 분류 모델에서 정답 라벨과 최대값의 인덱스가 동일하면 count하는 방식으로 예측 정확도를 판단할 수 있다.

### with torch_no_grad()

test 데이터 성능 확인 시 불필요한 기울기 계산을 생략하기 위한 구문. test 시에는 back propagation을 하지 않기 때문에 사용된다.

### MNIST 분류를 위한 nn모듈 정의

다음 코드는 28x28 사이즈의 MNIST 데이터셋을 분류하기 위해 정의된 nn모듈이다. 단순하게 linear layer 하나만을 이용한 형태다.

```python
class MNIST_CLASSIFIER(torch.nn.Module):
    def __init__(self):
        super().__init__()
        self.Linear = nn.Linear(784, 10)

    def forward(self, xb):
        out = self.Linear(xb)
        return out
```

이를 다음과 같이 수정하여 test 정확도를 98%로 향상시켰다.

```python
class MNIST_CLASSIFIER(torch.nn.Module):
    def __init__(self):
        super().__init__()
        self.sequential = nn.Sequential(
            nn.Linear(784,392),
            nn.BatchNorm1d(392),
            nn.ReLU(),
            nn.Linear(392,196),
            nn.BatchNorm1d(196),
            nn.ReLU(),
            nn.Linear(196,10)
        )

    def forward(self, x):
        out = self.sequential(x)
        return out
```

지금 보니 너무 파라미터 수가 늘어난 것 같기도 하다..
레이어 수 늘리기 / non linear activation function / batch normalization / hyper-parameter tuning 등을 적용했다.

특히 batch normalization을 적용했을 때 입력값 간의 편차가 줄어서 학습 속도가 매우 빨라짐을 확인했다.
그래서 epoch 수를 절반으로 줄여서 연산 수를 줄일 수 있었다.(early stopping)

## 참고한 링크

---

[파이토치-한국 사용자 모임](https://pytorch.kr/?_gl=1*1ieje05*_ga*MTQ3OTI3NjIyMS4xNzM5OTUzNTU4*_ga_LZRD6GXDLF*MTc0NDA5OTgzNC43LjAuMTc0NDA5OTgzNC42MC4wLjA.)
