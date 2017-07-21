
# Master Javascript’s New, Cutting-Edge Object Spread Operator



I recently read a [great explanation](https://hackernoon.com/javascript-the-spread-operator-a867a71668ca) of the spread operator applied to arrays. With Node v8.0.0 introducing object rest and spread functionality behind the harmony flag, I thought I’d write up an explanation for the spread operator applied to objects. It can be used on the front-end with the appropriate [babel plugin](https://babeljs.io/docs/plugins/transform-object-rest-spread/).

Both “rest operator” and “spread operator” refer to the same operator (…), used differently. When you see “rest”, it’s being used to gather up properties. When you see “spread”, it’s spreading them out.

Many of the use cases discussed here are taken from [http://2ality.com/2016/10/rest-spread-properties.html](http://2ality.com/2016/10/rest-spread-properties.html), written by [Dr. Axel Rauschmayer](http://rauschma.de/). I have essentially tried to boil down that page into a simpler guide with different examples.

## Spread Operator

The spread operator will spread all enumerable, own properties of one object to another object.

    const obj1 = { c: 3, d: 4 };
    const obj2 = { a: 1, b: 2, **...obj1** };

    console.log(obj2); // -> { a: 1, b: 2, c: 3, d: 4 }

A good use case is **creating a shallow copy** of an object.

    const obj = { a: 123, b: 456 };
    const objCopy = { ...obj };

    console.log(objCopy); // -> { a: 123, b: 456 }

Another use case is **merging objects** together.

    const obj1 = { a: 111, b: 222 };
    const obj2 = { c: 333, d: 444 };

    const merged = { ...obj1, ...obj2 };
    console.log(merged); // -> { a: 111, b: 222, c: 333, d: 444 }

### Clashing properties

When duplicate properties clash, the order determines the outcome. The property put in last wins. If we spread the object last:

    const obj1 = { a: 'abc', **b: 'def'** };
    const obj2 = { b: 123, c: 456, ...obj1};

    console.log(obj2); // -> { a: 'abc', **b: 'def'**, c: 456 }

If we spread the object first:

    const obj1 = { a: 'abc', b: 'def' };
    const obj2 = { ...obj1, **b: 123**, c: 456 };

    console.log(obj2); // -> { a: 'abc',** b: 123**, c: 456 }

### Default property assignment

Property overriding makes default property assignment simple. Let’s say we’re making an app that requires a user to enter information. We want some default values placed on the user object if they leave some fields blank.

    const enteredInfo = {
        name: 'John Doe',
        phoneNumber: '123-456-7890'
    };

    const defaultInfo = { 
        name: 'N/A',
        address: null,
        phoneNumber: null,
        email: null
    };

    const completeUserInfo = { ...defaultInfo, ...enteredInfo };

## The Rest Operator

To understand the rest operator, we first need to understand ES6 [object destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

    const obj1 = { 
        a: 'abc',
        b: 'def',
        c: 'ghi',
        d: 'jkl'
    };

    const { a, b } = obj1;

    console.log(a, b); // -> 'abc', 'def'

Object destructuring plucks requested properties off of an object and assigns them to their respective variables. In the above example, on line 2, we pluck the properties a and b off of obj1 and make them available as variables. obj1 remains untouched.

With the rest operator, we add some functionality to object destructuring. Let’s change the previous example to make use of the rest operator:

    const obj1 = { 
        a: 'abc',
        b: 'def',
        c: 'ghi',
        d: 'jkl'
    };

    const { a, b, **...remainingProperties** } = obj1;

    console.log(a, b); // -> 'abc', 'def'
    console.log(remainingProperties); // -> { c: 'ghi', d: 'jkl' }

We are gathering up the *rest* of the properties in obj1 that we did not explicitly decide to pluck off, and assigning them to a new object, remainingProperties. This variable is now available to use.

## Caveats

### Shallow Cloning

If the item being spread into the target is an object, only a *reference* to that object will be copied. The spread operator will not recursively deep clone properties. In addition, only own, enumerable properties are copied. The prototype chain won’t be consulted.

### Getters & Setters

If present, the spread operator will use the getter of the source object to retrieve the value of the property. On assignment, that value is put directly on the target object without the getter or setter. The getter and setter, if present on the target, are overwritten and replaced with the static value.

### Read-Only Properties

A read-only property present higher on the prototype chain will be overridden by the spread operator.

If you liked this, please hit the heart and feel free to share. You can also read my other recent articles.
[**Master Javascript’s || and && Logical Operators**
*Learn how || and && work to help you write simpler, powerful code.*codeburst.io](https://codeburst.io/javascript-and-logical-operators-89b2ac3409f8)
[**Master Map & Filter, Javascript’s Most Powerful Array Functions**
*Learn how Array.map and Array.filter work by writing them yourself.*medium.com](https://medium.com/@arnav_aggarwal/array-functions-map-filter-18a6e5f75da1)

Thanks for reading.
