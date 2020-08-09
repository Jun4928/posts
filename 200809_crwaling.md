# Puppeteer 크롤링 비동기 병렬처리하기
## for.. of, Promsie.all

**> wecode**의 2차 프로젝트로 [Nike](https://www.nike.com/kr/ko_kr/) 홈페이지를 클론하고 있다. 이번엔 프론트엔드가 아닌 NodeJS + Express + MongoDB 스택으로 백엔드를 맡게 되었다. 클론의 시작은 데이터를 구축하는 것인데, MongoDB 스키마 모델링을 한 후 곧장 크롤링 코드 작성에 들어갔다. 

라이브러리는 [puppeteer](https://github.com/puppeteer/puppeteer)를 사용했다. Chronium 엔진을 기반으로 한 Headless 브라우저로, 화면에 브라우저를 띄우지 않고 백그라운드로 실행 가능하다. JavaScript 코드로 브라우저에 원하는 명령을 실행할 수 있는 일종의 매크로라고 볼 수 있다. 이 라이브러리를 사용한 이유는, NodeJS 환경에서 제작한 JavaScript 코드가 브라우저에서 작동할 수 있기 때문이다. 

# puppeteer를 사용한 크롤링의 기본적인 flow
아래 코드의 출처는 Puppeteer 라이브러리 [github](https://github.com/puppeteer/puppeteer)
```javascript
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch(); // 1
  const page = await browser.newPage(); // 2
  await page.goto('https://example.com'); // 3

  // Get the "viewport" of the page, as reported by the page.
  const dimensions = await page.evaluate(() => { // 4
    return {
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight,
      deviceScaleFactor: window.devicePixelRatio
    };
  });

  console.log('Dimensions:', dimensions);

  await browser.close(); // 5
})(); //6
```

주의 해야 할 점은 puppeteer 에서 제공하는 거의 모든 함수는 **Promise를 반환하는 비동기**로 작동하기 때문에 await 키워드를 사용해야 하고, await 키워드는 항상 async 함수 안에서 사용되어야 한다.

1. puppeteer 객체의 launch 함수로 브라우저를 연다.
2. 1번에서 연 browser 객체의 newPage 함수로 새로운 페이지를 연다. 
3. 2번에서 연 page의 goto 함수로 원하는 페이지로 접속한다.
4. puppetee의 evaluate 함수로 넘겨주는 callback 함수는 브라우저의 맥락안에서 실행되는 코드다. 따라서 document 객체에 접근할 수 있고, 우리가 원하는 크롤링 작업을 할 수 있다. 즉, querySelectorAll 같은 함수를 사용해서 DOM 객체에 접근하고 원하는 데이터를 쏙쏙 빼올 수 있다는 이야기다.
5. 1번에서 연 브라우저를 닫는 코드
6. 1번에서 5번의 과정을 async 함수로 선언한 뒤 바로 실행시킨다. 익명함수를 생성하자마자 실행시키는 방법

# 마주한 문제
Puppeteer 가 띄어주는 Headless 브라우저는 매크로다. 사용자가 직접 크롬을 띄어서 클릭을 하고 스크롤을 내리는 행위를 코드로 할 수 있기 때문이다. 여기서 문제가 발생했다. Nike 홈페이지에서 매크로를 막아 놓았다. 아마도 limited 에디션으로 출시되는 신발 때문이지 아닐까 싶다. (리셀가가 어마어마하다.) 

원래는 위에서 작성한 puppeteer를 사용한 기본적인 크롤링 flow 에 따라 browser를 띄운 후 goto 함수로 페이지를 이동하며 필요한 정보를 크롤링 할 생각이었다. 하지만 매크로를 막아 놓았기 때문에 최초에 브라우저를 띄운 후 접속한 페이지 이외에 발생하는 모든 이벤트(클릭, 스크롤 등..)와 페이지 이동이 불가능 했다. 

# 해결책
![nike 신상품 페이지](./imgs/crawling/nike.png)
브라우저를 띄어서 페이지를 이동할 수 없다면, 먼저 링크를 크롤링 한 후 하나 하나의 링크마다 새로운 브라우저를 띄우는 방법을 생각했다. 위의 이미지는 나이키 홈페이지의 신상품 목록을 보여주는 페이지다. 

나는 다음과 같은 전략을 택했다.

1. 색상별 링크 크롤링 하기: getColors.js
2. 색상별로 페이지를 접근해서 제품 목록의 링크를 크롤링 하기: getPrducsUrls.js
3. 2번에서 크롤링 한 제품의 주소에 대해서 제품의 상세 정보 크롤링 하기: getProductObject.js

각각 함수의 실행결과를 NodeJS의 fs 모듈을 사용해서 배열의 형태를 json 파일로 저장했고, 다시금 다음 단계에서 json 파일을 읽어 들여서 링크를 하나하나 접근하며 필요한 정보를 크롤링 해오는 전략이다. 

총 16가지 색상에 대해서 페이지에 처음 뜨는 제품 목록의 링크를 크롤링 하고 나니 대략 300개의 링크가 생겼다. 이제 **getProductObject.js** 안에서 브라우저를 띄우고 필요한 정보를 가져오는 행위를 300번 하면 된다. 

*상세 코드는 다음 [github repo](https://github.com/wecode-bootcamp-korea/10-Likey-backend/tree/master/scraper) 에서 확인할 수 있습니다.*


# 정녕 300번의 반복적인 크롤링 작업을 순차적으로 할 것인가?

위의 전략에서 단연 제일 중요한 코드는 3번의 코드다. 브라우저를 띄어서 해당 제품의 링크로 접속 한 후 제품의 상세 정보를 객체의 형태로 리턴하는 함수가 담겨있는 코드다. 

나는 처음에 300개의 링크를 열여서 비동기 함수를 작성했음에도 불구하고 **순차적**으로 코드를 실행시켰다. 

```javascript
for (link of links) { // 1
  try {
    const productObject = await getProductObject(link); // 2
    productObject && results.push(productObject); // 3
  } catch (err) {
    continue;  // 4
  }
}
```

1. links 배열에는 제품의 링크들이 담겨있다. (대략 300개 정도)
2. 브라우저를 키고 해당 링크로 페이지를 이동시키고 evaluate 함수로 해당 제품의 정보를 크롤링하는 함수인 getProductObject 를 실행시켜서 리턴값으로 해당 제품의 정보를 담은 객체를 저장한다.
3. 객체를 json 파일로 저장할 배열에 push한다. 
4. try / catch 문으로 크롤링 중 발생한 에러를 잡아준다. 이 코드가 없으면 크롤링 중간에 전체 코드가 뻗어버림 => 처음부터 다시 크롤링 해야하는 불상사가..

위의 코드는 비동기 함수를 작성했지만, 본래의 절차지향적인 방법으로 작성된 코드다. 성능에 엄청난 저하를 일으킨다. 아니 무엇보다 비동기에 대한 이해가 부족한 코드다. 

비동기의 장점은 실행시킨 작업에 대해서 기다릴 필요가 없다는 것이다. 근데 위의 for of 문 안에서 실행시키는 비동기 함수는 await 키워드에 의해서 매 번 결과를 리턴해야 다음 링크로 넘어가도록 되어 있었다. 각각의 제품을 크롤링 하는 작업은 순서에 상관없기 때문에 이럴떄야 말로 비동기를 병렬처리 하기에 최적인 상황이다. 

위의 코드에 대해서 비동기 병렬처리를 구현하라는 리뷰를 받고 아래와 같이 리팩토링 했다. 
```javascript

const arrayToChunks = (array, CHUNKSIZE) => { 
  const results = [];
  let start = 0;

  while (start < array.length) {
    results.push(array.slice(start, start + CHUNKSIZE));
    start += CHUNKSIZE;
  }

  return results;
};

const main = async () => {
  const results = [];
  const CHUNKSIZE = 4;

  const chunkedLinks = arrayToChunks(urls, CHUNKSIZE); // 1

  for (chunk of chunkedLinks) { // 2
    const productObjectPromises = chunk.map((link) => getProductObject(link)); // 3: 비동기 병렬처리를 위한 핵심 코드
    const resolvedProducts = await Promise.all(productObjectPromises); // 4: 비동기 병렬처리를 위한 핵심 코드 

    console.log(resolvedProducts);
    resolvedProducts.forEach((product) => { // 5
      product && results.push(product);
    });
  }

  fs.writeFile( // 6
    "./proudcts.json",
    JSON.stringify(results, null, 2),
  )
    .then((resolved) => console.log("succeed"))
    .catch((rejected) => console.log(rejected));

  return;
};

main();
```

1. 300개의 링크가 들어있는 배열에 대해서 모두 비동기 병렬처리를 실행하면 메모리 누수로 인해서 프로그램이 뻗어비리기 때문에 chunk size(덩어리 크기)를 지정해서 300개의 링크가 들어있는 배열을 여러개의 chunk(덩어리)로 쪼갰다.
2. 위에서 쪼갠 배열은 이제 이중 배열이 된다. 예를들어 `[[1,2,3,4], [5,6,7,8], [9, 10, 11, 12] ...]` 와 같이된다. 하나하나의 덩어리를 for 문으로 작업한다. 즉, 한 번에 4개의 링크에 대해서 병렬 처리를 한다. 
3. 비동기 병렬처리를 위한 핵심코드다. `getProductObject` 함수는 비동기 함수이기 때문에 promise 객체를 리턴한다. 먼저 map 함수로 해당 덩어리(4개의 링크가 들어있는 배열) 에 대한 promise 객체를 변수에 저장한다.
4. Promise.all 함수로 3에서 반환받은 promise 객체에 대해서 비동기 병렬처리를 실행시키고 반환되는 순서에 따라서 배열에 저장한다. 이 비동기 병렬처리가 끝나야 다음 코드를 실행시킬 수 있다.
5. 반환된 객체가 담긴 결과를 forEach 문으로 하나하나 json 으로 저장할 result 배열에 담는다.
6. 최종 300개의 링크에 대해 4개씩 비동기 병렬처리를 한 후 결과가 담긴 객체를 json 파일로 저장 

이 글에서 제시된 모든 함수와 코드는 다음 [github 링크](https://github.com/wecode-bootcamp-korea/10-Likey-backend/tree/master/scraper)에서 확인할 수 있습니다.