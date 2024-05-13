---
title: 리액트 쿼리 - 서버 상태 정보
date: 2024-05-13 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## 리액트 쿼리 - 서버 상태 정보

![https://res.cloudinary.com/practicaldev/image/fetch/s--bKRbe8Sb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/4M4bkQ8/Screen-Shot-2020-05-14-at-14-23-23.png](https://res.cloudinary.com/practicaldev/image/fetch/s--bKRbe8Sb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/4M4bkQ8/Screen-Shot-2020-05-14-at-14-23-23.png)

### ✅ Fetching

초기 상태이며, 아웃소스(백엔드)에서 데이터를 가져올 때 발생

### ✅ Fresh

양쪽 정보가 동일한 상태이며, 정보를 다시 가져올 필요가 없음을 의미  
대부분의 경우 정보를 가져올 때 이 정보는 오래된 정보가 될 수 있으므로 Fresh 상태는 짧은 시간 지속됨

#### ✅ Stale

앱이 사용하고 있는 오래된 데이터를 의미  
프론트엔드에 오래된 정보가 있고 이를 백엔드에서 다시 가져와야 하거나, 프론트엔드가 아직 새 정보를 백엔드로 보내지 않아 백엔드가 업데이트가 되지 않아 발생함

### ✅ Inactive

데이터가 앱에서 사용되지 않는 경우 잠시 후 삭제될 가능성이 있음을 의미  
리액트 쿼리에는 브라우저에서 캐시를 관리하기 위한 가비지 콜렉션이 있음

### ✅ Delete

데이터가 일정 기간동안 비활성화되어 캐시에서 삭제된 경우 발생

## 캐시된 데이터 관리

![https://res.cloudinary.com/practicaldev/image/fetch/s--2FeTufbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/X3cPjBp/Screen-Shot-2020-05-14-at-17-20-38.png](https://res.cloudinary.com/practicaldev/image/fetch/s--2FeTufbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/X3cPjBp/Screen-Shot-2020-05-14-at-17-20-38.png)

1. 사용자가 홈페이지의 플랫폼에 들어가면 모든 영화가 요청됨
2. 사용자는 장르별 구분된 영화 카탈로그를 보기 위해 탐색 페이지로 이동
3. 이전에 요청한 영화가 표시되고, 리액트 쿼리는 백그라운드에서 재검증 함
4. 사용자가 설정 페이지로 이동하면 리액트 쿼리는 앱에서 사용되지 않는 영화 데이터를 감지하여 비활성 상태로 전환
5. 5분 또는 쿼리에서 구성한 시간이 지나면 리액트 쿼리는 캐시에서 정보를 제거함

참고 사이트

- 원티드 2024년 5월 챌린지
- [https://dev.to/rootstrap/react-query-and-management-of-server-state-8ol](https://dev.to/rootstrap/react-query-and-management-of-server-state-8ol)
