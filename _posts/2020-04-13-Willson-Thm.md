---
title: 효율적인 윌슨의 정리 구현 방법 (팩토리얼 구현)
tags: [ Math, Number Theory ]
category: math
date: 2020-04-13 00:00:00 +0900
lng_pair: id_willson
---

# 윌슨의 정리

Willson's Theorem은 간단히 말해서 어떤 수가 소수인지 판단할 수 있게 해주는
정리입니다. 간단한 정수론 정리 중 하나로 정수론을 배운다면 한번은 짚고 넘어가게
되죠.

$N$이 2 이상의 정수라면, 다음을 만족합니다.

$$ (N-1)! \equiv -1 \ (mod \ N) \iff \text{N은 소수} $$

한줄로 끝날 정도로 간단하기 때문에, 구현도 굉장히 쉽습니다.

```cpp
bool is_prime(int N) {
    int fac = 1;
    for (int i = 2; i < N; i ++) {
        (fac *= i) %= N;
    }
    return fac == (N-1);
}
```

이 코드의 시간복잡도는 당연하게도 $O(N)$ 입니다.

# 쓸모 없는 윌슨의 정리?

열심히 위에서 설명했습니다만, 정작 프로그래밍에서 윌슨의 정리는 별로 사용되지
않습니다. 바로 소수를 판단하는 더 효율적인 방법이 존재하기 때문입니다.

## [Trial divison](https://en.wikipedia.org/wiki/Trial_division)

2부터 시작해서 $\lfloor\sqrt{N}\rfloor$ 까지 전부 나눠봅니다. 만약 약수가 있다면
소수가 아닙니다.

> 시간복잡도 : $O(\sqrt{N})$, 공간복잡도 : $O(1)$

시간복잡도, 공간복잡도, 구현 난이도까지 전부 윌슨의 정리를 뛰어넘습니다.

## [에라토스테네스의 체](https://ko.wikipedia.org/wiki/%EC%97%90%EB%9D%BC%ED%86%A0%EC%8A%A4%ED%85%8C%EB%84%A4%EC%8A%A4%EC%9D%98_%EC%B2%B4)

자세한 설명은 생략합니다.

> 전처리 시간복잡도 : $O(NloglogN)$
>
> 소수 판정 시간복잡도 : $O(1)$
>
> 공간 복잡도 : $O(N)$

윌슨의 정리를 좀더 빠르게 개선할 수는 없을까요? 다행히도 윌슨의 정리를
효율적으로 구현할 방법이 존재합니다.

# 윌슨의 정리를 구현하는 효율적인 방법

이 비법의 핵심은 <b>빠른 팩토리얼 계산</b> 입니다.

먼저 Factorial과 비슷한 Sigma를 생각해볼까요? 이 경우에는 굉장히 간단합니다. 공식이 존재하니까요.

$$ \sum_{i=1}^N i = {N(N+1) \over 2} $$

하지만 곱셈, 즉 팩토리얼은 간단한 방법이 존재하지 않습니다.

$$ N! = \prod_{i=1}^N i = ??? $$

## 설명에 들어가기 전에

정확한 설명과 증명을 위해서는 여러가지 추상대수학 관련 지식이 필요합니다. (Ex.
polynomial ring) 하지만 이 포스트에서는 관련 지식을 최대한 제외하고, 가볍게
설명을 진행하려 합니다. 자세한 내용이 알고싶으신 분은 참고문서를 참조해주세요.

설명을 간단하게 하기 위해, N을 완전제곱수로 가정합니다.

$$m^2 = N$$

또한 m은 2의 제곱수라고 가정합니다.

$$ m = 2^i $$

## 다항식 만들기

우선 다음과 같은 다항식을 생각합니다.

$$ p(x) = (x+1)(x+2)...(x+m) $$

N이 완전제곱수이기 때문에, 결국 팩토리얼은 다음과 같이 나타내어집니다.

$$ N! = p(0)\times p(m)\times p(2m) ... p(m(m-1)) $$

무언가 감이 오시나요? $p(x)$가 곱해야하는 item 개수, 그리고 $N!$가 곱해야하는 item 개수가 각각 $\sqrt{N} = m$개로 잘 나눠진
것을 알 수 있습니다.

N!을 구하는 문제는 이제 두가지로 나누어졌습니다.

- $p(0), p(m), ... p(m(m-1))$을 구하기
- 구한 값을 곱하기 $p(0) \times p(m) \times ... \times p(m(m-1))$

위 두 문제를 빠르게 처리 한다면, 대략적으로 $O(\sqrt{N})$이 가능함을 추측해볼 수
있습니다.

## Multipoint Evaluation

주어진 자연수 $n$, 정수 $u_0, u_1, ... u_{n-1}$, 그리고 n보다 작은 차수의
다항함수 $f$에 대해 $ f(u_0), f(u_1), ... ,f(u_{n-1}) $ 를 계산하는 문제를
Multipoint evaluation이라고 합니다.

### Singlepoint Evaluation

빠른 Multipoint Evaluation는 Singlepoint Evaluation에서부터 시작합니다. 다음과
같은 방법이 알려져 있습니다.

$$ p(a) = p(x)\ mod\ (x-a) $$

[Polynomial remainder theorem](https://en.wikipedia.org/wiki/Polynomial_remainder_theorem)

- 위 이론은 $Z_n[x]$(계수가 $Z_n$인 다항식)에서도 성립합니다. ($x-a$가 monic
  polynomial 이기 때문)

이를 빠르게 구현하는데는 여러가지가 필요합니다... 자세한 설명은 너무 길어지기
때문에 생략합니다

- FFT 를 이용한 빠른 다항식 곱셈 $O(NlogN)$ (N은 다항식의 차수)
- 빠른 다항식 나눗셈 (Newton method 사용 시) $O(Nlog^2N)$ (N은 다항식의 차수)

### Multipoint Evaluation 구현

위 방법을 통한 Singlepoint Evaluation이 가능하게 되었습니다! 이제 분할정복을
사용하면 Multipoint Evaluation도 빠르게 답을 구할 수 있습니다.

### 트리 만들기

우리가 구하고 싶은 값들을 $p(a), p(b), p(c), p(d)$ 이라고 합시다. 먼저 해야할
일은 트리를 만드는 것입니다.

<table>
    <tbody>
        <tr>
            <td colspan="4" style="text-align:center">(x-a)(x-b)(x-c)(x-d)</td>
        </tr>
        <tr>
            <td colspan="2" style="text-align:center">(x-a)(x-b)</td>
            <td colspan="2" style="text-align:center">(x-c)(x-d)</td>
        </tr>
        <tr>
          <td style="text-align:center">(x-a)</td>
          <td style="text-align:center">(x-b)</td>
          <td style="text-align:center">(x-c)</td>
          <td style="text-align:center">(x-d)</td>
        </tr>
    </tbody>
</table>

표의 각 셀은 트리의 한 노드를 의미합니다. 리프노드로부터 차근차근 곱해
올라갈경우 $O(N)$번의 곱셈만으로 모든 노드를 채울 수 있습니다. 굳이 이런식으로
트리를 만드는 이유는 시간복잡도와 관련이 있습니다.

현재 우리가 하고 있는 곱셈이 <b>다항식 곱셈</b>이라는 점을 명심하세요. 이 곱셈은
$O(DlogD)$의 시간복잡도를 가지고 있습니다. <b>(D는 다항식의 차수 입니다!)</b>

따라서 이 트리를 만드는데 걸리는 시간복잡도는 다음과 같습니다. Evaluate
하고자하는 원소의 개수를 $m$이라 하면

$$ {m \over 2} \times {2 \log{2}} + {m \over 2^2} \times 2^2 \log{2^2} + ...\
\\ =\sum_{i=1}^{\log m} {m \over 2^i} \times 2^i \log{2^i} $$

$$ = m\sum_{i=1}^{\log{m}} i $$

결국 시간복잡도는

$$ O(m\log^2{m}) $$

### 트리 순회

Siglepoint Evaluation을 기억해주세요. 저희는 사실상 다음을 구해야합니다.

$$ p(x)\ mod \ (x-a)$$
$$ p(x)\ mod \ (x-b)$$
$$ p(x)\ mod \ (x-c)$$
$$ p(x)\ mod \ (x-d)$$

추가적으로 다음과 같은 사실을 이용합니다.

"

$q(x), r(x)$는 차수가 1 이상인 monic 다항식 (최고차항 계수가 1)

$$\displaylines{p(x)\ mod\ q(x) \\ = \Big( p(x)\ mod\ q(x)r(x) \Big)\ mod\ q(x)}$$

"

얼핏 의미없어 보이는 이러한 사실을 쓰는 이유는 계산량을 줄이기 위해서입니다.
다항식 나눗셈 계산량은 다항식의 차수에 의존하기 때문에, 차수가 더 적은
다항식으로 같은 결과를 얻을 수 있다는 사실은 굉장히 중요합니다.

계산은 트리를 만들때와 반대로, 루트부터 진행합니다.

<table style="text-align:center">
    <tbody>
        <tr>
            <td colspan="4" style="text-align:center">p(x) mod (x-a)(x-b)(x-c)(x-d)</td>
        </tr>
        <tr>
            <td colspan="2" style="text-align:center">p(x) mod (x-a)(x-b)</td>
            <td colspan="2" style="text-align:center">p(x) mod (x-c)(x-d)</td>
        </tr>
        <tr>
          <td style="text-align:center">p(x) mod (x-a)</td>
          <td style="text-align:center">p(x) mod (x-b)</td>
          <td style="text-align:center">p(x) mod (x-c)</td>
          <td style="text-align:center">p(x) mod (x-d)</td>
        </tr>
    </tbody>
</table>

여기서 중요한 포인트는 "자식노드의 계산은 오직 부모노드와, 이미 계산해두었던 (x-a)...(x-d) 로만 할 것" 입니다.

$$ r_{00}(x) = p(x)\ mod\ (x-a)(x-b)(x-c)(x-d) $$

예시의 루트 노드를 예로 들어봅시다. 저희는 이미 $(x-a)(x-b)(x-c)(x-d)$를 알고 있기 때문에 간단하게 $r_{00}(x)$를 계산할 수 있습니다.
이제 자식노드를 계산할 차례죠? 자식노드 계산에는 $p(x)$ 대신 $r_{00}(x)$를 사용합니다.

$$\displaylines{r_{10}(x)= r_{00}(x)\ mod\ (x-a)(x-b) \\ r_{11}(x) = r_{00}(x)\ mod\
(x-c)(x-d)}$$

이제 똑같은 과정을 반복합니다. 리프노드까지 도달하면
계산이 완료됩니다.

<table style="text-align:center">
    <tbody>
        <tr>
            <td colspan="4" style="text-align:center">p(x) mod (x-a)(x-b)(x-c)(x-d)</td>
        </tr>
        <tr>
            <td colspan="2" style="text-align:center">r00(x) mod (x-a)(x-b)</td>
            <td colspan="2" style="text-align:center">r00(x) mod (x-c)(x-d)</td>
        </tr>
        <tr>
          <td style="text-align:center">r10(x) mod (x-a)</td>
          <td style="text-align:center">r10(x) mod (x-b)</td>
          <td style="text-align:center">r11(x) mod (x-c)</td>
          <td style="text-align:center">r11(x) mod (x-d)</td>
        </tr>
        <tr>
            <td style="text-align:center">= p(a)</td>
            <td style="text-align:center">= p(b)</td>
            <td style="text-align:center">= p(c)</td>
            <td style="text-align:center">= p(d)</td>
        </tr>
    </tbody>
</table>

트리를 만들 때와 비슷한 과정으로 시간복잡도를 계산할 수 있습니다.

$$ O(m \log^3 m) $$

## 구한 값으로 곱하기

얻은 값을 곱해줍니다. 완성되었습니다.

# 결론

$O(\sqrt{N} \log^3 N)$ 의 시간복잡도로 윌슨의 정리를 구현해 볼 수 있습니다.

문제는

- 알고리즘의 상수가 너무 크기 때문에, 예상보다 더 느리다.
- 구현이 끔찍하게 복잡하다. 알고리즘에 필요한 FFT, 다항식 나눗셈은 그 자체로도
  복잡한데 이를 사용해 더 복잡한 알고리즘이 되었습니다.

즉, 아무리 열심히 해도, 윌슨의 정리는 본래 의도대로 사용하기 어렵습니다.. 😢

# 참고 문서

- [Factorials mod n and Wilson’s theorem](http://fredrikj.net/blog/2012/03/factorials-mod-n-and-wilsons-theorem/)
- [Fast Multipoint Evaluation On n Arbitrary Points](http://www.cecm.sfu.ca/CAG/theses/justine.pdf)
- [Polynomial remainder theorem](https://en.wikipedia.org/wiki/Polynomial_remainder_theorem)
- [Note on fast division algorithm for polynomials using Newton iteration](https://arxiv.org/abs/1112.4014)
- [flint2](https://github.com/wbhart/flint2/)
