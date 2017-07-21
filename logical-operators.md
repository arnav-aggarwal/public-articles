
# Master the Power Behind Javascript’s Logical Operators

Learn how || and && work to help you write simpler, powerful code.

![](https://cdn-images-1.medium.com/max/2000/1*IZBNrGFzTkPOmnGS69_O0w.png)

The [|| and &&](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators) operators are used ubiquitously throughout Javascript, yet many new developers don’t fully understand the mechanisms behind them. Let’s go into how these operators work and what the Javascript engine is doing under the hood. We can reveal some neat tricks and begin to understand some code patterns that we’ll encounter in the wild.

Let’s jump right in.

## || operator

The || operator is read left to right. The right side of the expression, however, is sometimes never evaluated at all. If the Javascript engine doesn’t have to evaluate the right side of the expression, it won’t. For example:

    if(x === 1 || x === 2) {
        doSomething();
    }

x === 1 will be evaluated first. **The || operator returns the first “truthy” value it finds, going left to right**. If x does indeed equal 1, the statement is true — no matter what the right side of the expression evaluates to, the result of the statement must be true. The Javascript engine won’t even try to evaluate the right side— it’s just a waste of processing power. In this case, if x !== 1, then the engine will try to evaluate the right side. If x === 2, the statement will evaluate to true and the code inside your if statement will run.

This idea of the engine dynamically deciding whether or not to run the right side of the expression is called [short circuit evaluation](https://en.wikipedia.org/wiki/Short-circuit_evaluation).

As a consequence of this, a shorter (but arguably more confusing) way to write an if statement is to use the || operator — for example, consider that we have some code in which we’ve set a variable named flag to true or false. Later down, we might see:

    if(flag === false) {
        doSomething();
    }

This will have the same effect as simply writing:

    flag || doSomething();

Because the left side, flag, will be evaluated as false, the || statement will then attempt to evaluate the right side, and therefore execute the function doSomething. However, if flag is true, the right side won’t even be evaluated. The Javascript engine will just move on.

In this example, we’re using || in an unconventional, almost hacky way. Usually an || statement’s end value, whether true or false, is used in an if statement or stored in a variable for later use. Here, we’re not even assigning its result to a variable. We’re using it for its short-circuit side effect.

## || Return Value

The || operator doesn’t actually return true or false— it returns the result of the left expression if that result is “truthy”; if the left expression is “falsey”, it returns the result of the right expression, whether it’s “truthy” or “falsey”. So, the result of 5 || 7 is 5. The result of null || 3 is 3. Examples:

If the left side is “truthy”, the result of || will ALWAYS be the left side.

    console.log(3 || 4); // -> 3
    console.log('Hi' || null); // -> Hi
    console.log({} || ''); // -> {}
    console.log(0 + 2 || 17); // -> 2

If the left side is “falsey”, the result of || will ALWAYS be the right side.

    console.log(false || 123); // -> 123
    console.log(null || 'abc'); // -> abc
    console.log(0 || null); // -> null
    console.log(false || undefined); // -> undefined
    console.log(-5 + 3 + 2 || 17); // -> 17

This means you can do things like this, which you’ll see out in the wild.

These two functions are equivalent. We’re trying to assign a default value to the parameter if it’s not passed in. The second function is just slightly more terse and you’ll see it frequently.

    function func1(parameter) {
        if(!parameter) {
            parameter = 10;
        }
    }

    function func2(parameter) {
        parameter = parameter || 10;
    }

## && Operator

And, of course, the && operator works similarly, but opposite.

    if(x < 3 && x > 0) {
        doSomething();
    }

In this block of code, the Javascript engine will first evaluate x < 3. If it results in false, the engine has no need to evaluate the right side. The entire expression *has* to be false. If x really is less than 3, the && operator must evaluate x > 0 to ensure that both statements are “truthy”. This means that

    if(flag === true) {
        doSomething();
    }

is functionally equivalent to

    flag && doSomething();

## Putting them together

These statements can also be used together. When they both appear in an expression, it’s a little tricker. You might be tempted to simply try to read them left to right. It’s a little more nuanced than that. **&& has precedence over ||**, meaning that && expressions will be evaluated first. For example, the following lines are equivalent:

    a || b && c || d && e

    a || (b && c) || (d && e)

A simple rule emerges, which is that you can pretend all && expressions are wrapped in parentheses. Once all && expressions have been evaluated, then go left to right:

    a || b && c || d && e && f

    a || (b && c) || (d && e) && f

    (a || (b && c)) || ((d && e) && f)

Let’s try some practice problems. See if you can figure out what the following will print out.

    console.log(0 || 7);
    console.log(0 || false);
    console.log(19 || 3);
    console.log(19 || 0);

    console.log(0 && 7);
    console.log(0 && false);
    console.log(19 && 3);
    console.log(19 && 0);

Now for some hard ones. Keep our order of operations rule in mind.

    console.log(true || true && false);
    console.log(false || 6 && undefined);
    console.log(true || 'abc' && 0);
    console.log(0 || 'abc' && 19 || false);
    console.log(7 && 3 && 0 || 4 && false);
    console.log(19 && 'Hello' && null || 4 || undefined && false);

That’s it! Go write some code.

If you liked this, please press the heart and feel free to check out my other publications.

* [Javascript Array Functions: A Walkthrough of Map & Filter](https://medium.com/@arnav_aggarwal/array-functions-map-filter-18a6e5f75da1)

* [Javascript’s New Object Spread Operator](https://medium.com/@arnav_aggarwal/master-javascripts-object-spread-operator-3803430e99aa)
