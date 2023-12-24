---
title: my sql foreign key 오류
date: 2023-12-19 18:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

### 외래키 등록을 위해 CLI로 명령어을 입력했을 때 발생한 오류

```sql
mysql> alter table posts
    -> add foreign key (user_id)
    -> references users (id);
```

```
ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails (`board`.`#sql-1680_8`, CONSTRAINT `fk_user_id` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`))
```

오류 1452(23000): 하위 행을 추가하거나 업데이트할 수 없습니다. 외래 키 제약 조건이 실패합니다(`board`.`#sql-1680_8`, CONSTRAINT `fk_user_id` FOREIGN KEY(`user_id`) 참조 `users`(`id` ))

posts 테이블을 확인해보니 user_id가 NULL인 것을 확인하였다. user_id의 값을 users 테이블에 있는 값으로 지정하고 다시 외래키 등록을 하니
정상적으로 등록되었다.
