# JavaScript Interview Questions & Concepts Guide 🚀

A comprehensive guide covering essential JavaScript concepts for technical interviews, from basics to advanced topics.

## Table of Contents

- [Core Basics](#core-basics)
- [Functions & Advanced Concepts](#functions--advanced-concepts)
- [ES6+ Features](#es6-features)
- [Array Methods](#array-methods)
- [String & Object Methods](#string--object-methods)
- [Asynchronous & Advanced Topics](#asynchronous--advanced-topics)
- [Practical Examples](#practical-examples)
- [Interview Tips](#interview-tips)

---

## Core Basics

### 1. Difference between `var`, `let`, and `const`

**Summary:**
- **`var`** — function-scoped, hoisted and initialized to `undefined`, can be re-declared in same scope
- **`let`** — block-scoped, hoisted to temporal-dead-zone (TDZ), cannot be re-declared but can be reassigned
- **`const`** — block-scoped, hoisted to TDZ, must be initialized at declaration, cannot be reassigned

**Pitfalls:**
- `var` can lead to bugs due to accidental re-declaration and function scoping
- `const` does not make objects immutable — only prevents reassignment of the binding

```javascript
function demo() {
  if(true) {
    var x = 1;
    let y = 2;
    const z = 3;
  }
  console.log(x); // 1 (function scoped)
  // console.log(y); // ReferenceError
  // console.log(z); // ReferenceError
}

const obj = {a:1};
obj.a = 2;        // allowed
// obj = {};      // TypeError: Assignment to constant variable.
```

### 2. What is hoisting?

Hoisting is JS engine behavior where declarations are conceptually moved to the top of their scope during compilation phase. For `var`, the declaration is hoisted and initialized to `undefined`. For `let`/`const`, the declaration is hoisted but they stay in the Temporal Dead Zone (TDZ) until execution reaches the line of declaration.

```javascript
console.log(a); // undefined
var a = 10;

// console.log(b); // ReferenceError
let b = 20;
```

> **Note:** Function declarations (not function expressions) are hoisted fully.

### 3. `==` vs `===`

- **`==`** performs type coercion before comparison (loose equality)
- **`===`** compares both type and value (strict equality) — **preferred**

```javascript
0 == false;    // true
0 === false;   // false

'5' == 5;      // true
'5' === 5;     // false
null == undefined; // true
null === undefined; // false
```

### 4. Truthy and Falsy values

**Falsy values:** `false`, `0`, `-0`, `0n` (BigInt zero), `""` (empty string), `null`, `undefined`, `NaN`

Everything else is truthy.

```javascript
if ("") console.log('truthy'); else console.log('falsy'); // falsy
if ([]) console.log('truthy'); // arrays are truthy
```

### 5. Scope in JavaScript

**Types:**
- **Global scope** — declared outside any function
- **Function scope** — `var` is function-scoped
- **Block scope** — `let` and `const` are block-scoped (inside `{}`)

```javascript
function f() {
  var a = 1; // function scope
  if(true) {
    let b = 2; // block scope
  }
  // console.log(b); // ReferenceError
}
```

### 6. What is `this`?

`this` is determined by call-site:
- Called as method → `this` is the object before the dot
- Regular function (non-strict) called standalone → global object
- Constructor (`new`) → newly created instance
- `call`/`apply`/`bind` can set `this`
- Arrow functions inherit `this` lexically

```javascript
const obj = {
  x: 10,
  getX() { return this.x; }
};
obj.getX(); // 10

const arrow = () => this; // lexical this
```

### 7. `null` vs `undefined`

- **`undefined`** — variable declared but not assigned or function without return
- **`null`** — explicit "no value" assigned by developer

```javascript
let a;
console.log(a); // undefined
let b = null;
console.log(b); // null
```

### 8. `typeof` operator

Returns a string representing the type. **Oddity:** `typeof null === 'object'` (legacy bug)

```javascript
typeof 5; // 'number'
typeof 'a'; // 'string'
typeof null; // 'object'  (quirk)
typeof undefined; // 'undefined'
typeof [] // 'object'
Array.isArray([]) // true
```

### 9. `for...in` vs `for...of`

- **`for...in`** iterates enumerable property keys (strings) of an object
- **`for...of`** iterates values of an iterable (arrays, strings, Maps, Sets)

```javascript
const arr = ['a','b'];
for (let i in arr) console.log(i); // '0', '1' (keys)
for (let v of arr) console.log(v); // 'a', 'b' (values)
```

### 10. `arguments` object

`arguments` is array-like (not an Array) available inside non-arrow functions. Use rest `...args` for modern code.

```javascript
function f() {
  console.log(arguments); // array-like
}
function g(...args) {
  console.log(Array.isArray(args)); // true
}
```

---

## Functions & Advanced Concepts

### 11. Closures

A closure is a function that remembers its lexical environment — it can access variables from an outer function even after the outer function has returned.

**Uses:**
- Data encapsulation (private variables)
- Function factories
- Memoization

```javascript
function makeCounter() {
  let count = 0;
  return function() {
    count++;
    return count;
  }
}
const counter = makeCounter();
console.log(counter()); // 1
console.log(counter()); // 2
```

### 12. Function declaration vs expression

**Declaration:**
```javascript
function foo() {} // Hoisted completely
```

**Expression:**
```javascript
const foo = function() {}; // Not hoisted
```

### 13. Arrow functions

**Features:**
- Shorter syntax
- No own `this` — uses lexical `this`
- No `arguments` object
- Cannot be used as constructors with `new`

```javascript
let obj = {
  name: 'K',
  arrow: () => console.log(this.name), // lexical this
  method() { console.log(this.name); } // correct method style
};
```

### 14. Synchronous vs asynchronous JS

- **Synchronous** — tasks run one after another; blocking behavior
- **Asynchronous** — tasks are scheduled and completed later; JS uses event loop

```javascript
console.log('A');
setTimeout(() => console.log('B'), 0);
console.log('C');
// Output: A C B
```

### 15. `call()`, `apply()`, `bind()`

- `fn.call(thisArg, arg1, arg2)` — invoke with `thisArg` and arguments individually
- `fn.apply(thisArg, [arg1, arg2])` — same as call but args as array
- `fn.bind(thisArg, arg1)` — returns a new function permanently bound to `thisArg`

```javascript
function greet(g) { console.log(g + ' ' + this.name); }
const p = {name: 'K'};
greet.call(p, 'Hello');
greet.apply(p, ['Hi']);
const bound = greet.bind(p, 'Hey');
bound();
```

### 16. Prototype chain

Every object has an internal prototype link (`[[Prototype]]` / `__proto__`). When property lookup fails on an object, JS walks up this chain to find it.

```javascript
function Person(name){ this.name = name; }
Person.prototype.greet = function(){ console.log(this.name); };
const p = new Person('K');
p.greet(); // finds greet on prototype
```

### 17. IIFE (Immediately Invoked Function Expression)

Create isolated scope and avoid polluting global namespace.

```javascript
(function() {
  const secret = 42;
  console.log('IIFE run');
})();
```

### 18. Higher-order functions

Functions that accept functions or return functions. Core of functional programming in JS.

```javascript
[1,2,3].map(x => x*2); // higher-order usage

const add = a => b => a + b; // returning function
```

### 19. Generators

Functions declared with `function*` that can yield multiple times and maintain internal state.

```javascript
function* idMaker() {
  let id = 0;
  while(true) yield id++;
}
const gen = idMaker();
console.log(gen.next().value); // 0
console.log(gen.next().value); // 1
```

### 20. Pure functions

Given the same inputs, always returns the same output and has no side effects.

**Benefits:** Easier testing, predictable

```javascript
function sum(a,b) { return a + b; } // pure
let x = 0;
function inc() { x++; } // impure
```

---

## ES6+ Features

### 21. Destructuring

Arrays and objects — concise unpacking.

```javascript
const [a, b] = [1, 2];
const {name, age} = {name: 'K', age: 25};

// default values
const {x=10} = {};
```

### 22. Template literals

Use backticks for multi-line strings and interpolation.

```javascript
const name = 'K';
const s = `Hello ${name}
New line here`;
```

### 23. Spread and rest (`...`)

- **Spread** expands iterables: `[...arr]` or `{...obj}`
- **Rest** collects remaining args: `function f(...args){}`

```javascript
const arr = [1,2];
const newArr = [...arr,3];

function sum(...nums) {
  return nums.reduce((a,b) => a+b, 0);
}
```

### 24. Default parameters

```javascript
function greet(name = 'Guest') {
  console.log('Hello', name);
}
greet(); // Hello Guest
```

### 25. Set and Map

- **Set** — collection of unique values
- **Map** — key-value pairs where keys can be any value

```javascript
const s = new Set([1,2,2]); // Set {1,2}
const m = new Map();
m.set({}, 'objVal');
```

### 26. WeakMap and WeakSet

Keys (WeakMap) or members (WeakSet) must be objects and are weakly held — garbage collector can collect them.

**Limitations:** Not iterable; cannot get size.

### 27. Promises and states

**States:** pending → fulfilled (resolved) → rejected

```javascript
const p = new Promise((resolve, reject) => {
  setTimeout(() => resolve(42), 1000);
});
p.then(val => console.log(val)).catch(err => console.error(err));
```

### 28. async/await

Syntactic sugar around promises: `await` pauses until Promise resolves.

```javascript
async function fetchData() {
  try {
    const res = await fetch('/api');
    const data = await res.json();
    return data;
  } catch(err) { console.error(err); }
}
```

### 29. ES modules vs CommonJS

**ES Modules:**
```javascript
export function foo(){};
import {foo} from './mod.js';
```

**CommonJS:**
```javascript
module.exports = {foo};
const {foo} = require('./mod');
```

### 30. Optional chaining `?.` and nullish coalescing `??`

- `?.` — safe access: `obj?.prop?.[0]`
- `??` — returns right-hand operand if left is null or undefined

```javascript
const user = {};
console.log(user.address?.city); // undefined (no error)
const val = null ?? 'default'; // 'default'
```

---

## Array Methods

### Key Methods Overview

| Method | Purpose | Returns | Mutates Original |
|--------|---------|---------|------------------|
| `map()` | Transform each element | New array | ❌ |
| `filter()` | Filter elements | New array | ❌ |
| `reduce()` | Reduce to single value | Any type | ❌ |
| `forEach()` | Iterate with side effects | `undefined` | ❌ |
| `find()` | Find first matching element | Element or `undefined` | ❌ |
| `some()` | Test if any element matches | Boolean | ❌ |
| `every()` | Test if all elements match | Boolean | ❌ |
| `sort()` | Sort elements | Same array | ✅ |
| `push()` | Add to end | New length | ✅ |

### Examples

```javascript
// map() - transform
const doubled = [1,2,3].map(x => x*2); // [2,4,6]

// filter() - select
const evens = [1,2,3,4].filter(n => n%2===0); // [2,4]

// reduce() - accumulate
const sum = [1,2,3].reduce((acc, curr) => acc + curr, 0); // 6

// find() - locate
const user = users.find(u => u.id === 2);

// some() & every() - test
[1,2,3].some(n => n>2); // true
[2,4].every(n => n%2===0); // true

// flat() & flatMap()
[1, [2, [3]]].flat(1); // [1,2,[3]]
[1,2,3].flatMap(x => [x, x*2]); // [1,2,2,4,3,6]

// sort() - IMPORTANT: provide comparator for numbers
[10,2,30].sort((a,b) => a-b); // [2,10,30]
```

---

## String & Object Methods

### String Methods

```javascript
const s = ' Hello World ';

// Extraction
s.slice(1, 6);        // 'Hello'
s.substring(1, 6);    // 'Hello'

// Testing
s.includes('World');  // true
s.startsWith('H');    // false (has leading space)
s.trim().startsWith('H'); // true

// Transformation
s.toUpperCase();      // ' HELLO WORLD '
s.trim();            // 'Hello World'
s.replace(' ', '-'); // 'Hello-World' (first occurrence)
s.replaceAll(' ', '-'); // 'Hello-World'
```

### Object Methods

```javascript
const obj = {a: 1, b: 2};

Object.keys(obj);     // ['a', 'b']
Object.values(obj);   // [1, 2]
Object.entries(obj);  // [['a',1], ['b',2]]

// Copying/Merging
const copy = {...obj};
const merged = Object.assign({}, obj1, obj2);

// Protection
Object.freeze(obj);   // Prevent changes
Object.seal(obj);     // Prevent add/remove props

// Property check
Object.hasOwn(obj, 'a'); // true (ES2022)
```

### Deep vs Shallow Copy

```javascript
const original = { x: { y: 1 } };

// Shallow copy - nested objects still referenced
const shallow = {...original};
shallow.x.y = 2;
console.log(original.x.y); // 2 (changed!)

// Deep copy options
const deep1 = JSON.parse(JSON.stringify(original)); // loses functions
const deep2 = structuredClone(original); // modern, preserves types
```

### Common Patterns

```javascript
// Remove duplicates
const unique = [...new Set(array)];

// Check if object is empty
const isEmpty = Object.keys(obj).length === 0 && obj.constructor === Object;

// Group array by property
const grouped = items.reduce((acc, item) => {
  const key = item.category;
  acc[key] = acc[key] || [];
  acc[key].push(item);
  return acc;
}, {});
```

---

## Asynchronous & Advanced Topics

### 51. Event Loop & Microtasks

**Event loop steps:**
1. Execute synchronous code on call stack
2. Process **microtask** queue (Promise handlers, `queueMicrotask`)
3. Process **macrotasks** (timers, I/O callbacks)
4. Render (in browsers), repeat

```javascript
console.log('Start');
setTimeout(() => console.log('setTimeout'), 0); // macrotask
Promise.resolve().then(() => console.log('promise')); // microtask
console.log('End');

// Output:
// Start
// End  
// promise
// setTimeout
```

### 52. Debounce vs Throttle

**Debounce:** postpone function until after pause in events
**Throttle:** ensure function runs at most once every interval

```javascript
// Debounce Implementation
function debounce(fn, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  };
}

// Throttle Implementation  
function throttle(fn, limit) {
  let inThrottle;
  return function(...args) {
    if (!inThrottle) {
      fn.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}
```

### 53. Memory leaks in JS

**Causes:**
- Forgotten timers (`setInterval`) referencing closures
- Global variables
- Detached DOM nodes retained by references
- Long-lived closures holding large objects

**Prevention:**
- Clear intervals/timeouts
- Remove event listeners when not needed
- Avoid long-lived closures holding large objects

### 54. Currying

Transform a function with multiple arguments into sequence of functions each taking a single argument.

```javascript
// Manual currying
function add(a) {
  return function(b) {
    return a + b;
  }
}
const add5 = add(5);
add5(2); // 7

// Generalized curry
const curry = (fn) => (...args) =>
  args.length >= fn.length ? fn(...args) : curry(fn.bind(null, ...args));
```

### 55. Module pattern

Use closures to create private state and expose API.

```javascript
const Counter = (function() {
  let count = 0;
  return {
    inc() { count++; },
    get() { return count; },
    reset() { count = 0; }
  };
})();

Counter.inc();
Counter.get(); // 1
// count is private, cannot be accessed directly
```

---

## Practical Examples

### A) Group array of objects by key

```javascript
const people = [
  {age: 20, name: 'A'},
  {age: 30, name: 'B'},  
  {age: 20, name: 'C'}
];

const groupedByAge = people.reduce((acc, person) => {
  const key = person.age;
  acc[key] = acc[key] || [];
  acc[key].push(person);
  return acc;
}, {});

console.log(groupedByAge);
// { 20: [{age: 20, name: 'A'}, {age: 20, name: 'C'}], 30: [{age: 30, name: 'B'}] }
```

### B) Debounced search input

```javascript
const debouncedSearch = debounce((query) => {
  fetch(`/search?q=${encodeURIComponent(query)}`)
    .then(response => response.json())
    .then(results => {
      // Update UI with results
      console.log('Search results:', results);
    });
}, 300);

// Usage: on input change
// inputElement.addEventListener('input', (e) => debouncedSearch(e.target.value));
```

### C) Deep clone using structuredClone

```javascript
const original = {
  date: new Date(),
  regex: /test/g,
  nested: { array: [1, 2, 3] }
};

// Modern approach (preserves Date, RegExp, etc.)
const copy = structuredClone(original);

// Note: Requires modern browser/Node.js support
```

### D) Promise-based retry mechanism

```javascript
async function retry(fn, maxAttempts = 3, delay = 1000) {
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (attempt === maxAttempts) throw error;
      
      console.log(`Attempt ${attempt} failed, retrying in ${delay}ms...`);
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}

// Usage
retry(() => fetch('/api/data').then(r => r.json()))
  .then(data => console.log(data))
  .catch(error => console.error('All attempts failed:', error));
```

---

## Interview Tips

### How to approach JavaScript interview questions:

1. **Start with definition** — Define the concept succinctly
2. **Explain behavior** — Cover edge cases and pitfalls  
3. **Show code example** — Demonstrate with working code
4. **Explain output** — Walk through why code behaves that way
5. **Mention implications** — Discuss performance, memory, alternatives when relevant

### Key areas to focus on:

- **Fundamentals:** Scope, hoisting, `this`, closures
- **Modern JavaScript:** ES6+ features, async/await, modules
- **Array/Object manipulation:** Know the methods and when to use them
- **Asynchronous concepts:** Event loop, promises, async patterns
- **Performance:** Memory leaks, optimization techniques

### Common gotchas to remember:

- `typeof null === 'object'`
- Arrow functions don't have their own `this`
- `const` objects are still mutable
- Event loop prioritizes microtasks over macrotasks
- `sort()` mutates the original array
- Closures can cause memory leaks if not handled properly

---

## Contributing

Feel free to contribute to this guide by:
- Adding more examples
- Fixing errors or typos
- Suggesting improvements
- Adding new concepts

## License

This guide is open source and available under the [MIT License](LICENSE).

---

**⭐ Star this repo if you found it helpful for your JavaScript interviews!**