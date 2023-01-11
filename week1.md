Week 1 Research Note
====================
URP 프로그램 1주차 연구노트 (1/2 ~ 1/8)

이번 주에는 선행 연구를 집중적으로 살펴보았다.
URP 주제와 관련된 연구실의 논문, JISET과 JEST를 읽고,
그 구현체인 ESMeta의 코드를 읽었다.

JISET
--------------------
>JISET is a first tool that automatically synthesize parsers and AST-IR translators directly from a given language specification.

## Motivation
 - ECMAScript와 JavaScript의 특징
   - JavaScript semantics is complex.
   - ECMAScript is the official JavaScript specification that has been annually updated since ES6. (자주 업데이트 됨)
   - ECMAScript describes the JavaScript syntax using a variant of the extended BNF(EBNF) notation, and its semantics using abstract algorithms written in English in a clear and structured manner. (굉장히 clear하게 쓰여있음)
 - 지금까지 제안된 IR-based semantics extraction
   - manually implement한 JavaScript Parser, AST-IR Translator에 의존한다.
   - Tedious, Labor-intensive, and error-prone to deal with the large size of modern JavaScript
## Contribution
  - Automatically Synthesize Parsers and AST-IR translators directly from ECMAScript
    - JISET is the first tool that automatically extract IR-based semantics from a language specification, ECMAScript. 
    - JISET bridges gaps between the specification written in a  natural language and tests. 
    - JISET is also forward compatible with new language features proposed for future ECMAScript specifications.


## Challenges
  - Syntactically,
    - #C1 ECMAScript utilizes its own variant of EBNF
      - No existing parser generation technique is directly applicable for this variant.
    - #C2 Automatic semicolon insertion in its parsing algorithm.
  - Semantically,
    - #C3 Abstract Algorithms in ECMAScript are written in natural language.
    - #C4 General and forward compatible representation of abstract algorithms is necessary.

## Implementations
  - Parser Generator
    - Built Lookahead Parser to overcome #C1
  - Algorithm Compiler to overcome #C3
    - Tokenizer
      - tokenizes each abstract algorithm into a list of tagged tokens.
    - Token List Parser.
      - generates token AST
    - Token AST Converter
      - generate an IRes function for a given token AST.

JEST
--------------------
JEST는

ESMeta
--------------------
### ESMeta.scala
###



| 기록자: 김재령 | 일자: 1월 8일 | 확인자: 이강욱 | 일자: |
|----------------|---------------|----------------|-------|