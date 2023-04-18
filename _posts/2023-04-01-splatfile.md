---
title: Splatfile 프로젝트 횡설수설 회고
tags: [ project, splatfile, splatoon ]
category: programming
date: 2023-04-01 17:02:12 +0900
lng_pair: id_splatfile_project
---

![splatfile-og](:splatfile-og.png){:data-align="center"}

# Splatfile

닌텐도 게임 소프트웨어 "스플래툰3"의 자기소개 프로필 작성 / 공유 서비스 [Splatfile](https://splatfile.deno.dev)

2개월동안 열심히 개발한 끝에 3/27일에 배포를 성공적으로 마쳤고, 여기서는 개발하면서 겪었던 어려움과 주저주저리를 두서없이 써보려구 한다.

# 만세!

현 시점(2023-04-04)까지의 지표!

- 최대 84K requests / 24 hours
- 생성된 계정 수 약 400개

이렇게까지 많은 사람들이 써줄거라고는 생각 못했었는데, 너무 고마웠다ㅠㅜ...

![만세 일러스트](:yatta.png){:data-align="center" style="width: 120px"}

참고로 Deno Deploy Free tier의 limit이 24시간당 100K라서 조금 조마조마했었다.

# 프로젝트 계기

시작은 비슷한 성격의 "자기소개 카드"에서 시작했었다. ([다양한 자기소개 카드들](https://twitter.com/hashtag/%E3%82%B9%E3%83%97%E3%83%A9%E3%83%88%E3%82%A5%E3%83%BC%E3%83%B33%E8%87%AA%E5%B7%B1%E7%B4%B9%E4%BB%8B%E3%82%AB%E3%83%BC%E3%83%89?src=hashtag_click))

"자기소개 카드"란 대략적인 자신의 성향, 하고싶은말, 기타 정보들을 적고 공유할 용도로 사용되는 이미지를 말한다.

주로 #스플래툰_트친소(트위터 친구 소개) 이런 해시태그와 함께 트위터에 올려서, 같이 게임을 할 친구를 구하는데 사용되곤 한다.

문제는 직접 만드는게 너무 힘들었다. 그래픽 편집 툴이 필요했고, 좀 더 예쁘게 만들려면 손글씨도 필요해서 많이 까다롭게 느껴졌다.

'이걸 간단하게 웹으로 작성할 수 있게 하면 많이 쓰지 않을까?'라는 생각이 들었고, 마침 흥미가 생겼었던 Deno fresh와 함께 이 프로젝트가 시작되었다!


# 어려웠던 점

## 프론트 초보

아무것도 모르는 상태에서 풀스택 프로젝트를 만드는 사람이 있다? 바로 여기있습니다

내가 서버개발자라서, 사실 프론트 개발이라고는 React 튜토리얼밖에 안써봤었다.

개발언어인 JS/TS도 사실상 처음 써보는거라, 개발하는 모든게 공부에 가까웠다.

오히려 무지해서 더 용감하다고 하던가, 용캐 어떻게든 시작하고 나니까 뭔가 만들어지긴 하더라...

어렵기는 했지만 그만큼 프론트 개발자 입장을 좀 더 잘 이해할 수 있게 된 계기가 된 것 같아서 뿌듯하다.

그도 그럴것이 프론트 작업하다가... 회사 프론트 개발자분 말을 이제서야 이해하게 된 AHA 모멘트가.. 너무 많았었어...

## Deno Fresh Preact의 호환성  

남이 만들어놓은 Component를 가져다 쓰기가 거의 불가능했다!!

첫째는 Deno에서 npm을 100%지원하지 않는다는 점과,
Deno Fresh의 [island의 architecture](https://fresh.deno.dev/docs/concepts/architecture) 특성상 preact의
몇몇 기능이 제한되는데 이 때문에 대부분의 컴포넌트가 작동을 하지 않았다...

preact가 자랑하는 "React"와의 호환성도 Deno Fresh에서는 작동을 하지 않았구... 결국 울면서 수많은 컴포넌트를
열심히 만들었다. 그만큼 프론트 실력은 성장한 것 같긴 하지만 슬픈건 슬픈거라 슬펐다.

## 디자인 컨셉

최대한 "스플래툰3-스러움"을 살리고 싶었는데, 문제는 이 "스플래툰3-스러움"이 웹으로 구현하기 너무 까다로웠다.

"스플래툰3-스러움"을 키워드로 요약해보자면

  - Chaos
  - 기울어짐
  - 스티커가 덕지덕지 붙은 로커
  - 찢어짐

직접 사진을 보자...

![스플래툰 메뉴 UI](:splatoon-ui-1.jpg){:data-align="center"}

![스플래툰 상점 UI](:splatoon-ui-2.jpg){:data-align="center"}

저 찢어져서 너덜거리고! 덕지덕지 붙어있고! 제각각 기울어져있는! UI가 보이나...

UI를 떠나서도... 스플래툰3 제작진은 컨셉에 너무 충실한 나머지, 플레이어 기본의상도 너덜거리게 만들었다. (두번째 사진에서 오른쪽 캐릭터가 입고 있는 누더기 옷이 스플래툰을 처음 플레이했을 때 받는 의상...)

어쨌든 문제는 이 컨셉을 웹으로 구현하기가 너무 어려웠다.

찢어지고 너덜거리는 컨테이너를 반응형으로 잘 만드는 방법은 아직도 모르겠고, 기울어진 컨테이너는 내부 글자를 너무 뭉개버려서 알아먹기 힘들어 잘 쓸 수 없었다.

![Splatfile 무기 카드 디자인](:splatfile-wp-card.png){:data-align="center"}

그래도 나름 힘냈어...

## 디자이너 구합니다

회사에서 일을 하면서, 디자이너분의 중요성이야 깨달아 알고 있었지만
몸으로 직접 겪어보니 더 절실히 느껴졌다.

이미지 리소스도 직접 만드려니 너무 죽을 상이었구...

그래도 회사 다니면서 디자이너 분들이 하시는 말씀 열심히 주워들어서
진짜 최소한(진짜 최소한)의 UX 선은 지킨 사이트가 나온 것 같아서 다행이다. 

디자이너 분들 사랑해요.

## 척박한 개발 생태계

Deno의 백엔드 관련 라이브러리는 상당히 부족한 편이었다.

가장 큰 문제였던 것은 비밀번호 해싱 라이브러리였는데, 나는 최신 (상대적으로) 해싱 알고리즘인 argon2, baloon을 쓰고 싶었지만, Deno에 쓸만한 적절한 구현체가 없었다. Deno용 argon2 구현체 중 유이한 것은 아래 두개였다.

### deno-argon2

가장 인기있는 프로젝트지만, 사용중단(deprecated)된 기능을 사용해서 현재는 작동하지 않음.

### argontwo:

구현을 살펴보니 제일 믿을만한 프로젝트였지만, 오직 hash함수만을 제공하고 있어서 verify 등을 직접 구현해야했다. 이는 검증되지 않은 나만의 코드를 신용할 수 없었기 때문에 포기했다.

그래도 어떻게든 사용해보고 싶어서, deno-argon2를 열심히 수정하여 PR을 올리고 머지되기까지 했다. ([deno-ffi 써보기](/posts/2023-02-09-deno-ffi)도 이 때 공부하면서 작성했다.)

그러나 메인테이너가 배포를 하지 않아서, 결국 검증된 scrypt 라이브러리를 사용하는 것으로 차선책을 택했다. 나중에 배포되고 좀 더 검증된 후에는 argon2로 마이그레이션할 예정이다.

# 끝으로

여러가지로 처음이고 헤매다보니, 아무리 생각해도 퀄리티가 내가 상상한만큼 나와주지 않았다.

이걸 공개해야하나 말하야하나 고민도 많이 했었지만, 사람들이 잘 써주는걸 보고 있으니 너무 기분이 뿌듯했다 ㅎㅎ

아무튼 나도 이제 힘내서 현생 살아야지! 장황한 긴 이야기 읽어주셔서 감사합니다!

# 고마웠던 분들

테스트를 비롯해서 여러가지 도와준 친구들 너무 고마워!!

[Special Thanks](https://splatfile.deno.dev/thanks)