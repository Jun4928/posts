# 2020 Curriculum to become a competent Junior Developer 
## 길은 보이고, 가기만 하면 된다. 꾸준히

이 글에서 내가 기록하고자 하는 바는 스스로 세운 커리큘럼과 그것을 이행하고자 하는 목표의식이다. 

## JavaScript 개발자가 되기 위한 커리큘럼
### 1. JavaScript + Data Structures and Algorithms
웹에서 사용되는 기술 스택을 들여다보니 엄청나게 다양하고 매력적인 JavaScript 라이브러리, 프레임워크들이 많다. 특히나 프론트엔드 쪽은 그 발전 속도가 어마어마한 것 같다. 그래서 그 빠른 물살에 속도를 잃고 우왕좌왕하고 싶지 않아서 (사실은 실력이 부족해서) JavaScript로 기본적인 자료구조를 구현해보고, 알고리즘 문제풀이를 시작했다. 왜냐하면, 이 기본기는 앞으로 개발자로서 문제해결 능력에 큰 기여를 할 것이기 때문이다. 내가 최종 목표로 하는 JavaScript Full Stack 개발자가 되기 위해서는, JavaScript를 사용한 폭넓은 문제해결 능력이 필히 요구된다. 

지금 한 달 정도 되었는데, 그간 어느 정도 JavaScript의 강력한 배열 관련 메소드들(map, reduce, filter, find,,) 과 Object, Map 같은 클래스의 사용도 익혔다. 더하여, 기본적인 자료구조(배열, 연결 리스트, 스택, 큐, 트리) 와 문자열 처리에 꽤나 익숙해졌다. 이 상태에서 앞으로 나아가기 위해서는 좀 더 깊은 수준의 자료구조와 알고리즘의 학습이 필요하다.

**지금까지 공부한 자료구조 및 알고리즘 Github Link**
- [Algorithms101](https://github.com/Jun4928/Algorithms101)

**앞으로 정복해야 할 대상**
- 자료구조 : 이진 탐색 트리(심화), AVL 트리, 트라이, 그래프, 힙, 우선순위 큐
- 알고리즘 : DFS&BFS(심화), 정렬 알고리즘(병렬, 퀵 등..), 동적 프로그래밍, 그리디 알고리즘, 최단 경로 알고리즘

**정복해야 할 문제들**
- [Leetcode](https://leetcode.com/problemset/all/)
- [Programmers](https://programmers.co.kr/learn/challenges)

### 2. 프로젝트 진행하기
일단, 첫 번째로 구상 중인 프로젝트는 수능 영어 단어 시각화 프로젝트다. 고등학생 영어 과외를 벌써 3년째 하고 있는데, 해를 거듭할수록 극악스러워지는 글의 구조와 단어들 때문에 수업을 하는 나도 괴리감이 온다. 영어를 이렇게까지 어렵고 복잡하게 배워야 하나.. (이 이야기는 글 몇 편이 나올 거 같다.)

가르치는 나도, 배우는 학생들도 그 수고를 덜기 위해서 EBS 홈페이지에 올라온 모의고사와 수능 PDF 파일을 이용해서 단순한 워드 카운트 프로그램부터 시작해서, 최종 형태로 웹에 서비스를 배포하는 것까지 목표로 하고 있다. 자연스레 이 서비스를 구현하려면 NodeJS, Express, MongoDB를 이용하고, 웹쪽은 Vanilla JS에 데이터 시각화 라이브러리인 D3.js나 chart.js를 익히고 사용하기에 이를 것이다. 

계획은 거창한데 끝까지 프로젝트를 완성 시키기 위해서는 부단한 노력이 필요하다. 현재는 구글링 해서 찾은 node 라이브러리인 pdf-parse를 이용해서 PDF->TXT로 변환해 놓았다. (그래도 시작이 반이라니까..) 

**프로젝트 완성을 위한 큰 세 갈래**
1. TXT 원본 데이터를 의미 있는 JSON 데이터로 변환, 구축하기 (빈도수, 해를 거듭하면서 바뀌는 단어 출제 경향 등..) 
2. 서비스 구축을 위한 백엔드 프레임워크 익히기 NodeJS, Express, MongoDB
3. D3.js, chart.js로 데이터 시각화 

**References**
- [coding train: data and apis in JavaScript](https://www.youtube.com/playlist?list=PLRqwX-V7Uu6YxDKpFzf_2D84p0cyk4T7X)

### 3. 블로그 글쓰기
위의 두 가지 커리큘럼에 대한 과정을 블로그에 기록하고자 한다. 이 기록 또한 배움의 과정이 될 것이다. (재귀인가..?)
스스로 가르치고 배우는 자가 되자.
