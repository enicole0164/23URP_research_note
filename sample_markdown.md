Title:
Renaming constant variable in class static block

<!-- Love javascript-obfuscator? Please consider supporting our collective:
👉  https://opencollective.com/javascript-obfuscator/donate -->

<!--- Provide a general summary of the issue in the Title above -->

## Expected Behavior
<!--- Tell us what should happen -->
Expected ReferenceError but got TypeError from the obfuscated code.

Original code
```JavaScript
class x { static { const x = { x } = 0 ; } }
```
Obfuscated code
```JavaScript
class x{static{const _0x4ecb98={x}=0x0;}}
```

## Current Behavior
<!--- Tell us what happens instead of the expected behavior -->

## Steps to Reproduce
<!--- Provide a link to a live example, or an unambiguous set of steps to -->
<!--- reproduce this bug. Include code to reproduce, if relevant -->
1.
2.
3.
4.

## Your Environment
<!--- Include as many relevant details about the environment you experienced the bug in -->
* Obfuscator version used:
* Node version used:

# Stack trace
<!--- Include stack trace -->

## Minimal working example that will help to reproduce issue
<!--- Include code -->
