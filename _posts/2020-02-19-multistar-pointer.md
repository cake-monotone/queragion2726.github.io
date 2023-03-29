---
title: "원하는 만큼 포인터에 별붙이기 int*****"
tags: [ Template Metaprogramming ]
category: programming
date: 2020-02-19 00:00:00 +0900
lng_pair: id_multistar_pointer
---

포인터 타입에 별을 원하는만큼 붙여보고 싶으신적 없으세요?

가슴이 따뜻해지는 기념일에 가족, 친구, 애인분께 별이 가득히 붙은 포인터 변수
하나 선물해드리는게 어떨까요?

![결과물 사진 int***...***](:multistarpointer.png)

# 먼저 알아야 할 문법들

- `template<>`
- `using Type = type;`
- `decltype`

## 템플릿 메타프로그래밍 (template metaprogramming)

C++의 제너릭 프로그래밍에 해당하는 `template` 은 다른언어의 제너릭 프로그래밍과
다른 점이 많다.

```cpp
template<typename T>
struct wrapper {
    T item;
};

wrapper<char> wrap;
```

우리가 이런 코드를 작성했다고 했을 때, C++은 런타임에 `wrapper<char>` 을
처리하지 않는다.

컴파일러는 그저 다음과 같은 코드를 만들어서 프로그램에 집어넣을 뿐이다.

```cpp
struct wrapper<char> {
    char item;
};
```

만약 코드에서 추가로 `wrapper<int>, wrapper<long long>`이 나타난다면 컴파일러는
추가로 새로운 wrapper를 하드코딩해서 프로그램에 집어넣는다.

```cpp
struct wrapper<int> {
    int item;
};
struct wrapper<long long> {
    long long item;
};
```

이런식으로 컴파일러에게 필요할때마다 하드코딩된 코드를 생성하도록 하는 것을
템플릿 메타 프로그래밍이라고 한다.

간단하게 타입을 바꿔치기 하는 것 뿐만 아니라, 조금 복잡한 작업들도 할 수 있는데,
다음 코드를 봐보자.

```cpp
template <int N>
struct Factorial {
  enum { value = N * Factorial<N - 1>::value };
};

template <>
struct Factorial<0> {
  enum { value = 1 };
};
```

[출처 위키백과 : 템플릿 메타 프로그래밍 항목](https://ko.wikipedia.org/wiki/%ED%85%9C%ED%94%8C%EB%A6%BF_%EB%A9%94%ED%83%80%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)

복잡해 보이지만 위에서 보였던 `wrapper`와 크게 다르지 않다.

```cpp
int val = Factorial<2>::value;
```

이런 코드가 나타난 순간 컴파일러는 다음과 같은 코드를 생성한다.

```cpp
struct Factorial<2> {
    enum { value = 2 * Factorial<1>::value };
};

struct Factorial<1> {
    enum { value = 1 * Factorial<0>::value };
};

struct Factorial<0> {
    enum { value = 1 };
};
```

`Factorial<0>` 은 더 이상 `Factorial<N-1>`을 요구하지 않기 때문에 코드는
`Factorial<0>`에서 생성을 멈춘다.

## using 키워드

C++11부터 typedef와 비슷한 문법이 추가되었다.

```cpp
using MY_TYPE = int;
MY_TYPE var;
```

`typedef` 와 소소한 차이점이 존재하지만 이 포스트에서는 중요하지 않으니
넘어간다.

## decltype 키워드

`decltype` 키워드는 표현식(expression)의 타입을 추론할 때 사용한다.

```cpp
int a = 1, b = 3;
decltype(a+b+1LL) var = a+b;
```

이는 결국 아래와 같은 코드가 된다.

```cpp
int a = 1, b = 3;
long long var = a+b;
```

컴파일 타임에 타입추론이 가능한 표현식이라면 얼마든지 사용가능하며, 비슷한
성격의 `auto` 보다 더 유연한 활용이 가능하다.

주로 리턴하는 함수의 반환 타입을 결정할 때 사용된다.

```cpp
template<typename T>
auto foo (T a) -> decltype(a+1LL) {
    //...
}
```

# 원하는 만큼 별을 만들자

대략적으로 이렇게 계획을 세워 볼 수 있다.

1. T******... 를 반환하는 함수 foo 만들기
2. using type = decltype(foo); 으로 사용하기 편하게 만들기

위에서 설명한 문법들을 활용하면 충분히 만들 수 있다.

```cpp
#define MULTISTAR_POINTER(T, D) decltype(multistar_pointer<T, D>::compile())

template<typename T, std::size_t D>
struct multistar_pointer {
    static constexpr auto compile() -> MULTISTAR_POINTER(T, D-1)* {
        return NULL;
    }

    using type = MULTISTAR_POINTER(T, D);
};

template<typename T>
struct multistar_pointer<T, 1> {
    static constexpr T* compile() {
        return (T*)NULL;
    }
    
    using type = T*;
};
```

`multistar_pointer<T, D>::compile` 함수는 별이 D개 붙어있는 포인터를 리턴한다.

return 타입을 결정하기 위해서는 어떻게 해야할까?

`Factorial` 예시와 비슷하게, `multistar_pointer<T, D-1>::compile` 함수를 가져다
쓰자.

우리가 `multistar_pointer<T, 1>::compile()` 만 잘 만들었다면,
`decltype(multistar_pointer<T,D-1>::compile())*`에 우리가 원하는 결과가 담길
것이다.

필요한 `decltype(multistar_pointer<T, D>::compile)`이 너무 길어서 매크로를
정의하고 사용하였다.

```cpp
multistar_pointer<vector<int>, 20>::type vec20pt;
auto str100pt = multistar_pointer<string, 100>::compile();
MULTISTAR_POINTER(size_t, 3) sz3pt;
```

다양한 방식으로 변수를 만들 수 있다.

`std::is_same` 을 이용해서 더 정확히 확인해 볼 수도 있다.

```cpp
int main(void) {
    cout << boolalpha;
    cout << is_same<int, int>::value << endl;
    // true
    cout << is_same<int***, multistar_pointer<int, 3>::type>::value << endl;
    // true
}
```

![결과물 사진 int***...*** 1000개](:multistarpointer2.png)

이게 왜 필요한지 묻지 마세요..

[다차원 자료구조](https://github.com/queragion2726/HyperDataStructure)
