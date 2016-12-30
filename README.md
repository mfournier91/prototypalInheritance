#OOJS Prototypal Inheritance

##Learning Objectives
* Describe what an Object prototype is
* Understand the javascript prototype chain
* Explain prototypal inheritance and demonstrate a use case for it
* Explain how does prototypal inheritance helps programmers


##Object prototypes
All javascript objects have prototypes. The prototypes themselves are actually objects which define what properties and methods an object should have. For example, if we make a new array we can automatically call push on it.

```
var myArr = [];
myArr.push(42);
console.log(myArr);  // [42]
```
That's because all Arrays inherit from Array.prototype, which has the push method on it. You can enter Array.prototype into your browser console to see what other methods arrays have. All javascript objects inherit properties and methods from their prototype.

Ok, this is pretty abstract so far. Why should I as a programmer care about prototypes?

Lets say you wanted to define a bunch of animal objects that were similar but different. You might do something like this.

```
var callie = {
  type: 'mammal',
  subType: 'cat',
  facialExpression: 'scowl',
  lifesPurpose: 'Kill all humans',
  speak: function() {
    console.log('meow');
  },
  legs: 4
}

var lady = {
  type: 'mammal',
  subType: 'dog',
  facialExpression: 'happy or whimpering',
  speak: function() {
    console.log('woof');
  },
  legs: 4
}
```

This works but it is not very DRY. We're repeating property names on similar objects. We can make our lives easier by making a prototype that new animal objects can inherit from.

##Constructor functions
We can use Constructor functions to make custom object prototypes. Here is an example of how we might write the code above.

```
function Animal(type, subType, facialExpression, speech) {
  this.type = type;
  this.subType = subType;
  this.facialExpression = facialExpression;
  this.speak = function() { console.log(speech)};
  this.legs = 4;
}
var callie = new Animal('mammal', 'cat', 'scowl', 'meow');
callie.lifesPurpose = 'Kill all humans';

var lady = new Animal('mammal', 'dog', 'happy or whimpering', 'woof');
```

This is much better because now, whenever we need a new animal, we just pass the values that are specific to the new instance of the Animal object as arguments into the Constructor function. If we want to add properties to our instance variable that are not present in the Constructor we can do so with dot notation. For example not all animals have a life's purpose but callie does so we added that manually after creating out instance for callie.  
