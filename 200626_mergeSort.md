# JavaScript로 Merge Sort(병합정렬) 구현하기
## JavaScript, Recursion, Divide and Conquer

# 돌파구
병합정렬은 크게 두 가지 함수로 이루어져 있다. 
1. `function merge(left, right)` : 이미 소팅된 배열 left, right 받아서 하나로 합치는 순수한 함수 
2. `function mergeSort(arr)` : 배열을 반으로 쪼개서 merge 함수에게 left, right 인자를 넘겨주는 함수 

이 때, merge함수는 순수한 함수이고, mergeSort는 재귀로 함수를 콜한다는 것을 인지해야 한다.

## merge function
**merge 함수는 이미 정렬 된 left(배열), right(배열)를 인자로 받아서 하나로 합치는 기능** 
```javascript
results array 선언
while left, right 모두 element 남아 있을 때까지
    left[0] <= right[0] left의 맨 앞에 것 빼서 results 에 푸쉬
    left[0] > right[0] right의 맨 앞에 것 빼서 results 에 푸쉬

return [...results, ...left, ...right] 남은 것 다 배열에 넣어주기
```

## mergeSort function
**mergeSort 는 반으로 쪼개서 merge 에게 인자를 넘겨주는 기능**
```javascript
이 함수의 recursive call 종료 조건은 legnth 가 1일 때, 더 이상 쪼갤 수 없게 된다. => 정렬된 배열인 셈이다.
length >=2 이면 계속해서 반으로 쪼갠다

return merge( mergeSort(left), mergeSort(right) );
// legnth 1이 될 때 까지 쪼개다가 length === 1인 배열을 만나면
// 그제서야 merge함수가 실행된다. legnth 가 1일때 가장 아랫단계에서 sorted 된 것이므로.. 쭉쭉 스택콜을 타고 올라온다.
```

# 구현
```javascript
const merge = function (left, right) { // 정렬된 왼쪽과 오른쪽 배열을 받아서 하나로 합치는 순수한 함수
	// left, right already sorted
	const result = [];
	while (left.length !== 0 && right.length !== 0) {
		left[0] <= right[0] ? result.push(left.shift()) : result.push(right.shift());	
	}

	return [...result, ...left, ...right]; // 아래 세줄과 같은 역할을 하는 코드
    // if(left.length === 0) results.push(...right);
    // if(right.length === 0) results.push(...left);
    // return results;
}

const mergeSort = function (array) {
	// ending condition: length === 1 인 배열이 들어올 때, 정렬이 끝난 것. 
	if (array.length === 1) return array;

	// 2로 나누고 내림을 해야
	// length 가 2일 때도 안전하게 배열을 slice 할 수 있다.
	const middleIndex = Math.floor(array.length / 2); 
	const left = array.slice(0, middleIndex);
	const right = array.slice(middleIndex);

	// 재귀로 계속해서 반으로 나누면서 length 가 1이 될때까지 쪼개고, 
	// 거꾸로 올라오면서 순수한 함수인 merge에 인자로 넣어서 다시 병합되어서 최종값을 리턴한다.
	return merge(mergeSort(left), mergeSort(right));
}


const arr = [4, -1, 0, -8, 0, 8, 91, 2, 3, 4, 98, 911, 21];

const result = mergeSort(arr);
console.log(result);

```