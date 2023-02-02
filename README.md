# JavaScript Closures

What do you think the code below logs to the console?

```js
for (var i = 0; i < 3; i++) {
	setTimeout(() => {
		console.log(i);
	}, i * 1000);
}
```

You might be surprised when we actually try it. The reason this code does not
behave as you might expect is due to a language feature called closures.

Closures are a classic topic in a JavaScript interview, but they have a
reputation for being challenging to understand. Today we'll dive into what they
are, and take a look at some examples that illuminate how they work.

## Goals

- Define closure
- Identify closure examples
- Write a closure

## What are Closures?

A closure is the lexical environment created by a function with references to
its surrounding variables. Closures give us access to variables in an outer
function's scope from an inner function.

JavaScript creates closures every time a function is created. In other words,
whenever a function is defined, a closure gets created.

## Closure Examples

Let's see what that actually means by taking a look at some examples of
closures.

### A Simple Example

We'll start with a relatively straightforward example. We know that we can
define and call functions in other functions.

```js
function outerFunction() {
	let mySpecialNumber = 123;

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

As we can see, JavaScript PRESERVES the `mySpecialNumber` variable even after
the outer function is done executing. This is due to closures. When we call the
inner function after the outer function is done executing, the inner function
still has access to the outer function's `mySpecialNumber` variable. 🤯

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
outer function,

### Document Button

### Revisiting the Set Timeout Code

## References

- MDN on Closures
