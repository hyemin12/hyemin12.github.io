---
title: react router - errorElement 번역
date: 2024-02-22 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

## ✨react router - errorElement 번역

[react router 공식 문서 - error-element](https://reactrouter.com/en/main/route/error-element)

## `errorElement`

`loaders`, `actions` 또는 component 렌더링에서 예외가 발생할 때 일반적인 라우팅 렌더(Route element) 대신 오류 경로(Route errorElement)가 렌더링됩니다.

> ! NOTE
>
> 만약 리액트 요소를 사용하지 싫다면,  
> `errorElement={<MyErrorBoundary />}` 대신에 ErrorBoundary 컴포넌트를 지정할 수 있습니다. (`ErrorBoundary={MyErrorBoundary}`)  
> 그러면 React Router가 내부적으로 createElement를 호출합니다.

이 기능은 데이터 라우터를 사용하는 경우에만 작동합니다.

```js
<Route
  path="/invoices/:id"
  // 예외가 여기서 발생하면,
  loader={loadInvoice}
  // 또는 여기서 예외가 발생하면,
  action={updateInvoice}
  // 또는 여기서 예외가 발생하면,
  element={<Invoice />}
  // 요소 대신 이것이 렌더링됩니다
  errorElement={<ErrorBoundary />}
/>;

function Invoice() {
  return <div>Happy {path}</div>;
}

function ErrorBoundary() {
  let error = useRouteError();
  console.error(error); // Uncaught ReferenceError: path is not defined
  return <div>Dang!</div>;
}
```

### #Bubbling; 버블링

라우트에 errorElement가 없으면 오류는 상위 라우트를 통해 버블링됩니다. 이를 통해 원하는대로 처리할 수 있습니다.

라우트 트리 최상단에 errorElement를 두어 거의 모든 오류를 한 곳에서 처리할 수 있습니다. 또는 모든 경로에 배치하고 오류가 없는 부분은 계속 정상적으로 렌더링되도록 할 수 있습니다. 이렇게하면 사용자가 하드 새로고침하지 않고도 오류에서 복구할 수 있는 옵션이 더 많이 제공됩니다.

### #Default Error Element; 기본 errorElement

> ! Important
>
> 프로덕션에 배포하기 전에 항상 루트 수준의 errorElement를 제공하는 것이 좋습니다. 왜냐하면 기본 error Element의 UI는 못생겼고, 최종 사용자가 사용하기에 적합하지 않기 때문입니다.

주어진 오류를 처리할 errorElement를 라우트 트리에 제공하지 않으면 오류가 버블링되어 기본 errorElement에 의해 처리됩니다. 기본 errorElement는 오류 메세지와 오류 스택을 제공합니다. 오류 스택이 프로덕션 빌드에 표시되는 이유에 일부 사람들이 의문을 제기했습니다. 일반적으로 보안상의 이유로 오류 스택을 노출시키고 싶지 않아 하기 때문입니다. 그러나 이는 주로 서버측 오류에 적용됩니다. 클라이언트측, react-router-dom의 경우 코드는 이미 브라우저에서 사용 가능하므로 어떤 숨김 작업도 보안을 위한 것은 아닙니다. 또한 오류를 콘솔에 기록하고 싶어하기 때문에 UI 표시에 숨기지 않습니다. UI에 표시하지 않고 콘솔에 기록하지 않으면, 애플리케이션 개발자가 버그에 대한 정보를 전혀 갖지 못하게되어 다른 문제를 발생시킵니다. 따라서 우리는 항상 배포하기 전에 루트 수준의 errorElement를 추가하는 것을 권장합니다!

### #throwing-manually; 던지기 메뉴얼

errorElement는 예상하지 못한 오류를 처리할 수도 있지만, 예상한 오류를 처리할 때도 사용할 수 있습니다.

특히 외부 데이터를 처리하는 loaders 및 actions에서는 데이터의 존재, 서비스의 가용성 또는 사용자의 액세스 권한을 예측할 수 없습니다. 이러한 경우에는 자체 예외를 던질 수 있습니다.

NotFound 예시

```js
<Route
  path="/properties/:id"
  element={<PropertyForSale />}
  errorElement={<PropertyError />}
  loader={async ({ params }) => {
    const res = await fetch(`/api/properties/${params.id}`);
    if (res.status === 404) {
      throw new Response("Not Found", { status: 404 });
    }
    const home = await res.json();
    const descriptionHtml = parseMarkdown(data.descriptionMarkdown);
    return { home, descriptionHtml };
  }}
/>
```

loader의 코드를 살펴봅시다. 데이터를 로드하는 동안 더이상 라우트를 렌더링할 수 없다고 판단되면 호출을 중단시키기 위해 throw할 수 있습니다. 데이터가 없을 때 loader의 나머지 작업을 걱정할 필요는 없습니다. 그저 던지고 빠져나오면 됩니다.

이는 라우트 컴포넌트에서 오류 분기 코드를 걱정할 필요가 없다는 것을 의미합니다. loader 또는 action에서 throw하면 라우트가 시도조차 하지 않습니다. 왜냐하면 errorElement가 렌더링되기 때문입니다.

loader 또는 action에서 무엇이든 반환(return)할 수 있는 것처럼 **응답, 오류 또는 일반 객체 등 무엇이든 던질 수 있습니다.**

### #throwing-responses; 응답 throw하기

어떤 것이든 throw하면 `useRouteError`를 통해 다시 제공되지만, 응답을 throw하면 react router가 자동으로 응답 데이터 구문을 분석하여 컴포넌트에 반환합니다.

또한 `isRouteErrorResponse`를 사용하여 바운더리에서 이 특정 유형을 확인할 수 있습니다. json과 결합하면 몇 가지 데이터와 함께 응답을 throw하여 바운더리에서 다양한 케이스를 쉽게 렌더링할 수 있습니다:

```jsx
import { json } from "react-router-dom";

function loader() {
  const stillWorksHere = await userStillWorksHere();
  if (!stillWorksHere) {
    throw json(
      {
        sorry: "You have been fired.",
        hrEmail: "hr@bigco.com",
      },
      { status: 401 }
    );
  }
}

function ErrorBoundary() {
  const error = useRouteError();

  if (isRouteErrorResponse(error) && error.status === 401) {
    // 응답 json이 자동으로 분석되어
    // `error.data`로 전달됩니다. 또한 상태에 액세스할 수 있습니다.
    return (
      <div>
        <h1>{error.status}</h1>
        <h2>{error.data.sorry}</h2>
        <p>
          Go ahead and email {error.data.hrEmail} if you
          feel like this is a mistake.
        </p>
      </div>
    );
  }

  // 특정 경로에 대한 특별한 경우가 아닌 경우
  // 상위 오류 바운더리가 처리하도록 다시 throw합니다.
  throw error;
}
```

이를 통해 일반적인 오류 바운더리를 생성할 수 있고, 일반적으로 오류 바운더리를 루트 라우트에 배치하여 여러 경우를 처리합니다.

```js
function RootBoundary() {
  const error = useRouteError();

  if (isRouteErrorResponse(error)) {
    if (error.status === 404) {
      return <div>This page doesn't exist!</div>;
    }

    if (error.status === 401) {
      return <div>You aren't authorized to see this</div>;
    }

    if (error.status === 503) {
      return <div>Looks like our API is down</div>;
    }

    if (error.status === 418) {
      return <div>🫖</div>;
    }
  }

  return <div>Something went wrong</div>;
}
```

### #abstractions; 추상화

데이터를 계속 로드할 수 없다고 판단되어 throw하는 패턴은 예외 상황을 매우 간단하게 처리할 수 있도록 합니다.

승인된 web token을 가져오는 함수가 있습니다.

```js
async function getUserToken() {
  const token = await getTokenFromWebWorker();
  if (!token) {
    throw new Response("", { status: 401 });
  }
  return token;
}
```

`getUserToken`을 사용하는 loader, action이 어떤 것이든 현재 호출 스택에서 코드 실행을 중지하고 애플리케이션을 오류 경로로 보낼 것입니다.

이제 프로젝트를 가져오는 함수를 추가해봅시다.

```js
function fetchProject(id) {
  const token = await getUserToken();
  const response = await fetch(`/projects/${id}`, {
    headers: { Authorization: `Bearer ${token}` },
  });

  if (response.status === 404) {
    throw new Response("Not Found", { status: 404 });
  }

  // fetch 실패
  if (!response.ok) {
    throw new Error("Could not fetch project");
  }
}
```

`getUserToken`덕분에 이 코드는 web token을 받는다고 가정할 수 있습니다.

- web token이 없으면 오류 경로로 렌더링됩니다.
- 그런 다음 프로젝트가 존재하지 않으면 어떤 loader가 이 함수를 호출하든 404를 elementError로 throw합니다.
- 마지막으로 fetch가 실패하면 오류가 발생합니다.

필요한 것이 없다고 생각된다면 간단하게 throw할 수 있고, 언제든지 최종 사용자에게 유용한 내용을 렌더링한다는 것을 알 수 있습니다.

이를 하나의 경로로 정리해보도록 하겠습니다.

```js
<Route path="/" element={<Root />} errorElement={<RootBoundary />}>
  <Route
    path="projects/:projectId"
    loader={({ params }) => fetchProject(params.projectId)}
    element={<Project />}
  />
</Route>
```

project 라우트는 오류에 대해 전혀 생각할 필요없습니다. `fetchProject`, `getUserToken`와 같은 loader 유틸리티 함수가 잘못되었다고 판단될 때마다 throw하고, `rootBoundary`가 모든 경우를 처리하기 때문에 프로젝트 라우트는 긍정적인 경로에만 집중할 수 있습니다.
