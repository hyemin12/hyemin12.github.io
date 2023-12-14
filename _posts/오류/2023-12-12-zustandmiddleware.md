---
title: zustand middleware persis type 오류
date: 2023-12-12 18:24:00 +09:00
categories: ["오류해결하기"]
tags: ["오류"]
---

```
Argument of type 'StateCreator<TodoState, [], [["zustand/devtools", never], ["zustand/persist", TodoState]]>' is not assignable to parameter of type 'StateCreator<TodoState, [], []>'.
  Type 'StateCreator<TodoState, [], [["zustand/devtools", never], ["zustand/persist", TodoState]]>' is not assignable to type '{ $$storeMutators?: [] | undefined; }'.
    Types of property '$$storeMutators' are incompatible.
      Type '[["zustand/devtools", never], ["zustand/persist", TodoState]] | undefined' is not assignable to type '[] | undefined'.
        Type '[["zustand/devtools", never], ["zustand/persist", TodoState]]' is not assignable to type '[]'.
          Source has 2 element(s) but target allows only 0.
```

```
'StateCreator<TodoState, [], [["zustand/devtools", never], ["zustand/persist", TodoState]]>' 유형의 인수는 'StateCreator<TodoState, [], [ 유형의 매개변수에 할당할 수 없습니다. ]>'.
  'StateCreator<TodoState, [], [["zustand/devtools", never], ["zustand/persist", TodoState]]>' 유형은 '{ $$storeMutators?: [] | 한정되지 않은; }'.
    '$$storeMutators' 속성 유형이 호환되지 않습니다.
      '[["zustand/devtools", 절대로], ["zustand/persist", TodoState]] | 정의되지 않음'은 '[] | 유형에 할당할 수 없습니다. 한정되지 않은'.
        '[["zustand/devtools", never], ["zustand/persist", TodoState]]' 유형은 '[]' 유형에 할당할 수 없습니다.
          소스에는 2개의 요소가 있지만 대상에는 0개만 허용됩니다.
```

Persist middleware을 이용해 state를 storage에 저장할 수 있는 기능을 위해 코드를 작성하던 도중 위와 같은 오류가 발생하였다.

공식 문서를 다시 찬찬히 읽어보니...

내가 예전 코드의 예시를 보고 작성했었기 때문에 발생한 오류였다.
공식 문서의 코드대로 devtools를 삭제했더니 오류가 사라졌다... ㅠㅠ

[zustand persisting-store-data 공식문서](https://github.com/pmndrs/zustand/blob/main/docs/integrations/persisting-store-data.md)

```js
const useTodoStore = create<TodoState>()(
  persist(
    (set) => ({
      todos: initialState,
      filter: "All",
      addTodo: (todo) => set((state) => ({ todos: [...state.todos, todo] })),
      editTodo: ({ text, id }) =>
        set((state) => ({
          todos: state.todos.map((item) => (item.id === id ? { ...item, text: text } : item)),
        })),
      toggleCompleteTodo: (id) =>
        set((state) => ({
          todos: state.todos.map((item) => (item.id === id ? { ...item, complete: !item.complete } : item)),
        })),
      deleteTodo: (id) =>
        set((state) => ({
          todos: state.todos.filter((item) => item.id !== id),
        })),
      searchTodo: (query) =>
        set((state) => ({
          todos: state.todos.filter((item) => item.text.includes(query.toLowerCase())),
        })),
      filterTodo: (filter) =>
        set((state) => ({
          filter,
        })),
      deleteCompleteTodo: () =>
        set((state) => ({
          todos: state.todos.filter((item) => !item.complete),
        })),
    }),
    { name: "todo-storage", storage: createJSONStorage(() => sessionStorage) }
  )
);

export default useTodoStore;

```
