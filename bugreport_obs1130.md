[Obfuscator] Renaming constant variable in class static block
==========================================

According to the JavaScript specification, running a JavaScript program `class x { static { const x = { x } = 0 ; } }` is expected to result in `ReferenceError`, but the output of Obfuscator is expected to result in `TypeError`.

Below is a detailed explanation using ECMAScript Specification. The first section is about how `class x { static { const x = { x } = 0 ; } }` result in `ReferenceError`.

Evaluation of `class x { static { const x = { x } = 0 ; } }` is done by following algorithms.

## 1. Evaluation of *ClassDeclaration* of ECMAScript Specification

*ClassDeclaration* : **class** *BindingIdentifier* *ClassTail*

The algorithm calls BindingClassDeclarationEvaluation of this *ClassDeclaration*.

## 2. Operation BindingClassDeclarationEvaluation of ECMAScript Specification 

*ClassDeclaration* : **class** *BindingIdentifier* *ClassTail*

In step 2, the algorithm calls ClassDefinitionEvaluation of *ClassTail* with arguments className and className.

## 3. Operation ClassDefinitionEvaluation of ECMAScript Speicifcation

In step 31-b, the algorithm calls Call with arguments elementRecord.[[BodyFunction]] and F, where elementRecord.[[BodyFunction]] represents the `const x = { x } = 0 ;` and F represents the evaluation result of *ClassDefinition* of x.

## 4. Operation Call of the ECMAScript Specification

In step 3, the algorithm calls F.[[Call]](V, argumentsList), where *F* represents the `const x = { x } = 0 ;`, *V* represents the evaluation result of *ClassDefintion* of x, and *argumentsList* is an empty List.

## 5. Operation FunctionObject.[[Call]] of the ECMAScript specification.

In step 6, the algorithm calls OrdinaryCallEvaluateBody(F, argumentList), where F represents the `const x = { x } = 0 ;` and *argumentsList* is an empty List.

## 6. Operation OrdinaryCallEvaluateBody of the ECMAScript specification.

The algorithm calls EvaluateBody of F.[[ECMAScriptCode]] with arguments F and argumentList, where F represents the `const x = { x } = 0 ;` and *argumentsList* is an empty List.

## 7. Operation ClassStaticBlockBody.EvaluateBody of the ECMAScript specification.

The algorithm calls EvaluateClassStaticBlockBody of ClassStaticBlockBody with argument functionObject, where functionObject represents the `const x = { x } = 0 ;`.

## 8. Operation ClassStaticBlockBody.EvaluateClassStaticBlockBody of the ECMAScript specification.

In step 1, the algorithm calls FunctionDeclarationInstantiation(functionObject, <<>>), where functionObject represents the `const x = { x } = 0 ;`.
This step creates binding of "x", which represents the class name.

In step 2, the algorithm calls the evaluation of *ClassStaticBlockStatementList*, where *ClassStaticBlockStatementList* represents `const x = { x } = 0 ;`

## 9. Evaluation of *AssignmentExpression* in ECMAScript specification.

In step 5, the algorithm calls DestructuringAssignmentEvaluation of assignmentPattern with argument rval, where rval represents 0.

## 10. Operation ObjectAssignmentPatter.DestructuringAssignment in ECMAScript specification.

In step 2, the algorithm calls PropertyDestructuringAssignmentEvaluation of Assignment PropertyList with argument value, where value represents 0.

## 11. Operation AssignmentProperty.PropertyDestructuringAssignmentEvaluation in ECMASCript specification.

In step 5, the algorithm calls PutValue(lref, v), where lref represents the left-hand-side class-name `x` and v represents `undefined`.

## 12. Operation PutValue in ECMAScript specification.

In step 6, the algorithm calls base.SetMutableBinding(V.[[ReferencedName]],W,V.[[Strict]]), where base is an environment record of lref, V.[[ReferencedName]] represents `x`, W represents `undefined`, and V.[[Strict]] represents `true`.

## 13. Operation DeclarativeEnvironmentRecord.SetMutableBinding in ECMAScript specification.

The envRec have a binding for N (class name `x`) so step 1 is skipped.
The class name `x` has not been initialized, yet. Therefore the program should throw `ReferenceError` exception.

- ?????? ?????????
  - envRec??? ????????? ???????????? ??????, ????????? closure??? ?????? ????????? ?????????
  - *V* represents the evaluation result of *ClassDefintion* of x, ???????????? ????????? intuitive?????? ??? ??? ?????????
  - initialize??? true ????????? ?????? ????????? ???????????????????
  - ++ `TypeError`??? ????????? ????????? ????????????
  - ??? ???????????? ?????? intended error??? ??? ??????... ????????????. ?????? Ignorable??? error??? ??? ??????.