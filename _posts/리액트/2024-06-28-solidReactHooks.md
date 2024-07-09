---
title: 리액트 SOLID React Hooks 작성 (번역)
date: 2024-06-28 13:24:00 +09:00
categories: ["프레임워크", "리액트"]
tags: []
toc: true
toc_sticky: true
---

원문: [https://www.perssondennis.com/articles/write-solid-react-hooks](https://www.perssondennis.com/articles/write-solid-react-hooks)

# SOLID React Hooks 작성

SOLID는 가장 일반적으로 사용되는 디자인 패턴 중 하나입니다. 많은 언어와 프레임워크에서 일반적으로 사용되고 있으며 React에서 사용하는 방법에 대한 기사도 몇 개 있습니다.

SOLID에 관한 각 React 문서는 모델을 약간 다른 방식으로 제시합니다. 일부는 컴포넌트에 적용하고 다른 일부는 TypeScript에 적용하지만, 그 중 후크에 원칙을 적용한 문서는 거의 없습니다.

Hooks는 React의 기반의 일부이므로 여기서는 SOLID 원칙이 Hooks에 어떻게 적용되는지 살펴보겠습니다.

## 단일 책임 원칙 (SRP)

SOLID의 첫 글자인 S는 가장 이해하기 쉬운 글자입니다. 본질적으로 이는 하나의 후크/컴포넌트가 한 가지 작업을 수행하도록 한다는 의미입니다.

```
// Single Responsibility Principle
A module should be responsible to one, and only one, actor
```

```
// 단일 책임 원칙
모듈은 단 하나의 actor만 담당해야함
```

예를 들어, 아래의 useUser 훅을 보면 user와 todoTask를 가져와서 사용자 객체에 합칩니다.

```js
import { useState } from "react";
import { getUser, getTodoTasks } from "somewhere";

const useUser = () => {
  const [user, setUser] = useState();
  const [todoTasks, setTodoTasks] = useState();

  useEffect(() => {
    const userInfo = getUser();
    setUser(userInfo);
  }, []);

  useEffect(() => {
    const tasks = getTodoTasks();
    setTodoTasks(tasks);
  }, []);

  return { ...user, todoTasks };
};
```

훅은 단일 책임 원칙을 준수하지 않기때문에 SOLID하지 않습니다. 이는 user 데이터를 가져오고, todoTask 데이터를 가져오는 작업을 수행하는 책임이 있기 때문입니다.

대신 위 코드는 user 데이터를 가져오는 훅, todoTask를 가져오는 훅으로 나눠야합니다.

```js
import { useState } from "react";
import { getUser, getTodoTasks } from "somewhere";

// useUser hook is no longer responsible for the todo tasks.
const useUser = () => {
  const [user, setUser] = useState();

  useEffect(() => {
    const userInfo = getUser();
    setUser(userInfo);
  }, []);

  return { user };
};

// Todo tasks do now have their own hook.
// The hook should actually be in its own file as well. Only one hook per file!
const useTodoTasks = () => {
  const [todoTasks, setTodoTasks] = useState();

  useEffect(() => {
    const tasks = getTodoTasks();
    setTodoTasks(tasks);
  }, []);

  return { todoTasks };
};
```

이 원칙은 모든 훅과 요소에 적용되며, 모두 각각 한 가지의 작업만 수행해야합니다. 스스로 생각해볼 사항:

1. UI(프레젠테이션)를 보여줘야하는 요소인가요? 데이터(로직)를 처리해야하는 요소인가요?
2. 어떤 유형의 데이터를 처리하는 훅인가요?
3. 훅/컴포넌트는 어떤 레이어에 속하나요? 데이터를 처리하나요? 아니면 UI의 일부인가요?

위의 모든 질문에 같은 답변을 하지 못하는 훅을 사용하고 있다면 단일 책임 원칙을 위반하는 것입니다.  
여기서 주목해야 할 흥미로운 점은 첫번째 질문입니다. 이는 실제로 UI를 렌더링하는 컴포넌트가 데이터도 처리해서는 안된다는 것을 의미합니다. 즉, 이 원칙을 엄격하게 따르려면 데이터를 표시하는 각 컴포넌트에 로직과 데이터를 처리할 수 있는 훅이 있어야합니다. 즉, 데이터를 표시하는 동일한 컴포넌트에서 데이터를 가져오면 안됩니다.

### 리액트에서 SRP를 사용하는 이유?

이 단일 책임 원칙은 실제로 리액트와 잘 어울립니다. 리액트는 컴포넌트 기반 아키텍처를 따릅니다. 즉, 함께 구성된 작은 컴포넌트로 구성되어 애플리케이션을 구축하고 형성할 수 있습니다. 컴포넌트가 작을수록 재사용 가능성이 높아집니다. 이는 컴포넌트와 훅 모두에 적용됩니다.  
이러한 이유로 리액트는 단일 책임 원칙에 어느 정도 기반을 두고 있습니다. 이를 따르지 않으면 항상 새로운 훅과 컴포넌트를 작성하고 어느 것도 재사용하지 않을 것입니다.  
단일 책임 원칙을 따르지 않으면 코드가 테스트하기가 어려워집니다. 또한 수백 줄에서 최대 1000줄의 코드의 테스트 파일을 발견할 수 있습니다.

## 개방/폐쇄 원칙 (OCP)

SOLID의 다음 글자인 Open/Closed 원칙을 계속 알아보겠습니다. OCP는 SRP와 마찬가지로 이해하기 쉬운 원칙 중 하나입니다.

```
// Open/Closed Principle
Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification
```

```
// 개방/폐쇄 원칙
소프트웨어 엔티티(클래스, 모듈, 함수 등)는 확장에 열려 있어야 하지만 수정에는 닫혀 있어야 함
```

최근 리액트를 시작한 초보자를 위해 번역해보면:

```
Write hooks/component which you never will have a reason to touch again, only re-use them in other hooks/components
```

```
훅/컴포넌트를 작성할 때 다시는 수정하지 않도록, 재사용할 수 있도록 작성해야함
```

이 글의 앞부분에서 단일 책임 원칙에 대해 언급한 내용을 다시 생각해보세요. 리액트에서는 작은 컴포넌트를 작성하고 함께 구성해야 합니다. 이것이 왜 도움이 되는지 살펴보겠습니다.

```js
import { useState } from "react";
import { getUser, updateUser } from "somewhere";

const useUser = ({ userType }) => {
  const [user, setUser] = useState();

  useEffect(() => {
    const userInfo = getUser();
    setUser(userInfo);
  }, []);

  const updateEmail = (newEmail) => {
    if (user && userType === "admin") {
      updateUser({ ...user, email: newEmail });
    } else {
      console.error("Cannot update email");
    }
  };

  return { user, updateEmail };
};
```

위의 훅은 user를 가져와 반환합니다. user 유형이 관리자인 경우에는 사용자는 이메일을 업데이트할 수 있습니다. 일반 유저는 이메일을 업데이트할 수 없습니다.  
위의 코드로는 당신은 해고되지 않을 것입니다. 하지만 아이들에게 자기전에 디자인 패턴 책을 읽어주는 백엔드 담당자는 Pete는 짜증이 날 것입니다.  
Pete는 무엇에 대해 불평할까요? 그는 아래와 같이 구성 요소를 다시 작성하도록 요청합니다. 관리자의 기능을 useAdmin 훅으로 옮기고, 일반 유저가 사용하는 기능만 useUser 훅에 남겨둬야합니다.

```js
import { useState } from "react";
import { getUser, updateUser } from "somewhere";

// useUser does now only return the user,
// without any function to update its email.
const useUser = () => {
  const [user, setUser] = useState();

  useEffect(() => {
    const userInfo = getUser();
    setUser(userInfo);
  }, []);

  return { user };
};

// A new hook, useAdmin, extends useUser hook,
// with the additional feature to update its email.
const useAdmin = () => {
  const { user } = useUser();

  const updateEmail = (newEmail) => {
    if (user) {
      updateUser({ ...user, email: newEmail });
    } else {
      console.error("Cannot update email");
    }
  };

  return { user, updateEmail };
};
```

Pete가 이 업데이트를 요청한 이유는 무엇인가요? 무례하고 까다로운 Pete는 나중에 다른 유형의 유저가 생겼을 때 If문으로 코드를 업데이트하는 대신 지금 훅을 다시 작성하고 내일 다시 코드리뷰를 하기를 원하기 때문입니다.  
부정적으로 말하면 그렇지만, 긍정적으로 표현하자면 새로운 useAdmin 훅을 사용하면 관리자 유저에게만 영향을 미치는 기능을 구현하거나 새로운 유형의 유저를 추가할 때 useUser 훅은 아무것도 변경하지 않아도 된다는 것입니다.  
새로운 유저 유형이 추가되거나 useAdmin 훅이 업데이트되면 UseUser 훅을 조작하거나 테스트를 업데이트할 필요가 없습니다. 즉, 새로운 유저 유형을 추가할 때 실수로 일반 유저에게 버그를 전달할 필요가 없습니다. 대신 새로운 userFakeUser 훅을 추가하면 고객이 급여 주말에 은행 계좌에 가짜 데이터가 표시되는 문제를 경험하기 때문에 상사가 금요일 오후 9시에 전화하지 않습니다.

![https://www.perssondennis.com/_next/image?url=/images/articles/write-solid-react-hooks/frontend-developer-under-the-bed.webp&w=750&q=75](https://www.perssondennis.com/_next/image?url=/images/articles/write-solid-react-hooks/frontend-developer-under-the-bed.webp&w=750&q=75)
_Pete의 아들은 스타게티 코드 개발자를 조심하는 법을 알고 있습니다._

### 리액트에서 OCP를 사용하는 이유?

리액트 프로젝트에 몇 개의 훅과 컴포넌트가 있어야하는지 논쟁의 여지가 있습니다. 각각 컴포넌트에는 렌더링 비용이 따릅니다. 리액트는 간단한 투두 목록 구현을 위해 22개의 디자인 패턴이 422개의 클래스로 이어지는 자바가 아닙니다. 그것이 Wild West Web(www)의 아름다움입니다.  
그러나 개방/폐쇄 원칙은 리액트에서도 사용할 수 있는 몇 안되는 패턴입니다. 위의 예시는 최소한으로 훅이 사용되었고, 많은 일을 하지 않았습니다. 더 본질적인 훅과 더 큰 프로젝트에서는 이 원칙 매우 중요해집니다.  
추가 훅이 필요하고 구현하는 데 오래 걸릴 수 있지만, 훅이 확장 가능해져서 더 자주 재사용할 수 있습니다. 테스트를 다시 작성해야하는 횟수가 적어지기 때문에 훅이 더 견고해집니다. 그리고 가장 중요한 것은 이전 코드를 건드리지 않으면 버그를 만들지 않는다는 것입니다.

![https://www.perssondennis.com/_next/image?url=/images/articles/write-solid-react-hooks/dont-touch-what-is-not-broken.webp&w=750&q=75](https://www.perssondennis.com/_next/image?url=/images/articles/write-solid-react-hooks/dont-touch-what-is-not-broken.webp&w=750&q=75)
_하느님은 깨지지 않은 것에는 손을 대지 않는다_

## Liskov 대체 원칙 (LSP)

아아, 이름이... 리스코프는 누굴까요? 그리고 그녀를 대체할 사람은 누구일까요? 그 정의는 말이 되기나 하나요?

```
If S subtypes T, what holds for T holds for S
```

```
S가 T의 하위타입이라면, T에 대해 유지되는 것은 S에도 유지됨
```

이 원칙은 상속에 관한 것입니다. 이는 리액트와 자바스크립트에서 대부분의 백엔드 언어만큼 자연스럽게 사용되지는 않습니다. 자바스크립트는 ES6가 등장하기 전까지 클래스조차 없었으며, 이는 프로토타입 기반 상속에 대한 문법적 설탕으로 [2015/2016년에 도입](https://caniuse.com/?search=class)되었습니다.

이 점을 염두에 두고, 이 원칙의 사용 사례는 실제로 코드의 모양에 따라 달라집니다. 리액트에서 이해되는 Liskov와 유사한 원칙은 다음과 같습니다.

```
If a hook/component accepts some props, all hooks and components which extends that hook/component must accept all the props the hook/component it extends accepts. The same goes for return values.
```

```
어떤 훅/컴포넌트가 Props를 받아야 한다면, 훅/컴포넌트를 확장하는 모든 훅/컴포넌트는 확장하는 훅/컴포넌트가 받는 모든 props를 받아야 한다. 반환값도 마찬가지이다.
```

이를 설명하기 위해 useLocalStorage와 useLocalAndRemoteStorage라는 두 개의 훅을 살펴보겠습니다.

```js
import { useState } from "react";
import {
  getFromLocalStorage,
  saveToLocalStorage,
  getFromRemoteStorage
} from "somewhere";

// useLocalStorage gets data from local storage.
// When new data is stored, it calls saveToStorage callback.
const useLocalStorage = ({ onDataSaved }) => {
  const [data, setData] = useState();

  useEffect(() => {
    const storageData = getFromLocalStorage();
    setData(storageData);
  }, []);

  const saveToStorage = (newData) => {
    saveToLocalStorage(newData);
    onDataSaved(newData);
  };

  return { data, saveToStorage };
};

// useLocalAndRemoteStorage gets data from local and remote storage.
// I doesn't have callback to trigger when data is stored.
const useLocalAndRemoteStorage = () => {
  const [localData, setLocalData] = useState();
  const [remoteData, setRemoteData] = useState();

  useEffect(() => {
    const storageData = getFromLocalStorage();
    setLocalData(storageData);
  }, []);

  useEffect(() => {
    const storageData = getFromRemoteStorage();
    setRemoteData(storageData);
  }, []);

  const saveToStorage = (newData) => {
    saveToLocalStorage(newData);
  };

  return { localData, remoteData, saveToStorage };
};
```

위의 훅을 예로 들면, useLocalAndRemoteStorage는 useLocalStorage의 하위 타입으로 볼 수 있습니다. 이는 useLocalStorage와 동일하게 로컬 스토리지에 저장하는 기능을 수행하면서, 추가로 데이터를 다른 곳에도 저장하는 기능을 확장했기 때문입니다.

두 훅은 일부 공통된 props와 반환값을 공유하지만, useLocalAndRemoteStorage는 useLocalStorage가 받는 onDataSaved 콜백 props를 포함하지 않습니다. 반환값의 이름도 다릅니다. useLocalStorage에서 로컬 데이터는 data로 되어 있지만, useLocalAndRemoteStorage에서는 localData로 되어 있습니다.

Liskov에게 물어본다면, 이것은 그녀의 원칙을 어기는 것이라고 말할 것입니다. 데이터를 서버측에도 저장하기 위해 웹 애플리케이션을 업데이트하려 할 때, 단순히 useLocalStorage를 useLocalAndRemoteStorage 훅으로 교체할 수 없다는 것을 깨달았은 그녀는 매우 화가 날 것입니다. 이는 일부 게으른 개발자가 useLocalAndRemoteStorage 훅에 onDataSaved 콜백을 구현하지 않았기 때문입니다.  
Liskov는 훅을 지원하도록 업데이트할 것입니다. 동시에, useLocalAndRemoteStorage의 로컬 데이터 이름과 일치하도록 useLocalStorage 훅의 로컬 데이터 이름도 업데이트할 것입니다.

```js
import { useState } from "react";
import {
  getFromLocalStorage,
  saveToLocalStorage,
  getFromRemoteStorage
} from "somewhere";

// Liskov has renamed data state variable to localData
// to match the interface (variable name) of useLocalAndRemoteStorage.
const useLocalStorage = ({ onDataSaved }) => {
  const [localData, setLocalData] = useState();

  useEffect(() => {
    const storageData = getFromLocalStorage();
    setLocalData(storageData);
  }, []);

  const saveToStorage = (newData) => {
    saveToLocalStorage(newData);
    onDataSaved(newData);
  };

  // This hook does now return "localData" instead of "data".
  return { localData, saveToStorage };
};

// Liskov also added onDataSaved callback to this hook,
// to match the props interface of useLocalStorage.
const useLocalAndRemoteStorage = ({ onDataSaved }) => {
  const [localData, setLocalData] = useState();
  const [remoteData, setRemoteData] = useState();

  useEffect(() => {
    const storageData = getFromLocalStorage();
    setLocalData(storageData);
  }, []);

  useEffect(() => {
    const storageData = getFromRemoteStorage();
    setRemoteData(storageData);
  }, []);

  const saveToStorage = (newData) => {
    saveToLocalStorage(newData);
    onDataSaved(newData);
  };

  return { localData, remoteData, saveToStorage };
};
```

공통 인터페이스(props, 반환값)을 훅에 적용하면, 훅을 매우 쉽게 변경할 수 있습니다. 그리고 Liskov원칙을 따라야 한다면, 다른 훅/컴포넌트를 상속하는 훅/컴포넌트는 상속하는 훅/컴포넌트로 교체할 수 있어야합니다.

![https://www.perssondennis.com/_next/image?url=/images/articles/write-solid-react-hooks/worried-liskov.webp&w=640&q=75](https://www.perssondennis.com/_next/image?url=/images/articles/write-solid-react-hooks/worried-liskov.webp&w=640&q=75)
_Liskov는 개발자가 자신의 원칙을 따르지 않을 때 실망합니다._

### 리액트에서 LSP를 사용하는 이유?

리액트에서는 상속이 많이 사용되지는 않지만, 내부에서는 확실히 사용됩니다. 웹 애플리케이션에서는 종종 비슷해보이는 컴포넌트들이 많이 있습니다. 텍스트, 제목, 링크, 아이콘 링크 등은 모두 비슷한 유형의 컴포넌트 이며 상솟을 통해 이점을 얻을 수 있습니다.  
IconLink 컴포넌트는 Link 컴포넌트를 감쌀 수도 있고 감싸지 않을 수도 있습니다. 동일한 인터페이스(동일한 props) 구현된다면, 애플리케이션 어느 곳에서나 Link 컴포넌트를 IconLink 컴포넌트로 교체하는 것이 아주 간단해지며 추가적으로 코드를 수정하지 않아도 됩니다.  
훅도 마찬가지 입니다. 웹 애플리케이션은 서버에서 데이터를 가져옵니다. 로컬 스토리지나 상태 관리 시스템을 사용할 수도 있습니다. 이러한 경우에도 props를 공유하여 상호 교환 가능하게 하는 것이 좋습니다.  
애플리케이션은 백엔드 서버에서 사용자, 작업, 제품 또는 기타 데이터를 가져올 수 있습니다. 이러한 함수들도 인터페이스를 공유함으로써 코드와 테스트를 재사용할 수 있습니다.

### 인터페이스 분리 원칙 (ISP)

좀 더 명확한 또 다른 원칙은 인터페이스 분리 원칙입니다. 정의는 아주 짧습니다.

```
No code should be forced to depend on methods it does not use
```

```
사용하지 않는 메소드에 의존하도록 코드를 강제하면 안된다.
```

이름에서 알 수 있듯이, 이것은 인터페이스와 관련이 있습니다. 기본적으로 함수와 클래스는 명시적으로 사용하는 인터페이스만 구현해야 한다는 것을 의미합니다. 인터페이스를 깔끔하게 유지하고, 클래스가 여러 개의 메소드가 있는 하나의 큰 인터페이스를 구현하도록 강요받는 대신에 몇 개를 선택해서 구현하도록 하는 것이 가장 쉬운 방법입니다.  
예를 들어, 웹 사이트를 소유한 사람을 나타내는 클래스는 두 개의 인터페이스, 즉 개인에 대한 세부 정보를 설명하는 Person이라는 인터페이스와 소유한 웹 사이트에 대한 메타데이터가 있는 웹 사이트에 대한 또 다른 인터페이스를 구현해야합니다.

```js
interface Person {
  firstName: string
  familyName: string
  age: number
}

interface Website {
  domain: string
  type: string
}
```

대신 소유자와 웹사이트에 대한 정보를 모두 포함하는 단일 인터페이스 웹사이트를 만들면 인터페이스 분리 원칙을 위반하는 것입니다.

```js
interface Website {
  ownerFirstName: string
  ownerFamilyName: number
  domain: string
  type: string
}
```

위의 인터페이스에 어떤 문제가 있을까요? 문제는 인터페이스를 덜 사용하게 만든다는 점입니다. 생각해보세요. 만약 human이 아니라 company라면 어떻게 하겠습니까? company는 실제로 family name(성)이 없습니다. 그러면 이 인터페이스를 human과 company 모두 사용할 수 있도록 수정하시겠습니까? 아니면 CompanyOwnedWebsite라는 새로운 인터페이스를 만들겠습니까?

그러면 선택적 프로퍼티가 많은 인터페이스, 또는 각각 또는 각각 PersonWebsite와 CompanyWebsite라는 두 개의 인터페이스가 생깁니다. 이 두 가지 솔루션 중 어느 것도 최적이 아닙니다.

```js
// Alternative 1

// This interface has the problem that it includes
// optional attributes, even though the attributes
// are mandatory for some consumers of the interface.
// 이 인터페이스는 문제가 있습니다.
// 몇몇 사용자에게 필수적인 속성임에도 불구하고, 선택적 속성을 포함하고 있습니다.
interface Website {
  companyName?: string
  ownerFirstname?: string
  ownerFamilyName?: number
  domain: string
  type: string
}

// Alternative 2

// This is the original Website interface renamed for a person.
// Which means, we had to update old code and tests and
// potentially introduce some bugs.
// 이것은 웹사이트의 인터페이스가 사람에 맞게 이름이 변경된 것입니다.
// 이전 코드와 테스트를 업데이트 해야했고, 잠재적으로 몇 개의 버그가 발생할 수 있습니다.
interface PersonWebsite {
  ownerFirstname: string
  ownerFamilyName: number
  domain: string
  type: string
}

// This is a new interface to work for a company.
interface CompanyOwnedWebsite {
  companyName: string
  domain: string
  type: string
}
```

ISP를 따르는 솔루션은 다음과 같습니다.

```js
interface Person {
  firstName: string
  familyName: string
  age: number
}

interface Company {
  companyName: string
}

interface Website {
  domain: string
  type: string
}
```

위의 적절한 인터페이스를 사용하면 회사 웹 사이트를 나타내는 클래스가 Company 및 Website 인터페이스를 구현할 수 있지만 Person 인터페이스의 ownerFirstname 및 familyName 속성을 고려할 필요가 없습니다.

### 리액트에서 ISP를 사용하나요?

따라서 이 원칙은 분명히 인터페이스에 적용됩니다. 즉, 타입스크립트를 사용하여 리액트 코드를 작성하는 경우에만 관련되어야 한다는 뜻입니다. 그렇죠?

당연히 아닙니다. 인터페이스를 타이핑하지 않는다고 해서 인터페이스가 없는 것은 아닙니다. 인터페이스는 어디에나 있지만 명시적으로 타이핑하지 않는 것뿐입니다.

리액트에서 각 컴포넌트와 훅에는 입력과 출력이라는 두 가지의 주요 인터페이스가 있습니다.

```js
// The input interface to a hook is its props.
// 훅에 대한 입력은 props
const useMyHook = ({ prop1, prop2 }) => {
  // ...

  // The output interface of a hook is its return values.
  // 훅의 출력은 반환값
  return { value1, value2, callback1 };
};
```

타입스크립트를 사용하면 일반적으로 입력 인터페이스를 입력하지만, 출력 인터페이스는 선택사항이기 때문에 종종 건너뜁니다.

```js
// Input interface.
// 입력 인터페이스
interface MyHookProps {
  prop1: string
  prop2: number
}

// Output interface.
// 출력 인터페이스
interface MyHookOutput {
  value1: string
  value2: number
  callback1: () => void
}

const useMyHook = ({ prop1, prop2 }: MyHookProps): MyHookOutput => {

  // ...

  return { value1, value2, callback1 }
}
```

훅이 prop2를 어떤 용도로도 사용하지 않으면, props에 작성되면 안됩니다. 단일 Prop의 경우 props의 목록과 인터페이스에서 제거하는 것이 쉬울 것입니다. 하지만 prop2가 객체 유형이라면 어떨까요?  
이전 챕터의 부적절한 Website 인터페이스 예시는 어떨까요?

```js
interface Website {
  companyName?: string
  ownerFirstname?: string
  ownerFamilyName?: number
  domain: string
  type: string
}

interface MyHookProps {
  prop1: string
  website: Website
}

const useMyCompanyWebsite = ({ prop1, website }: MyHookProps) => {

  // This hook uses domain, type and companyName,
  // but not ownerFirstname or ownerFamilyName.
  // 이 훅은 domain, type and companyName을 사용합니다.
  // 하지만 ownerFirstname 또는 ownerFamilyName은 사용하지 않습니다.

  return { value1, value2, callback1 }
}
```

이제 website prop이 있는 useMyCompanyWebsite가 있습니다. Website 인터페이스의 일부가 후크에서 사용되는 경우 전체 website prop을 간단히 제거할 수 없습니다. website prop을 유지해야 하며, 따라서 ownerFirstname과 ownerFamiliyName에 대한 인터페이스 prop도 유지해야 합니다. 즉, 이 후크가 회사를 위해 의도된 것이 사람이 소유한 웹사이트 소유자가 사용할 수 있다는 것을 의미하지만, 이 후크는 해당 용도에 적합하지 않을 가능성이 큽니다.

### 리액트에서 ISP를 사용하는 이유?

이제 ISP가 무엇을 의미하는지, 그리고 타입스크립트를 사용하지 않고도 리액트에서 어떻게 적용되는지 알아보았습니다. 위의 예시만 봐도 ISP를 따르지 않으면 몇 가지의 문제가 발생하는지 알 수 있습니다.

더 복잡한 프로젝트에서는 가독성이 가장 큰 문제입니다. 인터페이스 분리 원칙의 목적 중 하나는 가독성을 방해할 뿐인 불필요한 코드의 존재, 즉 혼란을 피하는 것입니다. 그리고 테스트 가능성도 잊으면 안됩니다. 실제로 사용하지 않는 props의 테스트 범위에 대해 신경 써야 할까요?

대규모 인터페이스를 구현하면 Props를 선택적으로 만들어야 합니다. 함수가 그러한 속성을 처리하는 것처럼 인터페이스에 나타나기 때문에 함수의 존재 여부와 잠재적인 오용을 확인하기 위해 더 많은 if문이 필요합니다.

## 의존성 역전 원리(DIP)

마지막 원칙인 DIP에는 상당히 오해가 많은 용어가 포함되어 있습니다. 의존성 반전, 의존성 주입, 제어의 역전의 차이가 무엇인지에 관한 것입니다. 먼저 용어들을 정의해보겠습니다.

#### 의존성 역전

의존성 역전 원칙(DIP)은 고수준 모듈이 저수준 모듈로부터 아무것도 가져오지 말아야 하며, 둘 다 추상화에 의존해야 한다고 말합니다. 이는 고수준 모듈이 자연스럽게 사용할 모듈의 구현 세부 사항에 의존할 수 있는 경우에도, 그러한 의존성이 없어야 한다는 것을 의미합니다.

고수준 모듈과 저수준 모듈은 서로의 내부 구현 세부 사항을 알지 못해도 사용할 수 있도록 작성되어야 합니다. 각 모듈은 인터페이스가 동일한 한, 대체 가능한 다른 구현으로 교체할 수 있어야 합니다.

#### 제어의 역전

제어의 역전(IoC)은 의존성 역전 문제를 해결하기 위해 사용되는 원칙입니다. 이는 모듈의 의존성이 외부 개체나 프레임워크에서 제공되어야 한다고 말합니다. 이렇게 하면 모듈 자체는 의존성을 사용하기만 하면 되며, 의존성을 생성하거나 어떤 방식으로든 관리할 필요가 없습니다.

#### 의존성 주입

의존성 주입(DI)은 IoC를 구현하는 일반적인 방법 중 하나입니다. 이는 의존성을 생성자나 setter 메서드를 통해 모듈에 주입함으로써 의존성을 제공합니다. 이 방법으로 모듈은 의존성을 생성하는 책임을 지지 않고도 사용할 수 있으며, 이는 IoC 원칙을 준수하는 방법입니다. 언급할 만한 점은 의존성 주입이 제어의 역전을 달성하는 유일한 방법은 아니라는 것입니다.

### 리액트에서 DIP가 사용되나요?

위에서 용어들을 명확히 한 후에 DIP 원칙이 의존성 역전에 관한 것임을 알고 나면, 그 정의를 다시 살펴볼 수 있습니다.

```
High-level modules should not import anything from low-level modules. Both should depend on abstractions
```

```
고수준 모듈은 저수준 모듈에서 아무 것도 가져오지 않아야 하며, 둘 다 추상화에 의존해야 합니다.
```

이것이 React에 어떻게 적용되는지 생각해보겠습니다. React는 일반적으로 의존성 주입과 관련이 있는 라이브러리가 아니기 때문에, 의존성 역전 문제를 어떻게 해결할 수 있을까요?

이 문제를 해결하는 가장 일반적인 방법은 훅입니다. 훅은 컴포넌트에 하드코딩되어 있으며, 컴포넌트의 구현을 변경하지 않고 다른 훅으로 교체하는 것은 불가능합니다. 개발자가 코드를 업데이트할 때까지 같은 훅이 동일한 인스턴스로 유지됩니다.

그러나 의존성 주입이 의존성 역전을 달성하는 유일한 방법은 아닙니다. 훅은 React 컴포넌트에 대한 외부 의존성으로 볼 수 있으며, 그 인터페이스(프롭스)를 통해 훅 내부의 코드를 추상화합니다. 이 방식으로 훅은 컴포넌트가 훅의 세부 사항을 알 필요없이 추상화된 인터페이스에 의존하게 되므로, 의존성 역전 원칙을 일종으로 구현한다고 볼 수 있습니다.

React에서 DIP를 더 직관적으로 구현하는 다른 방법으로는 HOCs(Higher-Order Components, 고차 컴포넌트)와 컨텍스트를 사용하는 것이 있습니다. 아래의 withAuth HOC를 살펴보세요.

```js
const withAuth = (Component) => {
  return (props) => {
    const { user } = useContext(AuthContext)

    if (!user) {
      return <LoginComponent>
    }

    return <Component {...props} user={user} />
  }
}

const Profile = () => { // Profile component... }

// Use the withAuth HOC to inject user to Profile component.
const ProfileWithAuth = withAuth(Profile)
```

위에 나온 withAuth HOC는 의존성 주입을 통해 Profile 컴포넌트에 사용자를 제공합니다. 이 예제의 흥미로운 점은 의존성 주입의 한 가지 사용 예시를 보여줄 뿐만 아니라, 실제로 두 개의 의존성 주입을 포함하고 있다는 것입니다.

이 예제에서 Profile 컴포넌트로의 사용자 주입뿐만 아니라, withAuth 훅 자체도 useContext 훅을 통해 사용자를 의존성 주입으로 가져옵니다. 코드 어딘가에서 누군가가 컨텍스트에 사`용자를 주입하는 프로바이더를 선언했을 것입니다. 이 사용자 인스턴스는 심지어 실행 중에 컨텍스트에서 사용자를 업데이트하여 변경할 수도 있습니다.

### 리액트에서 DIP를 사용하는 이유?

비록 의존성 주입이 리액트와 일반적으로 연관되어 있는 패턴은 아니지만, HOCs와 컨텍스트를 통해 실제로 구현되어 있습니다. 또한 많은 시장 점유율을 차지한 훅도 의존성 역전 원칙과 잘 일치합니다.

따라서 DIP는 이미 리액트 라이브러리 자체에 내장되어 있으며 물론 활용되어야 합니다. 이는 사용이 간편하며, 모듈 간의 느슨한 결합, 훅과 컴포넌트의 재사용성 및 테스트 가능성과 같은 이점을 제공합니다. 또한 단일 책임 원칙과 같은 다른 디자인 패턴을 구현하는 데도 도움이 됩니다.

하지만 간단한 해결책이 충분히 존재할 때 과도하게 패턴을 사용하려는 스마트한 솔루션을 구현하는 것은 지양해야 합니다. 웹이나 책에서 리액트 컨텍스트를 의존성 주입을 구현하는 유일한 목적으로 사용하라는 제안을 본 적이 있습니다. 아래와 같은 예시가 그러한 경우입니다.

```js
const User = () => {
  const { role } = useContext(RoleContext);

  return <div>{`User has role ${role}`}</div>;
};

const AdminUser = ({ children }) => {
  return (
    <RoleContext.Provider value={{ role: "admin" }}>
      {children}
    </RoleContext.Provider>
  );
};

const NormalUser = ({ children }) => {
  return (
    <RoleContext.Provider value={{ role: "normal" }}>
      {children}
    </RoleContext.Provider>
  );
};
```

위의 예제는 역할을 User 컴포넌트에 주입하긴 하지만, 이를 위해 컨텍스트를 사용하는 것은 극도로 지나친 사용입니다. 리액트 컨텍스트는 그 자체가 목적을 제공할 때 적절히 사용해야 합니다. 이 경우에는 단순한 prop을 사용하는 것이 더 나은 해결책이었을 것입니다.

```js
const User = ({ role }) => {
  return <div>{`User has role ${role}`}</div>;
};

const AdminUser = () => <User role="admin" />;

const NormalUser = () => <User role="normal" />;
```
