# Mortens noter til "5 lines of code"

***Refactoring can be expensive if you first have to understand the code!*** (p. 101)
## Rules
### Five Lines
### Either Call or Pass
Either ***call*** methods on an object or ***pass*** the object, not both
### If Only at the Start
### Function naming
- Honest - Describe the functions intention
- Complete - Capture all the functions does
- Understandable - Be undestandable for someone working in the domain

### Chapter 2 notes
Localizing invariants. Classes pulls functionality conserning the same data and thereby also pull invariants closer and localize them.
### Rule: 4.1.1 Never use if with else
Unless we are checking against a data type we do not control.
If elses are hardcoded decisions
Map third party types into types we control
Stand alone ***if***s are considered checks and ok at the start, ***if-else*** are decisions

Aim for late binding rather than early binding as else-if dictates. Also change by addition

### 4.1.3 REPLACE TYPE CODE WITH CLASSES
This refactoring pattern transforms an *enum* into an *interface*, and the enum's values becomes *classes*
1. Introduce new interface with temporary name. The interface should contain methods for each of the values in the enum
2. Create classes for each of the enums values. All methods except for the one corresponding to the class should `return false`
3. Rename the enum resulting in compiler errors
4. Change types from the old name to the temporary name and replace equality checks with the new methods
5. Replace remaining references to the enum values with instantiating the new classes, instead
6. When there are no more errors, rename the interface to its permanent name everywhere


### 4.1.5 PUSH CODE INTO CLASSES
1. Copy source method into all classes
2. Copy the method signature into the interface
3. Go through the method in all the classes
    1. Inline methods that return a constant expression (`Red::isRed()` replaced with `true`)
    2. Perform all computation up front. I.e. remove `if (true)` and `if (false) { ... }` and `false || true` becomes `true` 
    3. Change the name to a proper name
4. Replace the body of the original function with a call to our new method
### 4.1.7 INLINE METHOD
Inline the method at every call site and delete the method
1. Change method name to something temporary
2. Copy the body and note its parameteres
3. Wherever the compiler give errors, replace the call with the copied body and map the arguments to the parameters
4. Once we can compile without errors we know the original method is unused. Delete the original method

### 4.2.2 SPECIALIZE METHOD
This is esoteric because it goes against the instincts of many programmers. Specialized methods, the opposite of generalzied method, are called fewer places and hence becomes unused sooner...
1. Duplicate the method we want to specialize
2. Rename one of the methods to a new parameter name, and remove (or replace) the parameter we are using as the basis of our specialization
3. Correct the method accordingly so it has no errors
4. Swith the old calls over to use the new ones

### Rule: 4.2.4 NEVER USE SWTICH
One problem with switches is the fall-through logic. There is no way for us to tell the compiler if it is intentionally or not.

If a language does ***not*** allow omitting `default` then ***don't*** use `switch` ***at all***.

If a language does allow omitting `default` then `return` in every `case`.

### Rule: 4.3.2 ONLY INHERIT FROM INTERFACES
If inheriting from a class or abstract class you can accidentally forget a property or override some functionality!
Shared code causes coupling
Methods implemented in super classes are defaults which is bad

### 4.5.1 TRY DELETE THEN COMPILE
Editors has a hard time showing unused methods in iterfaces

1. Compile. There should be no errors
2. Delete a method from the interface
3. Compile
    1. If the compile fails, undo and move on
    2. Otherwise, go through each class and check wheter you can delete the same method from it without getting errors

### 5.1.1 UNIFY SIMILAR CLASSES
A set of constant methods is called a *basis*. A basis of two methods is a *two-point basis*. Unifying *X* classes we need at most *(X-1)-point basis*.

Unify classes that differs in a set of constant methods
1. Make all non-basis methods equal
    1. In the body of each version of the method, add an enclosing `if (true) { }` adound the existing code
    2. Replace `true` with an expression calling all the basis methods and comparing their result to their constant values
    3. Copy the body of each version, and past it with an `else` into all other versions
2. Now that only the basis methods are different, the second phase begins by introducing a field for each method in the basis and assigning its constant in the constructor
3. Change the method to return the new fields instead of the constant
4. Compile to ensure that we have not broken anything yet
5. For each class, one field at at time:
    1. Copy the default value of the field, and then make the default value a parameter
    2. Go through the compiler errors, and insert the default value as an argument
6. After all the classes are identical, delete all but one of the unified classes, and fix all the compiler errors by switching to the remaining class

### 5.2.1 COMBINE IFS
Join ifs with identical bodies (may require refactoring to achieve)
1. Verify that the bodies are indeed the same
2. Select the code between closing parenthesis of the first `if` and the opening parenthesis of the `else if`, press delete and type `||`. Insert an opening parenthesis after the `if` and a closing parenthesis before `{`. We always keep the parenthesis around the expressions to make sure we do not change the behavior.

### Rule: 5.3.2 USE PURE CONDITIONS
***No side effects, please!***

### 5.4.2 INTRODUCE STRATEGY PATTERN

### Rule: 5.4.3 NO INTERFACE WITH ONLY ONE IMPLEMENTATION
Never have an interface with only one implementation

### Rule: 5.4.4 EXTRACT INTERFACE FROM IMPLEMENTATION
Allows you to postpone introducing interfaces until they are needed
1. Create a new interface with the same name as the class we are extracting from
2. Rename the class from which we want to extract the interface, and make it implement the new interface
3. Compile, and go through the errors:
    1. If the error is caused by a `new`, change the instantiation to the new class name
    2. Otherwise, add the method that is causing the error to the interface

### 6.1.3 ELIMINATE GETTER AND SETTER
*Getters* breaks encapsulation and we are making our invariant global.
*Setters* introduce another layer of indirection, but often getters will be modified to return the new structure.
1. Make getter or setter private to get errors everywhere it is used
2. Fix the errors with PUSH CODE INTO CLASSES
3. The getter and setter is inlined as part of PUSH CODE INTO CLASSES It is therefore unused, so delete it to avoid other people trying to use it

### 6.2.3 ENCAPSULATE DATA
1. Create a class
2. Move the variable in the new class, replacing let with private. Simplify the variables' names; also make getters and setters for the variable
3. Because the variables are no longer in the global scope, the compliler helps us find all the references by giving errors. Fix these errors in the following five steps:
    1. Pick a good variable name for an instance of the new class
    2. Replace access with getters and setters on the pretend variable
    3. If we have errors in two or more different methods, add a parameter with the variable name from earlier as the first parameter, and put the same variable as the first argument in call sites
    4. Repeat until only one method error
    5. If we encapsulated variables, instantiate the new class at the point where the variables were declared. Otherwise, put the instantiation in the method that errors

# Part 2
## Chapter 7
> The halting problem6.1.3 ELIMINATE GETTER AND SETTER
>
> In general, programs are fundamentally unpredictable

### 7.1.3 Strength: Definite assignment
Jeg forst??r ikke hans tekstuelle kodeeksempel om at wrappe i en klasse og lave et objekt John!
### 7.1.4 Strength: Access Control
> **private** applies to the class *not* the object
### 7.1.5 Strength: Type Checking proves properties
Teaching the compiler properties of our program is the highest level of security we can get!
### 7.1.6 *Weakness* : Dereferencing null crashes our application
Foresl??r Christian at vi altid checker for _**null** f.eks. at **arr** ikke er **null** i nedenst??ende
```
function average(arr: number[]) {
    return sum(arr) / arr.length;
}
```
### 7.1.7 *Weakness* : Arithmetic errors cause overflows or crashes
E.g. division by zero i nedens??ende hvis kaldt med `average(f[])`
```
function average(arr: number[]) {
    return sum(arr) / arr.length;
}
```

Also consider adding and subtracting large numbers may cause overflow
### 7.1.8 *Weakness*: Out-of-bounds error crash our application

### 7.1.9 *Weakness*: Infinitie loops stall our application
### 7.1.10 *Weakness*: Deadlocks and race conditions cause unintended behavior

## The new team member - *The compiler*
### Gain safety by using the compiler as a todo list
E.g. to check all places for handling **default** in a enum, append _handled to the enum name. The compiler will tell all places, and as you check add _handled to the places. Finally remove _handled.

### Gain safety by enforcing sequences
See p. 183

### Gain safety by enforcing encapsulation
Localize invariants by using strict encapsulation.
### Gain safety by letting the compiler detect unused code
### Gain safety by definite values

## Don't fight the compiler
Happens when
- not understanding types
- being lazy
- not understanding architecture

### Understanding types
Author has some points on casting, dynamic types (any in TypeScript, dynamic in C#)

Run-time types: A method with, say 10 parameters, is changed to take a map. This might seem like a good idea, but you just prevented the compiler helping you do type checking, and now rely on runtime checks... 

### Laziness
Do use defaults as the compiler can't warn about wrong usage. Instead, don't be that lazy, and let the programmer answer the question every time. This might even expose holes in the understanding of the problem to be solved when the compiler asks us a question we can't answer.

Don't use inheritance (R4.3.2 and section 4.41)

***Question*** Unchecked Exceptions - Fatter jeg ikke en b??nne af!

### Architecture
Or more specifically the *micro architecutre*, that is architecture that affects this team, but not other teams. Avoided by 6.1.3 ELIMINATE GETTER AND SETTER.

### Pass *this* instead of private fields to avoid breaking encapsulation
## 7.3 Trusting the compiler
### Teach the compiler invariants
Whenever we have invariants in our program
1. Eliminate them
2. If you can't, then teach the compiler about them
3. If you can't, then teach the runtime about them with an automated test
4. If you can't, then teach the team about them by documenting them extensively
5. If you can't, then teach the tester about them, and test them manually
6. If you can't, then start praying because nothing earthbound can help you

*Can't* in this context means infeasible rather than impossible.
### Pay attention to warnings
*alarm fatigue*
# Intereassante pointer i kapitel 7
Hvad sker der med CC's refenrecer? F.eks. til section 4.41 p?? side 187?

Er lidt junior agtigt
Pr??senterer nogle interesante l??sninger p?? problemerene, f.eks. 7.1.3

The **compiler** should be part of the development team

Programming is not construction, it is *communication*, on multiple levels:
- We communicate with the computer when we tell it what to do
- We communicate with other developers when they read our code
- We communicate with the compiler whenever we ask it to read our code
# Chapter 8 - Stay away from comments
Comments are here considered comments inside methods and not Javadoc etc.

Intermediate comments are great!

Comments are not checked by the compiler, making them easier to write than code since there are no constraints on them.
> "*A common fallacy is to assume authors of incomprehensible code will somehow be able to express themselves lucidly and clearly in comments.*"
>
> -Kevlin Henney

### 8.1 Deleting outdated comments
### 8.2 Deleting commented-out code
### 8.3 Deleting tivial comments
### 8.4 Transform comments into method names
Languages have a property where the words used most often tend to be the shortest.

Use comments for planning
```
/// Fetch data
/// Check something
///   Transform
/// Else
///   Submit
```
### 8.5 Keeping invariant-documenting comments
non-local invariants

Keep such comments if you fail step 1-3 in chapter **7.3 - Teach the compiler invariants**

# Chapter 9 - Love deleting code
Code is a liability. We have to continuously maintian code regardless of whether it is valuable.
God pointe omkring automatisering ??verst side 201
## 9.2 Deleting code to get rid of incidental complexity
### Technical ignorance from inexperience
E.g. when unknowingly making bad decisions by adding unnecessary coupling leading to poor architecture

### Technical waste from time pressure
Never skip best practices! Not even under time pressure!

Do you want your new car three weeks sooner if it meant the breaks and airbags wouldn't be tested?

### Technical waste from circumstanses
Temporary suboptimal solutions are debt, permanent suboptimal solutions are waste!

### Technical drag from growing
Sparsely used features drags us when making change, hence they should be removed
Automated tests (intentionally) make it harder to change code as we need to change the test as well. This is not necessarily bad as sometimes being slow and stable is prefered over being fast!

## 9.3 Categorizing code based on intimacy
Intimately familiar - Code we have recently developed

Familiar - libraries and utilities we use often

Unknown - Everything in between

> Legacy code - Code that we are affraid to modify

## 9.4 Deleting code in a legacy system
### 9.4.1 Using the *strangler fig* pattern to get insight
1. Encapsulate the classes in a new package/namespace
2. Make a gate class in the new package and reduce all public modifers in the new package to be package-private
3. Fix errors by adding public functions in the gate class
4. Optionally adding all kinds of metrics on the public functions (call ok/fail, function called)

### 9.4.2 Using the *strangler fig* pattern to improve the code
L??s kapitel igen. Specielt sektionen om at ??ge kald til vigtig kode?!?!

## 9.5 Delete code from a frozen project
Hmmm hvad er pointen her?
Det handler vel om at have overblik over hvad man har liggende i dvale rundt omkring, som potentielt giver drag
Ha' en strategi for at holde ??je med kode i dvale og f?? det slettet
Samtidigt er der fokus p?? at implementere instrumentring i koden s?? man har styr p?? hvor meget det bliver brugt, ogs?? i spikes som har en lavere kvalitet (ikke refaktoreres, ikke testes)
## 9.6 Deleting branches on version control
Branches, like code, are technically almost free but are expensive in terms of
- mental overhead
- if long lived exposes ourselves to expensive, soul-crushing, error-prone merge conflicts
And clutter causes more clutter

## 9.8 Deleting testing code
N??et hertil

# Chapter 10 - Never be affraid to add code

# Chapter 11 - Follow the structure in the code
## 11.1 Categorizing structure based on scope and origin
Lidt om Conway's law
Domain experts define how the software should function, which means the system mirrors the experts behavior... Radar SW...
Inefficiencies in the code, can often find their real world source in the way the experts work... Hvad kan vi bruge det til?
Radars users are external because we can't retrain them...
## 11.2 Three ways code mirrors behavior
Behavior can be embedded in three ways in code
1. Control flow
2. Structure of data
3. In the data itself

L??S IGEN!
## 11.3 Adding code to expose structure
Ved impl af nye features er der stor usikkerhed og vi skal fokusere p?? korrekthed og udskyde refactoring. Ny kode testes ofte ogs?? intensivt. Som koden modnes, og strukturen bliver mere stabil, b??r den refaktoreres 

## 11.4 Observing instead of predicting, and using empirical techniques
Skak historien
Do not generalize unless you know it will be used as it otherwise adds unnecesssary code and accidental complexity.

## 11.5 Gaining safety without understanding the code
Refactoring moves behavior between control flow, data structures, and data. We can't refactor without making mistakes. Following are a few steps we can take, however non of them are failsafe, so we eventually have to accept the remaining risk.
### 11.5.1 Safety through testing
The risk is that the test may not cover where a mistake happens or not test what we expect them to.
### 11.5.2 Safety through mastery
Practice practice. Wax on, wax off. The risk remains the human refactorer.
### 11.5.3 Safety through tool assisatance
Risk is Tool bugs, Very low risk
### 11.5.4 Safety through formal verification
Risk is Tool bugs 
### 11.5.5 Safety through fault tolerance
Feature toggling allows for toggling to another, hopefully error free, method automatically that is...
## 11.6 Indentifying unexploited structures
Structures that come from the underlying domain, that is often older than the software, can safely be exploited.
Processes and teams tend to have shorter lifespan and should not be baked into the code.

How to look for exploitable structures
### 11.6.1 Exploiting whitespaces with extraction and encapsualtion
Look for blank lines
### 11.6.2 Exploiting duplication with unification
Use
- EXTRACT METHOD
- ENCAPSULATE DATA
- UNIFY SIMILAR CLASSES
### 11.6.3 Exploiting common affixes with encapsulation
Use
- NEVER HAVE COMMON AFFIXES
### 11.6.4 Exploiting the runtime type with dynamic dispatch
If classes are in our control make a new interface, change the variable to this type and make both classes implement the interface. Aka. use polymorphy.

If classes are **not** in our control, push the type inspection code to the edge of our code to ensure that the core of our code base is pristine. See NEVER USE IF WITH ELSE.

> # Questions:
>
> What is meant with "Data-encoded behavior can be used as a last resort and should be limited, as it is difficult to maintain safely due to lack of compiler support?
# A word on code dublication
 Code dublication is bad because it encourages divergence.

 MHL: Contrary: Code sharing is bad because it encourages convegence...
