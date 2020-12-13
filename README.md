[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fkingbj940429%2FBJ.DBPool&count_bg=%230090FF&title_bg=%23555555&icon=opslevel.svg&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

# DBPool

Nodejs Enables asynchronous query statement processing.

![npm](https://img.shields.io/badge/npm-v6.14.4-blue)

## Motivation 
- Problems with existing db.query

1) Node.js are basically asynchronous. so db.query is also non-synchronous.
console.log ("1");
db.query(()=>{});
consoel.log ("2");
Assuming that 1 and 2 are printed and db.query is output(The run will be in order.) So if you need a db result,
This code is passed first and null is printed.

2) There is an alternative to what is said in number 1. The function of db.query()=>{} includes res.json (query result value) etc.
You can put it in, but in this case, db.Dependent on the function query(()=>{}). The degree of coupling increases.

3) Npm called sequelize supports synchronous. db because async/await is availableI'll wait for a query.
The following logic is performed after receiving the result value. But the problem is that sequelize must create a table directly in this module.
That is, it does not relate to the tables on myysql workbench. Even the queries are different, so to write sequelize,
I have to re-learn the appropriate query sentence.

* Solution
```js
//By utilizing mysql2 npm, the code was implemented and resolved as follows.
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
## How to Use
1) Run npm i mysql2 dotenv in the terminal.
2) Insert DB information into .env.
3) Insert the DBPool.js file into the appropriate file.
4)
```js
//Add the dbPool module to the router you want to use and run the query statement as await dbPool ("querymoon").
Const dbPool = require('../config/config.js') //DB interlock
test = awit dbPool('select * from test.test'); //query statement executed
```

