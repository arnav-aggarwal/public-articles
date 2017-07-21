
# The Simple Rules to ‘this’ in Javascript

A few rules determine what the 'this’ keyword is inside a function

![](https://cdn-images-1.medium.com/max/2000/1*IZBNrGFzTkPOmnGS69_O0w.png)

Determining what this is is actually rather simple. The overarching rule is that this is determined at the time a function is invoked by inspecting where it’s called, its *call site*. It follows these rules, in order of precedence.

## Rules

1. If the new keyword is used when calling the function, this inside the function is a brand new object.

    function ConstructorExample() {
        console.log(this);
        this.value = 10;
        console.log(this);
    }

    new ConstructorExample();

    // -> {}
    // -> { value: 10 }

For more information about new, feel free to read my article.
[**Javascript’s “new” Keyword Explained as Simply as Possible**
*Normal Function Call*codeburst.io](https://codeburst.io/javascripts-new-keyword-explained-as-simply-as-possible-fec0d87b2741)

2. If [apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply), [call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call), or [bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) are used to call a function, this inside the function is the object that is passed in as the argument.

    function fn() {
        console.log(this);
    }

    var obj = {
        value: 5
    };

    var boundFn = fn.bind(obj);

    boundFn();     // -> { value: 5 }
    fn.call(obj);  // -> { value: 5 }
    fn.apply(obj); // -> { value: 5 }

3. If a function is called as a method — that is, if dot notation is used to invoke the function — this is the object that the function is a property of. In other words, when a dot is to the left of a function invocation, this is the object to the left of the dot. (ƒ symbolizes function in the code blocks)

    var obj = {
        value: 5,
        printThis: function() {
            console.log(this);
        }
    };

    obj.printThis(); // -> { value: 5, printThis: ƒ }

4. If a function is invoked as a *free function invocation*, meaning it was invoked without any of the conditions present above, this is the global object. In a browser, it’s window.

    function fn() {
        console.log(this);
    }

    // If called in browser:

    fn(); // -> Window {stop: ƒ, open: ƒ, alert: ƒ, ...}

*Note that this rule is the same as rule 3 — the difference is that a function that is *not* declared as a method automatically becomes a property of the global object, window. This is therefore an implicit method invocation. When we call fn(), it’s interpreted as window.fn(), so this is window.

    console.log(fn === window.fn); // -> true

5. If multiple of the above rules apply, the rule that is higher wins and will set the this value.

6. If the function is an ES2015 arrow function, it ignores all the rules above and receives the this value of its surrounding scope **at the time it’s created**. To determine this, go one line above the arrow function’s creation and see what the value of this is there. It will be the same in the arrow function.

![](https://cdn-images-1.medium.com/max/5056/1*cA1_BR0-E4OCKYLOhKTchQ.png)

    const obj = {
        value: 'abc',
        createArrowFn: function() {
            return () => console.log(this);
        }
    };

    const arrowFn = obj.createArrowFn();

    arrowFn(); // -> { value: 'abc', createArrowFn: ƒ }

Going back to the 3rd rule, when we call obj.createArrowFn(), this inside createArrowFn will be obj, as we’re calling it with dot notation. obj therefore gets bound to this in arrowFn. If we were to create an arrow function in the global scope, this would be window.

## Applying the Rules

Let’s go over a code example and apply our rules. Try figuring out what this will be with the two different function calls.

### Determining Which Rule Applies

    var obj = {
        value: 'hi',
        printThis: function() {
            console.log(this);
        }
    };

    var print = obj.printThis;

    obj.printThis(); // -> {value: "hi", printThis: ƒ}
    print(); // -> Window {stop: ƒ, open: ƒ, alert: ƒ, ...}

obj.printThis() falls under rule 3 — invocation using dot notation. On the other hand, print() falls under rule 4 as a free function invocation. For print() we don’t use new, bind/call/apply, or dot notation when we invoke it, so we go to rule 4 and this is the global object, window.

### When Multiple Rules Apply

When multiple rules apply, the rule higher on the list wins.

    var obj1 = {
        value: 'hi',
        print: function() {
            console.log(this);
        },
    };

    var obj2 = { value: 17 };

If rules 2 and 3 both apply, rule 2 takes precedence.

    obj1.print.call(obj2); // -> { value: 17 }

If rules 1 and 3 both apply, rule 1 takes precendence.

    new obj1.print(); // -> {}

## Libraries

Libraries will sometimes intentionally bind the value of this inside their functions. this is bound to the most useful value for use in the function. jQuery, for example, binds this to the DOM element triggering an event in the callback to that event. If a library has an unexpected this value that doesn’t seem to follow the rules, check its documentation. It’s likely being bound using bind.

### If this (no pun intended) was helpful, please hit the ❤ and feel free to subscribe and check out some of my other recent publications.
[**Javascript’s “new” Keyword Explained as Simply as Possible**
*Normal Function Call*medium.com](https://medium.com/@arnav_aggarwal/javascripts-new-keyword-explained-as-simply-as-possible-fec0d87b2741)
[**Master Map & Filter, Javascript’s Most Powerful Array Functions**
*Learn how Array.map and Array.filter work by writing them yourself.*medium.com](https://medium.com/@arnav_aggarwal/array-functions-map-filter-18a6e5f75da1)
[**Master Javascript’s New, Cutting-Edge Object Spread Operator**
*I recently read a great explanation of the spread operator applied to arrays. With Node v8.0.0 introducing object rest…*medium.com](https://medium.com/@arnav_aggarwal/master-javascripts-object-spread-operator-3803430e99aa)

### That’s it. Go write some code.
