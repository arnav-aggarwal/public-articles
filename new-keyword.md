
# Javascript’s “new” Keyword Explained as Simply as Possible



### Normal Function Call

To explain what new does, let’s start with just a normal function, called without new. We want to write a function that will create “person” objects. It’ll give these objects name and age properties based on parameters that it takes in.

    function personFn(name, age) {
        var personObj = {};

        personObj.name = name;
        personObj.age = age;
        
        return personObj;
    }

    var alex = personFn('Alex', 30);
    // -> { name: 'Alex', age: 30 }

Simple enough. We create an object, add the properties to it, and return it at the end.

### new

![Object-oriented programming](https://cdn-images-1.medium.com/max/5040/1*HFlvxchL2IMFERwQsQClRw.png)*Object-oriented programming*

Let’s create a function that does the same thing, but we want it to be invoked using new. This function will create the same object as the one above. Common practice is to make functions that are meant to be invoked with new start with a capital letter. These functions are also referred to as **constructors**.

    function PersonConstructor(name, age) {
        this.name = name;
        this.age = age;
    }

    var alex = new PersonConstructor('Alex', 30);
    // -> { name: 'Alex', age: 30 }

Invoking personFn normally and invoking PersonConstructor with new both result in the same object being created. What’s going on?

The new keyword invokes a function in a special way. It adds some implicit code that we don’t see. Let’s expand the above function to show everything that’s happening. The commented lines are pseudocode representing functionality that is implicitly added by the JS engine when using new.

    function PersonConstructor(name, age) {
        // this = {};
        // this.__proto__ = PersonConstructor.prototype;

        // Set up logic such that: if
        // there is a return statement
        // in the function body that
        // returns anything EXCEPT an
        // object, array, or function:
        //     return 'this' (the newly
        //     constructed object)
        //     instead of that item at
        //     the return statement;

        this.name = name;
        this.age = age;

        // return this;
    }

Let’s break it down. new:

1. Creates a new object and binds it to the this keyword.

1. Sets the object’s internal [[[Prototype]] property, __proto__](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto), to be the prototype of the constructing function. This also makes it so the [constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor) of the new object is prototypically inherited.

1. Sets up logic such that if a variable of any type other than object, array, or function is returned in the function body, return this, the newly constructed object, instead of what the function says to return.

1. At the end of the function, returns this if there is no return statement in the function body.

Let’s show that these statements are valid, one by one.

    function Demo() {
        console.log(this);
        this.value = 5;
        return 10;
    }

    /*1*/ var demo = new Demo(); // -> {}
    /*2*/ console.log(demo.__proto__ === Demo.prototype); // -> true
          console.log(demo.constructor === Demo); // -> true
    /*3*/ console.log(demo); // -> { value: 5 }

    function SecondDemo() {
        this.val = '2nd demo';
    }

    /*4*/ console.log(new SecondDemo()); // -> { val: '2nd demo' }

If you aren’t familiar with constructors or prototypes, don’t worry about it too much. You’ll run into them as you continue to learn Javascript. For now, just understand that the new object implicitly returned by the constructor function will be able to inherit properties and methods.

### Calling a non-constructor with new

What happens if we invoke a normal function like personFn using new? Nothing special. The same rules apply. in the case of personFn, we see nothing explicitly happening.

    var alex = new personFn('Alex', 30);
    // -> { name: 'Alex', age: 30 }

Why? Let’s add our implicit code in to personFn.

    function personFn(name, age) {
        // this = {};
        // this.constructor = PersonConstructor;
        // this.__proto__ = PersonConstructor.prototype;

        // Set up logic such that: if
        // there is a return statement
        // in the function body that
        // returns anything EXCEPT an
        // object, array, or function:
        //     return this (the newly
        //     constructed object)
        //     instead of that item at
        //     the return statement;

        var personObj = {};

        personObj.name = name;
        personObj.age = age;
        
        return personObj;
        
        // return this;
    }

The implicit code is still added in:

* It binds this to a new object and sets its constructor and prototype.

* It adds logic that will return this instead of a non-object.

* It adds an implicit return this statement at the end.

This doesn’t affect our code, since we don’t use the this keyword in our code. We also explicitly return an object, personObj, so the returning logic and the return this line have no use. Effectively, using new to invoke our function here has no effect on the output. If we were using this or if we weren’t returning an object, the function would have different effects when invoked with and without new.

If this was useful, please hit the heart and feel free to subscribe and read my other articles.

[Learn how Array.map and Array.filter work by writing them yourself.](https://medium.com/@arnav_aggarwal/array-functions-map-filter-18a6e5f75da1)

[Master || and && Logical Operators](https://codeburst.io/javascript-and-logical-operators-89b2ac3409f8)

[Learn how || and && work to help you write simpler, powerful code.](https://codeburst.io/javascript-and-logical-operators-89b2ac3409f8)


### That’s it. Go write some code.
