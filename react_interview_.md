# React.js Interview Q&A — Modern & Advanced 🚀

A comprehensive interview guide covering modern React concepts with Vite integration and advanced topics.

## Table of Contents
- [Core React Concepts](#core-react-concepts)
- [Component Architecture](#component-architecture)
- [React Hooks - Core](#react-hooks---core)
- [Advanced React Hooks](#advanced-react-hooks)
- [State Management Approaches](#state-management-approaches)
- [Performance Optimization](#performance-optimization)
- [Routing](#routing)
- [Testing](#testing)
- [Vite Project Structure](#vite-project-structure)
- [React Reconciliation](#react-reconciliation)
- [Virtualization](#virtualization)
- [Lazy Loading](#lazy-loading)
- [Advanced Topics](#advanced-topics)

## Core React Concepts

### Q1. What is React and why use it?
**Answer:**
React is a JavaScript library for building declarative, component-based UIs. It uses a virtual DOM for efficient rendering and supports one-way data flow.

**Key Features:**
- Declarative syntax
- Component-based architecture
- Reusable UI pieces
- Virtual DOM for performance

### Q2. What is JSX?
**Answer:**
JSX is a syntax extension that allows writing HTML-like code inside JavaScript.
```javascript
const element = <h1>Hello, React!</h1>;
```

### Q3. What is the Virtual DOM?
**Answer:**
A lightweight copy of the actual DOM stored in memory. React compares new and old virtual DOM trees (diffing) and updates only changed parts (reconciliation).

### Q4. Functional vs Class Components
| Feature | Functional Components | Class Components |
|---------|----------------------|------------------|
| Syntax | Function | ES6 Class |
| State | useState, useReducer hooks | this.state |
| Lifecycle | Hooks (useEffect) | Lifecycle methods (componentDidMount) |
| Performance | Generally faster | Slightly heavier |

## Component Architecture

### Q5. What are props in React?
Props (properties) are read-only data passed from parent to child components.
```javascript
function Greeting({ name }) {
  return <h1>Hello {name}</h1>;
}
<Greeting name="Kiran" />
```

### Q6. What is state in React?
State is mutable data managed inside a component.
```javascript
const [count, setCount] = useState(0);
return <button onClick={() => setCount(count+1)}>{count}</button>;
```

### Q7. What is lifting state up?
Moving state to a common ancestor so it can be shared between child components.

## React Hooks - Core

**What are React Hooks?**
Hooks are special functions introduced in React 16.8 that let you "hook into" React state and lifecycle features from function components.

### Core Hooks Overview
| Hook | Usage |
|------|--------|
| useState | Manage component state |
| useEffect | Side effects (fetching, timers) |
| useRef | Access/manipulate DOM, store mutable values |
| useContext | Access context values without prop drilling |

### useState
**Purpose:** Manages local state in functional components
**Signature:** `const [state, setState] = useState(initialValue);`
**Interview Tip:** Explain how useState is asynchronous and batched for performance.

### useEffect
**Purpose:** Performs side effects after render
**Signature:** `useEffect(effectFn, [dependencies]);`
**Interview Tip:** useEffect replaces lifecycle methods like componentDidMount, componentDidUpdate, and componentWillUnmount.

## Advanced React Hooks

### Additional Hooks
| Hook | Usage |
|------|--------|
| useReducer | Complex state logic |
| useMemo | Memoize expensive computations |
| useCallback | Memoize function references |
| useLayoutEffect | DOM mutations before painting |
| useImperativeHandle | Expose imperative methods from components |

### useRef
**Purpose:** Stores mutable values that persist across renders without causing re-renders
**Interview Tip:** Highlight that updating `.current` does not trigger a re-render
**Key Points:**
- Changing .current does not trigger a render
- Ideal for persisting values between renders

### useMemo
**Purpose:** Memoizes expensive computations to avoid recalculating unless dependencies change
**Interview Tip:** useMemo is for values, not functions
**Key Points:**
- Only recomputes when dependencies change
- Prevents wasted CPU work

```javascript
Example — Optimizing Expensive Calculations
import { useState, useMemo } from 'react';

function ExpensiveComponent({ num }) {
  const expensiveValue = useMemo(() => {
    console.log("Calculating...");
    return num ** 2; // pretend it's CPU-heavy
  }, [num]);

  return <p>Squared: {expensiveValue}</p>;
}
```


### useCallback
**Purpose:** Memoizes function references to prevent unnecessary re-renders of child components
**Interview Tip:** useCallback is for functions, useMemo is for values
**Key Points:**
- Use with React.memo to keep function identity stable
- Good for callbacks passed to deeply nested children


**Example** — Prevent Child Re-Renders
```javascript
import { useState, useCallback } from 'react';

const Child = React.memo(({ onClick }) => {
  console.log("Child rendered");
  return <button onClick={onClick}>Click Child</button>;
});

function Parent() {
  const [count, setCount] = useState(0);
  const handleClick = useCallback(() => {
    console.log("Child clicked");
  }, []);

  return (
    <>
      <button onClick={() => setCount(c => c + 1)}>Parent Count {count}</button>
      <Child onClick={handleClick} />
    </>
  );
}
```
### useContext
**Purpose:** Avoids prop drilling by sharing state globally across components
```javascript
const { theme, setTheme } = useContext(ThemeContext);
```
**Interview Tip:**  
Clarify useCallback is for functions, useMemo is for values.
Purpose:
Avoids prop drilling by sharing state globally across components.

**Example** — Theme Context
```javascript
import { createContext, useContext, useState } from 'react';
const ThemeContext = createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function ThemeButton() {
  const { theme, setTheme } = useContext(ThemeContext);
  return (
    <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
      Current Theme: {theme}
    </button>
  );
}

export default function App() {
  return (
    <ThemeProvider>
      <ThemeButton />
    </ThemeProvider>
  );
}
```
### useReducer
**Purpose:** Manages complex state logic with a reducer function (like Redux)
**Interview Tip:** useReducer is preferable for related state updates or when state logic is complex
```javascript
const [state, dispatch] = useReducer(reducer, initialState);
```

**Example** — Counter Reducer
```javascript
import { useReducer } from 'react';
function reducer(state, action) {
  switch(action.type) {
    case 'increment': return { count: state.count + 1 };
    case 'decrement': return { count: state.count - 1 };
    default: return state;
  }
}
function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </>
  );
}

```
### useLayoutEffect
**Purpose:** Runs synchronously after DOM mutations, before the browser paints
**Interview Tip:** Warn about potential UI blocking if used for heavy tasks
**Key Points:**
- Use when you need to measure DOM size/position before paint
- Avoid heavy work to prevent blocking UI

**Example** — Measuring DOM Elements
```javascript
import { useLayoutEffect, useRef } from 'react';
function Box() {
  const boxRef = useRef();
  useLayoutEffect(() => {
    console.log(boxRef.current.getBoundingClientRect());
  }, []);

  return <div ref={boxRef} style={{ width: 100, height: 100, background: 'red' }} />;
}

```


### useImperativeHandle
**Purpose:** Customizes the instance value exposed to parent via ref when using forwardRef
**Interview Tip:** It's advanced and mostly used in reusable component libraries

**Example:**
```javascript
useImperativeHandle(ref, () => ({ focus: () => ... }));
```
**Interview Tip:**  
Mention it’s advanced and mostly used in reusable component libraries.
Purpose:
Customize the value exposed by ref when using forwardRef.
Example — Exposing Methods to Parent

```javascript
import { useImperativeHandle, forwardRef, useRef } from 'react';
const Input = forwardRef((props, ref) => {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus()
  }));
  return <input ref={inputRef} />;
});

function App() {
  const inputRef = useRef();
  return (
    <>
      <Input ref={inputRef} />
      <button onClick={() => inputRef.current.focus()}>Focus Input</button>
    </>
  );
}
```

### Custom Hooks
**Purpose:** Encapsulate reusable logic into functions prefixed with `use`
**Interview Tip:** Show how custom hooks help DRY (Don't Repeat Yourself) and organize code
```javascript
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);
  useEffect(() => {
    const onResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', onResize);
    return () => window.removeEventListener('resize', onResize);
  }, []);
  return width;
}
```

**General Interview Rules:**
- Hooks must be called at the top level, not inside loops or conditions
- Only call hooks from React function components or custom hooks
- Hooks rely on call order for correct behavior

## State Management Approaches
- Local state (useState)
- Context API for small global states
- Redux / Redux Toolkit for complex state
- Zustand / Jotai (modern lightweight libraries)
- Server state: React Query / SWR

## Performance Optimization
- **Memoization:** React.memo, useMemo, useCallback
- **Lazy Loading:** React.lazy, Suspense
- **Code Splitting**
- Avoid unnecessary re-renders by proper key usage

## Routing
Using React Router v6+ (works with Vite)
```javascript
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home/>} />
        <Route path="/about" element={<About/>} />
      </Routes>
    </BrowserRouter>
  );
}
```

## Testing
- **Unit Testing:** Jest + React Testing Library
- **E2E Testing:** Cypress, Playwright

## Vite Project Structure
```
my-vite-app/
├── public/
├── src/
│   ├── assets/
│   ├── components/
│   ├── hooks/
│   ├── pages/
│   ├── store/
│   ├── App.jsx
│   ├── main.jsx
├── index.html
├── package.json
├── vite.config.js
```

**Setup:**
```bash
npm create vite@latest my-vite-app -- --template react
cd my-vite-app
npm install
npm run dev
```

## React Reconciliation

**Definition:**
Reconciliation is the process by which React updates the DOM by comparing the Virtual DOM with the previous version and applying only the necessary changes.

### Q1. How does React decide to re-render a component?
- If props or state change, React triggers reconciliation
- It compares the new Virtual DOM with the old one
- Updates only changed nodes (diffing algorithm)

### Q2. How does the diffing algorithm work?
- **Element Type Different?** → Replace the whole subtree
- **Same Type, Different Props?** → Update attributes
- **Lists?** → Uses key prop to match and move elements

### Q3. Why are key props important in lists?
They help React identify which items have changed, been added, or removed to avoid unnecessary re-renders.

```javascript
// ❌ Bad Keys
{items.map((item, index) => <li key={index}>{item}</li>)}

// ✅ Good Keys
{items.map(item => <li key={item.id}>{item.name}</li>)}
```

### Q4. How does reconciliation work with hooks?
- Hooks rely on order in which they are called
- During reconciliation, React matches hooks by their call position, not names

### Q5. How to optimize reconciliation?
- Use React.memo for pure components
- Use useCallback and useMemo to avoid re-creating props/functions unnecessarily
- Provide stable key props for lists

## Virtualization

**Why react-window Improves Performance:**
- Normally, rendering large lists (1000+ items) creates all DOM nodes at once
- react-window only renders items currently visible in the viewport plus a small buffer
- As you scroll, offscreen items are unmounted and new ones are mounted dynamically

**Key Props:**
| Prop | Purpose |
|------|---------|
| height | Viewport height |
| width | Viewport width |
| itemCount | Total number of items |
| itemSize | Fixed height/width of each item |
| children | Row renderer function |

**When to Use react-window:**
- ✅ Large tables/lists (10k+ items)
- ✅ Infinite scrolling
- ✅ Chat/message windows
- ✅ Data-heavy dashboards
- ❌ Not needed for small lists (<100 items)



## 
## Virtualization

**Interview Context:** For rendering large lists (1000+ items) efficiently.

**Problem:** Rendering massive lists creates all DOM nodes, impacting performance.

**Solution:** Only render visible items + small buffer.

**When to Use:**
- ✅ Large tables/lists (10k+ items)
- ✅ Infinite scrolling
- ❌ Small lists (<100 items) - overhead not worth it

**Library:** react-window provides `FixedSizeList` and `VariableSizeList`.


    Why react-window Improves Performance
    Normally, rendering a large list (1000+ items) creates all DOM nodes at once — this increases memory usage and slows down rendering.
    react-window only renders items currently visible in the viewport plus a small buffer.
    As you scroll, offscreen items are unmounted and new ones are mounted dynamically.

## Installation**
```javascript
npm install react-window
```
Basic Example — List with react-window
```javascript
import React from "react";
import { FixedSizeList as List } from "react-window";

const Row = ({ index, style }) => (
  <div style={style}>Row {index + 1}</div>
);

export default function App() {
  return (
    <List
      height={400}      // height of the viewport
      itemCount={1000}  // total items
      itemSize={35}     // height of each row (px)
      width={300}       // width of the list
    >
      {Row}
    </List>
  );
}
```


## Key Props
Prop	Purpose
height	Viewport height
width	Viewport width
itemCount	Total number of items
itemSize	Fixed height/width of each item (use VariableSizeList for dynamic)
children	Row renderer function
Dynamic Heights — VariableSizeList
```javascript
import { VariableSizeList as List } from "react-window";

const getItemSize = index => (index % 2 ? 50 : 30);

<List
  height={400}
  itemCount={1000}
  itemSize={getItemSize}
  width={300}
>
  {Row}
</List>
```

With API Data Example
//////
```javascript
import React, { useEffect, useState } from "react";
import { FixedSizeList as List } from "react-window";

const Row = ({ index, style, data }) => {
  const item = data[index];
  return <div style={style}>{item ? item.name : "Loading..."}</div>;
};

export default function App() {
  const [items, setItems] = useState([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/users")
      .then(res => res.json())
      .then(data => setItems(Array(1000).fill(data).flat()));
  }, []);

  return (
    <List
      height={400}
      itemCount={items.length}
      itemSize={35}
      width={300}
      itemData={items} // pass data
    >
      {Row}
    </List>
  );
}
```

#Performance Tips with react-window
    Use memo for row components if rows have complex child elements.
    Use itemData to pass all data instead of creating inline props (avoids re-creation).
    Set overscanCount to render a few extra items for smoother scrolling:

<List
  overscanCount={5}
  ...
>

    Combine with useCallback to avoid recreating event handlers.

When to Use react-window

✅ Large tables/lists (10k+ items)
✅ Infinite scrolling
✅ Chat/message windows
✅ Data-heavy dashboards

❌ Not needed for small lists (<100 items) — overhead might outweigh benefits.

## Lazy Loading

**Definition:**
Lazy loading is a performance optimization technique where React delays loading a component until it's actually needed, rather than at the initial page load.

**Why Use Lazy Loading:**
- **Performance** — Smaller initial bundle size = faster page load
- **User Experience** — Only load resources relevant to the current page
- **Bandwidth Efficiency** — Reduces unnecessary downloads

**React APIs for Lazy Loading:**
- `React.lazy()` — dynamically imports a component
- `<Suspense>` — wraps lazy-loaded components to provide fallback UI

**Basic Implementation:**
```javascript
const About = React.lazy(() => import('./About'));

<Suspense fallback={<div>Loading...</div>}>
  <About />
</Suspense>
```

**Best Practices:**
- Split by Route — load each page separately (biggest gain)
- Split by Component — for heavy components like charts, maps, editors
- Keep Fallback UI Simple — avoid slow or heavy loaders
- Prefetch if Possible — if you know user will need next view soon

**Limitations:**
- React.lazy only works with default exports
- Needs `<Suspense>` as a wrapper
- Not meant for data fetching (React 18 Suspense for data is different)
- Too much splitting can lead to too many network requests

**Interview Analogy:**
Think of lazy loading like a restaurant menu: Instead of cooking all dishes when the customer enters (slow), the chef waits until the dish is ordered (fast initial experience).

## Advanced Topics
- **Server-Side Rendering (SSR):** Next.js, Remix
- **Hydration** in SSR apps
- **React Concurrent Mode** (automatic batching, transitions)
- **Error Boundaries**
- **Suspense for Data Fetching**

## Common Practical Interview Questions
- How to debounce input in React
- How to fetch API data with error handling
- How to implement infinite scroll
- How to optimize list rendering with react-window
- How to integrate authentication flows with JWT

---

> **Interview Success Tips:**
> - Focus on understanding *why* these patterns exist, not just syntax
> - Explain trade-offs and performance implications
> - Demonstrate problem-solving approach
> - Practice explaining concepts clearly and concisely