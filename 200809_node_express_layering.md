# Node.JS 새내기의 프로젝트 디렉토리 레이어링 및 모듈화에 대한 고찰

NodeJS를 활용한 백엔드 서버 개발은 하나하나 집을 지어나가는 과정과 같다. 아무것도 없는 폴더에서 JavaScript를 활용한 프로젝트라고 명하는 node package manager 의 명령어인 `npm init` 부터 시작한다. 그리고 웹서버를 구현 할 때, 미들웨어와 라우팅 기능이 제공되는 express 프레임워크를 설치하고, 입맛에 맞는 DB를 연결시킨다. 보안관련된 라이브러리들을 직접 설치하고 미들웨어로 넣어주어야 하고 심지어 어떤 리퀘스트가 들어오는지에 대한 로그가 찍히는 기능도 없기 때문에 이 또한 직접 하나하나 넣어주어야 한다.

결론은 노드를 통해 개발 할 때, 어떤 라이브러리를 사용할 지, 반복되는 코드에 대한 모듈화를 어떻게 할지, 이 모듈들을 어떻게 폴더로 나누어서 레이어링을 해야 할지 처음부터 끝까지 다 선택사항이다. 

나는 이 개발 방식이 마음에 든다. 백엔드를 해 보겠다며 Django 프레임워크를 들여다 봤는데, 이미 지어진 대저택과 같아서 집의 구조를 하나하나 파악해야 했다. 별로 매력이 없었다. (내가 이미 JavaScript의 극성 팬이어서 그랬을 수도..) 나는 서버에 관련 된 기능들을 하나하나씩 쌓아 가고 싶은데 프레임워크의 기능을 파악해야 하는 것이 나에겐 매력으로 다가오지 않았다. 

감사하게도 이번 2차 프로젝트에서 노드로 백엔드를 개발할 수 있는 기회가 주어져서 재밌게 로직 구현에 대한 고민을 많이 하고 있다. 본격적으로 노드를 개발한지 1주일이 되었는데 그간 레이어링과 모듈화에 대한 고민의 흔적을 기록 하고자 한다. 

# 최대한 변태적으로(?) 쪼개자
앞서 서술하였듯, 노드 프로젝트는 아무것도 없는 것에서 부터 하나씩 쌓아 올리기 때문에 한 파일에 모든 것을 다 때려박다 보면 코드가 산으로 가게 된다. 처음에는 한 파일 안에서 흐름을 보는 것이 좋지 않을까? 라고 생각 했지만, 점점 기능이 추가되는 것을 고려 해 봤을 때 하나의 모듈(= 하나의 파일)이 하나의 기능만을 가지고 있다면 파일의 이름만 보고 무엇을 할 수 있는지 한 번에 파악이 가능할 것이다.

프로젝트의 가장 상위 폴더에는 세가지 기본적인 모듈이 있다.
- server.js
```javascript
const app = require("./app");
const http = require("http");
const server = http.createServer(app);
const mongoose = require("./database");

(async function () {
  try {
    // db connection
    await mongoose;
    console.log("DB CONNECTED");
    server.listen(8000, () =>
      console.log("Server is listening to port: ", 8000)
    );
  } catch (err) {
    console.log("DB CONNECTION ERROR");
    console.log(err);
  }
})();
```
파일의 이름처럼 서버를 키는 역할을 한다. express app 모듈을 가져와서 서버로 만들고, 데이터베이스 연결하는 모듈을 가져와서 디비를 연결시킨다. 

- app.js
```javascript
const express = require("express");
const hpp = require("hpp");
const helmet = require("helmet");
const cors = require("cors");
const bodyParser = require("body-parser");
const logger = require("morgan")("dev");

const router = require("./routes");

const app = express();
app.use(bodyParser.json());
app.use(hpp());
app.use(helmet());
app.use(cors());
app.use(logger);

// routes
router(app);

// general error handling
app.use((error, req, res, next) => {
  const { statusCode, message } = error;
  const status = statusCode || 500;
  error.statusCode = statusCode || 500;
  console.log(error);
  res.status(status).json({ message });
});

module.exports = app;
```

우리가 구현하는 백엔드 앱의 근간이 되는 파일이다. 각종 필요한 미들웨어 라이브러리들(로그찍는 라이브러리, 보안 관련된 라이브러리, CORS헤더 설정 라이브러리 들..)을 가져와서 express app 에 하나하나씩 쌓아준다. 

Express app은 결국 작은 미들웨어들의 모음이다. 미들웨어를 잘 이해하고 작성하는 것이 Express를 사용한 NodeJS 서버 개발의 핵심이다. 

각종 미들웨어를 입혀 준 다음에 router express router에 app을 인자로 넘긴다. URI/Endpoint 에 따른 API 구현이 작동하도록 하기 위한 필수 코드다. 

- database.js 
```javascript
const mongoose = require("mongoose");
const dotenv = require("dotenv").config();

module.exports = mongoose.connect(process.env.MONGO_URL, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
  useFindAndModify: false,
  useCreateIndex: true,
});
```
MongoDB의 쿼리문을 이해하지 못 해도, 백엔드 개발에 재미와 속도를 더해주는 ODM(Object Document Mapper)인 mongoose 를 사용해서 MongoDB를 연결 시켜서 모듈로 내보낸다. 

# 폴더구성

위에서는 백엔드 서버의 기반이 되어주는 server.js, app.js, database.js 에 대해서 살펴보았다. 다음은 프로젝트의 기능을 담당하는 각종 모듈들을 어떻게 레이어링을 할 것인지를 고민한 흔적이다. 

- routes: 유저의 요청에 따라 다른 작동을 할 수 있도록 하는 여러 엔드포인트들을 모듈화 시킨후 index 에서 router 함수로 모듈화. 여기서 controllers에 엔드포인트 제어를 위한 모듈을 임포트 해서 사용한다. 
- controllers: 엔드포인트 제어를 위한 모듈 (request, response를 처리하는 로직). 각각의 엔드포인트 마다 컨트롤러를 구현 하는데, 이 때 데이터 베이스에 접근하고 다양한 비지니스 로직이 다뤄져야 한다. 데이터 베이스와 관련된 작업은 DAO에, 복잡한 비지니스 로직은 services 폴더에 각각 모듈화 한다. 
- services: 비지니스 로직 (ex. 장바구니 기능을 위한 데이터 정제, 카카오 로그인 등)을 모듈화 한다.
- models: 앱 내에서 사용될 데이터 베이스 모델들을 객체로 모듈화 한다.
- DAO(DataBase Access Object): 데이터 베이스에 접근하는 쿼리문 함수로 모듈화
- middleware: 인증/인가와 같은 여러 엔드 포인트에서 자주 사용될 미들웨어 모듈화
- utils: 앱 내에서 자주 사용되는 함수 모듈 (ex. errorHandling, checkPassword)