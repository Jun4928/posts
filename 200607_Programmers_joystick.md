# Programmers Level2 조이스틱
## Greedy Algorithm

[문제링크](https://programmers.co.kr/learn/courses/30/lessons/42860)

# 돌파구
그리디 알고리즘은 간단하게, 문제해결을 위해서 수많은 선택을 거쳐야 한다고 했을 때 매 번 선택의 순간마다 더 좋은 또는 효율적인 것을 선택하는 알고리즘이다. 그래서, **지역적으로** 만 최적화된 의사결정이다. 다른말로 하면, 전체적으로 봤을 때 더 좋은 선택이 있을수도 있다. 왜냐하면, 갈래길에서 그 순간에서만 좋은 선택을 했기 때문이다. 

삶을 이루는 중요한 선택에서 왜, 나만을 위해 Greedy 하게 선택하면 안되는지.. 알려주는 알고리즘이다. 매 순간은 나의 이익을 취하는게, 나의 편함을 선택하는게 좋을지 모르지만, 길게 놓고 보면, 좋은 선택이 아니었다는 것을 후회하게 될 때. 나는 그리디(욕심적으로) 선택헀구나 라고 깨닫게 된다.

조이스틱문제는 의사결정 문제이다. 

크게 두가지의 로직으로 나누었다.
## 1. 입력된 문자가 나올 때까지 위, 아래로 조작 할 때 드는 비용을 구하는 모듈

## 2. 입력된 문자를 완성하기 위해서 왼쪽, 오른쪽으로 조작 할 때 드는 비용을 구하는 모듈
여기서 왼쪽으로 갈지, 오른쪽으로 갈지를 선택하는 알고리즘이 그리디 알고리즘이 된다. 

- `1.` 에서 구한 각각의 문자를 만들기 위해 조작해야 할 조이스틱의 비용이 담긴 배열을 `eachCost` 라고 하자. 그럼 이 배열의 0인 부분은 조작이 필요없는 부분이다. ('A' 는 조작할 필요가 없기 때문)
- `eachCost` 를 `path` 에 복사하고 배열의 인덱스가 0인 지점이 조작을 시작하는 시작점 이므로 0을 집어넣는다. 이 배열의 모든 요소가 0이 될 때에 조작을 마친 것이다.
- 매번 `path.some(value => value !==0)` 을 통해서 배열이 0이 남아있을 때에는 아래 로직을 수행하도록 한다.

## while 문 안의 로직
- toRight, toLeft 함수를 따로 빼내서 구현했다.
- `toRight` 함수는 오른쪽으로 이동할 떄 드는 비용과 조작해야할 인덱스를 리턴한다.
- `toLeft` 함수는 왼쪽으로 이동할 때 드는 비용과 조작해야할 인덱스를 리턴한다.
- 두 함수의 리턴값을 받아서 둘 중 작은 쪽으로 이동해서 조작한다.


# 구현
```javascript
const joystick = (name) => {
  if (!name) return 0;

  // ascii 코드로 변환 후, 조이스틱의 위 아래로 알파벳을 맞추는데 드는 비용을 구하기
  const ascii = [...name].map((char) => char.charCodeAt()) 
  const eachCost = ascii.map((num) => {
    if (num <= 78) return num - 65; // A-M 까지 위 방향키
    if (num > 78) return 91 - num; // O-z 까지 아래 방향키
  });
  const totalEachCost = eachCost.reduce((acc, num) => acc + num , 0); 
  // 조이스틱의 위 아래 조종하는데 드는 비용

  // 조이스틱의 왼쪽, 오른쪽 커서를 이동하는데 드는 비용 구하기
  // 'A'는 조작할 필요가 없으니 뛰어 넘는것이 포인트 
  const toRight = (arr, idx) => { // 오른쪽으로 이동 할 때,
    let length = 0;
    while (arr[idx] === 0) { // 0이 아니면 조작해야 할 곳을 찾은 것!
      length += 1;
      idx = idx < arr.length - 1 ? idx + 1 : 0; // 배열의 오른쪽 끝을 만나면 처음으로
    }

    return [length, idx]; // 가야 할 거리와 인덱스를 리턴
  };

  const toLeft = (arr, idx) => { // 왼쪽으로 이동 할 때,
    let length = 0;
    while (arr[idx] === 0) { // 0이 아니면 조작해야 할 곳을 찾은 것!
      length += 1;
      idx = idx > 0 ? idx - 1 : arr.length - 1; // 배열의 왼쪽 끝을 만나면 다시 뒤로
    }

    return [length, idx]; // 가야 할 거리와 인덱스를 리턴
  };

  let path = [...eachCost];
  path[0] = 0; // 
  let pathIdx = 0; // 처음시작은 배열의 시작 
  let shortestPath = 0; // 최종으로 짧은 조작을 저장할 변수
  while (path.some(value => value !== 0)) { // 배열에 0이 남아있을 때 까지 아래 로직을 실행한다. 
    const [rightLength, rightIndex] = toRight(path, pathIdx); // 오른쪽으로 갔을 때 드는 비용
    const [leftLength, leftIndex] = toLeft(path, pathIdx); // 왼쪽으로 갔을 때 드는 비용

    shortestPath += leftLength < rightLength ? leftLength : rightLength; // 비교해서 작은 값을 더하기 
    pathIdx = leftLength < rightLength ? leftIndex : rightIndex; // index 이동하기
    path[pathIdx] = 0; // 검사했으면 배열의 값을 0 으로 만들기
  }

  return shortestPath + totalEachCost;
};


console.log(joystick("JAZ"));
console.log(joystick("JAN"));
console.log(joystick("JEROEN"));
console.log(joystick("JAZAAAB"));
console.log(joystick(""));
console.log(joystick("BBBBAAAAB"));
console.log(joystick("AAABAAAA"));
```

# 테스트 케이스 오류
사실, 이 문제에는 테스트케이스에 오류가 있다.
`console.log(joystick("BBBBAAAB")` 일 때에는, 10이 맞는 답이지만, 위의 코드로 실행하면 12가 된다. 그렇지만, 프로그래머스의 모든 테스트 케이스는 통과한다.

`pathIdx = leftLength <= rightLength ? leftIndex : rightIndex;` 둘의 최소비용이 같으면 왼쪽으로 가도록 코드를 수정하면 위의 테스트케이스는 10으로 통과하지만, 프로그래머스의 11번 테스트 케이스는 통과하지 못 한다.
