# Programmers Level2 후보키
## 멱집합(모든 부분집합), DFS, 완전탐색

[문제링크](https://programmers.co.kr/learn/courses/30/lessons/42890)

# 돌파구
사실, 처음 이 문제를 접하고 완전탐색으로 풀어야 하는지 감을 잡지 못했다.
카카오 공식 홈페이지의 해설을 보고나서야 후보키로 선정될 수 있는 모든 속성에 대해서 검사해야 한다는 것을 깨달았다. 

후보키가 될 수 있는 조건은 
- 유일성: 데이터베이스의 모든 튜플이 유일하게 식별되어야 한다.
- 최소성: 유일성을 가진 후보키중에서도 꼭 필요한 속성들로만 구성되어야 한다. 

이 두가지 조건을 거쳐야 최종 후보키로 선정될 수 있다. 
그렇다면 후보키를 구하는 과정을 크게 세 부분으로 나누어 볼 수 있다. 

## 1. 후보키로 선정될 수 있는 모든 속성의 모든 부분집합(멱집합) 구하기 
바로 이전글에 포스팅 되어있는 멱집합을 구하는 함수로, 데이터베이스 속성들의 모든 부분집합을 구한다. 인풋으로 들어오는 데이터베이스의 테이블중 row가 되는 튜플의 length가 속성의 length가 되고, index가 속성의 index가 된다. 

[멱집합 포스트 링크](https://medium.com/@jun.choi.4928/javascript로-멱집합-powerset-리턴하는-함수-구현하기-f1cce8cc3268)

## 2. 1에서 구한 모든 속성의 부분집합에 대해 유일성을 판별하기
모든 속성의 부분집합에 대해 모두 유일성 검사를 한다. 유일성을 만족하는 기준은 속성(하나의 부분집합)으로 데이터베이스의 모든 튜플들이 구별되는가 이다.
이를 구현하기 위해서 JavaScript Array Filter 메소드를 사용했다. 유일성을 만족하면 true를 리턴, 유일성이 만족되지 않으면 false를 리턴해서 필터링을 거치는 구조이다. 

필터함수 안의 로직이 유일성을 판별하는 알고리즘이 되는데, 유일성을 판별하기 위해서 relation으로 들어온 DB테이블에 대해 처음부터 끝까지 해당 속성 값으로 튜플안에 들어있는 데이터를 string으로 치환한 후에 배열에 담고, includes 메소드를 사용해서 중복되는지 아닌지를 검사했다. 한 번이라도 중복된 값이 나오면 false를 리턴해서 해당 후보키는 필터에서 걸러지게 했다. 

## 3. 2에서 유일성이 판별된 키중에서 꼭 필요한 속성들만 담겨있는지 골라내기
위의 두번째 과정까지 거치면, 유일성을 만족하는 후보키들의 리스트를 골라낼 수 있다. 마지막으로 최소성을 만족하는 후보키들을 골라내야 한다. 

```
만약 어떤 슈퍼 키 X에 대해 X의 부분집합인 슈퍼 키 Y가 없다면 (X ⊃ Y인 슈퍼 키 Y가 없다면) X는 후보 키로 선택될 수 있습니다.
-- 카카오 해설 --
```
[2019 카카오 코딩테스트 해설](https://tech.kakao.com/2018/09/21/kakao-blind-recruitment-for2019-round-1/)

즉 유일성이 검증된 후보키가 다른 후보키들을 부분집합으로 가진다면, 이 후보키는 최소성을 만족하지 못한다. 왜냐하면, 불필요한 속성이 끼어있는 것이 되기 때문이다. 이 작업은 불필요한 속성이 있는지 없는지를 판단하는 작업인데, 이를 위해서 두번째 과정에서 걸러진 후보키들에 대해서 부분집합의 여부를 판별해서 부분집합을 가진다면 false, 가지지 않는다면 true를 리턴해서 필터링을 할 수 있다. 


# 구현
```javascript
const getSubsets = function (arr) {
  let flag = new Array(arr.length).fill(false);
  const subSets = [];

  const subSet = function DFS (depth) { // 부분 집합 구하는 재귀 함수, DFS 알고리즘
    if (depth === arr.length) { // 트리의 끝에 다다른 것 ==> 재귀 종료 조건
      const subSet = arr.filter((value, index) => flag[index]); // 해당 flag true => 부분집합 포함
      subSets.push(subSet); // 부분집합들을 담는 배열에 push

      return;
    }

    flag[depth] = true; // 해당 depth의 flag true = 트리의 왼쪽
    subSet(depth + 1); // 다시 재귀호출

    flag[depth] = false; // 해당 depth의 flag false = 트리의 오른쪽
    subSet(depth + 1); // 다시 재귀 호출
  }
  
  subSet(0); // depth 0 부터 시작
  return subSets;
}


const solution = function candidateKeys (relation) {
  const attributes = new Array(relation[0].length).fill(0).map((value, index) => value + index); 

  const subSets = getSubsets(attributes);  // 속성들의 부분집합 구하기

  const tupleToString = function (tuple, set) { 
    return set.reduce((str, value) => { // 부분 집합의 value만 선택해서 str 만들어서 return
      return str + tuple[value];
    }, '');
  };

  const superKeys = subSets.filter((set) => { // 모든 부분집합에 대해서 유일성 검사
    const tuples = [];
    
    for (let i = 0; i < relation.length; i++) { // 모든 튜플에 대해서
      const tupleStr = tupleToString(relation[i], set); // 각 튜플 -> 현재의 부분집합에 따라 str 으로 만들기
      if (tuples.includes(tupleStr)) return false; // 중복되면 바로 false return 되서 유일성 확보 X
      if (!tuples.includes(tupleStr)) tuples.push(tupleStr);  // 중복되지 않을 때만 tuples 에 push
    };

    return true // 위의 for문에서 false 로 return 되지 않으면, 유일성 확보 된 것
  });

  const isSub = function (key, comparedKey) { // comparedKey 의 모든 요소가 key 안에 포함되어야 comparedKey가 key의 부분집합이 된다.
    return comparedKey.every((value) => key.includes(value)); // Key > comparedKey 부분집합 성립하는지 확인하는 코드
  }

  const candidateKeys = superKeys.filter((key, index) => { // 유일성 검사 된 key 에 대해서 최소성 검사하기
    for (let i = 0; i < superKeys.length; i++) {
      if (i === index) continue; // 같은 것은 검사 X
      if (isSub(key, superKeys[i])) return false; // 현재 key가, 배열 중 하나라도 부분집합으로 가진다면, 최소성에서 탈락
    }

    return true; // 위의 for문에서 false 로 return 되지 않으면, 최소성을 만족하는 후보키다.
  });

  console.log(candidateKeys);
  return candidateKeys.length; // 후보키의 최대 개수
}

solution([["100", "ryan", "music", "2"], ["200", "apeach", "math", "2"], ["300", "tube", "computer", "3"], ["400", "con", "computer", "4"], ["500", "muzi", "music", "3"], ["600", "apeach", "music", "2"]]);

// edge test case.. 
solution([['b','2','a','a','b'],
  ['b','2','7','1','b'],
  ['1','0','a','a','8'],
  ['7','5','a','a','9'],
  ['3','0','a','f','9']]);

```