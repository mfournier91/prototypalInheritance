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

*Ok, this is pretty abstract so far. Why should I as a programmer care about prototypes?*

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
*Note: Constructor functions should start with a capital letter by convention*

Also note that when we expected values of properties to stay the same throughout various instances, we set the value in the Constructor. We gave legs an actual value in the Constructor because we expected all instances to have 4. If we needed to make a human instance of our animal object, we could either make legs a variable in the Constructor or overwrite the value like so.
```

var marc = new Animal('mammal', 'human', 'varied', 'i have opinions about stuff');
marc.legs = 2;

```
*This is very cool and all, but it seems like magic. Whats happening? What's 'new'? Whats 'this'? Whats the difference between the Constructor and the instance?*

##What's 'new'?

Remember that the constructor is just a function: it's just capitalized by convention and there's nothing inherently different about it except how we as programmers use it.
The instance is the object created by calling the constructor with the new keyword.
Keeping all that that in mind, lets add a console.log(this) to the constructor and lets call the function with and without the new keyword.
```

function Animal(type, subType, facialExpression, speech) {
  this.type = type;
  this.subType = subType;
  this.facialExpression = facialExpression;
  this.speak = function() { console.log(speech)};
  console.log(this);
}
var bob = new Animal('mammal', 'human', 'happy', 'my name is bob');
//LOGGED TO CONSOLE: //
// Animal {type:mammal, subType: human, facialExpression: 'happy', speak: function ()}
//                   //

// now without new
var notBob = Animal('mammal', 'human', 'happy', 'my name is bob');
//LOGGED TO CONSOLE: //
// Window {...}
//                  //
console.log(notBob) // undefined

```
When we define bob with the new keyword, 'this' refers to the instance object being created. When we define notBob without the new keyword, 'this' refers to the global object (window in your browser). So not only is notBob undefined but now if we log facialExpression we will get 'happy': we've leaked variables into the global namespace.
Basically, think of the new keyword as creating a new object and allowing the constructor function to refer to it via 'this'.

##Inheritance and the prototype chain
Describing the 'type' of an object seems kind of silly. Couldn't we have another object represent a specific type of a more generic object? Mammal is a type of animal for example, so why not make a mammal object instead of using 'type' as a property. This is the idea of inheritance: a more specific object inherits properties from a more generic object. For example all animals eat, therefore all mammals eat. However all mammalian species are capable of producing milk, yet not all animals can. We've discussed the concept of inheritance when we studied OOP in ruby. You may remember seeing something like this.
```
#... our animal class not shown

class  Mammal < Animal
  def produce_Milk
    puts "Producing milk"
  end
end

```
So the mammal class would inherit from the animal class and would have another method of its own. *So javascript is probably the same, right? Make an Animal class and a Mammal class that inherits from it.*
Not exactly. Inheritance in javascript is prototype-based not class based. There is a 'class' keyword in es6 but it's just syntactic sugar: in reality there is no class and inheritance is happening via the prototype chain of objects.
*What does that mean?*
It means that if you want to create inheritance hierarchies in javascript, you have to create a new prototype for subclasses based on superclass prototypes.
Let's just jump into some examples.

```

// let's start with some empty constructor functions that are unrelated to each other.
function Animal() {}
function Mammal() {}
function Kitty() {}
// lets make Mammal inherit from Animal
Mammal.prototype = Object.create(Animal.prototype);
// this says if you create a new Mammal it will have the properties and methods available from the Animal prototype
Kitty.prototype = Object.create(Mammal.prototype)
// Now new kitties have access to the properties and methods on the mammal prototype.

//lets add functionality.
Animal.prototype.eat = function() {console.log('yum')};
Mamal.prototype.milk = function() {console.log('made milk')};
Kitty.prototype.scratch = function() {console.log('I scratch you')};
// Make some instances
var callie = new Kitty();
var someAnimal = new Animal();
var someMammal = new Mammal();
// now callie can do everything.
callie.scratch();
callie.milk();
callie.eat();
// the mammal cannot scratch because it is not a cat
someMammal.scratch() // Error <Animal> has no method scratch
// the animal can only eat
// What if we change callie's eat method
Kitty.prototype.eat = function() {console.log('yummy wet food and catnip')};
callie.eat() // yummy wet food and catnip
someAnimal.eat() // yum
//This happens because javascript looks at the most specific prototype first and goes up the chain. So the most specific object's methods overwrite the methods from the objects it inherits from.
