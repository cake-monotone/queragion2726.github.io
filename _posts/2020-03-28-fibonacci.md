---
layout: post
title: 피보나치 수를 계산하는 21가지 방법
subtitle: 
tags: [ Math, Fibonacci ]
---

피보나치 수를 정말 여러가지 방법으로 계산해보자.

# 1. 재귀함수 연습

```python
def fibo(N):
    if N <= 1:
        return [0, 1][N]
    return fibo(N-1) + fibo(N-2)
```

> 시간 복잡도 : $O(2^N)$

# 2. 선형적인 방법

```python
def fibo(N):
    a, b = 0, 1
    for _ in range(N):
        a, b = b, a+b
    return a
```

> 시간 복잡도 : $O(N)$

# 3. 행렬 곱

```python
import numpy as np

def fibo(N):
    M = np.array([[1, 1],
                  [1, 0]])
    M = np.linalg.matrix_power(M, N)
    return M[1][0]
```
> 시간복잡도: $O(log N)$ (power 함수에 분할정복을 사용했을 경우)

$$
{
    \begin{pmatrix}
        f_{N+1} \\
        f_N \\
    \end{pmatrix}
}
=
{
    \begin{pmatrix}
        1 & 1 \\
        1 & 0 \\
    \end{pmatrix}
}^N
{
    \begin{pmatrix}
        f_1 \\
        f_0 \\
    \end{pmatrix}
}
$$

# 4. 피보나치 수열의 일반항

```python
def fibo(N):
    sqrt5 = 5 ** 0.5
    phi = (1 + sqrt5) / 2
    Phi = (1 - sqrt5) / 2
    return int((phi**N - Phi**N) / sqrt5)
```
> 시간 복잡도 $O(log N)$ (power 함수에 분할정복 사용시) 

$$
f_N = {
    {
        \phi ^N - \Phi ^N
    }
    \over
    {
        \sqrt{5}
    }
}
,
\left(
    \phi = {
        {1 + \sqrt{5}}
        \over
        {2}
    }
    ,
    \Phi = - {
        1
        \over
        \phi
    }
\right)
$$

$\phi$ 는 흔히 말하는 황금비

다만 이 방법은 실수 연산을 사용하기 때문에, N이 커지면 커질 수록 실수오차가 불어나 사용할 수 없다.

# 5. 피보나치 수열의 일반항 2

만약 어떤 소수 P로 나눈 나머지 결과를 얻고자 한다면, 실수 연산을 피할 수 있다.

```py
def mul(A: (int, int), B: (int, int), P: int) -> (int, int):
    '''
    (a, b)
    (a + b sqrt5) 꼴을 가진 두 튜플의 곱
    '''
    return (
        (A[0] * B[0] + 5 * A[1] * B[1]) % P,
        (A[0] * B[1] + A[1] * B[0]) % P
    )

def pow_for_phi(X, N, P):
    ret = (1, 0)
    while N != 0:
        if (N & 1) == 1:
            ret = mul(ret, X, P)

        X = mul(X, X, P)
        N //= 2
    return ret

def inv(X, P):
    '''
    모듈러 곱셈 역원
    '''
    return pow(X, P-2, P)

def fibo(N, P):
    inv2 = inv(2, P)
    phi = (inv2, inv2) # (1 + sqrt5) / 2
    Phi = (inv2, P-inv2) # (1 - sqrt5) / 2

    phiN = pow_for_phi(phi, N, P)
    PhiN = pow_for_phi(Phi, N, P)

    return (phiN[1] - PhiN[1] + P) % P
```

> 시간복잡도 $O(log N)$

조금 복잡해보이지만, 결국 하고 있는 계산은 피보나치 수열의 일반항 1과 크게 다르지 않다.

실수 대신, `(a, b)` 꼴로 된 정수 튜플을 사용하는데, 사실상 $a + b \sqrt{5}$와 같다고 보면 된다. 따라서 튜플끼리의 곱하기는 다음과 같다는 걸 알 수 있다.

$$
(a, b) \times (c, d) = (ac+5bd, ad+bc) \\
(a+b\sqrt{5}) \times (c+d\sqrt{5})=(ac+5bd) + (ad+bc) \sqrt{5} \\
$$

나머지 과정은 피보나치 수열의 일반항 1과 같다.

# 6. 피보나치 곱셈 공식

```py
import math

def fibo(N):
    ret = 1
    for k in range(1, (N-1)//2 + 1):
        cos_val = math.cos(k*math.pi / N)
        ret *= (1 + 4 * cos_val * cos_val)
    return int(ret)
```

> 시간복잡도 $O(N)$ 
> 
> cos 함수를 $O(1)$ 이라 가정

$$
f_N = \prod_{k=1}^{\lfloor (n-1)/2 \rfloor} (1 + 4\ \cos^2{k\pi \over n})
$$

이 역시 실수 연산을 사용하므로, 오차가 발생한다.

# 7. 기도하기

## 피보나치 수 판정법

$$
x \text{는 피보나치 수} 
\\
\iff
\\
5x^2 + 4 \text{ 혹은 } 5x^2 - 4 \text{가 완전 제곱수}
$$

Ex) $5 \times 3^2 + 4 = 49$가 완전 제곱수이므로 3은 피보나치수

## 몇번째 피보나치수 인지 알아내기

위에서 언급한 피보나치의 일반항을 잘 조작하면 다음과 같은 결과를 얻는다.

$$
n = log_{\phi} \big\{ {f_n \sqrt{5} + \sqrt{5f_n^2 \pm 4} \over 2} \big\}
$$

여기서 $5f_n^2 \pm 4$는 정수여야 한다.

Ex) 4번째 피보나치 수 3을 넣어보면 다음을 만족한다.

$$
log_\phi \big({7+3\sqrt{5} \over 2} \big) = 4
$$

이제부터 하려는 작업은 간단하다! n번째 피보나치 수가 나올 때까지 랜덤한 수를 뽑고 확인하고, 뽑고 확인하고 하자.

```py
import random
from math import sqrt, log

def is_square(N):
    if N == 1:
        return True
    sqrt_N = int(sqrt(N))
    return (
        (sqrt_N - 1) ** 2 == N or
        (sqrt_N) ** 2 == N or
        (sqrt_N+1) ** 2 == N
    )

def calc_fibo(N):
    if N == 1:
        return (True, None)

    if is_square(5*N*N+4):
        return (True, 5*N*N+4)
    elif is_square(5*N*N-4):
        return (True, 5*N*N-4)
    else:
        return (False, None)

def inv_fibo(F):
    valid_flag, val = calc_fibo(F)
    if not valid_flag:
        return None

    sqrt5 = sqrt(5)
    phi = (1 + sqrt5) / 2
    N = log(
        (F * sqrt5 + sqrt(val)) / 2,
        phi
    )
    return int(round(N))

def fibo(N, max_lim = 10000):
    if N <= 2:
        return [0, 1, 1][N]

    while True:
        X = random.randint(2, max_lim)
        valid_flag, _ = calc_fibo(X)

        if valid_flag and inv_fibo(X) == N:
            return X
```

평균적인 시간복잡도는 기하분포의 평균을 이용해 예상할 수 있다.

$$
O(MAX\_LIM) = O(f_n)
$$

비단 바보같아 보이는 방법이고 영원히 헤맬것 같은 방법이지만, 나름 합리적인🙄🙄 시간복잡도로 피보나치 수를 구할 수 있다. 다만 소수 오차가 생길 수 있음에 유의하자

# 8. 7번 최적화

추가 예정