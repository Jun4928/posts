# LeetCode 3. Longest Substring Without Repeating Characters
## Brute Force vs. Sliding Window using Set and Map

[문제링크](https://leetcode.com/problems/longest-substring-without-repeating-characters/solution/)

# 문제분석
> 문자열이 주어질 때, 반복되지 않는 문자들로만 이루어진 substring 중에 가장 긴 것의 길이를 리턴하기. 예를들어 "abcabccbb" 가 인풋으로 들어오면 "abc" 가 중복되지 않은 문자인 a, b, c 로만 이루어져있고, 가장 길기 때문에 3을 리턴하면 된다. 같은 맥락에서 "pwwkew" 가 인풋으로 들어오면 "wke" 가 중복되지 않은 문자로만 이루어진 가장 긴 substring 이기 때문에 3을 리턴한다. (여기서 pwke가 답이 될 수 없는 이유는 substring 이 아니라 subsequence 이기 때문이다.)

## substrings vs. subsequences
`abc` 에 대한 substrings와 subsequences를 구하면 다음과 같다.
- substring: `a`, `ab`, `abc`, `b`, `bc`, `c`, empty substring 즉, 연속되어 있는 것들만 묶일 수 있고
- subsequences: `a`, `b`, `ab`, `c`, `ac`, `bc`, `abc`, empty subsequence, 떨어져있는 문자들도 하나로 합쳐져서 subsquence 가 될 수 있다.

# Brute Force (시행착오)
```javascript
// Brute Force
const getPossibleWords = function (str) { // 모든 substring 을 구하는 함수
	const results = [];

	const words = function (chunkSize) {
		if (str.length === chunkSize) {
			results.push(str);
			return;
		}
		
		for (let index = 0; index + chunkSize <= str.length; index++) {
			const word = str.slice(index, index + chunkSize);
			results.push(word);
		}

		words(chunkSize + 1);
	}

	words(1);

	return results;
}

const lengthOfLongestSubstring = (str) => {
	if (str.length === 0) return 0;
	const possibleWords = getPossibleWords(str); // 모든 substring 구하기

	const onlyUniques = possibleWords.filter((word) => {  // 중복되지 않는 문자들로만 이루어진 문자열 구하기
		const wordSet = new Set(word); 		
    return wordSet.size === word.length;
	})

	return Math.max(...onlyUniques.map((word) => word.length)); // 가장 긴 length 리턴하기
};
```

위의 방법을 이용하면 시간복잡도가 O(n^3) 까지 증가한다. 왜냐하면, 모든 가능한 substring 을 구하고, 구한 substring 에 대해서 다시 filter 함수로 중복되지 않는 문자들로만 이루어진 문자열을 거른다. 그리고 가장 긴 length 를 리턴할 때에도 map 함수를 거친다. 따라서 최악의 경우에 O(n^3) 으로 복잡도가 증가하게 된다.

LeetCode 에서 테스트 케이스는 통과하지만, 최종 submit 을 하면 과다 메모리 사용으로 인해서 시스템이 뻗는다. 아마, getPossibleWords 함수에서 게속해서 재귀 함수로 단어를 쪼개는 것 때문이라고 생각한다. 

직관적으로 이해되고 쉬운 알고리즘이긴 하나, 성능이나 메모리 사용측면에서 최악의 케이스인 알고리즘이다. 분명 개선의 여지가 있다. 

# Sliding Window (using Set)
[LeetCode Solution Reference](https://leetcode.com/problems/longest-substring-without-repeating-characters/solution/)

위의 방법에서는 모든 substring 에 대해서 중복된 문자가 있었는지를 검사했다. 하지만 꼭 이럴 필요가 있을까? 그렇지 않다. 불필요한 일의 반복이라고 볼 수 있다. 왜냐하면, 만약 다음과 같은 substring `Stirng[i, j]: i부터 j까지의 substring`  이 인덱스 i부터 j-1 까지 이미 중복된 캐릭터가 없는 것을 검사했다면, `String[j]` 만 이미 `Stinrg[i, j]` 에 있는지 확인하면 된다. 

하나의 문자가 substring 안에 포함되어 있는지 아닌지를 확인하기 위해서는 substring 을 스캔하면된다. 하지만, 이것 또한 O(n^2)의 복잡도를 가지는 알고리즘이다. 더 나아질 수 있다.

바로 **HashSet** 을 sliding window로 사용하는 것이다. Hash를 이용하면 O(1)의 복잡도로 확인할 수 있다.

**Sliding Window** 란 추상적인 컨셉을 뜻한다. 주로 배열과 문자열 문제를 다루는데 사용된다. Window는 `[i, j) (닫힌 왼쪽, 열린 오른쪽)` 시작과 끝을 나타내는 인덱스들에 의해 정의된 요소들의 범위를 뜻한다. **Sliding Window**는 말 그대로 창이 슬라이딩하는 것과 같다. Window 의 경계안에서 한쪽의 방향으로 움직이는 것을 의미한다. 예를들어서, `[i, j)` 의 윈도우가 오른쪽으로 한칸 이동하면, `[i + 1, j + 1)` 이 된다. 

다시금 문제로 돌아가서, **HashSet**  을 사용해서 현재 window `[i, j) (j === i initially`의 캐릭터들을 저장한다. 그러고 나서 window를 j를 오른쪽으로 한 칸 이동한다. 만약 HashSet에 있지 않다면, j를 더 이동시킨다. `String[j]` 가 HashSet 안에 있을 때까지 이동시킨다. 이 때, 가장 긴 substring 을 찾은 것이다. 모든 i에 대해서 이 방법을 적용하면, 답을 구할 수 있다. 

```javascript
const lengthOfLongestSubstring = (str) => {
	const lengthOfStr = str.length;
	let hashSet = new Set();
	let longest = 0; // to update the longest substring length
	let i = 0; // sliding window indicies 
	let j = 0;

	while (i < lengthOfStr && j < lengthOfStr) {
		if (!hashSet.has(str[j])) { // hashSet에 str[j] 가 없을 때 창을 확장시킨다.
			hashSet.add(str[j++]); // 다음것을 set에 집어넣는다.
			longest = Math.max(longest, j - i); // update the longest substring length
		} else { // hashSet 에 str[j] 가 있을 때, (중복된 캐릭터가 있다는 뜻 => 다음 substring 을 찾기 위해서 slide 이동)
			hashSet.delete(str[i++]); // slide 를 이동시킨다. 이동하면서 set에서 제거해야 현재 i위치에서 윈도우를 확장시킬 수 있다. 
		}
	}

	console.log(hashSet);

	return longest;
}
```

이 답안의 시간복잡도는 O(2n) = O(n) 이 된다. 최악의 경우에 모든 캐릭터는 i와 j에 의해서 두번씩 방문되기 때문이다. 

# Optimized Sliding Window (using Map)
위에서는 Set을 사용해서 창을 이동시키면서 현재 문자가 중복되는지 아닌지를 검사했다면, 이번엔 Map을 사용한다. Map을 사용하면 오직 O(n) 으로 j만 움직이면서 검사할 수 있다. Set을 사용할 때, 현재 윈도우의 검사하는 문자가 중복되는지 아닌지를 검사했다. 하지만 Map을 사용하면 캐릭터에 해당하는 index를 저장할 수 있다. 그러므로 중복되는 문자를 찾았을 때 바로 그 문자들을 검사할 필요가 없게 된다. 

조금 더 자세히 설명하면, `String[j]` 가 범위 `[i, j)` 사이에서 `j'`와 중복을 가진다면, i를 하나씩 증가시킬 필요가 없다. `[i, j']` 사이에 있는 모든 요소에 대해서 검사를 할필요가 없고, i를 바로 `j' + 1` 로 이동시키면 된다. 

```javascript
const lengthOfLongestSubstring = (str) => {
	const lengthOfStr = str.length;
	let hashMap = new Map();
	let longest = 0;
	let i = 0;
	let j = 0;

	for (j; j < lengthOfStr; j++) { // j 만 배열을 순회 하므로 O(n) 알고리즘
		if (hashMap.has(str[j])) { // j 가 맵에 있다면 현재 윈도우에 대한 검사를 스킵한다.
			i = Math.max(hashMap.get(str[j]), i); // starting 포인트를 재설정한다.
}

		longest = Math.max(longest, j - i + 1);  // 최대길이를 업데이트한다. 
		hashMap.set(str[j], j + 1); // Map에 해당 캐릭터와 함께 그 다음 인덱스를 저장한다. (starting 포인트 i를 이동시키기 위해서)
	}

	return longest;
}
```

