# JavaScript Closures

What do you think the code below logs to the console?

```js
for (var i = 0; i < 3; i++) {
	setTimeout(() => {
		console.log(i);
	}, i * 1000);
}
```

You might be surprised when we actually try it. We can get this code to behave
in a more expected way with a language feature called closures.

Closures are a classic topic in a JavaScript interview, but they have a
reputation for being challenging to understand. Today we'll dive into what they
are, and take a look at some examples that illuminate how they work.

## Goals

- Define closure
- Identify closure examples
- Write a closure

## What are Closures?

A closure is the lexical environment created by a function with references to
its surrounding variables. Closures give us access to variables defined in an
outer function's scope from an inner function.

Remember that we can reach out into greater scopes in JavaScript, and closures
make use of this scoping rule.

JavaScript creates closures every time a function is created. In other words,
whenever a function is defined, a closure gets created.

### Scope Refresher

Remember that in programming, scope refers to the boundaries in which a variable
is created. In JavaScript, scope can be created by code blocks and function
blocks.

```js
function printNumber() {
	const num = 123;
	console.log(num); // logs 123
}

printNumber(); // 123
console.log(num); // ReferenceError: num is not defined
```

We can access `num` within the `printNumber()` scope, but outside of that
function, `num` is not a known variable.

That means that scope isolates variables to their own namespace. This is useful,
because it allows us to reuse common variable names without collisions.

```js
function foo() {
	const num = 1;
	console.log(num);
}

function bar() {
	const num = 2;
	console.log(num);
}

foo(); // 1
bar(); // 2
```

#### Nesting Function Scopes

We can make things more interesting by nesting scopes. Let's say we have an
outer function, from which we want to define and call an inner function. How do
the scopes work then?

```js
function outerFunction() {
	const outerNum = 1;

	function innerFunction() {
		console.log(outerNum);
	}

	innerFunction();
}

outerFunction();
```

As expected, the `innerFunction` can reach out into the scope of its parent
function and access those variables from an inner scope.

### Lexical Scoping

A key part of the closure definition is the idea of the lexical environment.
JavaScript implements a lexical scoping mechanism, also called static scoping,
which means that the accessibility of variables is determined by the position of
variables inside the nested scope. (Unlike dynamic scoping, which
[some languages do](https://stackoverflow.com/questions/1047454/what-is-lexical-scope).)

In other words, every inner scope can access its parent scopes. This scope is
determined at [lexing time](https://en.wikipedia.org/wiki/Lexical_analysis) by
the engine, just by parsing the source code, and not yet executing it.

## Closure Examples

Now that we've refreshed ourselves on scope and the idea of a lexical
environment, let's tackle the concept of a closure again.

> A closure is the combination of a function bundled together (enclosed) with
> references to its surrounding state (the lexical environment). In other words,
> a closure gives you access to an outer function's scope from an inner
> function. In JavaScript, closures are created every time a function is
> created, at function creation time.
> [(MDN)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)

### A Simple Example

We'll start with a relatively straightforward example. We now know that we can
define and call functions in other functions, and that the inner function has
access to the outer function's scope.

```js
function outerFunction() {
	const mySpecialNumber = 123;

	// this inner function creates a closure by referencing its surrounding lexical environment
	// in other words, by accessing the variables defined in the outer function
	function innerFunction() {
		return mySpecialNumber;
	}

	// return the reference to the inner function
	return innerFunction;
}

const innerReference = outerFunction(); // outer function is called
const innerReturn = innerReference(); // call the reference to the outer function's return value (innerFunction)
console.log(innerReturn); // 123
```

What is significant about this? In some other programming languages, the
variables created inside a function only exist while the function is being
executed. Once it's done executing, you might expect that the `mySpecialNumber`
variable no longer exists in memory. But this is not the case in JavaScript due
to closures.

As we can see, JavaScript _PRESERVES_ the `mySpecialNumber` variable even after
the outer function is done executing. The `innerFunction()` _closes over_, or
captures, the variable `mySpecialNumber`. It forms a closure.

Thus, when we call the inner function after the outer function is done
executing, the inner function still has access to the outer function's
`mySpecialNumber` variable. 🤯

### A More Interesting Example

Let's kick things up a notch and take a look at two separate closures created by
inner functions from the same outer function. We'll make an adder function
factory.

```js
function makeAdder(num1) {
	return function (num2) {
		return num1 + num2;
	};
}

const addThree = makeAdder(3); // this creates a closure for the addThree function
const addSeven = makeAdder(7); // this creates a closure for the addSeven function

console.log(addThree(5)); // 8
console.log(addSeven(10)); // 17
```

Notice that even though `addThree` and `addSeven` are created from the same
outer function, they create their own lexical environments, or closures. In the
`addThree` closure, the value of `num1` is 3, and in the `addSeven` closure, the
value of `num1` is 7.

Even after the `makeAdder` functions have been executed, the values of those
variables that are being referenced by the inner functions continue to live on
in the program, in those separate closures. Pretty cool, right?!

### Document Button

Another example that illustrates why we need closures is this code snippet:

```js
for (var i = 0; i < 5; i++) {
	var btn = document.createElement('button');
	btn.appendChild(document.createTextNode('Button ' + i));
	btn.addEventListener('click', function () {
		console.log(i);
	});
	document.body.appendChild(btn);
}
```

At first glance, what do you think this code does? What do you think will log
when the buttons are clicked?

You probably correctly guessed that five buttons will be created and appended to
the document body, `Button 0`, `Button 1`, etc. But you might be surprised to
find out that the buttons will all log 5. Why that counterintuitive behavior?

This code executes in two phases:

First, the for-loop runs and creates the buttons, creates their event listeners
and click handlers.

Second, once the loop is done running, the HTML buttons, if they detect a click
event, will call the click handler. Since `var` was used to declare`i`, it is
not bound to this code block or enclosed by the click handler.

Thus, the value of five accessible to the event listeners is the global value of
5 after the for-loop is done executing.

**💡 How can we fix this, using the concept of closures, so that the buttons log
the correct numbers?**

<!-- Create a closure around the event listener callback -->

<!-- ```js
for (var i = 0; i < 5; i++) {
	var btn = document.createElement('button');
	btn.appendChild(document.createTextNode('Button ' + i));
	btn.addEventListener(
		'click',
		(function (i) {
			return function () {
				console.log(i);
			};
		})(i)
	);
	document.body.appendChild(btn);
}
``` -->

### Closures & Data Encapsulation

One benefit of JavaScript closures is that they give us the ability to
encapsulate and make certain data private within a function.

Let's take a look at an example that creates a stack data structure.

```js
function createStack() {
	return {
		items: [],
		push(item) {
			this.items.push(item);
		},
		pop() {
			return this.items.pop();
		},
	};
}

const stack = createStack();
stack.push(10);
stack.push(5);
stack.pop(); // => 5

stack.items; // => [10]
stack.items = [1, 2, 3]; // encapsulation broken
```

The stack works as you might expect, but there's a problem -- anyone can access
the stack items, and potentially other details about the stack, which we don't
want to modify directly.

It breaks the encapsulation of the stack -- only the `pop()` and `push()`
methods should be public, and other data should not be accessible.

Let's refactor the implementation to use closures, so that there is no way to
access `items` outside of the function scope.

Think about it, and when you're ready, let's check out one possible
implementation.

<details><summary>One implementation</summary>

If we move the `items` array to a variable inside the `createStack` scope but
not inside the object it returns, there is no way to access the `items` array
directly.

The `items` array is now effectively private outside the scope of the
`createStack` function, and the stack is encapsulated, with only its `pop()` and
`push()` methods exposed.

`pop()` and `push()` are closures, capturing the `items` array from the
`createStack` scope.

```js
function createStack() {
	const items = [];
	return {
		push(item) {
			items.push(item);
		},
		pop() {
			return items.pop();
		},
	};
}
const stack = createStack();
stack.push(10);
stack.push(5);
stack.pop(); // => 5
stack.items; // => undefined
```

</details>

### Revisiting the Set Timeout Code

Now that we've taken a look at these examples, let's revisit the example from
the introduction.

```js
for (var i = 0; i < 3; i++) {
	setTimeout(() => {
		console.log(i);
	}, i * 1000);
}
```

Using what we've talked about today, answer the following questions.

1. Why does the code log the number "3" three times to the console? Use the
   concept of closures in your response.

<!-- This code executes in two phases.

In the first phase, the for loop iterates three times. During each iteration, a new anonymous logging function is created, which captures the variable i. setTimeout schedules the callback function for execution after i * 1000 ms. When the for-loop completes, i has a value of 3.

In the second phase, setTimeout executes the scheduled callback functions. It reads the current value of the variable i, which is 3 and logs 3 to the console. That's why we get 3, 3,3 logged to the console.


 -->

2. What are some ways we can modify the code to produce the expected result?

<!-- Option 1: use closures and wrap the setTimeout in an IIFE -->

<!-- ```js
for (var i = 0; i < 3; i++) {
	(function (x) {
		setTimeout(() => console.log(x), x * 1000);
	})(i);
}
``` -->

<!-- Option 2: In an ES6 context, use `let` instead of `var` to scope i variable to the for-loop -->
<!--
```js
for (let i = 0; i < 3; i++) {
	setTimeout(() => console.log(i), i * 1000);
}
``` -->

## References

- [MDN on Closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)
- [Dmitri Pavlutin on Closures](https://dmitripavlutin.com/javascript-closure/)
- [7 Interview Questions on JavaScript CLosures](https://dmitripavlutin.com/javascript-closures-interview-questions/)
- [37 Essential Javascript Interview Questions](https://www.toptal.com/javascript/interview-questions)
