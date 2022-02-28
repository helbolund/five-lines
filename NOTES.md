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
See chapter 4.3.2!
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

### 5-2-1 COMBINE IFS
Join ifs with identical bodies (may require refactoring to achieve)
1. Verify that the bodies are indeed the same
2. Select the code between closing parenthesis of the first `if` and the opening parenthesis of the `else if`, press delete and type `||`. Insert an opening parenthesis after the `if` and a closing parenthesis before `{`. We always keep the parenthesis around the expressions to make sure we do not change the behavior.

### Rule: 5.3.2 USE PURE CONDITIONS
***No side effects, please!***

