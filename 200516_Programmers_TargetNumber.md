# Programmers Level2 타겟넘버 
## 재귀, 이진트리, 깊이우선탐색

[문제링크](https://programmers.co.kr/learn/courses/30/lessons/43165)

## 문제분석 
> 함수의 인자로 배열과, 타겟넘버가 들어온다. 배열에는 수가 들어있고, 이 수를 더하고 빼기를 조합해서 타겟넘버를 만드는 것이 목표이다. 

예를들어 `[1,1,1,1,1], 3` 이 인풋으로 들어오면,
```
-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3
```
이렇게 총 5가지의 조합을 구성해서 5를 리턴해야 한다.

## 돌파구
나는 일단, 조합을 만들어내고, 각각 합을 구하는 방식으로 접근했다. 이 알고리즘에는 속도와 메모리 사용량에 치명적인 단점이 있다. => *보완해야 한다.* 

하지만, 처음으로 이진트리와 Depth Frist Search(깊이 우선 탐색) 알고리즘을 실제 문제에 활용해서 풀었다는 것에 의미가 있어서 기록 해 놓는다. 

위의 예를 통해 설명을 이어나가면, 결국 배열의 요소 즉 숫자마다 +, - 가 붙을 수 있고, 이렇게 해서 총 발생가능한 조합의 경우의 수는 2^5(다섯제곱) = 32 이다. 32개의 조합을 만들고, 케이스 마다 타겟넘버가 만들어지는지를 검사하면된다. 

그래서, 나는 32개의 조합을 이진트리에 담았고, DFS 알고리즘을 사용해서 모든 경우에 대해 합을 구했다. 
*(트리를 만드는것도, DFS알고리즘을 통해 합을 구하는 것도 계속해서 재귀함수를 호출하기 때문에 많은 양의 메모리가 사용된다.)*

그리고 마지막으로 합에 대해서 filter 함수를 거쳐서 target number 와 같은 배열을 만들고 length 를 리턴하면, 우리가 원하는 타겟넘버가 되는 조합의 수를 구할 수 있다. 

## 구현
- `class TreeNode` : 이진트리의 노드가 될 클래스
- `makeTree function` : 배열을 인자로 받아서, 왼쪽 오른쪽 트리를 생성해줌. (왼쪽: +, 오른쪽: -) 
- `sum function` : 트리를 받아서, 모든 케이스의 합을 배열로 리턴하는 함수

```javascript
class TreeNode {
  constructor(value) {
    this.val = value;
    this.left = null;
    this.right = null;
  }
}

const solution = function targetNumber(numbers, target) {

  // 조합 가능한 경우의 수를 만드는 핵심이 되는 함수
  const makeTree = function dfs (arr) { // 각 층(level)의 너비(width) 는 1, 2^1, 2^2, ... 2^arr.length 가 된다. 
    if(arr.length === 0) return [null, null]; // null, null
    const current = arr.shift(); // 값 하나에 +, - 두개의 노드를 만든다.
    const pnode = new TreeNode(current); // plus node
    const mnode = new TreeNode(-current); // minus node
    const [pchild, mchild] = makeTree(arr);  // recursive call
    pnode.left = pchild; // left => plus 
    pnode.right = mchild; // right => minus
    mnode.left = pchild; // left => plus
    mnode.right = mchild; // right => minus
    return [pnode, mnode]; // plus node, minus node return
  }

  const sum = function (node) { // 트리를 받아서, 하나의 줄기(=발생가능한 조합) 별로 모든값을 더해서 각 배열에 담아 리턴하는 함수 
    if(!node.left && !node.right) return [node.val]; // 최하단 만났을 때, 그 노드의 value return
    const left = sum(node.left).map(val => val + node.val); // 왼쪽에서 넘겨받은 배열에 map 함수로 현재 노드 값을 더한다.
    const right = sum(node.right).map(val => val + node.val); // 오른쪽에서 넘겨받은 배열에 map 함수로 현재 노드 값을 더한다.
    return [...left, ...right]; // spread syntax 로 하나의 배열로 만들어서 return
  }
 
  const [left, right] = makeTree(numbers); // 왼쪽, 오른쪽 두개의 트리가 만들어진다.
  const leftSum = sum(left); // 왼쪽 트리에 대한 sum
  const rightSum = sum(right); // 오른쪽 트리에 대한 sum
  return [...leftSum, ...rightSum].filter(sum=> sum === target ? true : false).length;

  // const root = new TreeNode(0); 루트를 생성하는 방법인데, 시간이나 메모리 면에서 좋지않다.
  // root.left = left;
  // root.right = right;
  // return sum(root).filter(sum=> sum === target ? true:false).length;
}

console.log(solution([1,1,1,1,1], 3));
// console.log(solution([1,2,3,4,5], 3));
// console.log(solution([1,2,3], 3));
```