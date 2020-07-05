# JavaScript Generic Memoization 구현하기
## Dynamic Programming(동적프로그래밍)

- [Reference1: Implementing Memoization in JavaScript by Arielle Gordon](https://medium.com/@reallygordon/implementing-memoization-in-javascript-5d140bb04166)
- [Reference2: Udemy JavaScript Alogirhtm Course](https://www.udemy.com/course/coding-interview-bootcamp-algorithms-and-data-structure/)
- [Reference3: Writing a generic memoize function in JavaScript](https://dsinecos.github.io/blog/How-to-write-a-generic-Memoize-function-in-JavaScript)

동적프로그래밍이 뭔지를 알아보려 했더니 처음보는 영어단어가 나를 가로 막는다. memoization??? 도대체 이 단어는 뭔가. 이 글이 동적프로그래밍과 memoization 을 검색하며 혼란에 빠진 사람들을 돕는 글이 되었으면 좋겠다. 

# Dynamic Programming이 필요한 이유
Dynamic Programming(동적 프로그래밍) 이라고 하면 겁부터 난다. 그냥 프로그래밍도 어려운데, 동적으로 프로그래밍을 하라고? 

사실은 말이 어렵지 개념은 간단하다. 우리가 프로그래밍을 하다보면, 같은 값을 내는 함수를 여러번 호출하게 되는 경우가 생긴다. 이 글에서는 가장 대표적인 재귀함수인 피보나치 함수로 예를 들겠다. 동적 프로그래밍이란 함수의 결과를 저장해서(caching) 같은 함수가 불릴 때 다시 실행시키지 않고 저장된 결과값을 return 하는 것을 말한다.

피보나치로 예를 들어보자.

```javascript
let fibonacci = function(number) {
  if (number === 0) return 0;
  if (number === 1 || number === 2) return 1;

  return fibonacci(number - 1) + fibonacci(number - 2);
}
```

이 함수는 재귀함수를 배울 때 가장 먼저 배우게 되는 함수다. 피보나치 수는 첫째 및 둘째 항이 1이며, 그 뒤의 모든 항은 바로 앞 두항의 합인 수열이다. 예를들면 다음과 같다.

```javascript
fibonaccis = [0, 1, 1, 2, 3, 5, 8, ...]
Fibonacci(0) = 0;
Fibonacci(1) = 1;
Fibonacci(2) = 1;
Fibonacci(3) = 2;
Fibonacci(4) = 3;
Fibonacci(5) = 5;
Fibonacci(6) = 8;
.
.
.

```

`F(n) = F(n-1) + F(n-2)`
즉, 해당 값은 그 전의 두 값의 합이 된다. 이 피보나치 수열은 바로 앞의 숫자를 뒤의 숫자로 나누면 값이 1.618에 수렴한다고 한다. 바로 `황금비` 다. 자연의 섭리에도, 미술의 세계에도, 마케팅의 세계에도 적용되는 황금비는 바로 이 피보나치 수열에 담겨있다.

아름다운 수열이지만, 컴퓨팅을 할 때에는 애를 먹게 된다. 왜냐하면, 해당번째의 피보나치 수열의 값을 구하기 위해서는, 이전의 모든 값들을 다 구해야 하기 때문이다. 

위에서 구현한 함수로 피보나치 수열의 5번째 값만을 구하려고 하더라도,,
```javascript
Fibonacci(5) => Fibonacci(4) + Fibonnaci(3) => Fibonacci(3) + Fibonacci(2) + Fibonacci(2) + Fibonacci(1) ...
Fibonacci(4) => Fibonacci(3) + Fibonacci(2) => Fibonacci(2) + Fibonacci(1) + Fibonacci(2) ... 
Fibonacci(3) => Fibonacci(2) + Fibonacci(1) ... 
Fibonacci(2) => return 1;
Fibonacci(1) => return 1;
Fibonacci(0) => return 0;
```

위와 같이 계속해서 재귀함수가 호출이 된다. 한자리 수여서 망정이지 만약 100번쨰, 1000번째, 10000번째 피보나치 수열의 값을 구하고 싶다면..?

수없이 많이 같은 함수가 반복되어서 쓸데없는 자원만 들게 된다. 최악의 성능 저하를 겪게 된다. 이 처럼 피보나치 함수를 구현하면 시간, 공간 복잡도가 O(2^n) 이 된다. 한마디로 닶이 없다는 이야기.

# Memoization (= Dynamic Programming)
이 때, 바로 **Dynamic Programming** 이 필요한 시점이다. 위에서 동적 프로그래밍 이라고 함은, 함수의 결과를 저장(caching) 하는 것이라고 했다. 

특별히 함수에 대해 같은 인자가 들어 올 때, 함수의 결과를 저장해 놓는 것을 **memoization** 이라고 부른다. memoization은 기억된다. 라는 라틴어 동사 *memorandum* 에서 유래되었다고 한다. 영국의 인공지능 개척자 Donald Michie 에 의해서 발명되었다.

**memoization**은 hash table 같은 자료구조에 이전에 불린 함수들의 결과를 저장해 놓는 기술을 의미한다. 그래서 똑같은 함수가 불린다면 이 hash table의 value 값을 리턴해 버리면 함수를 실행시킬 필요가 없다. 이 memoization의 힘은 피보나치와 같은 재귀함수에서 큰 힘을 발휘한다.

위에서 피보나치 함수의 인자로 5가 들어간다고 해도, 계속 쪼개져서 수 많은 함수의 호출을 야기시키는 것을 확인했다.

이제 **memoization** 함수로 우리의 노답인 피보나치 함수를 업그레이드 시켜보자. 

아이디어는 간단한다. 함수의 상태를 caching 하면 된다. JavaScript 에서 caching은? => object 자료구조 사용하면 됨.

정리하자면, **memoization** 기법을 사용해서 함수를 캐싱하면? 바로 동적 프로그래밍을 구현하는 셈이 된다.

# 구현
JavaScript 가 제공하는 클로저 기능을 사용하자. 
## outer Function = memoization
- memoization 함수는 함수를 인자로 받는다. 
- JavaScript의 클로저 기능 때문에 memoization 함수가 불려지면, cache(object)를 공유하게 된다.
- memization은 함수를 받아서 함수 자체를 return 한다.

## inner function = arrow function
- arrow functino으로 return 되는 함수를 구현한다. 이 때 memoization 이 범용적으로 사용될 수 있도록 어떤 인자든 받을 수 있게 spread syntax로 인자를 받는다. 이 때 인자로 들어온 args는 배열의 형태이다.
- cache(object)에 `cache[args]` 로 접근해서 있다면, 이미 이 인자로 함수가 불린 것 이므로 결과를 리턴한다.
- 없다면 인자로 들어온 함수를 인자와 함께 호출하고 `func(...args)` 결과값을 cache(oject)에 캐싱하고 리턴한다. 다시 spread syntax로 함수를 호출하는 이유는, args가 배열의 상태이기 때문이다. spread syntax를 사용해서 함수를 호출하면, 함수의 인자에 배열의 순서대로 들어가게 된다.

```javascript
let fibonacci = function(number) {
	if (number === 0) return 0;
	if (number === 1 || number === 2) return 1;

	return fibonacci(number - 1) + fibonacci(number - 2);
}

const memoization = (func) => { // input parameter: function
	const cache = {}; // empty hash map
	return (...args) => { // inner function, spread syntax for generic use
		if (cache[args]) {
			console.log(`${args} already called, return from cache`);
			return cache[args];
		}

		console.log(`${args} never called, execute and cache`);
		cache[args] = func(...args); // put result into cache

		return cache[args];
	}
}


fibonacci = memoization(fibonacci); // wrap fibonacci function with memoization
console.log(fibonacci(15));

// 실행결과 
// 15 never called, execute and cache
// 14 never called, execute and cache
// 13 never called, execute and cache
// 12 never called, execute and cache
// 11 never called, execute and cache
// 10 never called, execute and cache
// 9 never called, execute and cache
// 8 never called, execute and cache
// 7 never called, execute and cache
// 6 never called, execute and cache
// 5 never called, execute and cache
// 4 never called, execute and cache
// 3 never called, execute and cache
// 2 never called, execute and cache
// 1 never called, execute and cache
// 2 already called, return from cache
// 3 already called, return from cache
// 4 already called, return from cache
// 5 already called, return from cache
// 6 already called, return from cache
// 7 already called, return from cache
// 8 already called, return from cache
// 9 already called, return from cache
// 10 already called, return from cache
// 11 already called, return from cache
// 12 already called, return from cache
// 13 already called, return from cache
// 610
```

# 정리
memoization을 구현할 때 필수적인 사항들이 있다.

1. 위에서 피보나치와 같이, memoization 함수로 감싸질 함수는 **순수(pure)**함수여야 한다. 왜냐하면 캐싱을 하기 때문이다. 같은 인풋이 들어오면, 항상 같은 결과값이 나와야 한다.
2. memoization 기법은 성능을 얻는대신에 공간(메모리)를 더 많이 사용하게 된다. 캐싱해야 하는 함수의 인자의 크기와 다양성 그리고 결과에 따라 캐싱해야 하는 데이터가 언제나 변하기 때문이다.