---
title: mysql LIMIT 및 OFFSET 절 오류
date: 2024-01-04 18:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

풀스택 부트캠프 스프린트2 프로젝트를 진행하던 도중, 도서 검색시 페이지를 나눠서 조회할 수 있도록 하는 API를 설계할 때 발생한 오류이다.

values (바인딩 매개변수)도 숫자 타입으로 잘 들어가는데, Incorrect arguments to mysqld_stmt_execute 오류가 발생

### 오류 예1

- request
  - POST
  - localhost:8888/books?page=1
- sql values = [ 8, 0 ]

```bash
Error: Incorrect arguments to mysqld_stmt_execute
    at PromisePoolConnection.execute (C:\Users\user\Desktop\JS-project\book-store-api\node_modules\mysql2\promise.js:112:22)
    at getSqlQueryResult (C:\Users\user\Desktop\JS-project\book-store-api\utils\getSqlQueryResult.js:6:29)
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async getBooks (C:\Users\user\Desktop\JS-project\book-store-api\controller\booksController.js:31:28) {
  code: 'ER_WRONG_ARGUMENTS',
  errno: 1210,
  sql: 'SELECT * FROM books LIMIT ? OFFSET ?',
  sqlState: 'HY000',
  sqlMessage: 'Incorrect arguments to mysqld_stmt_execute'
}
```

### 오류 예2

- request
  - POST
  - localhost:8888/books/search?query=모던&page=1
- sql values = [ '모던', 6, 0 ]

```bash
Error: Incorrect arguments to mysqld_stmt_execute
at PromisePoolConnection.execute (C:\Users\user\Desktop\JS-project\book-store-api\node_modules\mysql2\promise.js:112:22)
at getSqlQueryResult (C:\Users\user\Desktop\JS-project\book-store-api\utils\getSqlQueryResult.js:6:29)
at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
at async getSearchBooks (C:\Users\user\Desktop\JS-project\book-store-api\controller\booksController.js:60:28) {
code: 'ER_WRONG_ARGUMENTS',
errno: 1210,
sql: '\n' +
' SELECT \* FROM books \n' +
' WHERE title LIKE CONCAT("%", ?, "%") \n' +
' LIMIT ? OFFSET ?',
sqlState: 'HY000',
sqlMessage: 'Incorrect arguments to mysqld_stmt_execute'
}
```

### 오류 예3

하드코딩으로 직접 값을 입력해도 동일한 오류가 발생

```js
const sql = `
    SELECT * FROM books
    WHERE title LIKE CONCAT("%", ?, "%")
    LIMIT ? OFFSET ?`;

const { rows, conn } = await getSqlQueryResult(sql, ["모던", 8, 0]);
```

#### 해결하려고 시도

1. 구글링
2. 바인딩 변수를 확인
3. chatGPT 3.5 버전에 물어보기

다양한 방법을 시도해봤지만 이유를 찾지 못해 마지막이라는 심정으로 chatGPT 4버전에 물어보니, 답변을 해줘서 해결하게 되었다.

강의에서 강사님은 LIMIT OFFSET절을 바인딩 매개변수를 사용해서 sql문을 실행시켰기때문에 완벽한 정답은 아니지만, 해결한 것에 대해 만족하고있다...

> 해결방법 (답변)
>
> MySQL은 LIMIT 및 OFFSET 절에서 바인딩 매개변수를 지원하지 않습니다. 그래서 이러한 오류가 발생하는 것입니다. 따라서, 바인딩 매개변수 대신 직접 값을 쿼리에 삽입하는 방식으로 해결해야 합니다.
>
> 하지만 직접 값을 쿼리에 삽입하는 경우 SQL 인젝션 공격에 취약해질 수 있으므로, 반드시 값을 검증하고 안전하게 변환해야 합니다.

### 해결

```js
const { StatusCodes } = require("http-status-codes");
const getSqlQueryResult = require("../utils/getSqlQueryResult");
const handleServerError = require("../utils/handleServerError");

const getBooks = async (req, res) => {
  const { categoryId, new: fetchNewBooks, page } = req.query;

  const limit = 8;
  const offset = limit * (page - 1);

  let sql = "SELECT * FROM books ";
  const values = [];

  if (categoryId) {
    sql += " WHERE category_id = ?";
    values.push(categoryId);

    if (fetchNewBooks) {
      sql +=
        " AND published_at BETWEEN DATE_SUB(now(), interval 30 DAY) AND NOW()";
    }
  } else if (fetchNewBooks) {
    sql +=
      " WHERE published_at BETWEEN DATE_SUB(now(), interval 30 DAY) AND NOW()";
  }

  if (page) {
    sql += `LIMIT ${limit} OFFSET ${offset}`;
  }

  try {
    const { rows, conn } = await getSqlQueryResult(sql, values);
    res.status(StatusCodes.OK).send({ lists: rows });
    conn.release();
  } catch (err) {
    handleServerError(res, err);
  }
};

const getSearchBooks = async (req, res, next) => {
  const { page, query } = req.query;

  let additional = "";

  if (page) {
    const limit = 6;
    const offset = limit * (page - 1);
    additional = ` LIMIT ${limit} OFFSET ${offset}`;
  }

  const sql = `
    SELECT * FROM books 
    WHERE title LIKE CONCAT("%", ?, "%") 
    ${additional}`;

  try {
    const { rows, conn } = await getSqlQueryResult(sql, [query]);
    res.status(StatusCodes.OK).send({ lists: rows });
    conn.release();
  } catch (err) {
    handleServerError(res, err);
  }
};

const getIndividualBook = async (req, res, next) => {
  const { bookId } = req.params;

  const sql = `SELECT * from books 
    LEFT JOIN category ON category.id = books.category_id 
    WHERE books.id = ?`;

  try {
    const { rows, conn } = await getSqlQueryResult(sql, [bookId]);
    res.status(StatusCodes.OK).send(rows);
    conn.release();
  } catch (err) {
    handleServerError(res, err);
  }
};

module.exports = {
  getBooks,
  getSearchBooks,
  getIndividualBook
};
```
