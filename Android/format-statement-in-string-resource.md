# Using Format Statement in a String Resource File

Today I learned you can add a string resource in the form of format statement and use it with `getString()` method.

> See also:
> [The Java Tutorials/Formatting][java_formatting]


## How to use it

In `string.xml`:

```xml
  <string name="greeting" formatted="false">Hi, %s.\n It is your %d time(s) here.</string>
```

__Note__
- use `\n` for new line. (just like pure java)
- `%s` formats given value as String.
- `%d` formats an integer as a decimal value.
- `%f` formats a floating point value as a decimal value.
- more conversion [here][java_formatting]

In java code (assume it extends Context):

```java
String formattedString = getString(R.string.greeting, "Name", 3);
```

Simply invoke `getString()` with string id and parameters you wish to put in the string.

## Format markers with position

You can always use position specified markers -`%[position]$[type]`.
Where `[type]` is the variable type and `[position]` is the index of input parameter.

For example:

```xml
  <string name="greeting" formatted="false">Hi, %1$s.\n It is your %2$d time(s) here.</string>
```

## Conclusion
This is a simple tips to use string resource more flexible.



[java_formatting]:https://docs.oracle.com/javase/tutorial/essential/io/formatting.html
