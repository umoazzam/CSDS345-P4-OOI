# CSDS 345: P4 - OO Interpreter Project

Authors: Kasper Latawiec, Usman Moazzam, Eric Xu

## The Language

In this homework, you will expand on the interpreter of part 3 by adding classes and objects (instances of classes)

### Example Program:

```
class A {
  var x = 6;
  var y = 7;

  function prod() {
    return this.x * this.y;
  }

  function set2(a, b) {
    x = a;
    y = b;
  }
}

class B extends A {
  function set1(a) {
    set2(a, a);
  }

  static function main () {
    var b = new B();
    b.set1(10);
    return b.prod();
  }
}
```

### Parser Constructs

```
class A {                  =>   (class A () body)
  body

class B extends A {        =>   (class B (extends A)  body)
  body

static var x = 5;          =>   (static-var x 5)
var y = true;              =>   (var y true)

static function main() {   =>   (static-function main () body)
  body

function f() {             =>   (function f () body)
  body

function g();              =>   (abstract-function g ())

class A { 
   A(x) {                  =>   (constructor (x) body) 
     body

new A()                    =>   (new A)

a.x                        =>   (dot a x)

new A().f(3,5)             =>   (funcall (dot (new A) f) 3 5)
```

## What Your Code Should Do

Your interpreter should now take two parameters, a file and a classname. For example, (interpret "MyProgram.j" "B"), where file is the name of the file to be interpreted, and classname is the name of the class whose main method you are to run. The function should call parser on the file file, and then lookup (string->symbol classname) in the state to get the desired class, and then lookup the main method of this class. The final value returned by your interpreter should be whatever is returned by main.

### Details

1. Note that we now allow the object type in our language. So, objects can be assigned to variables, passed as parameters, returned from functions, and thrown as exceptions.
2. All mathematical and comparison operators should only be implemented for integers, and logical operators should only be implemented for booleans.
3. You are not required to implement the == operator for objects, but you can if you wish.
4. The only operator that is required to work on objects is the dot operator.
5. The new operator will return an object of the desired class.
6. The new operator can only be used in expressions, not as the start of a statement.
7. Variables and methods can now be static (class) or non-static (instance).
8. The main method should be static.
9. The language supports use of this and super object references.
10. The top level of the program is only class definitions.
11. Each class definition consists of assignment statements and method definitions (just like the top level of part 3 of the interpreter).
12. Nested uses of the dot operator are allowed.

You should be able to create objects (using a generic constructor), set values, call methods, and use values this and super. You do not have to support user defined constructors. You do not have to support static fields or methods (other than the main method) and you do not have to support abstract methods.

## Other Language Features

Everything we did previously in the interpreter is still allowed: functions inside funtions, call-by-reference (if you chose to implement it). A function that is inside a static function will not have the static modifier, but it will be static simply by the nature of the containing function.

## For Some Additional Challenge:

**Add static (class) methods and fields.** For static methods, the only change is that the method will not get the "extra" parameter this. For static fields, you will need to change the places that do field lookup and assign so that the method looks in three different environments: the local environment, the class fields, and the instance fields. This will also require you to change how dot is handled because the left side of the dot can now be a class name.

**Add abstract methods.** The interpreter will only support non-static abstract methods. The change you must make is to give an abstract method an appropriate value in the body portion of the closure to indicate that the body does not exist. When an instance is created, you should verify that any abstract methods have been overridden. If any have not, give an appropriate error.

**Add user-defined constructors.** In the language, the constructor will look like a method that has the same name as the class name, but is not preceded with function, and in the parse tree it will be identified by constructor.

```
class A {
  A(x) {               =>  (constructor (x) body)
    body
  }
}
```

Constructors can be overloading, and constructors/new should have the following behavior:

1. Create the instance including space for all instance fields.
2. Lookup the appropriate constructor (if one exists). If no constructor exists, allow for a default constructor.
3. Call the constructor specified by the super or this constructor call that should be the first line of the constructor body (or automatically call the parent class constructor with no parameters if no super() is present).
4. Evaluate the initial value expressions for the fields of this class, in the order they are in the code.
5. Evaluate the rest of the constructor body.
6. As a hint, make the constructor list be a separate environment of the class from the method environment. That way constructors will not be inherited.
