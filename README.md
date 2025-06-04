React 19 introduces some updates, but the fundamentals of React Hooks remain the same. Hooks are functions that let you use **state** and other React features in **function components**.

Let’s go **step by step** through the **most important React 19 hooks** (and a few new ones). I'll explain each hook with **code examples** and use cases.

---

## 🔰 Step 1: `useState`

Used to **store local state** in a component.

### Example:

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

### How it works:

* `useState(0)` creates a state variable called `count`, starting at `0`.
* `setCount` updates `count`.

---

## 🔰 Step 2: `useEffect`

Used for **side effects** (e.g. fetching data, timers).

### Example:

```jsx
import { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  return <p>Time: {seconds}s</p>;
}
```

### Key Points:

* Runs **after** render.
* The empty array `[]` means run only once (like `componentDidMount`).
* Return function is for **cleanup** (like `componentWillUnmount`).

---

## 🔰 Step 3: `useRef`

Used to **reference a DOM node** or store a **mutable value** without causing re-renders.

### Example:

```jsx
import { useRef } from 'react';

function FocusInput() {
  const inputRef = useRef();

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus</button>
    </>
  );
}
```

---

## 🔰 Step 4: `useMemo`

Used to **optimize performance** by memoizing expensive calculations.

### Example:

```jsx
import { useMemo } from 'react';

function ExpensiveComponent({ num }) {
  const result = useMemo(() => {
    console.log('Calculating...');
    return num * 1000;
  }, [num]);

  return <p>Result: {result}</p>;
}
```

---

## 🔰 Step 5: `useCallback`

Used to memoize a **function** so it's not recreated on every render.

### Example:

```jsx
import { useCallback, useState } from 'react';

function Button({ onClick }) {
  return <button onClick={onClick}>Click Me</button>;
}

function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  return (
    <>
      <Button onClick={handleClick} />
      <p>Count: {count}</p>
    </>
  );
}
```

---

## 🔰 Step 6: `useContext`

Used to access a **context value** without prop drilling.

### Example:

```jsx
import { useContext, createContext } from 'react';

const ThemeContext = createContext('light');

function ThemedComponent() {
  const theme = useContext(ThemeContext);
  return <p>Theme: {theme}</p>;
}

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedComponent />
    </ThemeContext.Provider>
  );
}
```

---

## 🆕 React 19+ Hooks Updates (if you're using experimental or newer features)

### `useTransition` (for deferring updates):

```jsx
import { useTransition, useState } from 'react';

function Search() {
  const [input, setInput] = useState('');
  const [query, setQuery] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    setInput(e.target.value);
    startTransition(() => {
      setQuery(e.target.value);
    });
  };

  return (
    <>
      <input value={input} onChange={handleChange} />
      {isPending ? <p>Loading...</p> : <p>Results for: {query}</p>}
    </>
  );
}
```

---

## 🧠 The `use()` hook — this is a **new hook** introduced in **React 18 (and extended in React 19)**. It’s **experimental**, and used primarily in **React Server Components (RSC)**.

---

## ⚠️ What is `use()`?

The `use()` hook allows you to **unwrap a promise**, **load async data**, or **read context** in **Server Components**.

```tsx
// Only works in Server Components
const data = use(fetchData());
```

---

## ✅ Key Use Cases

### 1. **Unwrap a Promise (Server Components)**

```tsx
// Server Component
import { use } from 'react';

async function getUser() {
  const res = await fetch('https://api.example.com/user');
  return res.json();
}

export default function UserProfile() {
  const user = use(getUser());

  return <h1>Hello, {user.name}!</h1>;
}
```

* `getUser()` returns a Promise
* `use()` **suspends rendering** until the Promise resolves
* Only works in **Server Components** (not Client Components)

---

### 2. **Use Async Context (Experimental)**

React 19 lets you read a context that’s async:

```tsx
// server-context.ts
import { createContext } from 'react';

export const ThemeContext = createContext(Promise.resolve('dark'));
```

```tsx
// app.tsx (Server Component)
import { use } from 'react';
import { ThemeContext } from './server-context';

export default function Theme() {
  const theme = use(ThemeContext);
  return <p>Theme: {theme}</p>;
}
```

---

### ❌ Where NOT to use `use()`

* ❌ In **Client Components**
* ❌ Inside **event handlers**
* ❌ With non-promise values

It’s designed for **Server Components only**, and is different from `useState`, `useEffect`, etc.

---

## 🔥 Summary

| Feature       | `use()`                           |
| ------------- | --------------------------------- |
| Available in  | Server Components (React 18+)     |
| Purpose       | Await promises during render      |
| Works with    | Promises, async context, Suspense |
| Do not use in | Client Components or side effects |

---

## ✅ Want to try a live example?

You’ll need:

* Next.js with **App Router** (`app/` directory)
* React 18+ or 19 experimental build

---

