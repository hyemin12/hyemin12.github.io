---
title: tanstack query의 useQuery 오류
date: 2024-03-02 14:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
toc: true
toc_sticky: true
---

Book store 클라이언트 서버를 구축하던 도중, 서버에서 데이터를 가져오는 로직을 tanstack query를 사용하는 방식으로 변경하던 도중 발생한 오류이다.

useQuery의 첫번째 인자에서 다음과 같은 오류가 발생했다.

```
No overload matches this call.
Overload 1 of 3, '(options: UndefinedInitialDataOptions<unknown, Error, unknown, QueryKey>, queryClient?: QueryClient | undefined): UseQueryResult<unknown, Error>', gave the following error.
Argument of type 'string[]' is not assignable to parameter of type 'UndefinedInitialDataOptions<unknown, Error, unknown, QueryKey>'.
Property 'queryKey' is missing in type 'string[]' but required in type 'UseQueryOptions<unknown, Error, unknown, QueryKey>'.
Overload 2 of 3, '(options: DefinedInitialDataOptions<unknown, Error, unknown, QueryKey>, queryClient?: QueryClient | undefined): DefinedUseQueryResult<unknown, Error>', gave the following error.
Argument of type 'string[]' is not assignable to parameter of type 'DefinedInitialDataOptions<unknown, Error, unknown, QueryKey>'.
Type 'string[]' is not assignable to type 'UseQueryOptions<unknown, Error, unknown, QueryKey>'.
Overload 3 of 3, '(options: UseQueryOptions<unknown, Error, unknown, QueryKey>, queryClient?: QueryClient | undefined): UseQueryResult<unknown, Error>', gave the following error.
Argument of type 'string[]' is not assignable to parameter of type 'UseQueryOptions<unknown, Error, unknown, QueryKey>'.
Property 'queryKey' is missing in type 'string[]' but required in type 'UseQueryOptions<unknown, Error, unknown, QueryKey>'.ts(2769)
queryClient-Ho-z40Sw.d.ts(301, 5): 'queryKey' is declared here.
```

- 문제가 되었던 코드

```tsx
const useBooks = () => {
  const { search } = useLocation();

  const params = new URLSearchParams(search);

  const { data: booksData, isLoading: isBooksLoading } = useQuery(
    ["books", search],
    () => {
      const categoryIdParams = params.get(QUERYSTRING.CATEGORY_ID);
      const newsParams = params.get(QUERYSTRING.NEWS);
      const currentPageParams = params.get(QUERYSTRING.PAGE);
      fetchBooks({
        category_id: categoryIdParams ? Number(categoryIdParams) : undefined,
        new: newsParams ? true : undefined,
        page: currentPageParams ? Number(currentPageParams) : 1,
        limit: LIMIT
      });
    }
  );
};
```

오류 내용이 길지만, 오류의 내용은 심플하다.

**useQuery 함수에 전달하는 인자가 React Query 라이브러리에서 요구하는 형식과 맞지 않아 발생**하는 오류였다.

useQuery 함수는 첫 번째 인자로 queryKey를 필요로 하지만, 나의 경우에는 `'queryKey' is missing in type 'string[]'`, 배열 내에 queryKey가 없다는 내용이었다.

tanstack query의 공식 문서를 보면 queryKey는 배열로 받을 수 있다고 하였으나, 계속 오류가 발생하여 공식 문서에 있는 또 다른 방법으로 queryKey를 설정하니 오류가 해결되었다.
[관련 문서](https://tanstack.com/query/latest/docs/framework/react/overview#enough-talk-show-me-some-code-already)

- 수정 내용
  useQuery에 인자로 전달하던 형식을 key:value 형태로 객체의 속성을 정의하여 전달하도록 변경하였다.

```tsx
// 수정 전
useQuery(["books", search], () => {});

// 수정 후
useQuery({ queryKey: ["books", search], queryFn: () => {} });
```

- 수정 코드

```tsx
const useBooks = () => {
  const { search } = useLocation();

  const params = new URLSearchParams(search);

  const { data: booksData, isLoading: isBooksLoading } = useQuery({
    queryKey: ["books", search],
    queryFn: () => {
      const categoryIdParams = params.get(QUERYSTRING.CATEGORY_ID);
      const newsParams = params.get(QUERYSTRING.NEWS);
      const currentPageParams = params.get(QUERYSTRING.PAGE);
      fetchBooks({
        category_id: categoryIdParams ? Number(categoryIdParams) : undefined,
        new: newsParams ? true : undefined,
        page: currentPageParams ? Number(currentPageParams) : 1,
        limit: LIMIT
      });
    }
  });
};
```
