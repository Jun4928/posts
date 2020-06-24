# LocalStorage vs Cookies vs Session 1
## reference 번역 글

이 글은 다음 [Quora 링크](https://www.quora.com/What-is-the-difference-between-sessionstorage-localstorage-and-Cookies)를 번역한 글이다. 

항상 헷갈리는 세가지의 개념을 확실하게 짚고 넘어가고자 한다. 후에 웹앱을 개발할 때, 클라이언트와 서버사이에 데이터를 주고 받으며 유저에게 적절한 데이터를 보여주어야 한다. 이 때 데이터의 종류(아이디, 비밀번호, 접속 상태, 사진, 글)에 따라서 어떤 브라우저의 공간에 저장할지를 프론트엔드 개발자가 선택해야 한다. 그러기 위해서는 밑에 세가지 웹브라우저에서 사용할 수 있는 데이터 저장공간에 대한 개념과 차이점을 숙지하고 있어야 한다.


## LocalStorage:

> 1. Web storage can be viewed simplistically as an improvement on cookies, providing much greater storage capacity. Available size is 5MB which considerably more space to work with than a typical 4KB cookie.

간단하게 말하면 쿠기의 향상된 버전 정도로 볼 수 있다. 스토리지는 쿠키보다 훨씬 더 큰 저장 공간을 제공한다. 하나의 쿠키가 4KB를 제공하는 반면에 스토리지는 5MB를 제공한다. 쿠키와 비교하면 상당히 더 많은 공간이라고 할 수 있다.

> 2. The data is not sent back to the server for every HTTP request (HTML, images, JavaScript, CSS, etc) - reducing the amount of traffic between client and server.

모든 HTTP request(에를들어 HTML, 이미지, JavaScript, CSS, 등등..) 마다 서버에 데이터를 보내지 않는다. 결과적으로 클라이언트와 서버 사이의 상당한 양의 데이터 전송 트래픽이 줄어든다.

> 3. The data stored in localStorage persists until explicitly deleted. Changes made are saved and available for all current and future visits to the site.

로컬스토리지에 저장된 데이터는 명시적으로 삭제되기 전까지는 계속 남아있는다. 데이터에 대한 변화는 현재 그리고 미래에 사이트에 접근 할 때, 저장되고 사용가능 하다.

> 4. It works on same-origin policy. So, data stored will only be available on the same origin.

동일 출처 정책에서만 작동한다. 따라서, 저장된 데이터는 항상 같은 출처에서만 사용가능 하다.

> [동일 출처 정책(same-origin-policy)](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy) 란 어떤 출처에서 불러온 문서나 스크립트가 다른 출처에서 가져온 리소스와 상호작용하는 것을 제한하는 중요한 보안 방식이다. *MDN*

## Cookies:

> 1. We can set the expiration time for each cookie

각각의 쿠키에 대해서 만료 시간을 정할 수 있다. (로컬 스토리지는 명시적으로 삭제해야만 데이터가 지워짐)

> 2. The 4K limit is for the entire cookie, including name, value, expiry date etc. To support most browsers, keep the name under 4000 bytes, and the overall cookie size under 4093 bytes.

4키로바이트의 용량 제한은 모든 쿠키에 해당한다. 모든 쿠키라 하면 이름, 값, 만료 날짜 등.. 을 포함한다. 대부분의 브라우저를 지원하기 위해서 이름은 4천 바이트 이하를 유지하고 전체 쿠키의 크기가 4093 바이트 아래가 되도록 해야 한다. 

> 3. The data is sent back to the server for every HTTP request (HTML, images, JavaScript, CSS, etc) - increasing the amount of traffic between client and server.

로컬스토리지와는 달리 모든 HTTP요청에 마다 데이터가 서버에 보내진다. 상당한 양의 클라이언트와 서버간의 데이터 전송 트래픽이 증가한다.

## sessionStorage:

> 1. It is similar to localStorage.

위에서 설명한 로컬스토리지와 비슷하다.

> 2. Changes are only available per window (or tab in browsers like Chrome and Firefox). Changes made are saved and available for the current page, as well as future visits to the site on the same window. Once the window is closed, the storage is deleted

오직 윈도우 마다 변화가 가능하다. (크롬과 파이어복스 같은 브라우저에서는 탭). 로컬스토리지와 비슷하게 데이터에 대한 변화는 현재 페이지 뿐만 아니라 같은 윈도우에서 미래의 접근에 대해서도 저장되고 사용가능 하다. 하지만, 로컬스토리지와는 달리 윈도우(창)이 닫히면, 저장된 데이터는 삭제된다.

> 3. The data is available only inside the window/tab in which it was set.

데이터는 오직 이 데이터가 설정된 곳인 윈도우(창) 또는 탭 안에서만 사용 가능하다.

> 4. The data is not persistent i.e. it will be lost once the window/tab is closed. Like localStorage, it works on same-origin policy. So, data stored will only be available on the same origin.

로컬스토리지와 비슷하게 동일출처정책을 사용해서 저장된 데이터는 같은 출처에서만 사용가능하다. 하지만, 윈도우(창) 또는 탭이 닫힐때마다 데이터가 사라지기 때문에 로컬스토리지와는 달리 데이터가 영구적이지 않다.

## Recap
1. 로컬스토리지와 세션은 비슷하지만, 한 가지 명확한 차이점이 있다. 로컬스토리지는 명시적으로 데이터를 삭제할 때까지 영구적으로 저장되는 반면에, 세션은 윈도우 또는 탭이 닫히면 데이터가 사라진다. 쿠키는 데이터의 만료 날짜를 줘서 데이터가 사라지도록 한다.
2. 로컬 스토리지는 5MB, 쿠키는 4KB의 저장공간의 제한이 있다.
3. 쿠키는는 매번 HTTP 요청이 들어올 때마다 서버에 데이터를 건네지만, 로컬스토리지는 데이터를 보내지 않는다. (쿠키를 남발하면 클라이언트와 서버간의 데이터 전송 트래픽이 증가한다.)