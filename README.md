# Prototypal inheritance in Javascript
### Objectives

By the end of this lesson, students should be able to:

* Demonstrate a use case that explains prototypal inheritance and what kind of flexibility it gives to programmers 
* Use namespaces to organize application code 
* Define a custom constructor method that sets one or more properties of a new object

### Preparation
Before this lesson, students should already be able to:

* Write basic Javascript, HTML, CSS and jQuery
* Understand basic object-oriented design (classes, functions, variables)
* Write and run Javascript code using a browser directly from a local file or using an in-browser tool like JSFiddle (in this lesson, we’ll use JSFiddle)

### An intro to prototypal inheritance
##### What is ES6?
A new standard that introduces some constructs that make it easier to do traditional object oriented design

##### Why are we using this instead of the old prototype model?
There's no real "under the hood" difference between the two.  The biggest reason to use the newer model is to be clearer about what you are defining:

Pre-ES6:

```javascript
function Rectangle(width, height) { 
    this.width = width; 
    this.height = height; 
}
```

ES6:

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}
```

With the ES6 model, it is obvious you are defining a class that should have members and functions.

Using the `class` keyword also enforces using `new` when creating a new instance of the class.

### Let's code it!

First example:  https://jsfiddle.net/pheonixblade9/06pvmsj6/10/

* Navigate to jsfiddle.net
* Create a new fiddle
* Select jQuery 2.1.3 selected in the left hand dropdown

##### JS

This is a basic constructor that takes in two arguments and sets their values as properties.

```javascript
'use strict';

class Rectangle {
    constructor(width, height) {
        this.width = width;
        this.height = height;
    }
}
```

#### Note: what is 'use strict'?

`'use strict'` is a flag to let the Javascript engine know we're writing modern javascript.  It causes the compiler to flag certain behaviors that are unacceptable in newer versions of javascript such as omitting semicolons.  

Adding this at the top of the file is required for writing code using ES6 in Chrome.  Other browsers may not require it.

##### HTML

Add some basic HTML to take in a width and height:

```html
<div>Width:<span id="width" />
</div>
<div>Height:<span id="height" />
</div>
<button id="createNew">Create new rectangle</button>
<input id="widthInput" placeholder="width" />
<input id="heightInput" placeholder="height" />
```

#### CSS

Make the output a little bit prettier with some spacing:

```css
input, button, div {
    display: block;
    margin: 10px;
    padding: 5px;
}
span {
    margin: 3px;
}
```

#### Add functions to our prototype

Now let's add in a function inside the `Rectangle` class to calculate the area:

```javascript
//this is a function added to the prototype
calcArea() {
    return this.width * this.height;
}
```

This can be accessed directly or via a property, another ES6 feature:

```javascript
//this is a property accessor
get area() {
    return this.calcArea();
}
```

##### Add output fields in the HTML

We need a way to see what the values calculated as the area are, so we'll add more `div`s to demonstrate this immediately after the height `div`.  Here's what our final HTML will look like:

```html
<div>Width:<span id="width" />
</div>
<div>Height:<span id="height" />
</div>
<div>Property area:<span id="propArea" />
</div>
<div>Function area:<span id="funcArea" />
</div>
<button id="createNew">Create new rectangle</button>
<input id="widthInput" placeholder="width" />
<input id="heightInput" placeholder="height" />
```

##### Hook up our HTML and our JS via jQuery

We'll add some code that creates a Rectangle and calculates the `area` and sets it to our text outputs:

```javascript
$('#createNew').click(function () {
    var width = $('#widthInput').val();
    var height = $('#heightInput').val();
    //add validation here if you like
    //perhaps alert if the input values are not a number

    var r = new Rectangle(width, height);

    $('#width').text(r.width);
    $('#height').text(r.height);
    $('#propArea').text(r.area);
    $('#funcArea').text(r.calcArea());
    console.log(r);
});
```

You should now be able to click the "Create new rectangle" button and see the values output in the HTML.

You'll notice that both of the outputs for `propArea` and `funcArea` output the same number - this is to demonstrate the usage of properties and functions.  A property is essentially a function that returns a value inherent to the class.  In this case, it is `area`.


### Namespaces in Javascript (3min)
##### What are they used for?

Namespaces are a great way to separate out functionality into different sections of the code.  They also prevent method name collisions - something that can easily happen in large code bases.

##### Namespacing via functions

Example: https://jsfiddle.net/pheonixblade9/d44zpon8/

Namespacing can be done through classes or functions.  Here is a typical function namespacing example:

```javascript
//"namespacing" via functions

var cat = {
    meow: function () {
        alert('Meow!!');
    },
    scram: function () {
        alert('Zoom!');
    }
};

var dog = {
    bark: function () {
        alert('Bark! Bark!');
    },
    scram: function () {
        alert('Zoom zoom zoom!');
    }
};

cat.scram();
dog.scram();

cat.meow();
dog.meow(); //this doesn't work!
```

You may immediately notice a problem with this - it's easy to mix up method names and types here if you want to extend behavior of type of object.  This can be solved using inheritance.

##### Namespacing via classes

Example: https://jsfiddle.net/pheonixblade9/1ugh6nxf/

Going back to the `class` keyword we introduced earlier, there is an additional keyword, `extends` that allows a class to inherit from another.  All of the functions, properties, and variables are inherited down.  This is best explained through an example:

```javascript
'use strict';

class Animal {
    constructor(sound) {
        this.sound = sound;
    }

    makeSound() {
        alert(this.sound);
    }
}

class Dog extends Animal {
    constructor() {
        super('woof!');
    }
}

class Cat extends Animal {
    constructor() {
        super('meow!');
    }
}

var cat = new Cat();
cat.makeSound();

var dog = new Dog();
dog.makeSound();
```

Here, we can see that `Cat` and `Dog` are both inheriting from `Animal`.  This means that they can call the function `makeSound` from `Animal`.

The keyword `super` here tells the constructor to use the constructor from the class it is inheriting from.  This way, we can pass some information we know about the inheriting class directly - in this case, it's the sound the `Animal` makes.

`super` can also be used to access the methods of the class we're inheriting from:

```javascript
class Dog extends Animal {
    constructor() {
        super('woof!');
    }

    woof() {
        super.makeSound();
    }
```

Now, if we call `dog.woof()`, the call to `makeSound()` will occur just as if we called `makeSound()` directly.

You likely want to do something other than just call the methods already defined.  How do we do that?  By adding functions directly to the classes as demonstrated before!

```javascript
class Dog extends Animal {
    constructor() {
        super('woof!');
    }

    woof() {
        super.makeSound();
    }

    roll() {
        alert('the dog rolls in some leaves');
    }
}
```

Now we can call `roll()` on our `Dog` object and see what happens.  You'll notice it doesn't work with our `Cat object we created, since they are in different namespaces.

##### Final JS

```javascript
'use strict';

class Animal {
    constructor(sound) {
        this.sound = sound;
    }

    makeSound() {
        alert(this.sound);
    }
}

class Dog extends Animal {
    constructor() {
        super('woof!');
    }

    woof() {
        super.makeSound();
    }

    roll() {
        alert('the dog rolls in some leaves');
    }
}

class Cat extends Animal {
    constructor() {
        super('meow!');
    }

    meow() {
        super.makeSound();
    }

    scratch() {
        alert('the cat scratches you!');
    }
}

var cat = new Cat();
cat.meow();
cat.scratch();

var dog = new Dog();
dog.woof();
dog.roll();
```

##### Note: there is another way!

If you need to use prototypal inheritance on the fly without using a `class`, you can also simply access the object's `prototype` directly:

```javascript
function myProto(message) {
    this.message = message;
}

myProto.prototype = {
    constructor: myProto,
    alert: function () {
        alert(this.message);
    }
}

var newProto = new myProto('Hi there!');
newProto.alert();
```

This is effectively the same as defining it in the `class`.  It's just a different way of doing the same thing and is sometimes useful when needing to change the behavior of an object on the fly.

### Conclusion

Prototypal inheritance and classes are a great way to organize your code into functional units.  For larger projects, this is crucial.  It allows breaking individual chunks of code into different files and makes it easier to write maintainable, decoupled code.
