# Nest.js behind the curtain

## 제어의 역전, 의존성 주입, 싱글톤

[Nest.js](https://docs.nestjs.com/) 는 정말 견고한 소프트웨어다. SPA 프레임워크인 Angular와 대규모의 안정적인 서비스 운용에 사용되는 Java Spring의 철학을 공유하고 있다고 알고있다. 문서화도 정말 잘되어있어서 원하는 인터페이스(RESTful, GraphQL)에 맞게 Nest를 사용하는 것은 어려운일이 아니다.

최근 채팅앱을 개발하며 Nest.js 공식문서와 각종 레퍼런스를 참고해 멋진 아키텍처를 가지는 백엔드 어플리케이션을 구현했다. 근데, 한참 신나게 개발을 하던 중에 이런 생각이 들었다. _'그래서, 내가 작성한 코드들이 어떻게 동작하는 것이지?'_ 일종의 위기감이었다. 프레임워크가 어떤 원리 또는 설계에 의해서 작동하는 것을 알지 못 하면, 누군가가 이걸 왜 쓰냐고 물었을 때 경험밖에 이야기 할게 없다. 물론 경험 자체를 나누는 것이 나쁘지 않지만, 원리를 아는 것은 경험의 깊이를 더욱 깊게 해 준다.

Nest.js 공식문서를 읽다보면 아래와 같은 용어들을 마주하게 된다.

- IoC(Inversion of Control)
- Dependency Injection, Injectable
- Singleton

Nest.js 프레임워크의 주요 핵심 개념들인데, 추상적인 개념들이라 각각의 개념들이 가지는 연관성과 예시를 통해 정리 해보려 한다. _(Controller, Service 등의 MVC 패턴에 대한 이해가 있으면 글을 읽는데 훨씬 더 도움이 됩니다.)_

## IoC

먼저 알아두어야 할 점은 프레임워크의 핵심은 IoC에 있다는 것이다. 한국말로 해석하면 **제어의 역전** 이 된다. 즉, 프레임워크에게 프로그래머 개인이 제어해야 할 영역을 맡기는 것이다. 프레임워크와 라이브러리를 나누는 기준이 되기도 하는데, 제어의 역전은 주도권이 누구에게 있느냐? 에 대한 차이다. 라이브러리는 내가 주도권을 가지고 각각의 의존성을 설계하고 능동적으로 사용한다. 반면에, 프레임워크는 내가 작성한 앱이 수동적으로 프레임워크에 의해 사용된다. (=제어권이 역전 된 것을 의미한다)

Nest.js 는 프레임워크다. 따라서 우리는 앱의 동작 방식을 Nest.js 에게 맡긴 셈이다.

## DI(Dependency Injection)

DI는 의존성 주입을 의미한다. 말 자체가 너무 어렵다. 하지만 개념은 간단하다.

- 의존성 주입은 말 그대로 의존하고 있는 로직을 해당 클래스 또는 함수에 주입하는 것을 의미한다.

아래는 [dependency injecttion wekipedia](https://en.wikipedia.org/wiki/Dependency_injection#AngularJS_example)에 실려있는 5살에게 DI 설명하기에 대한 내용이다.

> 너가 냉장고에서 스스로 무언가를 꺼낼 때, 문제가 될 수 있단다. 냉장고의 문을 그대로 열어둘 수 있고, 엄마와 아빠가 너가 먹기를 원하지 않는 초콜릿이나 사탕같은것들을 왕창 꺼낼 수도 있지. 또는 엄마와 아빠가 냉장고에 넣어놓지 않은 땅콩버터를 찾을수도있를 미처 버리지 못해서 유통기한이 지난 땅콩버터를 찾을수도 있지.
> 그렇기 때문에 냉장고에서 필요한게 있을때 항상 말해주면 말해주면 좋겠다. "저는 점심에 빵과 함께 먹을 땅콩버터가 필요해요" 이런식으로. 그러면 엄마랑 아빠가 너가 점심을 먹기위해 식탁에 앉았을 때 땅콩버터가 준비될 수 있도록 할게

즉 다섯살 아이에게 냉장고를 제어할 수 있는 능력을 주는 것이 아니라 부모에게 냉장고에 대한 제어를 맡긴다. 바로 위에서 언급한 제어의 역전이다. 즉 의존성 주입은 제어의 역전의 구현체고, Nest.js 는 의존성 주입을 사용해서 제어의 역전을 구현한 프레임워크이다.

우리가 Nest.js 를 사용한다는 의미는 우리가 작성한 코드가 동작하는 제어를 맡긴다는 것이고, 의존성 주입을 통해 코드의 동작을 제어한다.

아직 말로는 추상적이기 때문에 예를 보자.
유저의 정보를 찾는 간단한 소프트웨어를 만들었다. [typedi](https://github.com/typestack/typedi)를 사용해서 의존성 주입을 구현했다.
각 모듈의 의존성은 다음과 같다.

> main.ts > usersSevice.ts > databaseSerivce.ts

**databaseService.ts**

```ts
import 'reflect-metadata'
import { Service } from 'typedi'

export interface User {
  id: number
  name: string
  age: number
}

@Service()
export class DatabaseService {
  users: User[]

  constructor() {
    this.users = [
      {
        id: 1,
        name: 'a',
        age: 20,
      },
      {
        id: 2,
        name: 'b',
        age: 25,
      },
    ]
  }

  getUsers(): User[] {
    return this.users
  }

  getUserById(userId: number): User {
    return this.users.find(({ id }) => id === userId)
  }
}
```

- databaseSerivce.ts 는 백엔드 어플리케이션에서 데이터베이스와 관련된 로직을 처리하는 서비스 로직이다.
- ORM의 간단한 구현체라고 봐도 무방하다.

**usersService.ts**

```ts
import 'reflect-metadata'
import { Service } from 'typedi'
import { DatabaseService, User } from './databaseService'

@Service()
export class UsersService {
  // 이 코드가 바로 Dependency Injection 의 핵심코드다.
  constructor(private readonly db: DatabaseService) {}

  getUsers(): User[] {
    return this.db.getUsers()
  }

  getUserById(id: number): User {
    return this.db.getUserById(id)
  }
}
```

- usersService 는 User 도메인과 관련한 비지니스 로직을 처리하는 모듈이다.
- `getUsers`, `getUserById` 와 같은 메소드를 구현함으로써 유저의 정보를 찾아서 리턴해 달라는 요청에 응답하는 `UsersController` 곳에서 사용된다.
- 즉 앞서 작성한 database 관련 레이어에 의존하는 로직이고, 최상단에 노출되는 컨트롤러에서 바로 데이터베이스의 접근을 하지 못 하도록 추상화된 레이어다.
- 이렇게 도메인에 따른 서비스 레이어를 구성하면 데이터베이스의 구현체가 바뀌어도 코드를 수정할 일이 줄어든다.
- 주석으로 달아놓은 `constructor` 부분을 주목해 보자
- TypeScript 의 [Parameter Properties](https://www.typescriptlang.org/docs/handbook/2/classes.html#parameter-properties)의 문법이 사용되었다.
- `public`, `private`, `protected`, `readonly` 의 키워드를 constructor 변수의 앞에 기입하면 이 변수가 실제로 이름과 값을 가지는 클래스의 속성이 된다.
- 따라서 아래에 `this.db` 에 접근해서 각각의 메소드를 사용할 수 있게 되는 것이다.
- 허나 이상한게 있다. 아직 `new` 키워드를 사용해서 인스턴스를 생성한적이 없는데 어떻게 이게 가능한 것일까? 바로 아래 코드를 살펴보자.

**main.ts**

```ts
import 'reflect-metadata'
import { Container } from 'typedi'
import { UsersService } from './usersService'

const usersInstance = Container.get(UsersService)
const users = usersInstance.getUsers()
const foundUser = usersInstance.getUserById(2)

console.log(users)
console.log(foundUser)
```

- 마지막으로 앱의 최상단인 main.ts 파일이다.
- 우리가 작성한 `usersService` 를 사용하는 코드다.
- 어디에서도 `new` 키워드를 사용한 인스턴스 생성이 없다.
- 의존성 주입 `typedi 라이브러리`에게 일을 위임한 것이다.
- Container 에서 `UsersService` 를 get 하는 순간 실제 인스턴스가 생성이되고, 의존하고 있던 `DatabaseService`의 인스턴스도 생성이 되어서 주입되는 것이다.

따라서 main.ts 를 실행한 결과는 다음과 같이 된다.

```ts
[ { id: 1, name: 'a', age: 20 }, { id: 2, name: 'b', age: 25 } ]
{ id: 2, name: 'b', age: 25 }
```

정리해보면,

- **@Service()** 를 클래스 선언부에 붙여줌으로써, 해당 클래스가 서비스 로직이라는 것을 의미한다.
- 서비스 로직은 다른 서비스 로직에 주입될 수 있다. 왜냐하면 서비스 로직끼리 의존하기 때문이다. (UsersService > DataService)
- 클래스의 `constructor` 메소드에 필요한 서비스를 주입할 때 `private readonly` 예약어를 사용했다.
- `new` 키워드로 인스턴스를 생성하지 않고, typedi 에게 클래스의 인스턴스를 생성하고 필요한 곳에 삽입할 수 있도록 제어권을 위임했다. (=Inversion of Control, 제어의 역전)

Nest.js 에서는 이런 서비스 로직을 [Provider](https://docs.nestjs.com/providers) 라고 칭하고, 항상 어딘가에 제공될 수 있기 때문에(?) Provider 라고 불리우는게 아닐까 싶다. Provider 들은 의존성 주입을 당하는 클래스 들이기 때문에 **@Injectable()** 데코레이터를 통해 선언부에 수식된다. 서비스 로직, 레포지토리, 팩토리, 헬퍼 등과 같은 클래스들이 Provider 범주 속에 속한다. 프로바이더의 메인 아이디어는 의존성을 주입할 수 있다는 것이다.

> 이것은 객체들이 서로간에 다양한 관계를 가질 수 있다는 것이고, 객체의 인스턴스들을 연결하는 기능이 Nest 런타임 시스템에게 위임된 것이다.

우리가 typedi 를 사용한 예제에서 Container 를 사용해서 인스턴스화 시키고, 의존성을 주입시킨 것 처럼 Nest.js 가 노드 프로세스를 실행시키고 런타임시에 이러한 일련의 일들을 한다.

그리고, 이런 일들을 가능하게 하는 것이 바로 메타데이터 때문이다. 데코레이터로 수식한 클래스들은 타입스크립트가 컴파일시에 메타데이터로 어떤 서비스에 의존하고 있는지 명시해준다. 따라서 Nest.js 가 후에 어떤 의존성들이 필요한지를 알 수 있다.

우리가 작성한 usersService.ts 를 컴파일 하면 usersService.js 파일의 하단에 이런 코드가 덧붙여진다.

```js
UsersService = __decorate(
  [
    typedi_1.Service(),
    __metadata('design:paramtypes', [
      databaseService_1.DatabaseService,
    ]),
  ],
  UsersService
)
```

- `design:paramtypes` 는 파라미터 타입 메타데이터를 설명하는 키가 된다. 이것 덕분에, 클래스가 참조하고 있는 의존성 배열을 얻어낼 수 있다. 즉 위의 예에서 보면 `UsersService` 는 `DatabaseService`에 의존하고 있다는 것을 알아 낼 수 있다.
- Nest.js 는 이런 메타데이터와 함께 런타임시에 동작하기 위해 [reflect-metadata](https://www.npmjs.com/package/reflect-metadata)패키지를 사용한다.

## Singleton

Nest.js 에서 핵심개념은 바로 우리가 작성한 모듈들 *(@Module 데코레이터로 수식된 엔티티 모듈 또는 @Injectable 데코레이터로 수식된 서비스 로직들 모두를 의미함)*이 **Singleton** 으로 작동된다는 것이다.

싱글톤은 말그대로 **한 개**란 뜻이다. 위에서 의존성 주입에 대해서 살펴볼 때 Nest.js 라는 프레임워크에게 클래스를 인스턴스화 하고 삽입하는 일련의 과정을 위임한다고 했다. 이 때 Nest.js는 디폴트로 인스턴스화 된 객체를 오로지 **한 개**만 유지한다.

Nest.js 에서 싱글톤 패턴을 사용하는 이유가 뭘까?

- 공유된 자원에 접근하기 위해서 (ex. 데이터베이스 커넥션)
- 새로운 인스턴스를 필요할 때 마다 매 번 생성할 필요가 없고, 필요하지 않을 때 인스턴스를 삭제 할 필요가 없다. (마치 냉장고 예에서 냉장고를 열고, 닫는 문제에서 벗어남) 직접 인스턴스화를 하게 되면 언제 어느 시점에 메모리를 관리해야 할지 결정하기 어렵다. (앱이 점점 커지게 된다면)
- 클래스 또는 함수가 어떤 의존성을 갖는지 **선언적** 으로 알 수 있다. (ex. constructor 메소드의 인자를 보고서)
- 어떤 클래스에서든지 접근할 수 있도록 global scope 를 가지도록 한다. (따라서 인스턴스가 실수로 더럽혀 지는 것을 방지하기 위해 `private readonly` 플래그를 통해 의존성 주입을 선언한다.)
- Node.js 는 싱글스레드로 작동하기 때문에 각각의 클래스 인스턴스들이 싱글톤으로 관리가 되어도 성능상에 문제가 없고 동시에 안전하다.
- 후에 scale out 을 할 때에도, 노드 프로세스 마다 분리된 싱글톤 객체들을 갖기 때문에 확장에도 용이하다.
- 오직 하나의 인스턴스만 존재하기 때문에 테스트가 용이하다는 장점도 있다.
- 이 인스턴스의 라이프사이클은 Nest.js 의 어플리케이션 라이프사이클에 종속되어 있다. Nest.js 앱이 처음에 실행될 때 모든 싱글톤 객체들이 인스턴스화 되고, 앱이 종료될 때 수거되는 방식이다.

Nest.js는 위에서 서술한 장점에 의해 싱글톤패턴을 기본적으로 사용하지만 사용 용도에 따라 request 마다 새로운 인스턴스를 생성하도록 할 수도있고, 의존성 주입을 받는 개체마다 매 번 새로운 인스턴스를 주입받을 수 있도록 설정도 가능하다. [Nest.js Injection Scope](https://docs.nestjs.com/fundamentals/injection-scopes#usage)

## Wrap Up

- IoC(제어의 역전): 우리는 Nest.js 라는 프레임워크를 사용함으로써 우리가 제어해야 할 영억을 위임했다.
- DI(의존성 주입): Nest.js 는 IoC를 구현하기 위해 의존성 주입이라는 개념을 사용했는데, 어플리케이션이 실행 될 때, 해당 클래스를 생성하기 위해 필요한 의존성을 생성하고 주입하는 코드를 동작시킨다.
- Singleton(싱글톤): 의존성 주입을 위해서는 클래스를 인스턴스화 해야 하는데, Nest.js의 기본 설정으로 하나의 클래스는 오로지 하나의 인스턴스만을 가지도록 한다.

## References

- Dependency Injection Wekipedia: https://en.wikipedia.org/wiki/Dependency_injection
- TypeDi: https://github.com/typestack/typedi
- TypeScript Parameter Property: https://www.typescriptlang.org/docs/handbook/2/classes.html#parameter-properties
- Wanago.io Dependency Injection: https://wanago.io/2020/06/15/api-with-nestjs-6-looking-into-dependency-injection-and-modules/
- Nest.js Injection Scopes: https://docs.nestjs.com/fundamentals/injection-scopes
