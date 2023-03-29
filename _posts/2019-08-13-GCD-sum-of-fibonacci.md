---
layout: post
title: "[BOJ 17372] 피보나치 수의 최대공약수의 합"
date: 2019-08-13 20:00:00 +0900
tags: [ Fibonacci, Math, Number Theory, BOJ ]
category: ps
lng_pair: id_gcd_sum
---

백준 온라인 저지 17372번 피보나치 수의 최대공약수의 합 풀이.

[BOJ 링크](https://boj.kr/17372)

## 미리 보면 좋은 포스트

[피보나치 수의 최대공약수](https://mono-cake.coffee/math/fibonacci/2019/08/02/GCD-of-fibonacci.html)

## 문제

다음을 구하자.

$$\sum_{i=1}^{N} \sum_{j=1}^{N} gcd(f_i, f_j)$$

피보나치수의 성질을 이용한다. $$gcd(f_i, f_j) = f_{gcd(i,j)}$$

사실상 다음과 같다.

$$\sum_{i=1}^{N} \sum_{j=1}^{N} f_{gcd(i, j)}$$

이대로는 절대로 시간복잡도 내에 해결할 수 없으므로, 시그마를 하나 떼어내보자.

이를 위해 $$gcd(i, j)$$를 하나의 정수로 묶는다. $$gcd(i, j) = d$$를 만족하는
$$(i,j)$$의 개수를 잘 구할 수만 있다면, 같은 d를 가진 피보나치수끼리 묶을 수
있을 것이다.

$$gcd(i, j)= d$$를 만족하는 $$(i, j)$$ 개수를 나타내는 함수를 $$cnt_N(d)$$라고
하면, 식은 다음과 같다.

$$\sum_{d=1}^{N} cnt_N(d) \times f_d $$

N이 굉장히 크기 때문에, 여전히 해결하기 어렵다. 우선 cnt함수부터 살펴보자.

### cnt(d)

$$ cnt_N(d) : gcd(i, j) = d $$ 을 만족하는 i, j 쌍의 개수 ($$1 \leq i, j \leq
N$$)

정말 나이브하게 모든 (i, j) 쌍을 검사할 수도 있겠지만 그러면 굳이 위에서
시그마를 떼어낸 이유가 없다.

이를 적절한 시간에 구하기 위해서, 다음과 같이 cnt를 정의한다.

$$ cnt_N(d) = cnt_{\lfloor N/d \rfloor}(1) $$

$$ cnt_N(1) $$ 이 사실상 $$N\times N$$ 격자 내 모든 서로소 쌍을 구하는 함수라는
것을 기억하자. $$gcd(i, j) = 1 \iff gcd(id, jd) = d$$ 이므로, $$cnt_N(d)$$ 를
구하기 위해서는 $$\lfloor N/d \rfloor \times \lfloor N/d \rfloor $$ 격자 내의
서로소 쌍 개수만 구하면 된다.

이제부터 $$ cnt_N(1) = S(N) $$ 이라고 편하게 표현하겠다.

그렇다면 S는 어떻게 구할 수 있을까?

$$N\times N$$ 격자를 상상하자. 이 격자에서 서로소가 아닌 것들의 개수를 하나하나
빼주면 답을 구할 수 있을 것이다. 격자의 개수 $$N^2$$ 부터 시작해서, $$gcd(i, j)
= 2 ... N$$을 하나하나 빼나가자.

$$ cnt_N(d) = S(\lfloor N/d \rfloor) $$ 이므로, 완성된 식은 다음과 같다.

$$ S(N) = N^2 - \sum_{d=2}^{N} S(\Bigl\lfloor {N \over d} \Bigr\rfloor)$$

### S(N) 개선

지금까지 열심히 해왔지만 완성된 S(N)도 문제를 해결하기에는 너무 느리다. 좀 더
개선을 해보자.

$$ \Bigl\lfloor {N \over d} \Bigr\rfloor $$ 에 중점을 두자. d가 N에 가까워질
수록 변화가 거의 없다는걸 알 수있다.

ex) $$ \Bigl\lfloor { 42 \over 23 } \Bigr\rfloor = \Bigl\lfloor { 42 \over 24 }
\Bigr\rfloor = \cdots = \Bigl\lfloor { 42 \over 42 } \Bigr\rfloor = 1 $$

이 점에 착안해서, 함수를 두 부분으로 나눌 수 있다.

$$ S(N) = N^2 - \sum_{d=2}^{\lfloor \sqrt{N}\rfloor} S(\Bigl\lfloor {N \over d}
\Bigr\rfloor) + \sum_{k=1}^{\lceil \sqrt{N} - 1 \rceil} (\Bigl\lfloor {N \over
k} \Bigr\rfloor - \Bigl\lfloor {N \over k+1} \Bigr\rfloor) S(k)$$

이제 $$N$$번이 아닌 $$\sqrt{N}$$의 반복으로 계산가능하게 되었다.

추가로 함수 S가 재귀적으로 잘 정의되었기 때문에 중간중간 결과를 재활용 할 수
있다.

N이 10<sup>9</sup>이기 때문에, 배열만으로 모든 S의 결과를 저장해 놓을 수가 없다.
10<sup>7</sup> 이내의 결과를 배열으로, 나머지는 unordered_map을 사용해
메모이제이션 한다.

미리 계산이 되어 있다는 가정하에, 대략 $$O(\sqrt{N})$$ 정도로 이 함수를 이용할
수 있다.

슬프게도 우리는 미리 계산이 되지 않은 상태에서 문제를 해결해나가야 하므로,
그렇지 않을 경우를 생각해보자.

우선 알아야할 사실은 다음과 같다.

- S(N) 계산과정에서 필요한 S는 오직 S(N)이 요구하는 S(d), S(N/k) 뿐이다.

- S(R) 을 계산하는데에는 $$\sqrt{R}$$ 만큼의 연산이 필요하다.

S(N)을 계산하기 위해 S(d), S(N/k) 를 재귀적으로 먼저 계산해야한다. 이런 전체
계산과정 중에 $$S(M \neq d, N/k)$$ 을 계산할 일은 없다는 뜻이다. 게다가
메모이제이션 덕분에 각각의 S(d), S(N/k)는 한번만 계산하면 된다.

즉 계산량은 대략 다음과 같다.

$$\sum_{d=1}^{\sqrt{N}} \sqrt{d} + \sum_{k=1}^{\sqrt{N}} \sqrt{N \over k}$$

대수적으로 풀기 어려우므로, 적분으로 계산한다.

$$\int_{0}^{\sqrt{N}} \sqrt{x} dx + \int_{0}^{\sqrt{N}} \sqrt{N\over x} dx$$

$$ = {8\over 3} N^{3\over 4}$$

시간복잡도는 대략적으로 $$O(N^{3\over 4})$$ 임을 알 수 있다.

N이 10<sup>9</sup> 이므로 아슬아슬하게 한번 정도는 사용가능하다.

- 추가

이 함수는 OEIS에도 수열로 등재되어 있다. [A018805](https://oeis.org/A018805)

### 다시 돌아와서

$$\sum_{i=1}^{N} \sum_{j=1}^{N} gcd(f_i, f_j) = \sum_{d=1}^{N} S(\Bigl\lfloor {N
\over d} \Bigr\rfloor) \times f_d $$

S(N) 개선할 때 사용했던 아이디어를 다시 활용할 수 있다.

$$ \sum_{d=1}^{\lfloor \sqrt{N}\rfloor} S(\Bigl\lfloor {N \over d} \Bigr\rfloor)
\times f_d+ \sum_{k=1}^{\lceil \sqrt{N} - 1 \rceil} (Fsum(\Bigl\lfloor {N \over
k+1} \Bigr\rfloor, \Bigl\lfloor {N \over k} \Bigr\rfloor) \times S(k) $$

Fsum(l, r) 은 $$i \in \left(l, r\right]$$ 번째 피보나치 수의 합을 의미한다.

자 이제 최종 시간복잡도를 계산하자.

- 식에 사용되는 S(d), S(N/k)는 S(N)을 한번 계산하면 모두 구해진다. O(1)만에
  사용가능
- Fsum은 O(log N)만에 계산가능하다. $$Fsum(1, N) = f_{N+2} - 1$$

$$ O (N^{3 \over 4} + \sqrt{N} log N) $$

## 끝

$$\sum_{i=1}^{N} \sum_{j=1}^{N} gcd(f_i, f_j) = \sum_{d=1}^{\lfloor
\sqrt{N}\rfloor} S(\Bigl\lfloor {N \over d} \Bigr\rfloor) \times f_d+
\sum_{k=1}^{\lceil \sqrt{N} - 1 \rceil} (Fsum(\Bigl\lfloor {N \over k+1}
\Bigr\rfloor, \Bigl\lfloor {N \over k} \Bigr\rfloor) \times S(k) $$

$$ S(N) = N^2 - \sum_{d=2}^{\lfloor \sqrt{N}\rfloor} S(\Bigl\lfloor {N \over d}
\Bigr\rfloor) + \sum_{k=1}^{\lceil \sqrt{N} - 1 \rceil} (\Bigl\lfloor {N \over
k} \Bigr\rfloor - \Bigl\lfloor {N \over k+1} \Bigr\rfloor) S(k)$$

$$ O (N^{3 \over 4} + \sqrt{N} log N) $$

코딩하자!

## 코드

```cpp
#include<bits/stdc++.h>

using namespace std;
using lint = long long int;

const int MAX_ABLE = 1e7;
const lint MOD = 1e9 + 7;

lint phisum_dp[MAX_ABLE + 1];
unordered_map < lint, lint > subdp;

lint phisum(int N) {
    if (N <= MAX_ABLE && phisum_dp[N] != 0)
        return phisum_dp[N];
    else if (N > MAX_ABLE && subdp[N] != 0)
        return subdp[N];

    lint ret = (lint) N * N;
    ret %= MOD;

    lint k = 2;

    for (; k * k <= N; k++) {
        ret -= phisum(N / k) - MOD;
        ret %= MOD;
    }

    for (int d = 1; N >= (lint) k * d; d++) {
        ret -= (lint)(N / d - N / (d + 1)) * phisum(d) - MOD;
        ret %= MOD;
    }

    if (N <= MAX_ABLE)
        return phisum_dp[N] = ret;
    else
        return subdp[N] = ret;
}

lint M1[2][2], M2[2][2], M3[2][2];

lint fibsum(int N) {
    N += 2;
    long long( * R)[2] = M1;
    long long( * A)[2] = M2;
    long long( * TMP)[2] = M3;
    for (int i = 0; i < 2; i++)
        for (int j = 0; j < 2; j++)
            R[i][j] = A[i][j] = 0;
    R[0][0] = R[1][1] = 1;
    A[0][1] = A[1][0] = A[1][1] = 1;

    while (N) {
        if (N & 1) {
            for (int i = 0; i < 2; i++) {
                for (int j = 0; j < 2; j++) {
                    TMP[i][j] = 0;
                    for (int k = 0; k < 2; k++) {
                        (TMP[i][j] += R[i][k] * A[k][j] % MOD) %= MOD;
                    }
                }
            }

            swap(TMP, R);
        }

        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                TMP[i][j] = 0;
                for (int k = 0; k < 2; k++) {
                    (TMP[i][j] += A[i][k] * A[k][j] % MOD) %= MOD;
                }
            }
        }
        swap(TMP, A);

        N >>= 1;
    }

    return (R[0][1] - 1 + MOD) % MOD;
}

lint fibsum(int l, int r) {
    if (l == 0)
        return fibsum(r);
    else
        return (fibsum(r) - fibsum(l - 1) + MOD) % MOD;
}

int main(void) {
    ios_base::sync_with_stdio(false), cin.tie(NULL);
    int N;
    cin >> N;
    phisum_dp[1] = 1;

    lint res = 0;

    lint k = 1;
    for (; k * k <= N; k++)
        (res += phisum(N / k) * fibsum(k, k) % MOD) %= MOD;

    for (lint d = 1; N >= k * d; d++)
        (res += phisum(d) * fibsum(N / (d + 1) + 1, N / d) % MOD) %= MOD;

    cout << res << endl;
}
```
