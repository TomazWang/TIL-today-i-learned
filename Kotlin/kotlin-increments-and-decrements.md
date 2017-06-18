# Kotlin Increments and decremets 

`++` and `--` don't act as the same in Kotlin as in Java. `a++` will be traslate
to `a.inc()` but will return the __original__ `a` value. On the other hand,
`++a` will return the new value after involke `a.inc()`.


```kotlin

var a = 1
println("a:" + a)   // a: 1


val b = a++
println("a:"+a)     // a: 2, a = a.inc()
println("b: "+b)    // b: 1, a++ returns a itself (before inc())

println("a: "+a)    // a: 2, a is now 2.
println("++a: "+(++a))  // ++a: 3, ++a the prefix form returns the value of inc()
println("a: "+a)    // a: 2, a is now 3.

```
