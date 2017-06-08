# Kotlin Style Guide

This style guide complements the
[Official Standard Coding Conventions](https://kotlinlang.org/docs/reference/coding-conventions.html).
It goes into more detail and makes some opinionated decisions.

The goals are, in order of importance:

**Readability**: A new reader should be able to figure out what the code does without any trouble.  

**Consistency**: Code style should should not change between files or projects.

**Brevity**: Code that is short and meaningful makes it much easier for readers to find what they need.

## Variable Declarations

### lateinit

Access modifiers should always be before `lateinit` in a variable declaration.

✅ DO:
```kotlin
private lateinit var x: String
```

❌ DON'T:
```kotlin
lateinit private var x: String
```

_Why:_ The access modifier of all member variables should be easily visible at a glance.

### Val Type

Don't hide the return type of `val` declarations.

✅ DO: 
```kotlin
override val x: Int
    get() = 1357
```

✅ DO: 
```kotlin
override val x: Int = 1357
```

❌ DON'T:
```kotlin
override val x = 1357
```

_Why_: The third one hides the type of the variable (i.e. it's not clear if `x` is an integer or a float).

### Nullable Parameters

Use `lateinit` instead of initializing variables to `null`, unless the variable is truly nullable.

✅ DO:
```kotlin
lateinit var x: String

init {
    x = "Some string value"
}
```

❌ DON'T:
```kotlin
var x: String? = null

init {
    x = "Some string value"
}
```

_Why_: Be careful because sometimes the auto-translator will do the second style. We've found that it breaks dependency injection too.

## Constructors

### Single Line

If a constructor is small, put it on a single line.

✅ DO:
```kotlin
class ShortConstructor(val name: String) : AnotherClass {
    ...
}
```

### Long List of Parameters

If a constructor has a long list of parameters, put each parameter on a separate line, starting with the first.

✅ DO:
```kotlin
class ManyParameters(
        val name: String,
        val x: Int,
        val y: Int,
        val z: Int
) {
    ...
}
```

Note the double tabbing.

❌ DON'T:
```kotlin
class ManyParameters(val name: String, val x: Int, val y: Int, val z: Int) {
    ...
}
```

❌ DON'T:
```kotlin
class ManyParameters(val name: String,
                     val x: Int,
                     val y: Int,
                     val z: Int
) {
    ...
}
```

### Inheritance

If a constructor has a long list of parameters and extends another class, put the subclass on the next line with the constructor's closing bracket.


✅ DO:
```kotlin
class ManyParameters(
        val name: String,
        val x: Int,
        val y: Int,
        val z: Int
) : AnotherClass(name) {
    ...
}
```

❌ DON'T:
```kotlin
class ManyParameters(
        val name: String,
        val x: Int,
        val y: Int,
        val z: Int) : AnotherClass(name) {
    ...
}
```

❌ DON'T:
```kotlin
class ManyParameters(val name: String,
                     val x: Int,
                     val y: Int,
                     val z: Int) : AnotherClass(name) {
    ...
}
```

## Lambdas Declarations

### Ignored Parameters

When not using an parameter, always name it `_`.

✅ DO:
```kotlin
observable.subscribe { _ -> 
    doSomething()
    ...
}
```

❌ DON'T:
```kotlin
observable.subscribe { ignored ->
    doSomething()
    ...
}
```

DON"T:
```kotlin
observable.subscribe {
    doSomething()
    ...
}
```

_Why_: `_` is concise and doesn't hide the fact that the lambda has a parameter.

### Using it

Only use `it` for simple lambda functions. As a broad rule, if you use the `it` more than once, give it a name.

✅ DO:
```kotlin
list.filter { it > 10 }
```

❌ DON'T:
```kotlin
observable.subscribe {
    doSomeOtherThing { it.filter { it > 10 } }
}
```

_Why_: `it`, while concise, can also hurt the code's readability. As much as possible, it should read like English (i.e. filter 'it' if it is greater than 10). If there is ever any doubt about what `it` is, give it a name.

## Function Call with Lambdas

### Lambda as Only Parameter

Use the LINQ style convention when possible

```kotlin
list.filter { it > 10 }
```

```kotlin
list.filter({ it > 10 })
```

_Why:_ Having `({` and `})` everywhere is just clutter, and we should get used to the new notation

### Ends with Single Lambda Parameter

When calling a function whose last parameter is a lambda, put the closure outside the round brackets.

✅ DO:
```kotlin
asyncCall ('https://example.com') {
    handleCallback()
    ...
}
```

❌ DON'T:
```kotlin
asyncCall ('https://example.com', {
    handleCallback()
    ...
})
```

_Why_: While unfamiliar expecially coming from java, the new notation is less cluttered with fewer `)}` sprinkled everywhere.  Swift has been using this notation to great success.

### Multiple Lambda Parameters

When calling a function that takes multiple lambdas as parameters, don't put the last one outside the round brackets.

✅ DO:
```kotlin
observable.subscribe(
    { value ->
        doSomething(value)
        ...
    },
    { error ->
        handleError(error)
        ...
    })
```

❌ DON'T:
```kotlin
observable.subscribe(
    { value ->
        doSomething(value)
        ...
    }) { error ->
        handleError(error)
        ...
    }
```

_Why_: The second one makes code flow difficult to follow.
