# JavaScript로 Quick Sort(퀵 정렬) 구현하기
## JavaScript, Recursion, Divide and Conquer

# 퀵 정렬
퀵 정렬은 병합 정렬과 보통 많이 비교되곤 한다. 둘 다 평균적으로 `O(nlogn)`의 성능을 갖기 때문이다. 

## 퀵 정렬, 병합 정렬 공통점
- Divide and Conquer(분할-정복) 알고리즘에 속한다.
- 둘 다 점점 탐색할 배열의 크기를 쪼개서 재귀함수에 넘겨준다.

## 퀵 정렬, 병합 정렬 차이점
- 병합정렬은 배열을 분할하는 방식이 반으로 쪼개는 것
- 퀵 정렬은 크게 두 가지 분할 방법이 있다.
- 퀵 정렬은 병합정렬과 달리 다른 메모리 공간을 사용하지 않는다. (오직 재귀 콜 스택을 위한 메모리만 사용됨, 그에 반면 병합정렬은 매 번 새로운 배열을 만들어 내므로 메모리 사용량이 더 큼)
- 드물지만, 파티션의 방법에 따라 최악의 경우에 O(n^2) 까지 성능이 낮아질 수 있음.
- 병합정렬은 stable 하지만, 퀵 정렬은 unstable 하다. (원소들 중에 같은 값이 있는 경우에 정렬 이후 순서가 초기 순서와 달라 질 수 있기 때문에)

## 그래서 퀵 정렬은?
퀵 정렬은 분할정복 알고리즘의 대표주자다. 개념은 간단하다. pivot(중심축) 을 정하고, 중심축 보다 작은 값들은 왼쪽으로 큰 값들은 오른쪽으로 보내는 것이다. 

이렇게 pivot을 정해서 왼쪽 오른쪽으로 나누고 다시금 왼쪽 오른쪽에 대해 재귀적으로 pivot을 정해서 왼쪽 오른쪽을 나누고,, 이 과정을 반복하면 결국 정렬이 완성 된다.

이 때 분할 방법에는 2가지가 있다. 
1. Lomutos' Partition (배열의 맨 마지막 값을 pivot으로 정하는 방식)
2. Hoare's Parition (물리적으로 배열의 중간값을 pivot으로 정하는 방식)

# QuickSort with Lomuto's Partition
## Lomuto's Partition
들어온 리스트의 가장 끝에 있는 값(end)을 pivot(중심축) 으로 정한다. 그래서 인자로 들어온 start부터 이 끝에 있는 값(pivot) 전까지 pivot과 비교연산을 한다.

이 때, 두개의 포인터를 가진다. 
1. 배열을 순회할 index 포인터
2. pivotIndex 포인터 (pivot보다 값이 큰 값의 인덱스를 임시저장 해놓는 용도)

index포인터로 배열의 처음부터 pivot전까지 순회한다. 이 때, pivot값보다 값이 작다면?
- pivot보다 큰 값을 가리키고 있던 pivotIndex에 저장 되어있던 큰 값과 자리를 바꾼다. (swap함수 호출)
- pivotIndex값 증가 (자리를 바꿨으니 그 다음 값으로 넘어감)

이렇게 pivot보다 작을 때에만 pivotIndex를 증가시키면, pivotIndex는 항상 pivot보다 크거나 같은 값을 가리키고 있는다.

그렇기 때문에, 배열을 순회하는 포인터인 index가 가리키는 값이 pivot보다 작으면 swap을 통해 작은 값이 배열의 앞쪽에 배치되도록 한다.

그리고 마지막으로 이 partition 함수 안에서 중요한 코드는 
`swap(array, pivotIndex, end);` 이다. 맨 마지막에 있던 pivot 값을 중심축으로 옮기는 것이다. 이 코드가 있어야 pivot이 중심축으로 오고 왼쪽에는 pivot보다 작은값들, 오른쪽에는 pivot보다 큰 값들이 오게 된다.

그리고, pivot값이 저장된 pivotIndex를 최종적으로 리턴한다.

## QuickSort function
퀵 소트 함수에서는 위의 lomuto's partition 함수를 계속해서 재귀로 call 하는 형태가 된다. 

이 재귀함수의 엔딩조건은 start가 end보다 크거나 같을 때이다. start가 배열의 앞에서 증가하는 형태고, end가 배열의 뒤쪽에서 앞으로 감소하는 형태이기 때문에 둘이 교차하는 지점이 배열의 정렬이 끝난 것을 의미한다.

partition 함수를 호출해서 pivotIndex를 받아오고,
받아온 pivotIndex를 가지고 배열의 왼쪽과 오른쪽을 분할해서 recursive call을 한다. 이 때, 이미 pivotIndex는 고정 된 것이니까 이 값을 제외한 왼쪽과 오른쪽에 대해서 재귀함수를 호출하면 된다.

## 구현
```javascript
const swap = function (array, front, back) {
	const tmp = array[front];
	array[front] = array[back];
	array[back] = tmp;
}

const lomutoPartition = function (array, start, end) {
	const pivotValue = array[end]; // lomuto's partition 은 항상 맨끝을 pivot으로 정한다.
	let pivotIndex = start;

	for (let index = start; index < end; index++) {
		if (array[index] < pivotValue) { // pivotValue보다 작으면 
			swap(array, index, pivotIndex);	// pivotIndex 와 swap 하고
			pivotIndex += 1; // pivotIndex 증가
		}
	}

	swap(array, pivotIndex, end); // pivotValue를 중간에 오게하기 위해서
	return pivotIndex;
}

const quickSortWithLomuto = function (array, start = 0, end = array.length - 1) { // 초기값: 배열의 시작과 끝
	if (start >= end) return; // ending condition	

	let pivotIndex = lomutoPartition(array, start, end);
	quickSortWithLomuto(array, start, pivotIndex - 1); // divide and conquer: left
	quickSortWithLomuto(array, pivotIndex + 1, end); // right
	
	return array;
}


const arr = [4, -1, 0, -8, 0, 8, 91, 2, 3, 4, 98, 911, 21];
const result1 = quickSortWithLomuto(arr);
console.log(result1);
```

# Quick Sort with Hoare's partition
## Hoare's Partition
Hoare의 분할방법은 위에서 서술한 Lomuto의 분할방법과 달리 pivot(중심축) 값을 배열의 medium(중앙) 에 놓는다.

Lomuto의 분할방법에서 비교연산을 위해서 start부터 end(=pivot) 전까지 모든 값에 대해서 탐색했다면, Hoare의 분할방법은 start, end 두 포인터를 앞과 뒤에서부터 이동시킨다.

- start는 배열의 왼쪽에서 오른쪽으로 이동시키면서 pivot(중심축) 보다 크거나 같은 값의 index를 찾는다.: `while` 문 사용해서 pivot값보다 작을 때 계속 증가 시킴
- end는 배열의 오른쪽에서 왼쪽으로 이동시킴녀서 pivot(중심축) 보다 작거나 같은 값의 index를 찾는다.: `while` 문 사용해서 pivot 값보다 클 때 계속 감소 시킴

이렇게 해서 찾아진 start와 end의 index가 아직 교차되지 않았다면, 두개의 값을 swap 하고, 그 다음 탐색을 위해서 start는 +1, end는 -1을 해준다.

start와 end가 교차되지 않았다면 위의 과정을 반복한다.

그리고, start와 end가 교차되면, 교차된 지점의 start 값을 return해서 배열을 나누는 경계로 사용한다.

## QuickSort function
Hoare의 분할 방법을 이용한 퀵 소트 함수는 위의 Lomuto의 분할 방법을 이용할 때와 큰 차이점은 없다. 로직은 같지만, 하나 주의해야 할 점은 Hoare의 분할방법으로 리턴되는 값이 이번엔 pivot 값이 아닌, 경계의 시작이 되는 index를 리턴하므로, 재귀함수를 호출 할 때 borderIndex도 포함시켜 줘야 모든 배열의 요소에 대해서 정렬이 가능하다.

## 구현
```javascript
const swap = function (array, front, back) {
	const tmp = array[front];
	array[front] = array[back];
	array[back] = tmp;
}

const hoarePartition = function (array, start, end) {
	const pivotValue = array[ Math.floor((start + end) / 2) ]; // 물리적으로 중간을 pivotValue로 정한다.

	while (start <= end) { // 교차 지점이 오기 전까지
		while (array[start] < pivotValue) start = start + 1; // array의 왼쪽에서 pivotValue보다 크거나 같은 값을  찾는다.
		while (array[end] > pivotValue) end = end - 1; // array의 오른쪽에서 pivotValue보다 작거나 같은  값을 찾는다.

		if (start <= end) { // 교차 되기 전이라면
			swap(array, start, end); // pivotValue 보다 크거나 같은값과, 작거나 같은값을 swap 한다.
			start = start + 1; // 그 다음 search를 위해서 start 1증가
			end = end - 1; // end 1감소
		}
	}

	return start; // 교차되고 난후 start를 그 다음 border index로 리턴한다. 
}


const quickSortWithHoare = function (array, start = 0, end = array.length - 1) { // 초기값: 배열의 시작과 끝
	if (start >= end) return; // ending condition 

	let borderIndex = hoarePartition(array, start, end);
	quickSortWithHoare(array, start, borderIndex - 1); // border의 왼쪽
	quickSortWithHoare(array, borderIndex, end); // border의 오른쪽

	return array;
}

const result2 = quickSortWithHoare(arr);
console.log(result2)
```

# References
- [Quick Sort Wikipedia](https://en.wikipedia.org/wiki/Quicksort)
- [Coding Train Quick Sort](https://www.youtube.com/watch?v=eqo2LxRADhU)