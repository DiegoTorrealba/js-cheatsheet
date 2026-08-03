---
tags:
  - TypeScript
  - Online-Learning
Date: 2026-08-01
---
This is the prompt I gave Claude Sonnet 5 High: 
> I am trying to teach myself TypeScript, but the course I am using relies on a decent understanding of JavaScript, which I haven't used in a couple months, but I am familiar with other coding languages like R, Python and Swift (but I haven't coded in Swift in a while either). Could you generate a cheatsheet of the essential JavaScript features, quirks, variables, methods, datatypes, typical coding patterns, promises, etc. I want to be able to quickly look over the cheatsheet any time I need to get a comprehensive refresher on JavaScript.

I then gave it 20 areas where it could improve its initial response, so here's what I received after.
# Outline
1. [[#Variable Declarations]]
2. [[#Data Types]]
3. [[#Equality & Truthiness]]
4. [[#Special Operators]]
5. [[#Strings]]
6. [[#Arrays]]
7. [[#Objects]]
8. [[#Functions]]
9. [[#Scope & Closures]]
10. [[#Control Flow]]
11. [[#Classes]]
12. [[#Asynchronous JavaScript]]
13. [[#Modules]]
14. [[#JSON]]
15. [[#Common Quirks & Gotchas]]
16. [[#Error Handling]]
17. [[#Handy Built-ins]]
18. [[#Quick Segue into TypeScript]]
# Variable Declarations
```javascript
let x = 5; // block-scoped; reassignable
const y = 10; // block-scoped; NOT reassignable (but object contents ARE mutable)
var z = 15; // function-scoped; avoid using
```
- ALWAYS prefer **`const`**, fall back to **`let`**. Never use `var`. 
- **NOTE**: <u><code>const</code> prevents <i>reassignment</i>, NOT mutation</u>. 
```javascript
const arr = [1, 2, 3];
arr.push(4); // fine -- mutating the array
arr = [1, 2]; // TypeError -- reassigning the binding
```
- **COMPARISON**: Unlike Python, JS has no true "constants" for object contents. `const` is like Swift's `let` in this respect. 
	- No True Constants
		- `const` only locks the **binding** (the name $\rightarrow$ value link), NOT the value itself. 
		- Primitives in JavaScript, however, feel fully constant because there's nothing "inside" them to mutate. 
		- If you genuinely want a frozen object, use `Object.freeze(person)` (***shallow freeze***: nested objects inside it are still mutable). 
	- `const` vs. Swift's `let`
		- Swift's `let` on a *class instance* (reference type) behaves exactly like JS's `const`: you can't reassign the variable, but you *can* mutate the instance's properties through it, because `let` only locks the reference, not the object it points to. 
		- Swift's `let` on a *struct* (a value type), however, is much stricter than JS's `const`, as the entire struct is immutable, properties included. 
		- TL;DR: <u>JS objects/arrays are always reference types</u>, so `const` in JS is consistently like Swift's `let` on a class -- never like Swift's `let` on a struct. 
- ***Hoisting***: `var` declarations (not values) are hoisted to the top of their function scope. `let`/`const` are hoisted too but live in a **temporal dead zone (TDZ)**---accessing them before declaration throws a `ReferenceError` instead of `undefined`. 
# Data Types
## Primitives
- ***Primitives*** are *immutable*, compared by *value*: 
	- `number` $\rightarrow$ no int/float distinction (`1` and `1.0` are the same type). 
	- [[#Strings|`string`]] 
	- `boolean`
	- `undefined` $\rightarrow$ a declared variable with no assigned value. 
	- `null` $\rightarrow$ intentional "no value". 
	- `symbol` $\rightarrow$ unique identifiers (rare, mostly for object keys)
	- `bigint` $\rightarrow$ for integers beyond `Number.MAX_SAFE_INTEGER` (`123n`). 
## Reference Types
- ***Reference types*** (compared by *reference*): `object`, `array`, `function` (functions in JavaScript are objects).
```javascript
typeof 42; // "number"
typeof "hi"; // "string"
typeof true; // "boolean"
typeof undefined; // "undefined"
typeof null; // "object" ; famous bug -- kept for backward compatibility
typeof []; // "object" -- use Array.isArray(x) to ckec for arrays
typeof {}; // "object"
typeof function(){}; // "function"
```
### `undefined` vs. `null`
- `undefined` = "never set"; `null` = "deliberately empty". 
- Both are falsy and `undefined == null` is `true` (loose equality), but `undefined === null` is `false`. 
- **COMPARISON**: No separate int/float/double like Swift -- just `number` (a 64-bit float). Watch for precision issues : `0.1 + 0.2 = 0.300000000000004`.
# Equality & Truthiness
```javascript
5 == "5"; // true -- coerces types before comparing
5 === "5"; // false -- checks type AND value. 

NaN === NaN; // false -- NaN is NEVER equal to anything, including itself
Number.isNaN(NaN); // true -- use this to check for NaN
```
- **Rule of Thumb**: **Always use `===` and `!==`**. Avoid using `==`/`!=` unless you specifically want type coercion. 
- **Falsy values** (everything else is truthy): `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, `NaN`. 
- **COMPARISON**: `"0"` (string) and `[]` and `{}` are all TRUTHY in JS $\rightarrow$ frequently trips up Python users, where empty containers are falsy. 
# Special Operators
## Optional chaining/Nullish Coalescing
```javascript
// Optional chaining -- safely access nested properties 
user?.address?.city; // returned undefined instead of throwing error if any link is null/undefined

// Nullish coalescing -- fallback only for null/undefined (NOT other falsy values)
const name = input ?? "default"; // "" or 0 would NOT trigger the fallback
const name2 = input || "default"; // "" or 0 WOULD trigger the fallback (common bug source)
```
**COMPARISON**: `?.` AND `??` are similar to Swift's optional chaining/`??`. 
## Spread
- ***Spread*** (`...`) takes something iterable (array, string, object) and unpacks it into individual elements, wherever a list of values is expected. 
```javascript
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5] -- copies arr1's elements then adds more

const obj1 = { a: 1, b: 2 }; 
const obj2 = { ...obj1, b: 99 }; // { a: 1, b: 99 } -- later keys override earlier ones

// Spread is also how you pass an array as individual arguments to a function: 
Math.max(...[4, 8, 15]); // same as Math.max(4, 8, 15); 
```
- NOTE that spread also makes a **shallow copy**, handy for "clone this array/object without mutating the original."
```javascript
const copy = [...arr1]; // a new array with same top-level values
```
## Rest
- ***Rest*** (`...`) is the reverse of spread, gathering individual arguments into an array. 
- While it looks identical, it does the opposite job: it appears in a function's *parameter list* and collects any number of incoming arguments into a single array.
```javascript
function sum(...nums) {
	// no matter how many arguments are passed in, `nums` is always a real array
	return nums.reduce((a, b) => a + b); 
}

sum(1, 2); // nums = [1, 2] -> 3
sum(1, 2, 3, 4); // nums = [1, 2, 3, 4] -> 10
sum(); // nums = [] -> reduce() with no initial value throws here, so be careful
```

- Rest can also grab "everything else" during destructuring: 
```javascript
const [first, ...others] = [1, 2, 3, 4]; // first = 1, others = [2, 3, 4]
```
### Aside about `[1, 2, 3, 4].reduce((a, b) => a + b);`
- Walks through the array, accumulating: 
	1. `1 + 2 = 3`
	2. `3 + 3 = 6`
	3. `6 + 4 = 10`
## Rule of Thumb of Spread vs. Rest
- **Spread** = Unpack collection into pieces.
- **Rest** = Gather pieces into a collection. 
- Context: 
	- Is it inside `[...]`/a call, or 
	- Is it inside a parameter list? 
## Destructuring
- ***Destructuring*** is unpacking values into named variables, letting you pull values out of arrays or objects in one line rather than accessing them iteratively. 
### Array Destructuring
- **Array destructuring** is position-based (order matters, names don't).
```javascript
const point = [10, 20]; 

const [x, y] = point; // x = 10, y = 20

// Skipping elements with commas: 
const [, second] = [1, 2, 3]; // second = 2 (first element skipped)

// Swapping variables - no temp variable needed: 
let a = 1; b = 2; 
[a, b] = [b, a]; // a = 2, b = 1
```
### Object Destructuring
- **Object destructuring** is name-based (you must match the property keys).
```javascript
const person = { name: "Ana", age: 30, city: "Lisbon" };  
const { name, age } = person; // name = "Ana", age = 30; variable names must match key names

// Renaming while destructuring: 
const { name: fullName } = person; // fullName = "Ana" (no variable called "name" is created)

// Default values (used only if the property is undefined or missing):
const { country = "Unknown" } = person; // country = "Unknown" -- key doesn't exist in person

// Combining rename + default:
const { name: n = "Anon" } = person; // n = "Ana" (default only kicks in if name were missing). 
```
### Nested Destructuring
```javascript
const user = { id: 1, address: { city: "Lisbon", zip: "1000"} };
const { address: { city } } = user; // city = "Lisbon" - NOTE: `address` itself isn't created as a variable here  
```
### Destructuring function parameters
- **Destructuring function parameters** are very common in real code, especially for options objects.
```javascript
function greet({ name, greeting = "Hello" }) {
	return `${greeting}, ${name}`;
} 

greet({ name: "Ana" }); // "Hello, Ana!"
greet({ name: "Ana", greeting: "Hey" }) // "Hey, Ana!"
```
### Destructuring comparison to Python and Swift
- Python:
	- Array destructuring is similar to Python's tuple unpacking.
	- Object destructuring has no direct Python equivalent (closest is manually doing `name = person["name"]` for each key).
- Swift:
	- More like Swift'spattern matching in `let (x, y) = ...` for tuples, though Swift doesn't destructure dictionaries by key name the same way. 
# Strings
```javascript
const s = `Hello, ${name}! You are ${age} years old.`; // template literals by using backticks

s.length;
s.toUpperCase() // s.toLowerCase();
s.trim();
s.includes("ell");
s.slice(0, 5); // like Python slicing, but no negative-step
s.split(",");
s.replace("a", "b"); // replaces FIRST match only; use replaceAll() for all 
```
- Strings are IMMUTABLE -- every method returns a NEW string.
- Multiline strings: use template literals (backticks), not `+` concatenation. 
## `.slice()` vs Python slicing
- `str.slice(start, end)` takes a start index (inclusive) and an end index (exclusive) --- the same half-open interval Python uses: 
```javascript
"hello world".slice(0, 5); // "hello"
"hello world".slice(6); // "world" - omit end -> goes to end of the string
"hello world".slice(-5); // "world" - negative indices count from the end, just like Python
"hello world".slice(-5, -1); // "worl" -- negative start AND end both work
```
- The DIFFERENCE, however, is in the third "step" argument. 
	- Python let's you write `s[start:end:step]`
		- `s[::2]` $\rightarrow$ every other character
		- `s[::-1]` $\rightarrow$ reversed. 
	- JavaScript's `slice()` only accepts two arguments, there's <u>no step parameter at all</u>. 
	- To get step/reverse behavior in JS, you reach for other tools instead:
```javascript
// Reversing a string (equiv. to Python's s[::-1])
"hello".split("").reverse().join(""); // "olleh" - split into chars, reverse array, rejoin

// Every other character (equiv. to Python's s[::2])
"hello world".split("").filter((_, i) => i % 2 === 0).join(""); // "hlowrd"
```
# Arrays
```javascript
const arr = [1, 2, 3]; 

arr.push(4); // add to end (mutates)
arr.pop(); // removes from end (mutates)
arr.shift(); // removes from front (mutates)
arr.unshift(0); // add to front (mutates)
arr.slice(1, 3); // returns copy, non-mutating
arr.splice(1, 2); // removes/inserts in place, MUTATES

// The big three for functional-style code (all NON-mutating, return NEW array):
arr.map(x => x * 2); 
arr.filter(x => x > 1); 
arr.reduce((acc, x) => acc + x, 0); // second arg = initial accumulator

arr.forEach(x => console.log(x)); // no return value, just side effects
arr.find(x => x > 1); // first matchign element (or undefined)
arr.findIndex(x => x > 1); 
arr.some(x => x > 1); // any match? -> boolean
arr.every(x => x > 1); // all match -> boolean
arr.includes(3); // value present? -> boolean
arr.join(", "); // array into string
arr.sort(); // sorts as STRINGS by default. Use `arr.sort((a, b) => a - b))` for numbers
```
- **COMPARISON**: `.map`/`.filter`/`.reduce` behave like:
	- Python's `map`/`filter`/`functools.reduce`
	- Swift's `.map`/`.fitler`/`.reduce`.
- Arrays are objects
	- `typeof [] === "object"` is `true`. 
	- Check using `Array.isArray()`. 
## `arr.splice()` in detail
`arr.splice(start, deleteCount, ...itemsToInsert)` is the "do anything" array method; it can remove, insert or replace elements **all in place** (it mutates the original array) and it **returns the removed elements** as a new array.
```javascript
const arr = ["a", "b", "c", "d", "e"];

// Remove only -- splice(start, deleteCount)
const removed = arr.splice(1, 2); 
// arr is now ["a", "d", "e"] - mutated!
// removed is ["b", "c"]

// Insert only -- deleteCount = 0, then list what to insert
const arr2 = ["a", "b", "c"];
arr2.splice(1, 0, "x", "y");
// arr2 is now ["a", "x", "y", "b", "c"] -- nothing removed

// Replace, delete some, insert some, in one call
const arr3 = ["a", "b", "c"];
arr3.splice(1, 1, "X");
// arr3 is now ["a", "X", "c"] - removed "b", inserted "x" in its place. 
```
- NOTE:
	- `arr.slice()` $\rightarrow$ copy (no-destructive)
	- `arr.splice()` $\rightarrow$ destructive
## `arr.map()`, `arr.filter()` and `arr.reduce()` in detail
- All three methods are non-mutating and return something new. 

**`arr.map()`** transforms every element, always returning an array of the *same length*: 
```javascript
[1, 2, 3].map(x => x * 2); // [2, 4, 6], one output per input, same order/count
```
 
 **`arr.filter()`** keeps only elements where the callback returns true, returns an array of *equal or shorter length*: 
```javascript
[1, 2, 3, 4].filter(x => x % 2 === 0); // [2, 4] // only elements that pass test
```

**`arr.reduce()`** walks through the array and "boils it down" to a single accumulated value (e.g., number, string, object or another array). 
	- Signature: `arr.reduce((accumulator, currentElement) => newAccumulator, initial Value)`
```javascript
[1, 2, 3, 4].reduce((acc, x) => acc + x, 0); 
```
- Step-by-step:
	1. acc = 0, x= 1 $\rightarrow$ 1
	2. acc = 1. x=2 $\rightarrow$ 3
	3. acc = 3, x = 3 $\rightarrow$ 6
	4. acc = 6, x = 4 $\rightarrow$ 10
- `arr.reduce()` can build anything, including turning an array into object: 
```javascript
["a", "b", "c"].reduce((acc, letter, i) => {
	acc[letter] = i;
	return acc;
}, {});

// { a: 0, b: 1, c: 2 }
```
- WARNING: If you omit the initial value, `arr.reduce` uses the array's first element as the starting accumulator and starts looping from the second element.
	- This throws an error on an empty array, so always pass an initial value when you can. 
## `arr.sort()` and why it sorts "as strings" by default
- By default, `.sort()` converts every element to a string and compares them by Unicode code point, NOT numeric value. This produces surprising results with numbers. 
```javascript
[25, 100, 9].sort();
// [100, 25, 9]  ⚠️ NOT [9, 25, 100]!
// Because as strings: "100" < "25" < "9"  (comparing character by character: '1' < '2' < '9')
```
- To sort numbers correctly, you supply a **compare function** that tells sort how to order any two elements:
```javascript
[25, 100, 9].sort((a, b) => a - b); 
// [9, 25, 100]
```
- The compare function above says: for each pair `(a, b)`, it's return value tells `sort` what to do:
	- Return is a **negative number** $\rightarrow$ `a` comes before `b`
	- Return is a **positive number** $\rightarrow$ `b` comes before `a`. 
	- Return is **zero** $\rightarrow$ leave their order unchanged. 
- `a - b` naturally produces negative when `a < b`, positive when `a > b`, and zero when equal, which is exactly "ascending numeric order."
	- Flip it to `b - a` for descending order.
- For strings, you'd usually use **`.localeCompare()`** instead of subtraction: 
```javascript
["banana", "apple", "cherry"].sort((a, b) => a.localeCompare(b));
```
- NOTE: `sort()` mutates the original array in place (like `splice()`, but unlike `map`/`filter`).
# Objects
```javascript
const person = {
	name: "Ana",
	age: 30,
	greet() { return `Hi, I'm ${this.name}`; } // note the use of `this`
};

person.name; // dot notation
person["name"]; // bracket notation (needed for dynamic keys)
Object.keys(person); // ["name", "age", "greet"]
Object.values(person); 
Object.entries(person); // [["name", "Ana"], ["age", 30], ...]

// Shorthand when key name === variable name
const name = "Ana", age = 30; 
const p = { name, age }; // same as { name: name, age: age}
```
## Object property shorthand, explained
- Common situation: You already have variables lying around, and you want to bundle them into an object using their *own names* as keys: 
```javascript
const name = "Ana";
const age = 30;

// The long way - repeating the key name and the variable name
const p1 = { name: name, age: age };

// The shorthand -- when the key you want matches the variable name exactly
const p2 = { name, age }; // JS infers the key name from variable name

// p1 and p2 are identical objects
```
- This comes up constantly when returning objects from functions: 
```javascript
function makePoint(x, y) {
	return {x, y}; // shorthand for {x: x, y: y}
}
```
- If you want a *different* key name than the variable, than you have to write it out: `{ fullName: name }`. 
# Functions
```javascript
// Function declaration -- hoisted (usable before definition in the file)
function add(a, b) { return a + b; }

// Function expression - NOT hoisted
const add2 = function(a, b) { return a + b; }; 

// Arrow function - short syntax, does NOT bind its own `this`
const add3 = (a, b) => a + b; // implicit return
const square = x => x * x;  // single param, no parens needed
const noop = () => {}; // empty body

// Default parameters
function greet(name = "friend") { return `Hi ${name}`; } 
```
## `this` -- the classic JS gotcha
- Regular `function` $\rightarrow$ `this` depends on *how it's called*  (can change at runtime).
- Arrow function $\rightarrow$ `this` is inherited from the enclosing scope, lexically, permanently. 
- **Rule of thumb**: Use arrow functions for callbacks/methods where you want `this` to stay tied to the surrounding context (e.g., inside a class's method callback). 
```javascript
class Timer {
	seconds = 0;
	start() {
		setInterval(() => { this.seconds++; }, 1000); // arrorw -> `this` = the Timer instance
		// setInterval(function() { this.seconds++ }, 1000); // regular fn -> `this` would be wrong
	} 
} 
```
## Declarations vs. expressions vs. arrow functions - when to reach for each
- The practical process people use is:
	1. **Function Declaration** - `function add(a, b) {...}`
		- Use for: standalone, named, top-level utility functions -- the "main" functions of a file/module.
		- Key trait: Fully hoisted, so you can call it before its line in the file (rarely relied on deliberately, but means declaration order doesn't matter for these).
		- Has its own `this` (depends on how it's called) and it's own `arguments` object. 
	2. **Function Expression** - `const add = function(a, b) {...}`
		- Use for:
			- Assigning a function to a variable/property conditionally; or
			- When you specifically want a named function when you pass around (e.g., as a callback) without polluting the outer scope with hoisting. 
		- Not hoisted the same way---the variable exists (if let/const) but isn't callable until that line executes. 
		- Same `this`/`arguments` behavior as declaration. 
	3. **Arrow function** - `const add = (a, b) => a + b`
		- Use for: 
			- Short callbacks (e.g., `.map(x => x* 2)`); and
			- Any place you want `this` to stay locked to the surrounding context (event handlers, class-method callbacks, `setTimeout`/`setInterval` bodies). 
		- Never has its own `this`, `arguments` or a `prototype` $\rightarrow$ it inherits `this` from wherever it's written, permanently. 
- Practical modern convention: 
	- Top-level/module-level named functions $\rightarrow$ **declaration** (`function`).
	- Object methods that need dynamic `this` (called as `obj.method()`) $\rightarrow$ regular **function** (shorthand method syntax `{ method() {} }` counts as this).
	- Everything else (i.e., callsbacks, one-off short logic, anything inside a class where you want `this` to mean "this instance") $\rightarrow$ **arrow function**. 
- so, you'll see `function` declarations reserved for "real," named, top-level functions in the file and every else use arrow functions. 
## So why do function expressions still exist at all?
- Q: If function declarations handle top-level functions and arrows handle callbacks, what's left for function expressions? 
- It's a bit historical, since expressions predate arrow functions, but there are several things only a real `function` (declaration or expression -- arrows can't do any of these) can do:
1. You need dynamic `this` (bound by *how* it's called), not lexical `this`. 
	- Arrow functions permanently inherit `this` from where they're written, and sometimes you specifically don't want that. 
```javascript
const obj = {
	name: "Ana";
	greet: function () { return `Hi, I'm ${this.name}`; } // `this` = whatever object calls .greet()
}; 

obj.greet(); // "Hi, I'm Ana"
// If greet were an arrow function, `this` would NOT refer to obj at all (it'd inherit it from the outer/module scope). 
```
2. You need the `arguments` object. 
3. It needs to be a generate function (`function*`)
4. Conditional/dynamic definition without hoisting quirks.
	- Since function expressions aren't hoisted the way declarations are, they're a clean way to pick between multiple functions bodies at runtime. This works with arrow too, but expressions are the traditional form. 
5.  IIFEs (Immediately Invoked Function Expressions) - defining a calling a function in one shot, historically used to create an isolated, private scope before `let`/`const`/ES modules existed. 
6. Named function expressions, for self-referencing recursion. 
# Scope & Closures
```javascript
function makeCounter() {
	let count = 0;
	return function () {
		count++;
		return count;
	};
}

const counter = makeCounter();
counter(); // 1
counter(); // 2 -> the inner function "remembers" count between calls
```
- A ***closure*** is a function bundled with references in its surrounding scope. This is how private state is commonly faked in JS (no real private variables outside of classes' `#private` fields). 
- Block scope (`{ }`) applies to `let`/`const` but NOT `var`. 
## Closures in more depth
- The idea is that <u>an inner function doesn't just "see" the other variables when created, <b>it keeps a <i>live reference</i> to them</b></u>, for as long as the inner function exists, even after the outer function has already finished running. 
```javascript
function makeCounter() {
  let count = 0;                       // this variable would normally disappear once makeCounter() returns
  return function () {
    count++;                             // but this inner function "closes over" count, keeping it alive
    return count;
  };
}

const counterA = makeCounter();    // counterA has its OWN private `count`
const counterB = makeCounter();    // counterB has a SEPARATE, independent `count`
counterA(); // 1
counterA(); // 2
counterB(); // 1 — completely independent from counterA's count
```
- Each call to `makeCounter()` creates a brand-new `count` variable and a brand-new closure over it--that's why `counterA` and `counterB` don't interfere with each other. 
- This pattern is JS's usual substitute for "private variables," where `count` is only reachable through the returned function, with no other way to access it from outside. 
## Block scope: `let`/`const` vs. `var` -- the classic loop bug
- `let`/`const` are scoped to the nearest `{}` block (if, for, while, or a bare block).
- `var` ignores blocks completely and is scoped to the whole enclosing function (or global scope). 
# Control Flow
```javascript
for (let i = 0; i < 5; i++) { } // classic
for (const item of arr) { } // values -- most common for arrays/iterables - "of"
for (const key in obj) { }  // keys - for plain objects (aviod on arrays) - "in"

switch (value) {
	case 1: console.log("one"); break; // don't forget `break` -> cases fall through otherwise. 
	default: console.log("other");
}

// Ternary
const result = age >= 18 ? "adult" : "minor";
```
## `for...of` vs.  `for...in` in detal
- `for...of` iterates over the **values** of an *iterable* (arrays, strings, Maps, Sets, etc.)
```javascript
const arr = ["a", "b", "c"];
for (const value of arr) { console.log(value); }   // "a", "b", "c" — the actual values, in order

for (const char of "hi") { console.log(char); }      // "h", "i" — strings are iterable too
```
- `for...in` iterates over the **enumerable property keys** of any *object* (including arrays, where the "keys" are index strings)
```javascript
const arr = ["a", "b", "c"];
for (const key in arr) { console.log(key); }   // "0", "1", "2" — strings, not numbers!

const obj = { x: 1, y: 2 };
for (const key in obj) { console.log(key); }   // "x", "y" — this is the useful case for for...in
```
### Why `for...in` is risky on arrays
- It gives you string indices ("0", not 0), which can cause subtle bugs if you do math with them.
- It also walks up the prototype chain---if any inherited/enumerable property exists (e.g., from a library polluting `Array.prototype`), it shows up too, which `for...of` never does. 
- It doesn't guarantee numeric order the way `for...of` does. 
### Rule of thumb
- Use `for...of` for arrays/iterables (you want values), use `for...in` only for plain objects (you want keys) 
- Better yet, prefer `Object.keys()`/`Object.entries()` with `for...of` for objects, since it sidesteps the prototype-chain gotcha entirely. 
```javascript
for (const [key, value] of Object.entries(obj)) { console.log(key, value); } 
```
## `break` in `switch` in more detail
- Without `break`, execution falls through to the next case, running its code too, regardless of whether the case's condition matched.
- `break` exits the switch block immediately, preventing fall-through. 
- Fall-through is occasionally used intentionally to group multiple cases that should share the same code. 
```javascript
switch (day) {
  case "Sat":
  case "Sun":                  // no code/break between these — both fall through to the same block
    console.log("Weekend");
    break;
  default:
    console.log("Weekday");
}
```
- But as a general habit, always add `break` unless you have a specific, commented reason for wanting fall-through. 
## Ternary operator explained
- Syntax: `condition ? valueIfTrue : valueIfFalse` 
	- Used for assignment because if/else doesn't produce a value you can assign directly. 
```javascript
const age = 20;
const status = age >= 18 ? "adult" : "minor";   // status = "adult"

// Equivalent, longer if/else version:
let status2;
if (age >= 18) {
  status2 = "adult";
} else {
  status2 = "minor";
}
```
- You can chain ternaries for multiple conditions, but it can be unreadable: 
```javascript
const grade = score >= 90 ? "A"
            : score >= 80 ? "B"
            : score >= 70 ? "C"
            : "F";
```
# Classes 
```javascript
class Animal {
	#secret = "hidden"; // private field -> #
	static count = 0; // shared across ALL instances; this case increases evrey time an animal object is created. 
	
	constructor(name) {
		this.name = name; // object field
		Animal.count++; 
	}
	
	speak() { return `${this.name} makes a sound.`; }
	
	get info() { return `Name: ${this.name}`; } // getter -> accessed like a property. 
}

class Dog extends Animal {
	speak() {
		return `${super.speak()} It's a bark!`; // call parent class method
	}
}

const d = new Dog("Rex") // Animal(name)
```
- **COMPARISON**: Familiar if you've used Swift/Python classes.
- JS classes are syntactic sugar over its prototype-based inheritance system under the hood. 
## Class property types
1. **Instance properties** `this.name` -- each instance gets its own independent copy, usually set in the constructor. 
2. **Static properties/methods** `static count = 0` -- belongs to the class itself, shared across all instances, accessed via the class name, not an instance. 
	- Use static for things that describe the class *as a whole* (e.g., a counter of instances, a utility/factory method, configuration constants) rather than any one object. 
3. **Private fields** (`#secret`) -- instance-scoped like instance properties, but only accessible from *inside the class's own methods*---completely invisible/inaccessible from outside code. 
## Calling a getter function
- A `get` method is defined with a function-like syntax but is accessed like a plain property with no parentheses, even though it runs code behind the scenes.
```javascript
class Animal {
  constructor(name) { this.name = name; }
  get info() { return `Name: ${this.name}`; }   // defined like a method...
}
const a = new Animal("Rex");
a.info;       // "Name: Rex" — accessed WITHOUT parentheses, like a property
a.info();     // TypeError — info is not a function, don't call it
```
- Getters are useful for computed, read-only-feeling properties that should look like plain data from the outside, even though they run a small computation each time they're accessed. 
- There's a matching `set` syntax for writing computed properties.
```javascript
class Animal {
	set nickname(value) { this._nickname = value.toUpperCase(); }
	get nickname() { return this._nickname; } 
}

const a = new Animal();
a.nickname = "rex"; // calls the setter, no parentheses
a.nickname; // "REX" // calls the getter
```
# Asynchronous JavaScript
- JavaScript is **single-threaded** but **non-blocking** $\rightarrow$ async operators are queued and handled via the **event loop**. 
```javascript
// Callback style (old, avoid nested these -> "callback hell")
fetchData(function(result) { console.log(result); });

// Promises
fetch(url)
	.then(response => response.json())
	.then(data => console.log(data)) 
	.catch(error => console.error(error))
	.finally(() => console.log("done"));
	
// async/await - cleaner syntax over promises, but same underlying mechanism
async function getData() {
	try { // putting async calls in try block
		const response = await fetch(url);  // preprending async methods using await
		const data = await response.json(); 
		return data;
	} catch (error) {
		console.error(error); 
	}  
}
```
- A ***Promise*** has 3 states: 
	1. `pending`: the initial state; the async operation (e.g., network request, timer, file read, etc.) hasn't finished yet.
	2. `fulfilled`: (a.k.a., "resolved"); the operation succeeded; the promise now holds a value, and any `.then()` handlers attached to it run (immediately, if attached after settling). 
	3. `rejected`: the operation failed; the promise now holds an error/reason, and `.catch()` handlers run instead. 
- `await` only works inside an `async function` (or top-level in modules).
- `Promise.all([p1, p2, p3])` $\rightarrow$ run in parallel, wait for all to finish. 
	- Takes an array of promises and returns a new promise, but waits for <u>every</u> promise to fulfill, then resolves with an array of their results, in the same order they were passed (not the order they finish in). 
	- WARNING: Fails fast; if *any single promise rejects*, `Promise.all` immediately rejects with that one error, even if the others were still pending or would have succeeded. 
		- Use `Promise.allSettled()` instead if you want the outcome of every promise regardless of individual failures as it never short-circuits and gives you any array of `{status, value}` and `{status, reason}` objects. 
```javascript
const [users, posts, comments] = await Promise.all([
  fetch("/users").then(r => r.json()),
  fetch("/posts").then(r => r.json()),
  fetch("/comments").then(r => r.json()),
]);
// All three requests run in PARALLEL, not one after another — much faster than three separate `await`s in sequence
```
- `Promise.race([p1, p2])` $\rightarrow$ resolves/rejects as soon as the first one settles (either fulfills or rejects). 
	- Settles as soon as the first promise settles, whether that first one fulfills or rejects -- the rest are simply ignored (their results are discarded, not cancelled). 
```javascript
const result = await Promise.race([
	fetch("/data"),
	new Promise((_, reject) => setTimeout(() => reject("timeout"), 5000))
]);
// Whichever finishes "first", commonly used to implement a timeout for a slow request
```
- **Rule of thumb between Promise.all() vs. Promise.race()**: Use `Promise.all()` when you want *all* the results and they can run independently; use `Promise.race()` when you only care about whichever finishes or fails first, like enforcing a timeout.
- **COMPARISON**: 
	- Conceptually similar to Swift's `async`/`await`
	- The mechanics (event loop, microtask queue) differ from Python's `asyncio` but the syntax feel is close. 
# Modules
```javascript
// math.js
export const PI = 3.14;
export function add(a, b) { return a + b; }
export default function multiply(a, b) { return a * b; } // one default export per file

// main.js
import multiply, { PI, add } from "./math.js";
import * as math from "./math.js"; // namespace import
```
- **ES Modules** (`import`/`export`) is the modern standard.
- Might seen **CommonJS** (`require`/`module.exports`) in older Node.js code $\rightarrow$ don't mix the two styles in one file. 
## Default exports and named exports, in more detail
- A file can export things in two different ways, and they're imported differently: 
- ***Named exports***: You can have as many as you want per file, and the name matters: 
```javascript
// math.js
export const PI = 3.14;
export function add(a, b) { return a + b; }

// main.js -- import names must match exactly (curly braces), though you can rename with `as`
import { PI , add } from "./math.js";
import { add as addNumbers } from "./math.js"; // renaming on import
```
- ***Default export***: At most one per file, meant to represent "the main thing this file exports":
```javascript
// math.js
export default function multiply(a, b) { return a * b; }

// main.js - NO curly braces, and you can name it whatever you want on import
import multiply from "./math.js";
import timesTwo from "./math.js"; // this works too 
```
- Key difference from named imports: because there's only one default per file, JS doesn't need you to reference it by a specific name -- you just assign it to whatever local variable name you like. 
### Combining both in one file
```javascript
// math.js
export const PI = 3.14; // named
export default function multiply(a, b) { return a * b; } // default

// main.js
import multiply, { PI } from "./math.js"; // default import first, then named imports in braces
```
# JSON
```javascript
JSON.stringify(obj); // object -> JSON string
JSON.parse(jsonString); // JSON string -> object
```
- `undefined`, functions and symbols are silently dropped by `JSON.stringify`. 
## What gets dropped by `JSON.stringify`, in detail 
- JSON (the text format) only knows a small set of types: objects, arrays, strings, numbers, booleans, and null. 
	- It has NO concepts of functions, undefined, or symbols---the JSON spec simply doesn't define a syntax for them. 
	- So, when you stringify a JS object containing these, `JSON.stringify` just silently omits the key entirely instead of throwing an error. 
```javascript
const obj = {
	name: "Ana",
	age: undefined, // will be dropped
	greet: function() { return "hi"; }, // will be dropped
	id: Symbol("x"), // will be dropped
	active: null, // KEPT -- null is valid JSON
}

JSON.stringify(obj); // `{"name": "Ana", "active": null}`
```
- Inside *arrays* specifically, `undefined` and functions aren't dropped by converted to `null` instead (since array position/length matters and can't just disappear):
```javascript
JSON.stringify([1, undefined, function(){}, 3]); // `[1, null, null, 3]`
```
- Matters most when debugging why a property vanishes after sending it to an API/localStorage? 

# Common Quirks & Gotchas

| Quirk                         | Example                                                                   | Note                                                                                               |
| ----------------------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Loose equality coercion       | `"" == 0` $\rightarrow$ `true`                                            | Use `===` always.                                                                                  |
| `NaN` isn't equal to itself.  | `NaN === NaN` $\rightarrow$ `false`                                       | Use `Number.isNan()`                                                                               |
| Adding arrays/objects         | `[] + []` $\rightarrow$ `""`; `[] + {}` $\rightarrow$ `"[object Object]"` | Coercion to strings/numbers is weird --- avoid `+` for anything but numbers/strings.               |
| Floating point                | `0.1 + 0.2 !== 0.3`                                                       | Standard binary float imprecision. Get around by rounding(?)                                       |
| `typeof null`                 | `"object"`                                                                | Historic bug, permanent                                                                            |
| Array holes vs. undefined     | `[1, , 3]`                                                                | Sparse arrays behave inconsistently across methods.                                                |
| Automatic semicolon insertion | Returning on a new line                                                   | `return \n { x: 1 }` returns `undefined`, not the object -> keep `{` on the same line as `return`. |
| Global object leakage         | Assigning to undeclared var in non-strict mode.                           | Always declare with `let`/`const`.                                                                 |
| Mutability of `const` objects | `const o = {}; o.x = 1;` works                                            | `const` locks the <u>binding</u>, NOT the contents.                                                |
## What are Array Holes
- An ***array hole*** is a genuinely missing slot in array--not the same thing as a slot that explicitly holds a value `undefined`.
- You create one by leaving a gap in an array literal, or by setting an array's `.length` manually:
```javascript
const withHole = [1, , 3]; // index 1 is a hole
const withUndefined = [1, undefined, 3]; // index 1 explicitly holds undefined

withHole.length; // 3
withHole[1]; // undefined -- reading a hole gives you undefined, same as a real one

// BUT, they behave differently one you use array methods that check for "existence":
withHole.forEach(x => console.log(x)) // logs 1, 3 -- hole is skipped entirely
withUndefined.forEach(x => console.log(x)) // logs 1, undefined, 3

withHole.map(x => x * 2); // [2, <1 empty item>, 6] -- the hole is preserved as a hole in the output
withUndefined.map(x => x * 2); // [2, NaN, 6] -- undefined * 2 = NaN, a real computed value

console.log(withHole); // [1, <1 empty item>, 3] // note console shows "empty item", not undefined
```

```javascript
const arr = new Array(3); // [ <3 empty items> ] - length 3, but every slot is a hole, none assigned
arr.length; // 3
arr.forEach(x => console.log(x)); // logs nothing at all - forEach skips every hole
```
# Error Handling
```javascript
try {
	riskyOperation();
} catch (error) {
	console.error(error.message);
} finally {
	cleanup();
} 

throw new Error("Something went wrong"); // custom errors: class MyError extends Error {} 
```
## Catching different error types
- Unlike Python (which lets you write multiple `except SomeError:` clauses) or Swift (`catch SomeError { }`), <u>JavaScript only has ONE <code>catch</code> block per <code>try</code></u>,.
- In JavaScript, there's no built-in "catch this specific error type, then that other type" syntax. Instead, you catch everything in one block and branch inside it, usually with `instanceof`: 
```javascript
class ValidationError extends Error {
	constructor(message) {
		super(message);
		this.name = "ValidationError";
	}
}

class NetworkError extends Error {
	constructor(message) {
		super(message);
		this.name = "NetworkError";
	}
}

try {
	doSomethingRisky();
} catch (error) {
	if (error instanceof ValidationError) {
		console.log("Fix your input:", error.message);
	} else if (error instanceof NetworkError) {
		console.log("Retry the request:", error.message);
	} else {
		console.log("Unexpected error:", error.message);
		throw error; // rethrow anything you don't know how to handle, rather than silent
	}
}
```
- Key pieces:
	- Custom error classes extend the built-in `Error` and typically set `this.name` so you can identify them (also useful in logs/stack traces). 
	- `instanceof` checks the error's class, letting you branch like a multi-catch.
	- Re-throwing `throw error;` inside the `else` is a common, important pattern---it means "I only know how to handle specific error types; anything else should keep propagating up."
# Handy built-ins
```javascript
Math.max(1, 2, 3);
Math.min(...arr); 

Math.random();
Math.floor();
Math.ceil();
Math.round();

Number("42");
parseInt("42px"); 
parseFloat("3.14"); 
String(42);
Boolean(0);

Array.from({length: 5}, (_, i) => i); // [0, 1, 2, 3, 4]

console.log()
console.table()
console.error()
```

## `Array.from()`
- `Array.from()` builds a real array from something isn't quite a normal array--either an iterable (strings, Sets, Maps) or an array-like object (something with a `.length` and indexed items, but missing all the array methods; common with DOM APIs like `document.querySelectorAll()`).
- Signature: `Array.from(source, mapFunction)` -- the second argument is optional and, if given, transforms each element as it's collected (like doing `.map()` immediately afterward, but in one step):
```javascript
Array.from("hello"); // ["h", "e", "l", "l", "o"];

Array.from(new Set([1, 2, 2, 3])); // [1, 2, 3];

Array.from({ length: 3 }); // [undefined, undefined, undefined]

// Powerful pattern
Array.from({ length: 5 }, (_, i) => i); // [0, 1, 2, 3, 4]
```
- Breaking down `Array.from({ length: 5 }, (_, i) => i)`:
	1. `{ length: 5 }` isn't a real array - it's a plain object that merely claims to have length 5. `Array.from` treats everything with a numeric `.length` as "array-like" and will iterate indices `0` through `4`. 
	2. For each index, it calls the mapping function `(_, i) => i`, passing `(currentValue, index)`. 
		- Since there's no real value at each slot, the first parameter is ignored (conventionally named `_`), and `i` (the index) is returned. 
	3. Resulting array `[0, 1, 2, 3, 4]` 
# Browser & DOM Basics
- The ***Document Object Model* (DOM)** is the browser's live, in-memory tree representation of your HTML page, and it's what JS uses to read and change what's actually on screen. 
	- None of this exists in Node.js; it's browser-specific. 
## Selecting elements
```javascript
document.querySelector(".btn"); // first matching element or null if none found
document.querySelectorAll(".item"); // all matching elements as a NodeList (not a real array)
document.getElementById("header"); // by id - no leading "#", unlike querySelector
```
- Both `querySelector` and `querySelectorAll` accept *any CSS selector* string -- `.class`, `#id`, `div > p.active`, etc. -- so if you know CSS selectors, you already know how to target elements. 
- `querySelectorAll` returns a *NodeList*, which supports `.forEach()` but NOT `.map()`/`.filter()`/`.reduce()` directly. 
	- Convert to a real array to use those using `Array.from(nodeList)` or `[...nodeList]`. 
## Reading & changing content
```javascript
el.textContent = "Hello"; // sets and reads PLAIN TEXT -- safe, never parsed as markup
el.innerHTML = "<b>Hi</b>"; // sets/reads HTML -- gets parsed and rendered as markup
el.value; // current value of form inputs (text fields); use el.checked for checkboxes
```
- **Rule of thumb**: Default to `textContent`. Only use `innerHTML` when you actually need to insert markup, and never with raw/untrusted user input -- it gets parsed and executed as real HTML, which is a classic XSS (cross-site scripting) vulnerability. 
## Creating & Inserting elements
```javascript
const div = document.createElement("div");
div.textContent = "New box";
parentEl.appendChild(div); // adds as the last child of parentEl
parentEl.append(div); // like appendChild, but also accepts plain strings and multiple arguments
el.remove(); // removes this element from the page entirely
```
## Attributes, classes and data-attributes
```javascript
el.setAttribute("src", "photo.jpg");
el.getAttribute("src"); 

el.classList.add("active");
el.classList.remove("active");
el.classList.toggle("active"); // adds it if missing, removes it if present -- common for show/hide UI toggles
el.classList.contains("active");

el.dataset.userId; // reads a custom data-user-id="..." HTML attribute (dataset auto-converts kebab-case -> camelCase)
```
## Traversing the DOM tree
```javascript
el.parentElement; // the direct parent
el.children; // direct element children (an HTMLCollection, not a plain array)
el.closest(".card"); // walks UP the tree to find the nearest ancestor (or itself) matching a selector 
el.querySelector(".x"); // searches DOWN the tree, scoped only within this element
```
## Events
```javascript
button.addEventListener("click", (event) => {
	console.log(event.target); // the specific element that triggered the event
	event.preventDefault; // cancel's the browser's default behavior (e.g., form submit, link navigation)
});

button.removeEventListener("click", handlerFn); // must pass the SAME function reference used in addEventListener
```
- Use an *arrow function* for the handler when you want `this` to refer to the surrounding context (e.g., a class instance).
- Use a *regular function* when you specifically want `this` to refer to the element that the event fired on. This is the default behavior of a normal function used as a DOM event handler. 
- **Event delegation**: Instead of attaching a listener to every individual child element (e.g., 100 list items), attach one listener to their shared parent and inspect `event.target` to determine which child was actually interacted with. 
```javascript
list.addEventListener("click", (event) => {
	if (event.target.matches("li")) {
		console.log("Clicked:", event.target.textContent); 
	}
});
```
## Timers
```javascript
const id = setTimeout(() => console.log("later"), 1000); // runs ONCE after 1000ms
clearTimeout(id); // cancels it befor eit fires

const intervalId = setInterval(() => console.log("tick"), 1000); // runs REPEATEDLY every 1000ms 
clearInterval(intervalId); // stops the repetition. 
```
## `window` vs. `document`
- **`window`**: the global browser object that holds things like `window.location` (current URL), `window.innerWidth` and is actually where `setTimeout`/`setInterval`/`fetch` technically live. 
	- Also top-level scope for browser JS.
- **`document`**: specifically represents the loaded HTML page. 
	- This is what you use for selecting, reading and modifying page content. 
# Quick Segue into TypeScript
- TS-specific additions you'll layer on top are mainly:
	- Type annotations: `let x: number = 5;`
	- Interfaces/type aliases for object shapes
	- Generics (`<T>`)
	- `enum`, stricter `null`/`undefined` handling (`strictNullChecks`).
	- Compile-time-only constructs (types disappear at runtime); it's still just JS underneath. 
- TypeScript doesn't change JavaScript's runtime behavior, it just adds a type-checking layer before compiling down to plain JavaScript. 