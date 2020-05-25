# Programmers Level2 쇠막대기
## 스택, 큐

[문제링크](https://programmers.co.kr/learn/courses/30/lessons/42585)

## 돌파구
문제가 길어서 어려워 보이지만(?), 생각보다 간단하다. 
괄호가 나오는 문제는 항상 스택을 이용하면 간단하게 풀린다. 결국 괄호의 쌍을 맞추는게 핵심이기 때문에 괄호가 열리고 닫힐 때 stack의 push와 pop 기능을 사용하면 된다. 

이번 문제에서는 괄호의 여닫이가 막대기로 치환되었고, 괄호가 `()` 이렇게 붙어있는 경우에는 레이저로 막대기를 자르는 역할을 한다. 문제상에 레이저는 인접한 쌍 `()` 으로 표현되고, 모두 레이저라고 명시 해 두었다. 그래서, 먼저 입력받은 string 의 `replace` 메소드를 사용해서, 정규표현식으로 `()` 을 레이저를 의미하는 `R`로 바꾸었다. 

그리고, 주어진 문자열에 대해서, 배열로 쪼갠후에 forEach 문으로 각각의 요소에 대해서 주어진 사항을 처리하도록 코드를 구현했다. 
- `(` 는 막대가 시작된다는 것이고, stack 에 push 를 해준다.
- `)` 는 막대의 끝이기 때문에 각 막대의 쪼개진 개수가 담긴 stack 의 상위 요소를 pop 해서 결과값에 더해준다.
- `R` 을 만나면, 스택에 쌓인 막대를 쪼개주기 위해서 stack 의 모든 요소에 +1 을 해준다.

## 구현
```javascript
function solution(arrangement) {
  let string = arrangement;
  string = string.replace(/\(\)/gi, 'R'); // 정규표현식 () => Razor 로 바꾸기
  
  let stack = [];
  let result = 0;
  [...string].forEach((value) => {
    if(value === '(') stack.push(1); // 실선 시작
    if(value === ')') result += stack.pop(); // 실선 끝, 결과값에 더해주기
    if(value === 'R') stack = stack.map(each=> each+1); // 스택에 있는 실선 잘라서 막대의 개수 더해주기
  });

  return result;
}
```
