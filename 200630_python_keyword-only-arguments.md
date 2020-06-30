# Python Keyword-Only Arguments Syntax

**영문으로 작성된 [Python Keyword-Only Arguments Syntax: getKT](https://getkt.com/blog/python-keyword-only-arguments/)의 글의 번역과 함께 추가 예제를 수록 해 놓았습니다.**

# 이전 python 버전의 한계
Variable Length Positional Arguments(가변위치인수) `*varargs`를 사용해서 들어오는 순서가 중요한 Positional Arguments(위치인수)의 어떤 수든 다 받아낼 수 있었다. 

또한 같은 맥락에서, Variable Length Keyword Arguments(가변키워드인수)문법을 사용해서 `**kwargs` Keyword Arguments(키워드인수) 가 몇개가 되었던 다 받아낼 수 있다.

```python
def add(a, b, *varargs, **kwargs):
  print a + b
  print varargs
  print kwargs

add(1,2,3,4,5, name='jun', language='python')
```

다음과 같이 출력된다. 즉, 앞에 있는 위치인수를 제외한 나머지 위치인수들 `3, 4, 5`는 가변위치인수인 `varargs`에, 그리고 그 뒤의 키워드인수들 `name='jun', language='python`은 가변키워드인수인 `kwargs`에 담긴다. 자료형을 보면 `varargs` 는 **튜플**, `kwargs` 는 **딕셔너리** 자료형에 담기게 된다. 

```
3
(3, 4, 5)
{'name': 'jun', 'language': 'python'}
```

이 현존하는 문법의 한계점을 짚어보마젼, 가변위치인수, 가변키워드인수인 `*varargs`와 `**kwargs` 외에는 어떠한 키워드 인수들을 받지 못한다.

오직 위치인수 또는 키워드인수로만 선택해서 함수에 값을 넘겨줄 수 있다.

한가지 이 문법에 대한 큰 제한사항은 일반적인 인수들이 항상 가변위치인수와 가변키워드 인수 앞에 와야한다.

# Keyword-Only Arguments 
이 기능은 함수를 선언하는 방식에 변화를 준다. 함수가 실행되는 순간에 인자를 함수에 넘겨줄 때 오직 키워드인수를 사용해서 함수에 넘겨주도록 한다. 그렇지 않으면 에러를 발생시킨다. 
*이 Keyword-Only Arguments는 위치인수에 의해서 자동으로 채워지지 않는다.*

이 문법이 왜 유용한지 살펴보면
- 가독성을 높여준다.
- API 함수들이 함수의 모호함을 피하기 위해서 Keyword-Only Arguments 로만 중요한 인자들을 받을 수 있다.
- Keyword-Only Arguments로만 인수들을 받을 수 있도록 함수를 만들 수 있다.
- 함수가 엄청나게 많은 양의 인수들을 받는 때와 같은 `selected case scenario` 에서 유용하게 사용된다.

오직 두 종류의 Keyword-Only Arguments 가 있다.
1. Non-Default Keyword-Only Arguments
2. Default Keyword-Only Arguments

# Non-Default Keyword-Only Arguments
함수 선언식에서 default 값이 없는 keyword-Only Arguments를 의미한다. 이 인수들로 함수가 선언된다면 함수는 상응하는 키워드와 함께 호출되어야 한다. 그렇지 않으면 에러를 일으킨다.

```python
def add_non_default_keyword_only_arguments(a, b, *, caller):
  print(a + b)
  print(caller)
 
add_non_default_keyword_only_arguments(2, 3)
```

함수를 선언할 때 Non-Default Keyword-Only Arguments를 사용했지만, 호출할 때 인자를 넘겨주지 않아서 아래와 같은 아래가 뜬다. 

```
Traceback (most recent call last):
  File "add.py", line 38, in <module>
    add_non_default_keyword_only_arguments(2, 3)
TypeError: add_non_default_keyword_only_arguments() missing 1 required keyword-only argument: 'caller'
```

# Default keyword-Only Arguments
이름에서도 알 수 있듯이, 이 인수들은 기본값과 함께 함수가 정의된다. 이 인수들로 선언된 함수는 함수를 실행시킬 때 상응하는 키워드가 호출되지 않아도 에러를 일으키지 않는다.

```python
def add_default_keyword_only_arguments(a, b, *, caller="jun"):
  print(a + b)
  print(caller)
 
add_default_keyword_only_arguments(2, 3)

# 실행결과
# 5
# jun
```

바로 위의 Non-Default Keyword-Only Arguments 에서 선언하고 호출한 함수와 달리 에러를 일으키지 않는다.

# Keyword-Only Arguments Syntax
이러한 문법의 변화는 꽤나 간단하다. 가변위치인수인 `*varargs` 뒤에 키워드인수를 정의할 수 있게 해준다.

```python
def add_keyword_only_arguments(*numbers, caller="jun"):
  print(numbers)

  sum_result = 0
  for value in numbers:
    sum_result += value

  print(sum_result)
  print(caller)
```

다음과 같은 출력 결과를 갖는다.

```
(3, 4, 5, 6, 7, 8)
33
jun choi
```

`caller="jun"` 는 가변위치인수 뒤에 선언이 되어있으므로 Keyword-Only Arguments 가 된다.

위에서 선언한 함수에 대해서 일반적인 위치변수들과 혼합해서 사용한다면 다음과 같이 사용할 수 있다.

```python
def add_mixed_with_keyword_only_arguments(a, b, *numbers, caller="jun"):
  print(a, b)
  print(numbers)
  sum_result = a + b
  for value in numbers:
  sum_result += value

  print(sum_result)
  print(caller)
```

다음과 같은 출력 결과를 갖는다. 위에서 선언한 `add_keyword_only_arguments` 함수의 가변위치인수 앞에 일반적인 위치인수 선언이 추가 된 것 뿐이다.

```
1 2
(3, 4, 5, 6, 7, 8)
36 
jun choi
```

마지막으로 함수선언식의 다양한 인수들을 종합 해보면 다음과 같다.

```python
def add_all_mixed(a, b, *numbers, caller, print_option=True, **kwargs):
  sum_result = a + b
  for value in numbers:
  sum_result += value
  if print_option:
  print(a, b)
  print(numbers)
  print(sum_result)
  print(caller)
  print(kwargs)
 
  add_all_mixed(1,2,3,4,5,6,7,8, caller="jun choi", lastname="jun", firstname="choi")
  add_all_mixed(1,2,3,4,5,6,7,8, caller="jun choi", lastname="jun", firstname="choi", print_option=False)
```

다음과 같은 출력 결과를 갖는다.

```python
add_all_mixed(1,2,3,4,5,6,7,8, caller="jun choi", lastname="jun", firstname="choi") 실행결과
1 2 # a, b: Regular Positional Arguments
(3, 4, 5, 6, 7, 8) # Variable Length Positional Arguments (*varargs)
36
jun choi # Keyword-only
{'lastname': 'jun', 'firstname': 'choi'} # Variable Length Keyword Arguments (*kwargs)

add_all_mixed(1,2,3,4,5,6,7,8, caller="jun choi", lastname="jun", firstname="choi", print_option=False) # print_option Keyword-Only Argument 에 False값을 입력하므로 제어문에 의해 모든 print 함수가 호출되지 않는다.
```

# Keyword-Only Arguments Syntax Error 사례 
## 1. Non-Default Keyword-Only Argument 에러나는 경우

```python
def func_param_with_var_args(name, *args, age):
    print("name=",end=""), print(name)
    print("args=",end=""), print(args)
    print("age=",end=""), print(age)

func_param_with_var_args("정우성", "01012341234", "seoul", 20) # 에러발생
func_param_with_var_args("정우성", "01012341234", "seoul", age=20) # 에러해결
```
```
출력결과
name=정우성
args=('01012341234', 'seoul')
age=20
```
`*args` 뒤에 선언된 `age` 인자는 `Non-Default Keword-Only Argument` 인데 함수를 호출할 때 인자를 넘겨주지 않아서 에러가 난다. 이를 수정하려면 바로 아랫줄 처럼 `Non-Default Keyword-Only Argument`인 `age` 에 값을 할당해서 함수를 호출하면 된다.

## 2. Keyword-Only Arguments를 올바른 위치에 배치하자
![python arguments order 출처: https://getkt.com/blog/python-keyword-only-arguments/](https://getkt.com/wp-content/uploads/2019/02/python-function-definition-arguments-kind-and-order.jpg)

위의 이미지를 참고하면 함수를 선언할 때 `Keyword-Only Arguments` 의 위치가 올바르게 배치되어야 하는 것을 볼 수 있다. `Keyword-Only Arguments` 의 위치는 * 하나로 표시하는 `Varialbe Length Positional Arguments(가변위치인자)` 와 ** 이렇게 두개로 표시하는 `Variable Length Keyword Arguments` 사이에 와야한다. 그렇지 않으면 에러를 일으킨다.

다음과 같은 경우에 `**kwargs` 는 함수를 선언할 때 가장 마지막에 오고, `Keyword-Only Argument` 인 `address=0`이 그 앞에 선언되어야 하는데 자리가 바뀌었기 때문에 에러가 난다.
둘의 위치를 바꿔주면 자신이 가야할 올바른 위치에 배치되므로 에러가 발생하지 않는다.

```python
#def func_param_with_kwargs(name, age, **kwargs, address=0): # 에러발생
def func_param_with_kwargs(name, age, address=0, **kwargs): # 에러해결 
    print("name=",end=""), print(name)
    print("age=",end=""), print(age)
    print("kwargs=",end=""), print(kwargs)
    print("address=",end=""), print(address)

func_param_with_kwargs("정우성", "20", mobile="01012341234", address="seoul")
```
```
출력결과
name=정우성
age=20
kwargs={'mobile': '01012341234'}
address=seoul
```

## 3. 위의 두가지 에러가 혼합된 경우

```python
def mixed_params(name="아이유", *args, age, **kwargs, address): # Keyword-Only Argument인 address가 올바른 위치를 찾아가지 못 함.
    print("name=",end=""), print(name)
    print("args=",end=""), print(args)
    print("age=",end=""), print(age)
    print("kwargs=",end=""), print(kwargs)
    print("address=",end=""), print(address)


mixed_params(20, "정우성", "01012341234", "male" ,mobile="01012341234", address="seoul") # Non-Default Keyword-Only Argument인 age가 함수를 호출할 때 할당되지 않음.
```

위의 두 가지 에러:

1. 함수를 호출할 때, Non-Default Keyword-Only Argument 의 값을 할당해서 호출해야한다.
2. Keyword-Only Arguments 를 함수 선언식에서 올바르게 위치 시켜야한다.

위 두가지 에러를 수정한 코드는 다음과 같다.

```python
def mixed_params(name="아이유", *args, age, address, **kwargs):
    print("name=",end=""), print(name)
    print("args=",end=""), print(args)
    print("age=",end=""), print(age)
    print("kwargs=",end=""), print(kwargs)
    print("address=",end=""), print(address)


mixed_params(20, "정우성", "01012341234", "male" ,mobile="01012341234", address="seoul", age="2")
```
```
출력결과
name=20
args=('정우성', '01012341234', 'male')
age=2
kwargs={'mobile': '01012341234'}
address=seoul
```