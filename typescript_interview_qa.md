# TypeScript Interview Questions & Answers

## Basic Level Questions

### 1. What is TypeScript and why would you use it?

**Answer:** TypeScript is a strongly typed superset of JavaScript developed by Microsoft. It adds static type checking to JavaScript and compiles down to plain JavaScript.

**Benefits:**
- **Type Safety**: Catches errors at compile time rather than runtime
- **Better IDE Support**: Enhanced autocomplete, refactoring, and navigation
- **Code Documentation**: Types serve as inline documentation
- **Easier Refactoring**: Large codebases become more maintainable
- **ES6+ Features**: Access to latest JavaScript features with backward compatibility

### 2. What are the basic types in TypeScript?

**Answer:**
```typescript
// Primitive types
let name: string = "John";
let age: number = 30;
let isActive: boolean = true;
let value: null = null;
let notDefined: undefined = undefined;

// Array types
let numbers: number[] = [1, 2, 3];
let names: Array<string> = ["Alice", "Bob"];

// Object type
let person: { name: string; age: number } = {
  name: "John",
  age: 30
};

// Function type
let greet: (name: string) => string = (name) => `Hello ${name}`;

// Any type (avoid when possible)
let anything: any = "could be anything";

// Unknown type (safer than any)
let userInput: unknown = getData();
```

### 3. What's the difference between `interface` and `type`?

**Answer:**

**Interface:**
```typescript
interface User {
  name: string;
  age: number;
}

// Can be extended
interface Admin extends User {
  permissions: string[];
}

// Can be merged (declaration merging)
interface User {
  email: string; // This gets merged with the above User interface
}
```

**Type:**
```typescript
type User = {
  name: string;
  age: number;
};

// Can use union types
type Status = "pending" | "approved" | "rejected";

// Can use intersection types
type Admin = User & {
  permissions: string[];
};
```

**Key Differences:**
- Interfaces can be extended and merged, types cannot be merged but can use intersections
- Types can represent primitives, unions, and computed types
- Interfaces are generally preferred for object shapes, types for unions and computed types

### 4. What are union types and intersection types?

**Answer:**

**Union Types (`|`):** A value can be one of several types
```typescript
type StringOrNumber = string | number;
let value: StringOrNumber = "hello"; // OK
value = 42; // Also OK

function formatId(id: string | number): string {
  if (typeof id === "string") {
    return id.toUpperCase();
  }
  return id.toString();
}
```

**Intersection Types (`&`):** Combines multiple types into one
```typescript
type Person = { name: string };
type Employee = { employeeId: number };

type Staff = Person & Employee;

const worker: Staff = {
  name: "John",
  employeeId: 123
}; // Must have both properties
```

### 5. What is type assertion and when would you use it?

**Answer:**
Type assertion tells TypeScript to treat a value as a specific type when you know more about the type than TypeScript does.

```typescript
// Angle bracket syntax (not recommended in JSX)
let someValue: any = "hello world";
let strLength: number = (<string>someValue).length;

// As syntax (preferred)
let strLength2: number = (someValue as string).length;

// Common use case with DOM elements
const input = document.getElementById("myInput") as HTMLInputElement;
input.value = "Hello"; // TypeScript knows this is an input element

// Non-null assertion operator
const user = getUser()!; // Tells TypeScript this won't be null/undefined
```

## Intermediate Level Questions

### 6. Explain generics in TypeScript with examples

**Answer:**
Generics allow you to create reusable components that work with multiple types while maintaining type safety.

```typescript
// Basic generic function
function identity<T>(arg: T): T {
  return arg;
}

let stringResult = identity<string>("hello"); // Type is string
let numberResult = identity(42); // Type inference: number

// Generic interface
interface Repository<T> {
  save(item: T): void;
  findById(id: number): T | null;
  findAll(): T[];
}

class UserRepository implements Repository<User> {
  save(user: User): void { /* implementation */ }
  findById(id: number): User | null { /* implementation */ }
  findAll(): User[] { /* implementation */ }
}

// Generic constraints
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): T {
  console.log(arg.length); // OK, because T has a length property
  return arg;
}

logLength("hello"); // OK
logLength([1, 2, 3]); // OK
// logLength(123); // Error: number doesn't have length property
```

### 7. What are conditional types?

**Answer:**
Conditional types allow you to create types that depend on a condition, similar to ternary operators for types.

```typescript
// Basic conditional type
type Check<T> = T extends string ? true : false;

type A = Check<string>; // true
type B = Check<number>; // false

// Practical example: NonNullable
type NonNullable<T> = T extends null | undefined ? never : T;

type Example = NonNullable<string | null>; // string

// With infer keyword
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;

function getString(): string {
  return "hello";
}

type StringReturn = ReturnType<typeof getString>; // string

// Distributive conditional types
type ToArray<T> = T extends any ? T[] : never;
type Example2 = ToArray<string | number>; // string[] | number[]
```

### 8. What are mapped types?

**Answer:**
Mapped types create new types by transforming properties of existing types.

```typescript
// Basic mapped type
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type User = {
  name: string;
  age: number;
};

type ReadonlyUser = Readonly<User>;
// Result: { readonly name: string; readonly age: number; }

// Partial type
type Partial<T> = {
  [P in keyof T]?: T[P];
};

type PartialUser = Partial<User>;
// Result: { name?: string; age?: number; }

// Custom mapped type
type Stringify<T> = {
  [K in keyof T]: string;
};

type StringifiedUser = Stringify<User>;
// Result: { name: string; age: string; }

// With template literal types
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type UserGetters = Getters<User>;
// Result: { getName: () => string; getAge: () => number; }
```

### 9. Explain the `keyof` operator and `typeof` operator

**Answer:**

**`keyof` operator:** Returns a union of string literal types representing the keys of an object type.

```typescript
interface Person {
  name: string;
  age: number;
  email: string;
}

type PersonKeys = keyof Person; // "name" | "age" | "email"

function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person: Person = { name: "John", age: 30, email: "john@example.com" };
const name = getProperty(person, "name"); // Type is string
const age = getProperty(person, "age"); // Type is number
```

**`typeof` operator:** Gets the type of a variable or property.

```typescript
const config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
  retries: 3
};

type Config = typeof config;
// Result: { apiUrl: string; timeout: number; retries: number; }

function processUser(user: typeof person) {
  // user has the same type as the person variable
}

// With functions
function multiply(a: number, b: number): number {
  return a * b;
}

type MultiplyFunction = typeof multiply;
// Result: (a: number, b: number) => number
```

### 10. What are utility types in TypeScript?

**Answer:**
Utility types are built-in type helpers that facilitate common type transformations.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// Partial<T> - makes all properties optional
type PartialUser = Partial<User>;
// { id?: number; name?: string; email?: string; age?: number; }

// Required<T> - makes all properties required
type RequiredUser = Required<PartialUser>;

// Pick<T, K> - picks specific properties
type UserContact = Pick<User, "name" | "email">;
// { name: string; email: string; }

// Omit<T, K> - omits specific properties
type UserWithoutId = Omit<User, "id">;
// { name: string; email: string; age: number; }

// Record<K, T> - creates an object type with specified keys and values
type UserRoles = Record<"admin" | "user" | "guest", boolean>;
// { admin: boolean; user: boolean; guest: boolean; }

// Exclude<T, U> - excludes types from union
type StringOrNumber = string | number | boolean;
type StringOrNumberOnly = Exclude<StringOrNumber, boolean>; // string | number

// Extract<T, U> - extracts types from union
type OnlyString = Extract<StringOrNumber, string>; // string

// ReturnType<T> - gets return type of function
function getUser(): User { /* */ }
type UserReturnType = ReturnType<typeof getUser>; // User
```

## Advanced Level Questions

### 11. What are template literal types and how do you use them?

**Answer:**
Template literal types use template literal syntax to create new string literal types based on existing ones.

```typescript
// Basic template literal type
type World = "world";
type Greeting = `hello ${World}`; // "hello world"

// With unions
type Color = "red" | "blue" | "green";
type Quantity = "one" | "two" | "three";
type ColorQuantity = `${Quantity} ${Color}`;
// "one red" | "one blue" | "one green" | "two red" | "two blue" | etc.

// Practical example: CSS properties
type CSSUnit = "px" | "em" | "rem" | "%";
type Size = `${number}${CSSUnit}`;

function setWidth(width: Size) {
  // width must be like "10px", "2em", "100%", etc.
}

// With utility types
type EventName<T extends string> = `on${Capitalize<T>}`;
type ClickEvent = EventName<"click">; // "onClick"

// Advanced pattern matching
type GetRouteParams<T extends string> = 
  T extends `${string}:${infer Param}/${infer Rest}`
    ? { [K in Param | keyof GetRouteParams<Rest>]: string }
    : T extends `${string}:${infer Param}`
    ? { [K in Param]: string }
    : {};

type Params = GetRouteParams<"/user/:id/post/:postId">;
// Result: { id: string; postId: string; }
```

### 12. Explain variance in TypeScript (covariance, contravariance, bivariance)

**Answer:**
Variance describes how type relationships change when types are used in different positions.

```typescript
// Covariance (return types)
// If A extends B, then (args) => A extends (args) => B
class Animal { name: string; }
class Dog extends Animal { breed: string; }

type AnimalFactory = () => Animal;
type DogFactory = () => Dog;

// This is OK (covariant)
let animalFactory: AnimalFactory = (() => new Dog()) as DogFactory;

// Contravariance (parameter types)
// If A extends B, then (B) => void extends (A) => void
type AnimalHandler = (animal: Animal) => void;
type DogHandler = (dog: Dog) => void;

// This is OK (contravariant)
let dogHandler: DogHandler = ((animal: Animal) => {
  console.log(animal.name);
}) as AnimalHandler;

// Function parameters are contravariant with --strictFunctionTypes
function handleDog(handler: DogHandler) {
  handler(new Dog());
}

// This works because AnimalHandler can handle any Animal (including Dog)
handleDog((animal: Animal) => console.log(animal.name));

// Bivariance (method signatures - less strict)
interface EventHandler {
  handle(event: Event): void;
}

class ButtonHandler implements EventHandler {
  // In method signatures, this would be allowed even with stricter types
  handle(event: MouseEvent): void { } // More specific than Event
}
```

### 13. What are higher-order types and provide examples?

**Answer:**
Higher-order types are types that operate on other types, similar to higher-order functions.

```typescript
// Type-level functions using conditional types
type Head<T extends readonly unknown[]> = T extends readonly [infer H, ...unknown[]] ? H : never;
type Tail<T extends readonly unknown[]> = T extends readonly [unknown, ...infer R] ? R : [];

type First = Head<[1, 2, 3]>; // 1
type Rest = Tail<[1, 2, 3]>; // [2, 3]

// Recursive type for deep operations
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

interface NestedObject {
  user: {
    profile: {
      name: string;
      settings: {
        theme: string;
        notifications: boolean;
      };
    };
  };
}

type PartialNested = DeepPartial<NestedObject>;
// All properties at every level become optional

// Type-level string manipulation
type Split<S extends string, D extends string> = 
  S extends `${infer T}${D}${infer U}` 
    ? [T, ...Split<U, D>] 
    : [S];

type Words = Split<"hello-world-typescript", "-">; // ["hello", "world", "typescript"]

// Function composition at type level
type Pipe<T, U extends readonly [(arg: T) => any, ...((arg: any) => any)[]]> = 
  U extends readonly [(arg: T) => infer R]
    ? R
    : U extends readonly [(arg: T) => infer R, ...infer Rest]
    ? Rest extends readonly [(arg: R) => any, ...((arg: any) => any)[]]
      ? Pipe<R, Rest>
      : never
    : never;

declare function pipe<T, U extends readonly [(arg: T) => any, ...((arg: any) => any)[]]>(
  value: T,
  ...fns: U
): Pipe<T, U>;

const result = pipe(
  "hello",
  (s: string) => s.length,
  (n: number) => n * 2,
  (n: number) => n.toString()
); // Type is string
```

### 14. Explain branded types and nominal typing in TypeScript

**Answer:**
TypeScript uses structural typing, but sometimes you need nominal typing. Branded types simulate nominal typing.

```typescript
// Problem with structural typing
type UserId = number;
type PostId = number;

function getUser(id: UserId): User { /* */ }
function getPost(id: PostId): Post { /* */ }

const userId: UserId = 123;
const postId: PostId = 456;

// This compiles but is logically wrong
getUser(postId); // Should be an error but isn't!

// Solution: Branded types
type Brand<K, T> = K & { __brand: T };

type UserId2 = Brand<number, "UserId">;
type PostId2 = Brand<number, "PostId">;

// Helper functions to create branded types
function createUserId(id: number): UserId2 {
  return id as UserId2;
}

function createPostId(id: number): PostId2 {
  return id as PostId2;
}

function getUser2(id: UserId2): User { /* */ }
function getPost2(id: PostId2): Post { /* */ }

const userId2 = createUserId(123);
const postId2 = createPostId(456);

// getUser2(postId2); // Error! Cannot assign PostId2 to UserId2

// More advanced branding with validation
class Email {
  private __emailBrand!: never;
  
  constructor(private value: string) {
    if (!this.isValidEmail(value)) {
      throw new Error("Invalid email");
    }
  }
  
  private isValidEmail(email: string): boolean {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
  }
  
  toString(): string {
    return this.value;
  }
}

function sendEmail(to: Email, subject: string) {
  // Guaranteed to receive a valid email
}
```

### 15. What are module augmentation and declaration merging?

**Answer:**

**Declaration Merging:** TypeScript can merge multiple declarations with the same name.

```typescript
// Interface merging
interface Window {
  customProperty: string;
}

interface Window {
  anotherProperty: number;
}

// Result: Window now has both properties
declare const window: Window;
window.customProperty; // OK
window.anotherProperty; // OK

// Namespace merging
namespace Validation {
  export interface StringValidator {
    isAcceptable(s: string): boolean;
  }
}

namespace Validation {
  export class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
      return /^[A-Za-z]+$/.test(s);
    }
  }
}
```

**Module Augmentation:** Extending types from external modules.

```typescript
// Augmenting Express Request interface
declare global {
  namespace Express {
    interface Request {
      user?: {
        id: string;
        email: string;
      };
    }
  }
}

// Now you can use req.user in Express middleware
app.use((req, res, next) => {
  req.user = { id: "123", email: "user@example.com" };
  next();
});

// Augmenting third-party library
declare module "lodash" {
  interface LoDashStatic {
    customMethod(value: any): boolean;
  }
}

// Module augmentation with conditions
declare module "*.vue" {
  import { DefineComponent } from "vue";
  const component: DefineComponent<{}, {}, any>;
  export default component;
}
```

### 16. What are the different ways to handle asynchronous operations in TypeScript?

**Answer:**

```typescript
// Promise types
async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

// Generic promise handling
async function handleAsync<T>(
  promise: Promise<T>
): Promise<[T | null, Error | null]> {
  try {
    const result = await promise;
    return [result, null];
  } catch (error) {
    return [null, error as Error];
  }
}

// Usage
const [user, error] = await handleAsync(fetchUser(123));
if (error) {
  console.error(error);
} else {
  console.log(user.name); // TypeScript knows user is not null
}

// Observable types (RxJS example)
import { Observable } from 'rxjs';

function createUserStream(): Observable<User> {
  return new Observable(subscriber => {
    // Implementation
  });
}

// Async generators
async function* asyncGenerator(): AsyncGenerator<number, void, unknown> {
  let i = 0;
  while (i < 5) {
    yield new Promise(resolve => setTimeout(() => resolve(i++), 1000));
  }
}

// Type-safe error handling with Result pattern
type Result<T, E = Error> = 
  | { success: true; data: T }
  | { success: false; error: E };

async function safeApiCall<T>(url: string): Promise<Result<T>> {
  try {
    const response = await fetch(url);
    const data = await response.json();
    return { success: true, data };
  } catch (error) {
    return { success: false, error: error as Error };
  }
}
```

## Expert Level Questions

### 17. How do you implement a type-safe builder pattern?

**Answer:**

```typescript
// Type-safe builder with method chaining
interface QueryBuilder<T> {
  select<K extends keyof T>(...keys: K[]): QueryBuilder<Pick<T, K>>;
  where<K extends keyof T>(key: K, value: T[K]): QueryBuilder<T>;
  limit(count: number): QueryBuilder<T>;
  execute(): Promise<T[]>;
}

class TypeSafeQueryBuilder<T> implements QueryBuilder<T> {
  private selectedFields?: (keyof T)[];
  private conditions: Array<{ key: keyof T; value: any }> = [];
  private limitCount?: number;

  constructor(private table: string) {}

  select<K extends keyof T>(...keys: K[]): QueryBuilder<Pick<T, K>> {
    this.selectedFields = keys;
    return this as any; // Type assertion needed for return type change
  }

  where<K extends keyof T>(key: K, value: T[K]): QueryBuilder<T> {
    this.conditions.push({ key, value });
    return this;
  }

  limit(count: number): QueryBuilder<T> {
    this.limitCount = count;
    return this;
  }

  async execute(): Promise<T[]> {
    // Build and execute query
    const query = this.buildQuery();
    // Execute query logic here
    return [];
  }

  private buildQuery(): string {
    // Implementation details
    return "";
  }
}

// Usage
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

const users = await new TypeSafeQueryBuilder<User>("users")
  .select("name", "email") // Result type becomes Pick<User, "name" | "email">[]
  .where("age", 25) // Type-safe: age must be number
  .limit(10)
  .execute();

// users has type: Pick<User, "name" | "email">[]
```

### 18. How do you create a type-safe event system?

**Answer:**

```typescript
// Event map definition
interface EventMap {
  'user:login': { userId: string; timestamp: Date };
  'user:logout': { userId: string };
  'post:created': { postId: string; authorId: string; title: string };
  'post:deleted': { postId: string };
}

// Type-safe event emitter
class TypedEventEmitter<T extends Record<string, any>> {
  private listeners: {
    [K in keyof T]?: Array<(data: T[K]) => void>;
  } = {};

  on<K extends keyof T>(event: K, listener: (data: T[K]) => void): void {
    if (!this.listeners[event]) {
      this.listeners[event] = [];
    }
    this.listeners[event]!.push(listener);
  }

  emit<K extends keyof T>(event: K, data: T[K]): void {
    const eventListeners = this.listeners[event];
    if (eventListeners) {
      eventListeners.forEach(listener => listener(data));
    }
  }

  off<K extends keyof T>(event: K, listener: (data: T[K]) => void): void {
    const eventListeners = this.listeners[event];
    if (eventListeners) {
      const index = eventListeners.indexOf(listener);
      if (index > -1) {
        eventListeners.splice(index, 1);
      }
    }
  }
}

// Usage
const emitter = new TypedEventEmitter<EventMap>();

// Type-safe event listening
emitter.on('user:login', (data) => {
  // data is automatically typed as { userId: string; timestamp: Date }
  console.log(`User ${data.userId} logged in at ${data.timestamp}`);
});

// Type-safe event emission
emitter.emit('user:login', {
  userId: "123",
  timestamp: new Date()
});

// Compile-time error for wrong event data
// emitter.emit('user:login', { userId: 123 }); // Error: userId should be string

// Advanced: Event filtering
type FilterEvents<T, U> = {
  [K in keyof T]: T[K] extends U ? K : never;
}[keyof T];

type UserEvents = FilterEvents<EventMap, { userId: string }>;
// Result: 'user:login' | 'user:logout'

function subscribeToUserEvents(
  userId: string,
  callback: (event: UserEvents, data: EventMap[UserEvents]) => void
) {
  // Implementation
}
```

### 19. How do you implement advanced type guards and predicates?

**Answer:**

```typescript
// Basic type guards
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function isNumber(value: unknown): value is number {
  return typeof value === "number" && !isNaN(value);
}

// Object type guards
interface User {
  id: number;
  name: string;
  email: string;
}

interface Admin {
  id: number;
  name: string;
  permissions: string[];
}

function isUser(obj: any): obj is User {
  return obj && 
    typeof obj.id === "number" &&
    typeof obj.name === "string" &&
    typeof obj.email === "string" &&
    !("permissions" in obj);
}

function isAdmin(obj: any): obj is Admin {
  return obj &&
    typeof obj.id === "number" &&
    typeof obj.name === "string" &&
    Array.isArray(obj.permissions);
}

// Generic type guard factory
function hasProperty<K extends string>(
  obj: object,
  prop: K
): obj is object & Record<K, unknown> {
  return prop in obj;
}

// Advanced: Assertion functions
function assertIsString(value: unknown): asserts value is string {
  if (typeof value !== "string") {
    throw new Error("Expected string");
  }
}

function processValue(value: unknown) {
  assertIsString(value);
  // TypeScript now knows value is string
  console.log(value.toUpperCase());
}

// Discriminated union type guards
type Shape = 
  | { kind: "circle"; radius: number }
  | { kind: "rectangle"; width: number; height: number }
  | { kind: "triangle"; base: number; height: number };

function isCircle(shape: Shape): shape is Extract<Shape, { kind: "circle" }> {
  return shape.kind === "circle";
}

function calculateArea(shape: Shape): number {
  if (isCircle(shape)) {
    return Math.PI * shape.radius ** 2; // TypeScript knows shape.radius exists
  }
  
  switch (shape.kind) {
    case "rectangle":
      return shape.width * shape.height;
    case "triangle":
      return (shape.base * shape.height) / 2;
    default:
      const _exhaustive: never = shape; // Exhaustiveness check
      throw new Error("Unknown shape");
  }
}

// Custom type predicate with generics
function isArrayOf<T>(
  value: unknown,
  guard: (item: unknown) => item is T
): value is T[] {
  return Array.isArray(value) && value.every(guard);
}

// Usage
const data: unknown = [1, 2, 3, "4"];
if (isArrayOf(data, isNumber)) {
  // data is number[] here, but this would be false due to "4"
}
```

### 20. How do you work with complex type inference and the `infer` keyword?

**Answer:**

```typescript
// Basic infer usage
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
type Parameters<T> = T extends (...args: infer P) => any ? P : never;

function example(a: string, b: number): boolean {
  return true;
}

type ExampleReturn = ReturnType<typeof example>; // boolean
type ExampleParams = Parameters<typeof example>; // [string, number]

// Inferring array element types
type ArrayElement<T> = T extends (infer U)[] ? U : never;
type StringArrayElement = ArrayElement<string[]>; // string

// Inferring promise resolve types
type PromiseType<T> = T extends Promise<infer U> ? U : never;
type AsyncFunctionReturn<T> = T extends (...args: any[]) => Promise<infer R> ? R : never;

async function fetchData(): Promise<{ id: number; name: string }> {
  return { id: 1, name: "John" };
}

type FetchDataReturn = AsyncFunctionReturn<typeof fetchData>;
// Result: { id: number; name: string }

// Complex inference: Flattening nested arrays
type Flatten<T> = T extends (infer U)[]
  ? U extends (infer V)[]
    ? Flatten<V>
    : U
  : T;

type Nested = number[][][];
type Flattened = Flatten<Nested>; // number

// Inferring object value types
type Values<T> = T extends { [K in keyof T]: infer V } ? V : never;

type ObjectValues = Values<{ a: string; b: number; c: boolean }>;
// Result: string | number | boolean

// Advanced: Parsing function signatures
type ParseFunction<T> = T extends (
  ...args: infer Args
) => infer Return
  ? {
      args: Args;
      return: Return;
      arity: Args['length'];
    }
  : never;

function complexFunction(
  a: string,
  b: number,
  c?: boolean
): Promise<User[]> {
  return Promise.resolve([]);
}

type Parsed = ParseFunction<typeof complexFunction>;
// Result: {
//   args: [string, number, boolean?];
//   return: Promise<User[]>;
//   arity: 3;
// }

// Recursive inference: Deep object paths
type Path<T, K extends keyof T = keyof T> = 
  K extends string
    ? T[K] extends Record<string, any>
      ? K | `${K}.${Path<T[K]>}`
      : K
    : never;

type DeepValue<T, P extends string> = 
  P extends `${infer K}.${infer Rest}`
    ? K extends keyof T
      ? DeepValue<T[K], Rest>
      : never
    : P extends keyof T
    ? T[P]
    : never;

interface NestedData {
  user: {
    profile: {
      settings: {
        theme: string;
        notifications: boolean;
      };
    };
  };
  posts: Array<{ title: string; content: string }>;
}

type AllPaths = Path<NestedData>;
// "user" | "user.profile" | "user.profile.settings" | "user.profile.settings.theme" | 
// "user.profile.settings.notifications" | "posts"

type ThemeType = DeepValue<NestedData, "user.profile.settings.theme">; // string
```

### 21. What are phantom types and how do you use them for compile-time validation?

**Answer:**

```typescript
// Phantom types carry information only at compile time
type Phantom<T, U> = T & { __phantom: U };

// Example: Units of measurement
type Meters = Phantom<number, "meters">;
type Feet = Phantom<number, "feet">;
type Seconds = Phantom<number, "seconds">;

function meters(value: number): Meters {
  return value as Meters;
}

function feet(value: number): Feet {
  return value as Feet;
}

function seconds(value: number): Seconds {
  return value as Seconds;
}

// Type-safe calculations
function calculateSpeed(distance: Meters, time: Seconds): Phantom<number, "meters/second"> {
  return (distance / time) as Phantom<number, "meters/second">;
}

const distance = meters(100);
const time = seconds(10);
const speed = calculateSpeed(distance, time); // OK

// const wrongSpeed = calculateSpeed(feet(100), time); // Error!

// State machine with phantom types
type State = "idle" | "loading" | "success" | "error";
type StateMachine<S extends State> = Phantom<object, S>;

class DataLoader<S extends State = "idle"> {
  private state: StateMachine<S>;

  constructor(state: S) {
    this.state = {} as StateMachine<S>;
  }

  // Only available in idle state
  load(this: DataLoader<"idle">): DataLoader<"loading"> {
    return new DataLoader("loading");
  }

  // Only available in loading state
  onSuccess(this: DataLoader<"loading">): DataLoader<"success"> {
    return new DataLoader("success");
  }

  onError(this: DataLoader<"loading">): DataLoader<"error"> {
    return new DataLoader("error");
  }

  // Only available in success or error state
  reset(this: DataLoader<"success" | "error">): DataLoader<"idle"> {
    return new DataLoader("idle");
  }
}

// Usage
const loader = new DataLoader("idle");
const loadingLoader = loader.load(); // OK
const successLoader = loadingLoader.onSuccess(); // OK
// const errorFromIdle = loader.onError(); // Error! Can't call onError from idle state
```

### 22. How do you implement advanced pattern matching with TypeScript?

**Answer:**

```typescript
// Pattern matching with discriminated unions
type Pattern<T> = {
  [K in T as T extends { type: infer U } ? U : never]: T extends { type: K }
    ? (value: T) => any
    : never;
};

type Action = 
  | { type: "increment"; payload: number }
  | { type: "decrement"; payload: number }
  | { type: "reset" }
  | { type: "setUser"; payload: { id: string; name: string } };

function match<T extends { type: string }, R>(
  value: T,
  patterns: Pattern<T> & { _?: (value: T) => R }
): R {
  const pattern = patterns[value.type as keyof typeof patterns];
  if (pattern && typeof pattern === "function") {
    return pattern(value as any);
  }
  if (patterns._) {
    return patterns._(value);
  }
  throw new Error(`No pattern matched for ${value.type}`);
}

// Usage
function reducer(state: number, action: Action): number {
  return match(action, {
    increment: (action) => state + action.payload, // action is properly typed
    decrement: (action) => state - action.payload,
    reset: () => 0,
    setUser: () => state, // Ignore user actions for this reducer
  });
}

// Advanced: Recursive pattern matching for nested structures
type JSONValue = 
  | string 
  | number 
  | boolean 
  | null 
  | JSONValue[] 
  | { [key: string]: JSONValue };

type JSONPattern<T extends JSONValue, R> = 
  T extends string ? { string: (value: string) => R } :
  T extends number ? { number: (value: number) => R } :
  T extends boolean ? { boolean: (value: boolean) => R } :
  T extends null ? { null: () => R } :
  T extends (infer U)[] ? { array: (value: U[], match: <V>(item: U) => JSONPattern<U, V>) => R } :
  T extends object ? { object: (value: T, match: <K extends keyof T, V>(key: K, value: T[K]) => JSONPattern<T[K], V>) => R } :
  never;

// Compile-time SQL query builder
type SQLQuery<T> = {
  from: <Table extends string>(table: Table) => SQLQueryWithTable<T, Table>;
};

type SQLQueryWithTable<T, Table extends string> = {
  select: <Fields extends keyof T>(...fields: Fields[]) => SQLQueryWithSelect<T, Table, Fields>;
  where: (condition: string) => SQLQueryWithTable<T, Table>;
};

type SQLQueryWithSelect<T, Table extends string, Fields extends keyof T> = {
  where: (condition: string) => SQLQueryWithSelect<T, Table, Fields>;
  build: () => `SELECT ${Fields extends string ? Fields : string} FROM ${Table}`;
};
```

### 23. How do you handle complex type transformations and metaprogramming?

**Answer:**

```typescript
// Type-level arithmetic
type Tuple<T extends number, R extends unknown[] = []> = 
  R['length'] extends T ? R : Tuple<T, [...R, unknown]>;

type Add<A extends number, B extends number> = 
  [...Tuple<A>, ...Tuple<B>]['length'];

type Sum = Add<3, 4>; // 7 (at type level!)

// Type-level string manipulation
type Reverse<S extends string> = 
  S extends `${infer First}${infer Rest}` 
    ? `${Reverse<Rest>}${First}` 
    : "";

type Reversed = Reverse<"hello">; // "olleh"

// Object transformation pipelines
type Transform<T> = {
  readonly [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
} & {
  readonly [K in keyof T as `set${Capitalize<string & K>}`]: (value: T[K]) => void;
};

interface Person {
  name: string;
  age: number;
}

type PersonAccessors = Transform<Person>;
// Result: {
//   readonly getName: () => string;
//   readonly getAge: () => number;
//   readonly setName: (value: string) => void;
//   readonly setAge: (value: number) => void;
// }

// Type-level validation
type ValidateEmail<T extends string> = 
  T extends `${string}@${string}.${string}` ? T : never;

type Email = ValidateEmail<"user@example.com">; // OK
// type InvalidEmail = ValidateEmail<"invalid-email">; // never

// Advanced object manipulation
type DeepMutable<T> = {
  -readonly [P in keyof T]: T[P] extends object ? DeepMutable<T[P]> : T[P];
};

type DeepRequired<T> = {
  [P in keyof T]-?: T[P] extends object ? DeepRequired<T[P]> : T[P];
};

// Type-safe object path access
type PathValue<T, P extends string> = 
  P extends keyof T 
    ? T[P]
    : P extends `${infer K}.${infer Rest}`
    ? K extends keyof T
      ? PathValue<T[K], Rest>
      : never
    : never;

function getValue<T, P extends string>(obj: T, path: P): PathValue<T, P> {
  return path.split('.').reduce((current: any, key) => current?.[key], obj);
}

const user = { profile: { settings: { theme: "dark" } } };
const theme = getValue(user, "profile.settings.theme"); // Type is string

// Recursive type for tree structures
type TreeNode<T> = {
  value: T;
  children?: TreeNode<T>[];
};

type FlattenTree<T> = T extends TreeNode<infer U> 
  ? U | (T extends { children: (infer C)[] } 
    ? C extends TreeNode<any> 
      ? FlattenTree<C> 
      : never 
    : never)
  : never;
```

### 24. How do you implement dependency injection with TypeScript?

**Answer:**

```typescript
// Token-based dependency injection
const TOKENS = {
  UserService: Symbol("UserService"),
  EmailService: Symbol("EmailService"),
  Logger: Symbol("Logger"),
} as const;

type Token = typeof TOKENS[keyof typeof TOKENS];

// Service interfaces
interface IUserService {
  getUser(id: string): Promise<User>;
  createUser(data: Omit<User, "id">): Promise<User>;
}

interface IEmailService {
  sendEmail(to: string, subject: string, body: string): Promise<void>;
}

interface ILogger {
  log(message: string): void;
  error(message: string, error?: Error): void;
}

// Container type
type ServiceMap = {
  [TOKENS.UserService]: IUserService;
  [TOKENS.EmailService]: IEmailService;
  [TOKENS.Logger]: ILogger;
};

class DIContainer {
  private services = new Map<Token, any>();
  private factories = new Map<Token, () => any>();

  register<T extends Token>(
    token: T,
    factory: () => ServiceMap[T]
  ): void {
    this.factories.set(token, factory);
  }

  get<T extends Token>(token: T): ServiceMap[T] {
    if (this.services.has(token)) {
      return this.services.get(token);
    }

    const factory = this.factories.get(token);
    if (!factory) {
      throw new Error(`Service not registered: ${String(token)}`);
    }

    const service = factory();
    this.services.set(token, service);
    return service;
  }
}

// Decorator-based injection
type Constructor<T = {}> = new (...args: any[]) => T;

function Injectable<T extends Constructor>(constructor: T) {
  return class extends constructor {
    static isInjectable = true;
  };
}

function Inject(token: Token) {
  return function (target: any, propertyKey: string | symbol | undefined, parameterIndex: number) {
    const existingTokens = Reflect.getMetadata("design:paramtypes", target) || [];
    existingTokens[parameterIndex] = token;
    Reflect.defineMetadata("design:paramtypes", existingTokens, target);
  };
}

// Service implementations
@Injectable
class UserService implements IUserService {
  constructor(
    @Inject(TOKENS.Logger) private logger: ILogger,
    @Inject(TOKENS.EmailService) private emailService: IEmailService
  ) {}

  async getUser(id: string): Promise<User> {
    this.logger.log(`Fetching user ${id}`);
    // Implementation
    return {} as User;
  }

  async createUser(data: Omit<User, "id">): Promise<User> {
    const user = { ...data, id: generateId() };
    await this.emailService.sendEmail(
      user.email,
      "Welcome!",
      "Welcome to our platform"
    );
    return user;
  }
}

// Type-safe service locator pattern
class ServiceLocator {
  private static container = new DIContainer();

  static register<T extends Token>(
    token: T,
    factory: () => ServiceMap[T]
  ): void {
    this.container.register(token, factory);
  }

  static get<T extends Token>(token: T): ServiceMap[T] {
    return this.container.get(token);
  }
}

// Usage
ServiceLocator.register(TOKENS.Logger, () => new ConsoleLogger());
ServiceLocator.register(TOKENS.EmailService, () => new EmailService());
ServiceLocator.register(TOKENS.UserService, () => new UserService(
  ServiceLocator.get(TOKENS.Logger),
  ServiceLocator.get(TOKENS.EmailService)
));

const userService = ServiceLocator.get(TOKENS.UserService); // Fully typed!
```

### 25. How do you create type-safe APIs and validate runtime data?

**Answer:**

```typescript
// Runtime type validation with compile-time safety
type Validator<T> = (value: unknown) => value is T;

function createValidator<T>(schema: {
  [K in keyof T]: Validator<T[K]>;
}): Validator<T> {
  return (value: unknown): value is T => {
    if (typeof value !== "object" || value === null) {
      return false;
    }

    const obj = value as Record<string, unknown>;
    
    return Object.entries(schema).every(([key, validator]) => {
      return validator(obj[key]);
    });
  };
}

// Basic validators
const isString: Validator<string> = (value): value is string => 
  typeof value === "string";

const isNumber: Validator<number> = (value): value is number => 
  typeof value === "number" && !isNaN(value);

const isBoolean: Validator<boolean> = (value): value is boolean => 
  typeof value === "boolean";

function isArray<T>(itemValidator: Validator<T>): Validator<T[]> {
  return (value): value is T[] => 
    Array.isArray(value) && value.every(itemValidator);
}

function isOptional<T>(validator: Validator<T>): Validator<T | undefined> {
  return (value): value is T | undefined => 
    value === undefined || validator(value);
}

// Schema definition
const UserValidator = createValidator({
  id: isString,
  name: isString,
  email: isString,
  age: isNumber,
  isActive: isBoolean,
  tags: isArray(isString),
  metadata: isOptional(createValidator({
    lastLogin: isString,
    preferences: createValidator({
      theme: isString,
      notifications: isBoolean,
    }),
  })),
});

// Type-safe API endpoint
type ApiResponse<T> = 
  | { success: true; data: T }
  | { success: false; error: string };

async function createTypeSafeEndpoint<T>(
  validator: Validator<T>,
  handler: (data: T) => Promise<any>
) {
  return async (request: Request): Promise<ApiResponse<any>> => {
    try {
      const body = await request.json();
      
      if (!validator(body)) {
        return { success: false, error: "Invalid request data" };
      }

      const result = await handler(body); // body is guaranteed to be type T
      return { success: true, data: result };
    } catch (error) {
      return { success: false, error: (error as Error).message };
    }
  };
}

// Advanced: Schema-to-type inference
type InferType<T> = T extends Validator<infer U> ? U : never;

type UserType = InferType<typeof UserValidator>;
// Automatically infers the correct User type from the validator

// Zod-style schema builder (simplified)
class Schema<T> {
  constructor(private validator: Validator<T>) {}

  parse(value: unknown): T {
    if (this.validator(value)) {
      return value;
    }
    throw new Error("Validation failed");
  }

  safeParse(value: unknown): { success: true; data: T } | { success: false; error: string } {
    if (this.validator(value)) {
      return { success: true, data: value };
    }
    return { success: false, error: "Validation failed" };
  }

  optional(): Schema<T | undefined> {
    return new Schema(isOptional(this.validator));
  }

  array(): Schema<T[]> {
    return new Schema(isArray(this.validator));
  }
}

const string = () => new Schema(isString);
const number = () => new Schema(isNumber);
const boolean = () => new Schema(isBoolean);

function object<T>(schema: { [K in keyof T]: Schema<T[K]> }): Schema<T> {
  const validators = Object.fromEntries(
    Object.entries(schema).map(([key, schemaInstance]) => [
      key, 
      (schemaInstance as Schema<any>)['validator']
    ])
  ) as { [K in keyof T]: Validator<T[K]> };
  
  return new Schema(createValidator(validators));
}

// Usage
const UserSchema = object({
  id: string(),
  name: string(),
  age: number(),
  isActive: boolean(),
});

type User = InferType<typeof UserSchema['validator']>;
// Automatically gets the correct type!
```

### 26. Advanced Generic Constraints and Conditional Type Manipulation

**Answer:**

```typescript
// Multiple constraints with conditional types
type ExtractArrayType<T> = T extends (infer U)[] ? U : never;
type ExtractPromiseType<T> = T extends Promise<infer U> ? U : never;

// Complex constraint combinations
type AsyncArrayProcessor<T> = T extends Promise<(infer U)[]> 
  ? (items: U[]) => Promise<U[]>
  : T extends (infer U)[]
  ? (items: U[]) => U[]
  : never;

// Type-level function composition
type Compose<F, G> = F extends (arg: infer A) => infer B
  ? G extends (arg: B) => infer C
    ? (arg: A) => C
    : never
  : never;

type StringToNumber = (s: string) => number;
type NumberToBoolean = (n: number) => boolean;
type Composed = Compose<StringToNumber, NumberToBoolean>; // (s: string) => boolean

// Advanced key manipulation
type PrefixKeys<T, P extends string> = {
  [K in keyof T as K extends string ? `${P}${K}` : never]: T[K];
};

type SuffixKeys<T, S extends string> = {
  [K in keyof T as K extends string ? `${K}${S}` : never]: T[K];
};

interface OriginalType {
  name: string;
  age: number;
}

type Prefixed = PrefixKeys<OriginalType, "user_">; 
// { user_name: string; user_age: number; }

// Conditional type chains
type ProcessType<T> = 
  T extends string 
    ? `processed_${T}`
    : T extends number
    ? T extends 0
      ? "zero"
      : "non_zero_number"
    : T extends boolean
    ? T extends true
      ? "yes"
      : "no"
    : "unknown";

type Result1 = ProcessType<"hello">; // "processed_hello"
type Result2 = ProcessType<0>; // "zero"
type Result3 = ProcessType<5>; // "non_zero_number"
type Result4 = ProcessType<true>; // "yes"
```

### 27. How do you implement type-safe state management?

**Answer:**

```typescript
// Type-safe Redux-like state management
type ActionType = {
  [K in keyof ActionCreators]: ActionCreators[K] extends (...args: any[]) => infer A 
    ? A extends { type: any } 
      ? A 
      : never 
    : never;
}[keyof ActionCreators];

interface AppState {
  user: {
    id: string | null;
    name: string;
    email: string;
  };
  posts: {
    items: Post[];
    loading: boolean;
    error: string | null;
  };
  ui: {
    theme: "light" | "dark";
    sidebarOpen: boolean;
  };
}

// Action creators with automatic type inference
const actionCreators = {
  setUser: (user: { id: string; name: string; email: string }) => ({
    type: "SET_USER" as const,
    payload: user,
  }),
  
  clearUser: () => ({
    type: "CLEAR_USER" as const,
  }),
  
  loadPosts: () => ({
    type: "LOAD_POSTS" as const,
  }),
  
  loadPostsSuccess: (posts: Post[]) => ({
    type: "LOAD_POSTS_SUCCESS" as const,
    payload: posts,
  }),
  
  loadPostsError: (error: string) => ({
    type: "LOAD_POSTS_ERROR" as const,
    payload: error,
  }),
  
  toggleTheme: () => ({
    type: "TOGGLE_THEME" as const,
  }),
} as const;

type ActionCreators = typeof actionCreators;
type Action = ActionType;

// Type-safe reducer with exhaustive checking
function rootReducer(state: AppState, action: Action): AppState {
  switch (action.type) {
    case "SET_USER":
      return {
        ...state,
        user: {
          ...state.user,
          ...action.payload, // TypeScript knows payload structure
        },
      };
      
    case "CLEAR_USER":
      return {
        ...state,
        user: {
          ...state.user,
          id: null,
        },
      };
      
    case "LOAD_POSTS":
      return {
        ...state,
        posts: {
          ...state.posts,
          loading: true,
          error: null,
        },
      };
      
    case "LOAD_POSTS_SUCCESS":
      return {
        ...state,
        posts: {
          ...state.posts,
          items: action.payload, // TypeScript knows this is Post[]
          loading: false,
        },
      };
      
    case "LOAD_POSTS_ERROR":
      return {
        ...state,
        posts: {
          ...state.posts,
          loading: false,
          error: action.payload, // TypeScript knows this is string
        },
      };
      
    case "TOGGLE_THEME":
      return {
        ...state,
        ui: {
          ...state.ui,
          theme: state.ui.theme === "light" ? "dark" : "light",
        },
      };
      
    default:
      const _exhaustiveCheck: never = action; // Ensures all cases are handled
      return state;
  }
}

// Type-safe selectors
type Selector<T> = (state: AppState) => T;

const selectors = {
  getUser: (state: AppState) => state.user,
  getPosts: (state: AppState) => state.posts.items,
  getTheme: (state: AppState) => state.ui.theme,
  isLoading: (state: AppState) => state.posts.loading,
} as const;

// Advanced: Lens-based state updates
type Lens<S, A> = {
  get: (s: S) => A;
  set: (a: A) => (s: S) => S;
};

function lens<S, A>(
  getter: (s: S) => A,
  setter: (a: A) => (s: S) => S
): Lens<S, A> {
  return { get: getter, set: setter };
}

// Property lens helper
function prop<S, K extends keyof S>(key: K): Lens<S, S[K]> {
  return lens(
    (s: S) => s[key],
    (value: S[K]) => (s: S) => ({ ...s, [key]: value })
  );
}

const userLens = prop<AppState, "user">("user");
const nameLens = prop<AppState["user"], "name">("name");

// Compose lenses
function compose<A, B, C>(
  lens1: Lens<A, B>,
  lens2: Lens<B, C>
): Lens<A, C> {
  return lens(
    (a: A) => lens2.get(lens1.get(a)),
    (c: C) => (a: A) => lens1.set(lens2.set(c)(lens1.get(a)))(a)
  );
}

const userNameLens = compose(userLens, nameLens);
```

## Expert-Level Practical Questions

### 28. How would you implement a type-safe ORM query builder?

**Answer:**

```typescript
// Database schema definition
interface Tables {
  users: {
    id: number;
    name: string;
    email: string;
    age: number;
    created_at: Date;
  };
  posts: {
    id: number;
    title: string;
    content: string;
    author_id: number;
    published: boolean;
    created_at: Date;
  };
  comments: {
    id: number;
    post_id: number;
    author_id: number;
    content: string;
    created_at: Date;
  };
}

// Query builder implementation
class QueryBuilder<
  T extends keyof Tables,
  Selected extends keyof Tables[T] = keyof Tables[T]
> {
  constructor(
    private table: T,
    private selectedFields: Selected[] = Object.keys({} as Tables[T]) as Selected[]
  ) {}

  select<K extends keyof Tables[T]>(...fields: K[]): QueryBuilder<T, K> {
    return new QueryBuilder(this.table, fields);
  }

  where<K extends keyof Tables[T]>(
    field: K,
    operator: "=" | "!=" | ">" | "<" | ">=" | "<=",
    value: Tables[T][K]
  ): QueryBuilder<T, Selected> {
    // Add where condition logic
    return this;
  }

  join<
    J extends keyof Tables,
    FK extends keyof Tables[T],
    PK extends keyof Tables[J]
  >(
    joinTable: J,
    foreignKey: FK,
    primaryKey: PK
  ): JoinQueryBuilder<T, J, Selected> {
    return new JoinQueryBuilder(this.table, joinTable, foreignKey, primaryKey, this.selectedFields);
  }

  async execute(): Promise<Pick<Tables[T], Selected>[]> {
    // Execute query and return results
    return [];
  }
}

// Join query builder
class JoinQueryBuilder<
  T extends keyof Tables,
  J extends keyof Tables,
  Selected extends keyof (Tables[T] & Tables[J])
> {
  constructor(
    private table: T,
    private joinTable: J,
    private foreignKey: keyof Tables[T],
    private primaryKey: keyof Tables[J],
    private selectedFields: Selected[]
  ) {}

  select<K extends keyof (Tables[T] & Tables[J])>(
    ...fields: K[]
  ): JoinQueryBuilder<T, J, K> {
    return new JoinQueryBuilder(
      this.table,
      this.joinTable,
      this.foreignKey,
      this.primaryKey,
      fields
    );
  }

  async execute(): Promise<Pick<Tables[T] & Tables[J], Selected>[]> {
    // Execute join query
    return [];
  }
}

// Factory function for type inference
function from<T extends keyof Tables>(table: T): QueryBuilder<T> {
  return new QueryBuilder(table);
}

// Usage examples
const users = await from("users")
  .select("name", "email")
  .where("age", ">", 18)
  .execute();
// Type: Pick<Tables["users"], "name" | "email">[]

const postsWithAuthors = await from("posts")
  .join("users", "author_id", "id")
  .select("title", "name")
  .where("published", "=", true)
  .execute();
// Type: Pick<Tables["posts"] & Tables["users"], "title" | "name">[]

// Advanced: Type-safe migrations
type Migration<From, To> = {
  up: (schema: From) => To;
  down: (schema: To) => From;
};

type AddColumn<T, K extends string, V> = T & Record<K, V>;
type DropColumn<T, K extends keyof T> = Omit<T, K>;

const addEmailColumn: Migration<
  { users: Omit<Tables["users"], "email"> },
  { users: Tables["users"] }
> = {
  up: (schema) => ({
    users: {} as AddColumn<typeof schema.users, "email", string>
  }),
  down: (schema) => ({
    users: {} as DropColumn<typeof schema.users, "email">
  })
};
```

## Common Interview Scenarios

### 29. Debug and fix TypeScript compilation errors

**Question:** What's wrong with this code and how would you fix it?

```typescript
// Problematic code
interface Config {
  apiUrl: string;
  timeout?: number;
}

class ApiClient {
  constructor(private config: Config) {}
  
  async request<T>(endpoint: string, data?: any): Promise<T> {
    const response = await fetch(this.config.apiUrl + endpoint, {
      method: 'POST',
      body: JSON.stringify(data),
      timeout: this.config.timeout || 5000, // Error 1
    });
    
    return response.json(); // Error 2
  }
}

const client = new ApiClient({ apiUrl: "api.com" }); // Error 3
```

**Answer:**

```typescript
// Fixed version
interface Config {
  apiUrl: string;
  timeout?: number;
}

class ApiClient {
  constructor(private config: Config) {}
  
  async request<T>(endpoint: string, data?: any): Promise<T> {
    // Fix 1: fetch doesn't have timeout option directly
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), this.config.timeout || 5000);
    
    try {
      const response = await fetch(this.config.apiUrl + endpoint, {
        method: 'POST',
        body: data ? JSON.stringify(data) : undefined,
        headers: {
          'Content-Type': 'application/json',
        },
        signal: controller.signal,
      });
      
      clearTimeout(timeoutId);
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }
      
      // Fix 2: Add proper error handling and type assertion
      const result: T = await response.json();
      return result;
    } catch (error) {
      clearTimeout(timeoutId);
      throw error;
    }
  }
}

// Fix 3: Add protocol to URL
const client = new ApiClient({ apiUrl: "https://api.com" });
```

### 30. Performance optimization question

**Question:** How would you optimize TypeScript compilation and runtime performance?

**Answer:**

**Compilation Performance:**
```typescript
// 1. Use project references for large codebases
// tsconfig.json
{
  "compilerOptions": {
    "composite": true,
    "incremental": true,
    "tsBuildInfoFile": ".tsbuildinfo"
  },
  "references": [
    { "path": "./packages/shared" },
    { "path": "./packages/client" }
  