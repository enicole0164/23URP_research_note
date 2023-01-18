Title:
Renaming constant variable in class static block results in TypeError

According to the ECMAScript specification, running a JavaScript program `class x { static { const x = { x } = 0 ; } }` is expected to result in `ReferenceError` but after obfuscating the JavaScript program, it results in `TypeError`.

<!-- Love javascript-obfuscator? Please consider supporting our collective:
👉  https://opencollective.com/javascript-obfuscator/donate -->

<!--- Provide a general summary of the issue in the Title above -->

## Expected Behavior
<!--- Tell us what should happen -->
<!-- Expected ReferenceError but got TypeError from the obfuscated code. -->

Running the following JavaScript code using Node.js results in ReferenceError.

```JavaScript
class x { static { const x = { x } = 0 ; } }
```
<img width="456" alt="obfuscator_referenceerror" src="https://user-images.githubusercontent.com/48983829/213082925-ceabb5ed-5da6-431f-8365-b15c4ba2edd3.png">

## Current Behavior
<!--- Tell us what happens instead of the expected behavior -->

However, as shown below, running the obfuscated code using Node.js results in TypeError.

Obfuscated code
```JavaScript
class x{static{const _0x2340e4={x}=0x0;}}
```
<img width="377" alt="obfuscator_typeerror" src="https://user-images.githubusercontent.com/48983829/213082935-a88c18b2-91ee-43b8-b84e-f9dc85b1101f.png">

In summary, obfuscated code results in `TypeError`, because obfuscator have renamed constant variable in class static block. (e.g. `x` to `_0x2340e4`)
1. Original code and obfuscated code both evaluates `{ x } = 0` and `{x}=0x0` to initialize `x` in `x = { x } = 0` and `_0x2340e4` in `_0x2340e4={x}=0x0`.
2. When resolving `x` in `{x}`, the original code resolves to `x` in `const x` and the obfuscated code resolves to `x` in `class x`.
3. In case of the original code,
    1. `ReferenceError` exception arises because it tries to set mutable binding for uninitialized `x`, which refers to `const x`.
4. In case of the obfuscated code,
    1. the `TypeError` exception arises because it tries to change the value of an immutable binding `x`, which refers to  `class x`.


## Steps to Reproduce
<!--- Provide a link to a live example, or an unambiguous set of steps to -->
<!--- reproduce this bug. Include code to reproduce, if relevant -->
1. Obfuscate the following program using https://obfuscator.io.
2. Run the following program using Node.js v18.13.0.

## Your Environment
<!--- Include as many relevant details about the environment you experienced the bug in -->
* Obfuscator version used: https://obfuscator.io.
* Node version used: v18.13.0

# Stack trace
<!--- Include stack trace -->

## Minimal working example that will help to reproduce issue
<!--- Include code -->

* * *
Below is a detailed explanation using ECMAScript specification.

Evaluation of `class x { static { const x = { x } = 0 ; } }` is done by following algorithms.

### 1. [Operation ClassDefinitionEvaluation of ECMAScript specification](https://tc39.es/ecma262/#sec-runtime-semantics-classdefinitionevaluation)

In step 3, the algorithm creates an immutable binding of `x`.

In step 27, the algorithm initializes the binding of `x` with `F`.

<img width="700" alt="obs_3" src="https://user-images.githubusercontent.com/48983829/213086652-444f58ba-079f-46a6-9726-2d5bc36d1a8b.png">
<img width="700" alt="obs_4" src="https://user-images.githubusercontent.com/48983829/213086668-5b5ed8d2-3498-47ae-8c1f-3f7ce0f1bedb.png">

In step 31-b, the algorithm calls **Call**_(initializer,receiver)_.
where _initializer_ represents the `const x = { x } = 0 ;` and _receiver_ represents the evaluation result of *ClassDefinition* of x.

<img width="700" alt="obs_5" src="https://user-images.githubusercontent.com/48983829/213086687-58a74cba-1edd-4e2e-90c2-4c2972df3896.png">

### 2. [Operation ClassStaticBlockBody.EvaluateClassStaticBlockBody of the ECMAScript specification](https://tc39.es/ecma262/#sec-runtime-semantics-evaluateclassstaticblockbody)

This step evaluates the _ClassStaticBlockBody_, which represents `const x = { x } = 0 ;` and creates binding of variables in _ClassStaticBlockBody_ in class environment.

In step 1, the algorithm calls **FunctionDeclarationInstantiation**_(functionObject, <<>>)_, where functionObject represents the `const x = { x } = 0 ;`.
This step creates binding of `x`, which represents the constant variable name `const x`.

In step 2, the algorithm calls the evaluation of *ClassStaticBlockStatementList*, where *ClassStaticBlockStatementList* represents `const x = { x } = 0 ;`

<img width="700" alt="obs_10" src="https://user-images.githubusercontent.com/48983829/213089286-e051a9cf-8676-485d-80bc-46a22ec175ba.png">


### 3. [Evaluation of _LexicalBinding_ in ECMAScript specification](https://tc39.es/ecma262/#sec-let-and-const-declarations-runtime-semantics-evaluation)
*LexicalBinding* represents `x = {x} = 0`, and it can be divided into *BindingIdentifier* `x` and *Initializer* `= {x} = 0`.

This algorithm intends to resolve *bindingId* and evaluate *Initializer* to initialize binding at step 5.

Note that binding for the constant variable `x` is **not initialized** until it reaches step 5. 

In step 4, the algorithm calls the evaluation of *Initializer*.

<img width="700" alt="obs_11" src="https://user-images.githubusercontent.com/48983829/213089312-ca97f6bc-9ba8-499e-b3a5-b70b3fd0461a.png">

### 4. [Evaluation of _AssignmentExpression_ in ECMAScript specification](https://tc39.es/ecma262/#sec-assignment-operators-runtime-semantics-evaluation)

*AssignmentExpression* represents `{x} = 0`, and it can be divided into *LeftHandSideExpression* `{x}` and *AssignmentExpression* `0`.

In step 5, the algorithm calls **DestructuringAssignmentEvaluation**_(value)_ of _assignmentPattern_, where _value_ represents 0 and _assignmentPattern_ represents `{x}`.

<img width="700" alt="obs_12" src="https://user-images.githubusercontent.com/48983829/213089835-c42cf0da-6661-4e42-9f97-ada32dc26f80.png">

Then, **DestructuringAssignmentEvaluation** calls **PropertyDestructuringAssignmentEvaluation**_(value)_, where _value_ represents 0.

### 5. [Operation AssignmentProperty.PropertyDestructuringAssignmentEvaluation in ECMASCript specification](https://tc39.es/ecma262/#sec-runtime-semantics-propertydestructuringassignmentevaluation)

In step 2, the algorithm resolves `x` as the constant variable `x`.

In step 5, the algorithm calls **PutValue**_(V, W)_, where _V_ represents a reference of the constant variable `x` and _W_ rerpresents `undefined`.

<img width="700" alt="obs_14" src="https://user-images.githubusercontent.com/48983829/213090859-acb52282-5a33-47d2-bad8-5909b818b3ac.png">

Then, **PutValue** calls **SetMutableBinding**_(N, V, S)_ of _base_, where _base_ represents the environment that binding of constant variable `x` is stored, _N_ represents `x`, _V_ represents `undefined`, and _S_ represents `True`.

### 6. [Operation SetMutableBinding in ECMAScript specification](https://tc39.es/ecma262/#sec-declarative-environment-records-setmutablebinding-n-v-s)

In step 3, the algorithm throws a **ReferenceError** exception, because the binding for N, which is `x` in envRec has not yet been initialized.

In detail, the `x` in envRec refers the `x` in `const x`. Therefore, it has not been initialized.

<img width="700" alt="obs_16" src="https://user-images.githubusercontent.com/48983829/213091849-16f6e1cf-79e9-478b-87db-82ace2e2d19d.png">

---
In comparison, the evaluation of the obfuscated JavaScript program, `class x{static{const _0x2340e4={x}=0x0;}}`, is similar but different in following steps.

### 2. [Operation ClassStaticBlockBody.EvaluateClassStaticBlockBody of the ECMAScript specification](https://tc39.es/ecma262/#sec-runtime-semantics-evaluateclassstaticblockbody)

In step 1, the algorithm creates an immutable binding of `_0x2340e4` in class environment.

In step 2, the algorithm calls the evaluation of *ClassStaticBlockStatementList*, where *ClassStaticBlockStatementList* represents `const _0x2340e4 = { x } = 0x0 ;`.

<img width="700" alt="obs_10" src="https://user-images.githubusercontent.com/48983829/213089286-e051a9cf-8676-485d-80bc-46a22ec175ba.png">


### 5. [Operation AssignmentProperty.PropertyDestructuringAssignmentEvaluation in ECMASCript specification](https://tc39.es/ecma262/#sec-runtime-semantics-propertydestructuringassignmentevaluation)

In step 2, the algorithm resolves `x` as the class `x`.

In step 5, the algorithm calls **PutValue**_(V, W)_, where _V_ represents a reference of the class `x`.

<img width="700" alt="obs_14" src="https://user-images.githubusercontent.com/48983829/213090859-acb52282-5a33-47d2-bad8-5909b818b3ac.png">

Then, **PutValue** calls **SetMutableBinding**_(N, V, S)_ of _base_, where _base_ represents the environment that binding of class `x` is stored.

### 6. [Operation SetMutableBinding in ECMAScript specification](https://tc39.es/ecma262/#sec-declarative-environment-records-setmutablebinding-n-v-s)

In step 5-b, the algorithm throws a **TypeError** exception, because the binding for `x` in envRec is an immutable binding (class binding).

Since this is an attempt to change the value of an immuatble binding, the algorithm throws **TypeError** exception.

<img width="700" alt="obs_16" src="https://user-images.githubusercontent.com/48983829/213091849-16f6e1cf-79e9-478b-87db-82ace2e2d19d.png">
