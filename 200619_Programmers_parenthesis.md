# Programmers Level2 괄호변환
## 재귀, 문자열 처리

# 돌파구
여느 다른 카카오 코딩 테스트 문제들 처럼, 문제를 정확히 파악하고 요구사항에 따라 코드로 구현해 내는 세심함이 필요한 문제다. 사실 이 문제에서는 들어온 괄호쌍이 균형잡혀있는지, 올바른 괄호 문자열인지를 판별하는 함수만 구현 해 내면, 나머지는 문제에 주어진 대로 재귀함수를 구현하면 된다. 이 때, 문자열처리, 배열을 다루는 스킬이 요구된다. 

## 1. 문제에서 주어진 대로, 입력된 괄호문자열에 대해 u, v로 나누기 
함수를 구현하든, 재귀함수를 구현하든 입력된 인자에 대해서 데이터의 불변성을 유지하는 것이 좋다. 후에 발생할 추적이 되지 않는 오류를 막기 위함이다. 그래서 요즘 코드를 작성 할 때에도 함수로 들어오는 인자를 변형하지 않으려고 노력하고 있다. 

그래서, 이번 문제에서도 입력받은 문자열 p에 대해서 u, v로 분리하기 위해서 JavaScript의 spread syntax 를 사용했다. 
```javascript
const [first, ...v] = [...p] // 들어온 문자열 p를 배열로 만들고, 배열 destructuring
const u = [first]; // 위에서 얻어진 첫번째 값은 배열의 맨 앞에 있는 값, 
// v는 index1번부터 끝까지의 배열이 담겨있음 
```
이렇게 u와 v를 나눈후에 while문을 사용해서 균형잡힌 괄호 문자열이 확인되는 지점에서 break를 걸어서 u와 v를 분리했다.

## 2. 균형잡힌 괄호 문자열인지 확인하는 함수 구현하기
```
'(' 와 ')' 로만 이루어진 문자열이 있을 경우, '(' 의 개수와 ')' 의 개수가 같다면 이를 균형잡힌 괄호 문자열이라고 부릅니다.
```
나는 isBalanced 라는 함수로 따로 구현했다. 문제상에서 분리된 u배열이 인자로 들어오면, `(` 와 `)`의 개수를 각각 세서 개수가 같은지를 비교했다. 
이 때, map 함수를 사용해서 true, false를 리턴하게 한 다음, `filter(Boolean)` 으로 false 를 걸러서 개수를 확인했다. 

## 3. 올바른 괄호 문자열인지 확인하는 함수 구현하기
```
'('와 ')'의 괄호의 짝도 모두 맞을 경우에는 이를 올바른 괄호 문자열이라고 부릅니다.
```
짝이 맞는 괄호 문자열을 판별하는 문제는 `스택(Stack)`자료구조를 사용한 대표적인 문제다. 
**괄호는 열리면, 닫혀야 한다.** 라는 성질 때문에, 열릴때에는 스택에 집어넣고, 닫힐 때에는 스택에서 뺀다. 이렇게 모든 괄호문자열에 대해서 반복문을 돌리고 난 후에도 스택의 길이가 0이라면, 올바른 괄호 문자열이 되는 것이다. 

## 4. 재귀함수 구현하기
이 부분은 어렵게 생각 할 필요없고, 문제에 주어진 요구사항대로 코드를 구현하기만 하면 된다. 

# 구현
```javascript
const isBlanced = function (u) { // u: array[]
    const left = u.map((element) => element === "(").filter(Boolean); // boolean false 거른다 (의 개수 만큼 true 남음
    const right = u.map((element) => element === ")").filter(Boolean);  // boolean false 거른다 ) 개수 만큼 true 남음

    return left.length === right.length; // length 같으면 균형잡힌 괄호 문자열
}

const isRight = function (u) { // u: array[]
  const stack = [];
  u.forEach((element) => {
    if (element === "(") stack.push(element);
    if (element === ")") stack.pop();
  });

  return stack.length === 0; // 괄호가 짝을 이루어서 stack 의 length가 0이면 올바른 괄호 문자열
}

const solution = function (p) {
  if (p.length === 0) return ""; // 재귀 엔딩 조건 빈 배열일 때 빈 문자열 리턴

  const [first, ...v]= [...p]; // u, v 분리하기, input으로 들어온 p 문자열은 불변
  const u = [first]; 
  while (v.length !== 0) {
    u.push(v.shift());
    if (u.length % 2 === 0 && isBlanced(u)) break; // 균형 잡힌 곳에서 종료
  }

  if (isRight(u)) { // u가 올바른 괄호 문자열 일때
    const rightParen = solution(v.join('')); // 나머지 'v' 에 대해서 재귀
    return u.join('') + rightParen; 
  }

  if (!isRight(u)) { // u가 올바른 괄호 문자열이 아닐 때, 문제의 요구사항에 따라서 코딩 
    let result = "(";
    const rightParen = solution(v.join('')); 
    result = result + rightParen + ")";

    const changedU = u.filter((element, index, arr) => { // 처음과 끝 제거 데이터의 불변성을 위해서 filter method 사용
      if (index === 0 || index === arr.length - 1) return false;
      return true;
    }).map((element) => { // 함수 chaining 으로 바로 ) => (,  ( => ) 로 뒤집는다.
      if (element === "(") return ")";
      if (element === ")") return "(";
    });

    result = result + changedU.join('');
    return result;
  }
}

const result1 = solution("(()())()");
const expected1 = "(()())()";
console.log(result1);

const result2 = solution(")(");
const expected2 = "()";
console.log(result2);

const result3 = solution("()))((()");
const expected3 = "()(())()";
console.log(result3);
```