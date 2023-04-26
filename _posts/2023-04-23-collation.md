---
title: 다양한 나라에서 문자열을 정렬하려면? Collation이란?
tags: [ project, postgresql, collation ]
category: programming
date: 2023-04-23 15:47:02 +0900
lng_pair: id_collation
---

# 문자열 정렬의 어려움

정렬은 컴퓨터 공학도라면 누구라도 알고 있는 친숙한 문제입니다. 컴퓨터 공학도가
아니더라도 국어사전, ㄱㄴㄷ정렬 등으로 익숙하게 느끼는 분들도 많을 겁니다.

그렇다면 문자열 정렬은 어떤식으로 하면 좋을까요? 아마도 문자의 코드값 시퀸스의
사전순(lexicographical order) 정렬을 떠올리셨을 겁니다. 어라? 이거면 된거
아닐까요?

그러나 정답은 아닙니다. 지역(나라)에 따라 혹은 언어에 따라 불충분할 수 있습니다.
그나마 우리가 주로 사용하는 한글은 별다른 노력 없이도 썩 괜찮은 정렬 결과가
나오는 편입니다만, 지역/언어에 따라서 매우 불편하게 느껴지는 결과가 나올 수
있습니다.

## 예외들

### 문화권마다 다른 "알파벳 순서"

흔히 하는 오해는, 우리가 아는 알파벳 순서와 인코딩된 코드값의 순서가 일치한다는
믿음입니다.

우리가 자주 쓰는 영문자죠. 로마자 혹은 라틴 알파벳이라고 불리는 문자는 많은
언어를 표현하기 위해서 사용됩니다. 하지만 모든 문화권에서 우리가 흔히 아는 A-Z의
순서로 알파벳을 쓰지는 않습니다.

대표적으로 리투아니아어에서 'y'는 'i'와 'k' 사이에 위치합니다. i < y < k

먼나라 이야기 같나요? 무려 한글도 같은 문제를 가지고 있습니다. 놀랍게도 남한과
북한의 한글 자음순서는 서로 다릅니다.

> 남한: ㄱ ㄲ ㄴ ㄷ ㄸ ㄹ ㅁ ㅂ ㅃ ㅅ ㅆ ㅇ ㅈ ㅉ ㅊ ㅋ ㅌ ㅍ ㅎ

> 북한: ㄱ ㄴ ㄷ ㄹ ㅁ ㅂ ㅅ ㅈ ㅊ ㅋ ㅌ ㅍ ㅎ ㄲ ㄸ ㅃ ㅆ ㅉ ㅇ

간단히 말해 남한에서는 "여유" < "하루" 이지만, 북한에서는 "하루" < "여유"가
됩니다.

[리투아니아어 알파벳](https://en.wikipedia.org/wiki/Lithuanian_orthography)

[한글의 알파벳 순서](https://en.wikipedia.org/wiki/Hangul#Alphabetic_order)

### 자음보다 앞에오는 모음

몇몇 언어에서는 모음이 자음 앞에 오기도 합니다. (태국어, 라오어 등등) 그럼에도
불구하고 단어는 자음 순으로 정렬이 되어야 합니다.

예를 들어, 태국어 단어 "เกาะ"는 'ก'이 자음이므로, 'เ'이 아닌 'ก'와 함께
정렬되어야 합니다.

[Thai String Collation](https://www.unicode.org/L2/L1999/99167.pdf)

### 동음이자

일본어는 동일한 음가의 문자를 두벌씩 가지고 있습니다. 바로 히라가나와
가타카나로, 모양이 다를 뿐 정렬 시에는 같은 음의 히라가나와 가타카나가 같은
레벨로 취급되어야 하는 경우가 생깁니다.

> ぱちぱち~＝パチパチ

여기에 일본어는 반각문자도 많이 사용합니다.

> パチパチ~=ﾊﾟﾁﾊﾟﾁ

### 합자

> æ = a + e

> ໜ = ຫ + ນ

æ를 아시나요. 최근엔 아이돌 걸그룹 에스파 덕분에 유명해진 글자입니다. 두 글자가
합쳐져서 만들어진 글자 "합자"는 정렬이나 비교과정에서 같은 문자로 취급해야할
경우가 생깁니다.

### 기타

많은 내용을 적었지만, 이것이 전부는 아닙니다.

- 용도에 따라 달라지는 정렬 기준 (대소문자 구별 없이 정렬, 인명 정렬)
- 나라에 따른 정렬 (중국의 병음 정렬, 대만의 획 정렬)
- 악센트 문자, 이체자 고려

등등

한국어(대한민국) 화자라면 이 내용들이 그렇게까지 중요한지 의문이 들 수도
있습니다. 하지만 위에서 언급한 내용들은 극히 끔찍한 예외들을 적은 게 아닙니다.
그 언어 / 문화권 사람이라면 어느정도 당연하다고 느껴지는 내용이기에 적용되지
않으면 눈에 띄게 됩니다.

굳이 비유하자면 커다랗고 촌스럽게 느껴지는 고딕글씨체가 적용된 한국어 페이지를 보는
느낌이려나요. 못 쓸 정도는 아니지만, 자신의 나라를 그렇게 세심하게
고려하지는 않았다는 인상을 줍니다. 만일 글로벌 서비스가 이런 방식으로 서비스된다면,
브랜드 이미지에도 영향을 미칠 수 있다고 생각합니다.

# Collation

그렇다면 어떻게 이런 다양한 언어들을 잘 정렬할 수 있을까요? 여기서
"Collation"이라는 개념이 등장합니다.

일반적으로는 정렬 이라고 번역되기도 합니다만. (컴퓨터에서 특히 locale과 관련하여)
"언어/문화/나라/용도"에 따라서 자연스럽게 정렬하는 것을 표현할 때
사용됩니다.

가장 간단하게 collation을 하는 방법은, OS가 제공하는 locale기능을 이용하는
것입니다. (참고로 Ubuntu 같은 경우에는 glibc의 locale을 통해 이를 제공하고
있습니다) 많은 언어에서 이러한 OS의 collation을 사용할 수 있도록 지원하고 있습니다.
다음 코드는 파이썬 예시입니다.

```python
import locale

# ja_JP.utf-8 로케일이 설정되어 있다고 가정
locale.setlocale(locale.LC_COLLATE, 'ja_JP.utf-8')
sorted(['か', 'あ', 'カ', 'ア', 'ｱ'], key=locale.strxfrm)
```

정렬 결과는 당연하게도 OS마다 다를 수 있습니다. 따로 OS 표준이 없기 때문에 어떤
정렬 결과를 주던 OS의 마음인 것입니다.

좀 더 플랫폼에 영향을 받지 않는 방법은, 서드파티 라이브러리인
[ICU Collation](https://unicode-org.github.io/icu/userguide/collation/)을
사용하는 방법이 있습니다. 이 구현체는 Unicode Collation
Algorithm([UCA](https://www.unicode.org/reports/tr10/))를 준수할 뿐만 아니라
UCA에서 지원하지 않는 기능도 여럿 지원합니다. 일반적으로 OS보다는 좀 더
자연스러운 collation이 가능합니다.

```python
import icu

collator = icu.Collator.createInstance(icu.Locale('ja_JP'))
sorted(['か', 'あ', 'カ', 'ア', 'ｱ'], key=collator.getSortKey)
# >> ['あ', 'ア', 'ｱ', 'か', 'カ']
```

# PostgreSQL에서의 Collation

RDBMS인 PostgreSQL에서도 collation을 지원합니다.
[Postgres 공식 문서](https://www.postgresql.org/docs/15/collation.html)

기본적으로 PostgreSQL는 OS에서 제공하는 Collation을 사용합니다. 또 ICU가
세팅되어 있다면 icu의 collation 또한 사용할 수 있습니다. 사용할 수 collation들의
목록은 다음과 같은 쿼리로 확인할 수 있습니다.

```sql
SELECT * FROM pg_collation;
```

Database를 생성할 때, Default Collation이 정해지고, 테이블의 칼럼을 생성 /
수정할 때 Collation을 지정할 수 있습니다. Collation을 지정하지 않았다면 해당
Database의 default Collation으로 작동하게 됩니다.

Expression으로 사용하는 법도 있습니다.

```sql
SELECT full_name FROM account ORDER BY (full_name COLLATE "ko_KR") DESC
```

하지만 이 방법은 Index의 도움을 받을 수가 없으므로 (다른 collation이 적용된 index는
사용할 수 없으므로), 느린 속도는 각오해야 합니다. 그렇기 때문에 성능을 중요시하는 경우에는
테이블 생성 시 Collation을 지정하는 것이 좋습니다

# 마치며

Collation에 대한 이해도 중요하지만, 좀 더 많은 개발자들이 언어에 대해 넓은 폭의 시야를
가졌으면 합니다. 한국이 글로벌 시장에 더 많이 녹아들면서, 다양한 언어를 처리해야 하는 경우가 점점 늘어나고 있습니다. 
다른 언어가 개발된 프로그램에서 어떻게 처리될 것인가 한번쯤 더
고민해보면 좋을 것 같은 생각을 했습니다.

# 여담

북한의 자모음 순서가 ICU Collation에 반영되지 않았다는 것은 흥미롭습니다. 북한용
프로그램을 만드려면 아무래도 직접 구현해야 할 것 같네요.

네이버 라오어 사전이 라오어 합자를 제대로 지원하지 않는다는 것도 흥미로운 사실입니다. 
합자와 분리자가 전혀 다른 검색결과를 보여줍니다.

PostgreSQL의 Collation이 OS에 의존적이기 때문에 개발 과정에서 혼란을 야기할 수 있습니다. 개발환경과 프로덕션 환경의 차이로 인해 Collation 설정이 달라지는 경우도 있을 것입니다. 이러한 문제를 해결하려면, 초기 세팅시 Collation을 잘 고려해야합니다. 특히 Windows를 사용하는 Azure 클라우드를 사용할 때 조심해야합니다. 최근에는 개선이 되기는 했는데, 예전 Azure PostgreSQL을 사용할 때는 "English_Unite States.1254" 같은 이상한 윈도우 locale을 볼 수 있었습니다.