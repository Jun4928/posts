# 기록하지 않으면 사라질 찰나의 2주

## > wecode 1차 팀 프로젝트 후기

두 주간, 아니 정확히 말하면 12일 남짓한 시간동안 팀원들과 부대끼며 코드를 작성했다. 그 사이 잊지못할 에피소드, 그리고 잊고 싶지 않은 감정과 생각들을 기록하려 이 글을 써내려간다.

# 한 눈에 보는 프로젝트

## Pilly 웹사이트 React based Web App으로 클론

- [Pilly](https://pilly.kr/): 설문조사에 기반해 유저마다 다른 영양제를 추천하고, 영양제 구독 서비스를 제공하는 웹 기반 사업
- 프로젝트로써의 가치: 설문조사-장바구니-주문으로 이어지는 웹 기반 사업의 흐름을 React 앱으로 구현할 수 있음
- 기간: 12일 (20.07.20-20.07.31), 하루에 10시간씩 총 120시간
- 참여인원: 프론트엔드 3명, 백엔드 2명
- 사용한 라이브러리: React, React Router, React-Redux, Redux, React-Slick, Sass
- 협업 툴: Slack, Trello, Github
- Github Repository: [Front-End Repo](https://github.com/wecode-bootcamp-korea/10-bigstar-pharmacy-frontend)
- 마스터 브랜치를 제외한 총 11개의 기능별 브랜치
- 12일간, 19개의 Pull Request, 100개의 commit
- React 웹앱을 구성하는 src폴더 안의 구조: 42개의 폴더, 80개의 파일

```
├── Routes.js
├── actions
│   └── index.js
├── components
│   ├── Loading
│   │   ├── Loading.js
│   │   └── Loading.scss
│   ├── Nav
│   │   ├── Nav.js
│   │   └── Nav.scss
│   └── ScrollToTop
│       └── ScrollToTop.js
├── index.js
├── pages
│   ├── Cart
│   │   ├── Cart.js
│   │   ├── Cart.scss
│   │   ├── Noitems
│   │   │   ├── Noitems.js
│   │   │   └── Noitems.scss
│   │   ├── Price
│   │   │   ├── Item
│   │   │   │   ├── Item.js
│   │   │   │   └── Item.scss
│   │   │   ├── Price.js
│   │   │   └── Price.scss
│   │   └── testApi.js
│   ├── DetailView
│   │   ├── DetailIconData.js
│   │   ├── DetailView.js
│   │   └── DetailView.scss
│   ├── Login
│   │   ├── Login.js
│   │   └── Login.scss
│   ├── Main
│   │   ├── Answer
│   │   │   ├── Answer.js
│   │   │   └── Answer.scss
│   │   ├── AnswerData.js
│   │   ├── Main.js
│   │   └── Main.scss
│   ├── MyPilly
│   │   ├── DeliveryDetail
│   │   │   ├── DeliveryDetail.js
│   │   │   └── DeliveryDetail.scss
│   │   ├── MyPilly.js
│   │   └── MyPilly.scss
│   ├── Order
│   │   ├── DelieveryInfo
│   │   │   └── DelieveryInfo.js
│   │   ├── Order.js
│   │   ├── Order.scss
│   │   ├── OrderInfo
│   │   │   ├── OrderInfo.js
│   │   │   ├── OrderInfo.scss
│   │   │   └── ProductDetail
│   │   │       ├── ProductDetail.js
│   │   │       └── ProductDetail.scss
│   │   ├── PaymentInfo
│   │   │   ├── PaymentInfo.js
│   │   │   └── PaymentInfo.scss
│   │   └── UserInfo
│   │       └── UserInfo.js
│   ├── Polls
│   │   ├── Contents
│   │   │   ├── Answers
│   │   │   │   ├── CheckBox
│   │   │   │   │   ├── CheckBox.js
│   │   │   │   │   └── CheckBox.scss
│   │   │   │   ├── RadioButton
│   │   │   │   │   ├── RadioButton.js
│   │   │   │   │   └── RadioButton.scss
│   │   │   │   └── TextInput
│   │   │   │       ├── TextInput.js
│   │   │   │       └── TextInput.scss
│   │   │   ├── Contents.js
│   │   │   ├── Contents.scss
│   │   │   └── Question
│   │   │       ├── Question.js
│   │   │       └── Question.scss
│   │   ├── FinalPage
│   │   │   ├── FinalPage.js
│   │   │   └── FinalPage.scss
│   │   ├── Polls.js
│   │   ├── Polls.scss
│   │   ├── PollsHeader
│   │   │   ├── PollsHeader.js
│   │   │   └── PollsHeader.scss
│   │   └── StartPage
│   │       ├── StartPage.js
│   │       └── StartPage.scss
│   ├── Products
│   │   ├── Product
│   │   │   ├── Product.js
│   │   │   └── Product.scss
│   │   ├── Products.js
│   │   ├── Products.scss
│   │   └── test.js
│   ├── ResultsPage
│   │   ├── ResultsPage.js
│   │   └── ResultsPage.scss
│   ├── Review
│   │   ├── Review.js
│   │   ├── Review.scss
│   │   └── UserReview
│   │       ├── UserReview.js
│   │       └── UserReview.scss
│   └── Signup
│       ├── Signup.js
│       └── Signup.scss
├── reducers
│   └── index.js
├── styles
│   ├── _mixin.scss
│   ├── common.scss
│   ├── pilly-main.scss
│   ├── reset.scss
│   └── surveyv2-result.scss
├── urls
│   └── urls.js
└── util
    └── throttle.js

42 directories, 80 files
```

- 구현 영상: [Youtube Link](https://www.youtube.com/watch?v=dKeQ2rYbfp0)

# Episodes

## 1. 팀, 그리고 나

그간 혼자서 React 학습을 위해 Unsplash, Youtube, JSON Placeholder 와 같은 Public API를 통해 간단한 앱을 만들어 본 것이 전부였다. 팀으로 하나의 결과물을 만들어 낸다는 것에 기대를 하고 프로젝트에 임했다.

역시나 팀으로 하는 협업은 생각만큼 달콤하지 않다. 생각해보면 협업은 힘든것이 당연하다. 왜냐하면 실력도 생각도 개성도 각기 다른 사람들과 하루에 10시간씩 같은 공간에 머물며 하나의 목표를 성취한다는 것, 이 사실 자체만으로도 우리에게 피로감을 준다. 하지만, 전달하려는 것은 앞서 서술한 힘듦과 피로함이 아니다. 이 피로함을 상쇄시킬 수 있는 것은 협업하는 방식과 개인의 태도다.

1. 협업 방식

결론부터 말하자면, 재미없으면 하기 싫다. 협업을 하는데 혼자 하는 것보다 성취가 보이지 않고 능률이 떨어진다면 누가 협업을 하려고 하겠는가. 반대로 말하면, 협업을 통해 내 능력밖의 일을 해냈을 때의 기쁨이 같이 일하는 것의 즐거움이다.

우리는 **> wecode** 에서 제안한 밀첩한, 날렵한 이란 뜻을 가진 agile 협업 방식으로 팀 프로젝트를 진행했다. 매일 아침 15-20분간의 짧은 회의와 월요일에는 한 주간 해야 할 일을 정리하고 기록하는 스프린트 미팅을 두 번 가졌다. 데일리 미팅에서는 각각 돌아가면서 현재 본인 작업의 진행상황과 앞으로 해야할 일들을 이야기했고, 이를 토대로 '그' 날에 이뤄야 할 목표를 매일마다 새롭게 설정했다. '그' 날에 포인트를 준 이유는, 매일마다 우리가 성취해야 할 목표를 새롭게 설정하는데에 있다. 우리는 2주 안에 웹 기반 사업의 로직을 구현하는 것이 목표였는데, 이를 위해서는 매일마다 구현해야 할 양이 분명 존재했다. 하지만 우리는 이 구현 목표를 첫날에 다 정하고 계획에 맞춰(전형적인 waterfall 방식) 개발하지 않았다. 같은 업무를 하더라도 과중되는 업무량이 한 번에 주어지는 것과 끊어서 작은 성취를 이루어 내는 것에는 큰 차이가 있다. 따라서, 우리는 우리의 현재 시점에서 할 일들을 새롭게 계획하고 그 날 그 날 기능을 추가 해 가는 식으로 개발 해 나갔다.

우리가 설정한 목표를 하루 하루 이뤄나간다는 것은 계속해서 앞으로 나아갈 수 있도록 해주는 중요한 동기가 된다. 우리팀은 프로젝트가 진행되는 동안 매일마다 프론트엔드와 백엔드가 같은 기능을 개발했다. 우리가 구현한 컴포넌트의 레이아웃이 백엔드 API와 연동 됨으로써 생명력을 얻는 성취감을 매일마다 얻을 수 있었다.

한 가지 더 기록하고 싶은 협업의 방식은 바로 짝코딩이다. 기능별로 브랜치를 나눈다고 해도, 서로 붙어서 React 컴포넌트의 설계와 그 안에 들어갈 로직을 함께 고민했다. 그 고민의 결과물이 바로 설문조사 기능이다. 설문조사를 구현하기에 앞서 다른 프론트엔드 팀원과 두시간 정도 머리를 맞대고 그림을 그려가며 설문조사 페이지에 들어갈 컴포넌트를 구성했다. 마치 새롭게 하나의 앱을 짜는 과정과 같았는데, 재사용 가능하고 가벼운 컴포넌트들이 탄생하게 되었다. (설문조사 기능에 대한 글은 추후에 올릴 예정)

분명 혼자의 능력으로는 이 프로젝트를 해낼 수 없었기에 팀원들에게 고마움의 마음을 전한다.

2. 개인의 태도

나는 개인적으로 팀프로젝트가 내 개발능력을 폭발적으로 상승 시킨다고는 생각하지 않는다. 일반적인 의미로도 실력은 고통의 결과물이기 때문에 절대적인 외로움과 고립감 없이는 늘지 않는다고 생각한다. 개발 인생에도 분명 혼자 싸워야할 몫이 있다.

애초에 나는 내 실력 향상을 목표로 두기보다 팀으로 함께하며 얻어갈 수 있는 것들을 내 개인의 목표로 삼았다. 개발의 즐거움, 일로만 상호작용하는 것이 아니라 인간으로서 함께할 때 성취할 수 있는 결과물을 눈으로 확인하고 싶었다. 다른 팀원은 어떨지 모르지만, 나는 우리가 할 수 있었던 최선의 결과를 마주했다고 생각한다.

아직 2차 프로젝트가 남아있지만 단언 할 수 있는 것은, **함께하는 개발은 즐겁다** 라는 것이다.

## 2. 지옥에서온 버전관리자 Git, Github

깃과 관련해서 내가 저지른 실수 두가지를 기록한다.

1. local git master를 더럽힌 행위

git 과 github를 통한 협업을 진행 할 때 주의해야 할 것은 바로 데이터의 흐름이다. 종택멘토님의 말에 따르면, 원격저장소(github master) => 로컬마스터(git master) => 로컬브랜치(git branch) 로 데이터가 단방향으로 흘러야 서로간의 충돌없이 원할한 작업이 가능하다고 했다. 로컬브랜치에서 원격저장소로 pull request 를 보내는 행위까지 포함하하면 데이터의 순환고리를 만들 수 있는데, 양방향으로 데이터가 흐르면 커밋내역과 버전관리가 힘겨워 진다.

나는 pull request를 보낸 브랜치에서 마스터 브랜치로 체크아웃을 하고 다른 작업을 이어가고 싶었다. 그래서 아무생각없이 마스터 브랜치로 아직 merge 되지 않고 pull request를 보낸 브랜치의 내역을 직접 merge 시켰다. 로컬의 마스터 브랜치는 항상 원격저장소(github master)로 부터 최신화된 코드를 받아오는 기능을 해야 하는데, 나는 이 신성한 마스터 브랜치를 더럽힌 것이다. 그 대가는 생각보다 혹독했다.

내 로컬마스터(git master)의 시점과 원격저장소(github master)의 시점이 다르기 때문에 최신화된 코드를 받아올 수 없는 불상사가 발생했다.

해결방법

```
1. git log또는 tig 명령어로 로컬마스터(git master)가 더렵혀 지기 전 상태의 HASH 값을 복사한다.
2. git reset --hard 명령어로 위에서 복사한 HASH값을 인자로 주어서 시점을 되돌린다.
3. git pull origin master 명령어로 원격저장소(github master)의 코드를 당겨와 최신화 한다.
4. 원래 작업하던 브랜치로 이동해서 작업을 이어간다.

아예 작업을 할 때, 각각의 브랜치는 서로간의 의존도를 줄이는 편이 낫다. 로컬에서 손 merge는 절대 금물. 데이터의 단방향 흐름을 기억하자.
```

2. git stash 명령어로 임시저장한 내역을 날려버린 행위

종택멘토님께서 알려주신 stash(임시저장) 명령어를 사용해서 작업을 하고 싶었다. 브랜치간 이동할 때마다 작업내역을 기록(commit) 해야 넘어갈 수 있는데 의미없는 커밋내역이 더럽게 쌓이지 않게 하기 위해 작업한 내영을 임시저장 할 수 있다.

여기까지는 좋다. 근데, 임시저장한 내역이 브랜치마다 독립적으로 저장되는 것이 아니라, 모든 브랜치들이 공유하는 임시저장 공간에 저장된다. 나는 이 사실을 모르고 이 곳 저 곳 여러 브랜치들을 신나게 돌아다니다가 그만, 다른 브랜치에서 `git stash pop` 명령어를 써버렸다. pop 명령어는 최근의 임시저장한 내역을 적용함과 동시에 삭제시킨다. `git stash apply, git stash drop` 두가지의 명령어를 한 번에 수행하는 기능을 가지고 있다.

A라는 브랜치에 적용되야 할 내역이, B라는 브랜치에 적용되었고(+ pop명령어로 사라져 버렸고), 이미 뒤섞여진 변경 사항을 하나하나 일일히 찾는 것은 너무나도 고통스러운 작업 이었기에 5시간에 걸친 레이아웃을 다시 작성해야 했다.

교훈: git 명령어를 치기전엔 세 번은 더 맞는지 생각하고 치자. 안다고 섣불리 타자치지 말자.

## 마치며

마지막으로 3시간 동안 도저히 찾을 수 없었던 에러의 원인이 바로 오타 한 점에 있었다는 점을 기록하며 글을 마무리 한다.

설문조사의 기능에서 백엔드로 부터 넘겨받은 질문지의 type 에 따라서 다른 유형(RadioBox, CheckBox, TextBox)의 컴포넌트를 렌더링하는 로직이었다.

1번부터 19번까지 type 으로 넘겨받은 데이터에 따라 체크박스들이 잘 렌더링 되었는데, 20번 설문지부터 체크박스가 렌더링되지 않는 오류였다. 나의 상태관리 로직에 문제가 있을거라 예상하고 문제해결을 위해 componentDidUpdate, setState 콜백함수, 모든 타입체크하는 로직 앞에 console.log 붙여보기 등 다양한 방법을 시도해 봤다. 막차 시간 때문에 결국 문제를 해결하지 못했다. 디버깅도 안되고 어이가 없어서 거짓말을 외쳤다.

다음날 오전, 다시 같은 문제를 붙들고 있는데 HOXY나 하는 마음에 설문조사 기능을 맡은 백엔드 동료에게 물었다. 혹시 type 이 제대로 오는게 맞냐고. 아니나 다를까 1번부터 19번사이에 체크박스인 설문 유형의 데이터에서는 type값이 **check** 로 오다가, 20번 질문 이후의 설문지에는 type 값이 **ckeck** 였던 것이다. 데이터베이스에 type 값을 저장할 때 부터 오타가 나 있었던 것이다. 윌리엄의 3단 분노가 생각나는 순간이었지만, 백엔드로 부터 오는 응답에 담겨오는 데이터를 꼼꼼히 살펴보지 않은 나의 잘못이 컸음을 인정했다.

1. POST MAN, console.log 등 다양한 디버깅 툴 이용하기
2. 백엔드와 주고받는 데이터는 문서화 시켜놓기
3. 백엔드의 데이터에만 의존하는 방식의 개발 지양하기 (mock data 준비 해 놓기)
