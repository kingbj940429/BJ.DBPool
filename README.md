# DBPool
* 기존 db.query에 대한 문제점
```
1) Node.js는 기본적으로 비동기식 처리를 한다. 따라서 db.query도 비동식 처리를 하기때문에 
console.log("1");
db.query(()=>{}); 
consoel.log("2");
라고 가정하면 1, 2 가 출력되고 db.query가 실행된다. 따라서 db 결과값이 필요한 경우 
이 코드를 우선 넘어가버려 null이 출력된다.
2) 1번에서 말한것에 대해선 대안이 있다. db.query(()=>{})의 함수 안에 res.json(쿼리 결과값)등을 
넣을수 있겠지만, 이럴 경우 db.query(()=>{}) 라는 함수안에 종속되어 버리고 만다. 결합도가 높아진다.
3) sequelize라는 npm은 동기식을 지원한다. async/await 사용이 가능하기 때문에 db.query를 기달렸다가 
결과값을 받고 다음 로직을 수행한다. 하지만 문제는 sequelize는 이 모듈안에 직접 테이블을 생성해야한다. 
즉 mysql 워크벤치의 테이블과는 연동이 안된다. 심지어 쿼리도 다르기때문에 sequelize를 쓰기 위해선 
그에 맞는 쿼리문 공부를 다시 해야한다.
```
* 해결법
```js
//mysql2 npm을 활용하여 아래와 같이 코드를 구현하여 해결하였다.
const dbPool = async (queries) => {
    const connection = await pool.getConnection(async conn => conn);
    try {    
      const [rows] = await connection.query(queries);
      connection.release();
      return rows;
    } catch (err) {
      console.log('Query Error');
      connection.release();
      return false;
    }
  };
module.exports = dbPool;
```
## 사용법
1) npm i mysql2 dotenv 을 터미널에서 실행시켜줍니다.
2) .env에 DB정보를 넣어줍니다.
3) 알맞은 파일에 DBPool.js 파일을 넣어줍니다.
4)
```js
//사용하고 싶은 라우터에 dbPool 모듈을 추가해주고 쿼리문은 await dbPool("쿼리문") 으로 실행하면 됩니다.
const dbPool = require('../config/config.js') //DB 연동
test = await dbPool('select * from test.test'); //실행되는 쿼리문
```
### 한계점
* 동기식으로 처리하기 때문에 대용량을 처리하면 속도가 저하될 수 있습니다.

