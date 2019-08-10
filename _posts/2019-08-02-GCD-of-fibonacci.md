---
layout: post
title: "피보나치수의 최대공약수"
date: 2019-08-02 01:21
tags: [Fibonacci, Math, NumberTheory]
category: [math, fibonacci]
color: "#3FB488"
cover: 
subtitle: 
---

피보나치수의 최대공약수를 생각해보자. 

$$ gcd(F_i, F_j) $$

왠지 서로 붙어있을 일이 없을것만 같은, 서먹서먹한 개념 두개가 섞여있다. 마치 화해 안한 친구 두명을 억지로 붙여 놓고 사진찍은 것 같은 느낌...

처음에 보고 이게 깔끔하게 정리가 되려나 싶었는데, 생각보다 어렵지 않게 풀려서 포스팅한다.

유클리드 호제법 증명을 먼저 공부하면 도움이 많이 된다.

$$ gcd(F_i, F_j) = F_{gcd(i, j)} $$

## 먼저 알아야 할 것

- 최대공약수
- 피보나치수
- 유클리드 호제법

두 정수 A, B의 최대공약수를 $$(A, B)$$, i번째 피보나치 수는 F<sub>i</sub> 라고 표현하자.

### lemma 1

두 정수 $$A, B \ (A \geq B)$$에 대해, 다음과 같은 식이 성립한다. (유클리드 호제법)

$$ (A, B) = (A - B, B) $$

### lemma 2

양의 정수 i와 k에 대해 다음을 만족한다.

$$F_{i+k} = F_{k-1} F_i + F_{k} F_{i+1}$$

식은 복잡해 보이지만, 그렇게 어렵지 않다. 믿음이 안간다면 직접 $$F_{i+k}$$를 전개해보자. $$F_{i+k}$$ 를 $$F_i$$ 와 $$F_{i+1}$$ 로 나타낼 수 있다는게 핵심이다.

### lemma 3

양의 정수 A, B, C에 대해 다음을 만족한다. (단, B, C는 서로소)

$$(A \times B, C) = (A, C)$$



## 증명

### Thm 1.

<div style="font-size: 133%;">
$$(F_i, F_{i+1}) = 1$$
</div>

---

먼저 $$(F_i, F_{i+1})$$를 살펴보자.

lemma 1을 사용하면 i값 상관 없이 $$F_i$$와 $$F_{i+1}$$ 이 서로소라는 것을 알 수 있다.

$$ 
\begin{array}{rl}
(F_{i+1}, F_{i})  & = (F_{i} + F_{i-1}, F_i) \\
                & = (F_i, F_{i-1}) \\
                & \dots \\
                & = (F_2, F_1) \\
                & = (1, 1) \\
                & = 1 \\
\end{array} 
$$

### Thm 2.

$$i \leq j$$ 를 만족하는, 양의 정수 i, j 에 대해서 다음을 만족한다.

<div style="font-size: 133%;">
$$(F_i, F_j) = (F_{i-j}, F_j)$$
</div>

---

이번에는 좀더 확장시켜서 생각해보자.

$$(F_{i+k}, F_i)$$를 lemma 2를 사용해 전개한다.

$$(F_{i+k}, F_i) = (F_{k-1} F_i + F_{k} F_{i+1}, F_i)$$


$$
\begin{array}{rll}
(F_{k-1} F_i + F_{k} F_{i+1}, F_i) &= (F_{k} F_{i+1}, F_i) & \text{from lemma1} \\
                                   &= (F_{k}, F_i) & \text{from Thm1, lemma3} \\
\end{array}
$$

$$F_{k-1} F_i $$ 는 $$F_i$$의 배수이므로, lemma 1을 활용해 지워버릴 수 있다.

Thm1을 기억하자. $$F_i와 F_{i+1}$$ 은 서로소다. $$F_{k} F_{i+1}$$ 도 lemma 3를 활용해 $$F_{k}$$ 로 줄일 수 있다.

결국 다음과 같은 깔끔한 식을 얻는다.

$$(F_{i+k}, F_i) = (F_{k}, F_i)$$

$$ \iff $$

$$(F_{j}, F_i) = (F_{j-i}, F_i)$$

### Thm 3.

<div style="font-size: 133%;">
$$(F_{i}, F_{j}) = F_{(i, j)}$$
</div>

---

필요한 것들은 모두 얻었다.

위 식을 유클리드 호제법과 같은 방식으로 계속 전개할 수 있다.

$$ 
\begin{align}
\begin{split}
(F_i, F_j)  &= (F_{i-j}, F_j) \\
            &= \ ... \\
            &= (F_a, F_0) \\
\end{split}
\end{align} $$

무려 $$F_0$$는 0이므로, $$(F_a, F_0) = F_a$$ 을 만족한다.

이번에는 반대로 거슬러 올라가보자.

$$ 
\begin{align}
\begin{split}
F_a &= F_{(a, 0)} \\
    &= \ ... \\
    &= F_{(i, j)} \\
\end{split}
\end{align} $$

## 끝

$$ gcd(F_i, F_j) = F_{gcd(i, j)} $$

이렇게 신기하게 정리되는걸 보면, 아무래도 gcd랑 피보나치는 생각보다 사이가 좋았나보다. 