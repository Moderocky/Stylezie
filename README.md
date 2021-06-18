Stylezie
=====

### Opus #3

A succinct, consistent and helpful set of Java code conventions, styling guides and general organising rules designed to improve code readability and consistency.

Many existing style conventions are either very old or based on a very old format - some even older than twenty years (and many of the younger Java users!)

While the language itself has remained relatively similar, many of the conventions that originally made good sense are no longer relevant due to improvements in either the language itself or to the tools we use for writing it. IDE warnings have reduced the need for explicit nullness, automatic wrapping has reduced the need for set line-lengths, etc. Although we should never assume these technologies are used by everybody (some people still use vim!) it decreases the necessity for such measures.

As such, this guide aims to provide a more modern and reasonable set of style and code conventions for the discerning programmer. :)

*Written by Kenzie, with some inspiration from [Oracle](https://www.oracle.com/technetwork/java/codeconventions-150003.pdf) and [Google](https://google.github.io/styleguide/javaguide.html) respectively.*

## Preamble

The importance of code conventions should not be understated.

- Conventions make code consistent, which prevents confusion.
- Conventions make code faster to read - you can glance at third-party code and understand what something is when conventions are followed.
- Conventions make code easier to understand - revisiting complex, old code in the future will be painless if the code can be easily interpreted.
- Conventions help with analysis - if clear and standard rules are followed, errors and feedback will be easier to interpret.
- Conventions can help with efficiency, both for the programmer and the machine - having a consistent structure can make your work environment faster, and some conventions may have a real impact on runtime efficiency.

This guide includes:

1. Naming conventions for most elements.
2. Flow control and usage conventions.
3. Structuring conventions for larger projects. (TODO)
4. Testing conventions. (TODO)
5. Documentation conventions. (TODO)

Please note that some entries might seem unusual or different from existing and widely-used standards. Most of these differences will be explained (and the purpose clearly stated.)
However, you may not agree with some of them and feel they are in contrast to your own requirements. If this is the case, feel free to pick and choose - as long as you do so consistently. :)

## Common Ideas and Assumptions

1. Assume your code will be used in tandem with lots of other libraries, projects and third-party code. This helps you to work with the idea that you need to share resources with other things. It also means you should avoid the global state, and any common namespaces not related to your project that might conflict with others.

2. Names should reflect purpose. In almost every case, the name of an element should clearly refer to what it does. This helps to improve readability.

3. Always use the narrowest scope. For access modifiers and modules, you should only expose things as much as they need to be. Exposing unnecessary things can pollute documentation and confuse a user, and may also put your program at risk if an unsafe internal method is exposed.

4. Keep initialism consistent. If a name should be in `lowerCamelCase`, use `xml` rather than `xML`. If a name should be in `UpperCamelCase`, use `HTML` rather than `Html`. Avoid uncommon abbreviations unless obvious or necessary.
   
5. Code should be keyword-centric. New lines will almost always start with either an action keyword or, where none is appropriate, a type. This rule helps to keep code purpose-oriented and avoids unnecessary or unclear line-breaks.

## 1. Naming Conventions

### Module Names

Modules were introduced in Java 9 as a simple way to control access and organise a large project.

An introduction can be found [here](https://www.oracle.com/corporate/features/understanding-java-9-modules.html).

A module should have a clear and singular purpose. It must be defined in a file named `module-info.java`.

Modules should be used consistently - if a project uses modules, it should be entirely modular.

Module names are similar to package names. They should be written in lowercase, separated by `.` and follow the
traditional reversed-domain style. Module names should start with and use a domain owned by the creator, or one in
direct reference to the project.

Examples:

- I own `kenzie.mx`, I may safely use `mx.kenzie.something`
- I do not own `kenzie.me`, therefore I should not use `me.kenzie`
- `project.thing` is not a valid domain, therefore I should not use it.

After the initial reversed domain, the module name should contain the project name, and also offer an indication of what
is contained in it.

Good examples include:

- `mx.kenzie.myproject.http` (for a project named myproject, and a module about http connections)
- `mx.kenzie.myproject.sockets` (for a project named myproject, and a module about sockets)

Bad examples include:

- `mx.kenzie` (this is just a domain, it tells me nothing about the project or the module, and will conflict if I
  re-used this name)
- `mx.kenzie.sockets` (this tells me about the module, but not about the project it's in)
- `sockets` (this is just a word, it doesn't tell me where the module comes from or who made it - if I get an error from
  a third-party library's module, I need to know how to find it!)

### Module Entries

The entries in the `module-info.java` descriptor should be clear and consistent.

There are no specific necessities to the orders of your entries, but these should be grouped by type for clarity and to
avoid confusion. It is generally practical to put the requirements first.

Entries that have multiple comma-separated values should have the values placed on their own lines. Module and package
names can be quite long, and this will help to keep it organised.

This should be kept on one line, as it is short and clear:
```opens org.example.one to org.example.two;```

This should be split over multiple lines, as it may otherwise run past the line length:

```
opens org.example.one to
    org.example.two,
    org.example.three,
    org.example.four;
```

A larger example can be seen below:

```java
module org.example.project.thing {
   requires java.base;
   requires java.net.http;
   requires static java.se;
   requires transitive java.instrument;
   requires transitive java.desktop;
   opens org.example.something;
   opens org.example.one to org.example.two;
   opens org.example.one to
           org.example.two,
           org.example.three,
           org.example.four;
}

```

### Package Names

1. Package names should be written in lowercase, separated by `.` and follow the traditional reversed-domain style,
   using alphanumeric characters (see caveat). This is done for the sake of consistency and has been standard since
   Java's inception.
2. Package names should start with and use a domain owned by the creator, or one in direct reference to the project.
   This is partly as a way to help identify the creator or source of code, and partly for consistency. Your code is your
   responsibility - to an extent. If somebody finds an error in a compiled, third-party library and wants to report it
   to the creator, this provides an easy way for them to contact the creator. In some cases, a reversed email or
   sub-domain may be more appropriate to use as a package starter.
3. Package names should include a reference to the project. This allows you to avoid conflicts with your other projects,
   and makes it clear where the library comes from.

Good examples:

- `mx.kenzie.myproject.sockets` - This is a domain I own, it references my project name (myproject), and the package is
  a clear indication of its purpose (sockets)

Bad examples:

- `project.thing` is not a valid domain, therefore I should not use it.
- `org.my-domain.myproject` contains an illegal character.
- `org.example.myProject` has incorrect casing.

Caveat: If your domain contains a character that is illegal in a package name (such as `-`) this is the only time when
an alternative may be used.

For example, the domain `ab-cd.com` could not be used as a package name. While `com.abcd` would be a sensible
alternative, this might cause confusion (especially if the result domain is in-use or removal of the hyphen affects
readability or changes the meaning.) As a result, the `_` underscore character may be used as a replacement.

Note that this is a deviation from the original Java style guide. I believe that the result (improving readability and
avoiding the potential conflict) justifies its use.

*Justification:*
Not only do unique package names prevent any unexpected conflicts, they also provide some identification as to the producer or the purpose of the application. Shaded code, such as third-party libraries without clear manifests or POM files, may be difficult to find the creator or source of. The package name will give an indication of the creator's github, or the owning corporation's domain name. This makes it easier for a potential user of a library to report bugs or find documentation.

### File Names

All Java code-containing files should have their names formatted in `UpperCamelCase`. This includes classes, interfaces,
annotations and records. Class files should share the name of either the public class, or the most important class
inside them.

The only exceptions to this rule are `package-info.java` and `module-info.java` which require those specific names.

*Justification:*
This is a requisite for public classes, and should be propagated to non-public code files for the sake of consistency.

### Class Names

Class names should not regularly exceed a length of 24 characters. This number does not include type parameters, file
extensions or extraneous parts.

Class names should start with a letter, and contain only alphanumeric characters.

Class names should be at least three characters, and should avoid acronyms unless very common (see XML, HTML, etc.)

Class names should rarely be identical to that of a public JDK class (e.g. you shouldn't name a class `Integer`)
except when the word is in very common use (e.g. `List` or `Remote`) or the JDK class is incredibly obscure
(e.g. `UnsafeFieldAccessorFactory`.)

Class names should be succinct and should describe the purpose of a class (see common assumptions.)

Internal interface names may be preceded by an uppercase `I` for clarity. This is especially useful when abstracting an
existing class to an interface layer. This should be done only for internal use or to avoid conflicts.

Abstract class names may be preceded by `Abstract` for the same reasons as above.

When using procedurally-generated classes (such as via bytecode-generating libraries) the class name should be prefixed with the special character `$` to differentiate it from regular classes.

Prefixes should be avoided for any other case.

Good examples:

- `SocketServer` Not a JDK class, describes its purpose, short and succinct.
- `ByteVector` Not a public JDK class, describes its purpose, not too long.
- `StringSorter` Clear, does what it says on the tin.
- `HTTPServerManager` Clear, expressive, not too long.

Bad examples:

- `Integer` A very common JDK class.
- `stringSorter` Incorrect casing.
- `ByteVector_` Contains a non-alphanumeric character.
- `Bytevector` Incorrect casing.
- `AlphabeticAndNumericStringSorter` Overly lengthy, clumsy.
- `X` Too short, not descriptive.
- `PQ24AJC` Has no bearing on the contents.


*Justification:*
By using this consistent naming style for class names, it becomes much easier to identify the use of class names in raw code. The use of `UpperCamelCase` helps to differentiate between words in the name, while making it shorter than the equivalent divider in `snake_case`. This is also a very common naming style and there is no good reason for deviating from it. Avoiding sharing the name of JDK classes will help to prevent confusion when seeing unqualified class names in raw code. It can also prevent qualifying problems for classes in the `java.lang` package.

### Field Names

Field names should be at most 24 characters.

Static field names should describe the purpose of the stored object.

Dynamic field names should either describe the purpose of the stored object, or be named after its type.

Fields can be split into distinct groups. As these groups behave differently, it is helpful to use different code conventions for them.

#### Static Constants

A constant is a value that cannot be changed during a program's runtime. In Java, these are denoted with the `static` and `final` keywords.
These values are assigned when the class is loaded, and should never change after that point.

'True' constants are `static final` primitive fields that go into the constant pool. These values cannot be altered by any means once they have been assigned.

'Effective' constants are `static final` fields of any object type. These values should never change during normal program execution, **however** it is possible to alter them using unsafe reflection.

All `static final` fields should be named in `UPPER_SNAKE_CASE`.

#### Static Fields

Non-final static fields should be named in `lowerCamelCase`.

#### Dynamic Fields

Dynamic fields should always be named in `lowerCamelCase`. This is irrespective of any other modifiers.
Dynamic constants should still be named in `lowerCamelCase`, despite being in the constant pool.

*Justification:*
Despite being in the constant pool, dynamic constants are only accessible via an instance and are, for almost all intents and purposes, simply final fields.

### Enum Names

Java 'enums' are effective constants with special additional properties.

They should be named as constants are, in `UPPER_SNAKE_CASE`.

### Method Names

Method names should always be named in `lowerCamelCase`, starting with a letter.
Method names should not exceed 24 characters.
Method names should describe their purpose accurately and succinctly.

Methods that access a private field should be prefixed with `get` and `set` accordingly. Methods that access a boolean should be prefixed with `is`.

The `$` and `_` character are available, but are traditionally reserved for Java's internal use.
The `$` character may be used in auto-generated methods internal to a library.

Methods for internal reflection should be prefixed with `reflected$` (for use with ReflectionFactory.)

Private methods for internal use may be suffixed with a `0` if a public method with the same name exists.
An example can be seen below:
```java 
public void myMethod() {
    // perform security access check here
    this.myMethod0(); // call internal version
}

private void myMethod0() {
   // internal behaviour
}
```

So-called 'getter' methods from a record should not be preceded by `get...` as these are effectively constant references
to (supposedly) immutable fields.

Good method names:
- `sortStrings` Clear name and purpose.
- `getAge` Clear name, clear get-behaviour.
- `destroy` Clear name and purpose.
- `setBehaviour` Clear name and purpose.

Bad method names:
- `i1054c` Random, obfuscated, no clue as to what it does.
- `SetName` Improper casing.
- `a` Too short.
- `setTimeAndDateOfTimestamp` Too verbose, could be more succinct.
- `set` No indication of what is being set, could be more descriptive.

### Local Variable Names

Local variables should be named in `lowerCamelCase` and avoid numeric characters where possible. The name should describe either the type of the variable or the purpose.

Variable names should be at most 16 characters. Single-character variable names should be avoided unless directly relevant, such as naming grid coordinates `x` and `y`.

Good variable names:
- `final String name` Variable purpose is clear and type can be assumed.
- `final String string` Variable type is clear.
- `final Object handle` Variable purpose is clear, name is short and succinct.
- `final int age` Variable purpose is clear at use, and type can be assumed.
  
Bad variable names:
- `final String s` Name is too short and unclear.
- `final Method m1` Nothing can be assumed from the variable name. Easy to confuse.
- `final String obj` Name does not relate to variable type or purpose.


*Justification:*
Variables with clear names make code more legible and make it easier to understand what is going on by looking at any point of the code. If the purpose and the type of the variable can be assumed just by reading the name then this makes improving or working with the code a lot easier. 

The exception to this rule is for primitive variables in very short methods where the variable type is immediately clear at use. The single-letter names correspond to the type descriptors for primitives.
Note: this does not mean single-letter names have to be used - by all means pick a name that describes the purpose of the variable, though in many very short methods this is cumbersome and unnecessary.

|Type|Label|
|----|-----|
|boolean|`z`|
|char|`c`|
|byte|`b`|
|short|`s`|
|int|`i`|
|long|`l`|
|float|`f`|
|double|`d`|

### Parameter Names

Method parameters should be named as variables are, following the same rules. However, the convention of describing the parameter's purpose is much more important here, as the parameter name will be the only clue to the input's purpose outside any documentation provided.

Good parameter names:
- `final String name` Parameter purpose is clear and type can be assumed.
- `final int age` Parameter purpose is clear to method user.
  
Bad parameter names:
- `final String string` Parameter purpose may be unclear unless it can be inferred from the method name.
- `final int var` Parameter purpose is unclear to user, and type is unclear when used in code.


*Justification:*
All of the justification for variable names applies here. As well as this, parameter names are somewhat front-facing and provide a very basic level of documentation for using your method, so they ought to be clear to the user. 

### Block Label Names

Labels for conditions, loops and blocks should be named to give some clue as to the section's purpose.
They should be written in `lower_snake_case` to be immediately differentiable from method, variable or field names and to make sure they are legible and clear to the user. Numbers should be avoided.

Implicit labels that apply to a single thing (such as a loop) do not require braces if placed properly.
See structural conventions for more details.

Blocks should very rarely have no label applied unless used for necessary flow-control rather than organisation.

Good label names:
- `loop_citizens: // for...` Describes the purpose of this section clearly.
- `check_valid: //...` Describes the purpose, if a `break` instruction is used the exit is clear.
- `prepare_exceptions: //...` Describes the purpose, easy to break from.

Bad label names:
- `label: //...` Provides no information.
- `x: //...` Name is too short. Provides no details.
- `block12: //...` Confusing. The break instruction could be unclear to the reader.


*Justification:*
Labels are very different from all other members in that they affect the body structure rather than providing any storage or execution function. Having a different naming convention helps to make their difference clear, as well as being slightly more legible than the typical `camelCase` that Java uses.

### Type Parameter Names

Type parameters are found in generic classes and methods. Their names should be written in `UpperCamelCase`, similar to class names.
A type parameter should not have the same name as a class if that class is used (or has a chance to be used) within the scope of that parameter. Hence, naming a type parameter `Type` is only acceptable if the `java.lang.reflect.Type` interface would never be used within its scope.
Type parameter names should describe their purpose.

This breaks from Oracle's original [type parameter conventions](https://docs.oracle.com/javase/tutorial/java/generics/types.html).

**Justification:**
Oracle advocated for single-letter (`T`, `K`, `V`, etc.) type parameter names in order to differentiate them from classes or other elements. They felt this was, at the time, necessary due to the rudimentary nature of IDEs and potentially making it unclear. Later style guides have revised this to allow names beginning with certain prefixes or suffixes (`TValue`, `ValueType`, etc.) for the same reasons.
Respectfully, I no longer feel this is necessary, if it ever was. Type parameters are not classes, but they are stand-in descriptors of them. If the type parameter is declared in the header then I feel it is no different from importing a class rather than using the fully-qualified name. IDE technology has improved significantly, so the differentiation is no longer such an issue. 

Good type parameter names:
- `DataRecord` Name and purpose are clear.
- `ObjectType` Name and purpose should be clear in use case.
- `InputType` Suitable for a type parameter that overloads a method's input type.
- `Value` Suitable for a generic class like map where value type is generic.

Bad type parameter names:
- `T` Tells the reader nothing about it.
- `Object` Hides a very common Java class.
- `String` Hides a very common Java class.
- `TValue` Unnecessary prefix.
- `T1` Cryptic and unhelpful.


## 2. Usage and Flow Structure Conventions

### Class Layout

The order of entries in a class should follow a consistent schema and make use of line-breaks to separate members clearly.

#### Package Definition

The package definition should be the first entry in the file. A line break should follow before any further entries.
```java
package com.example;

public class Alice {
    
}
```

#### Imports

If imports are used, these should go between the package and the leading class declaration, with line breaks to separate the sections.

```java
package com.example;

import java.util.Arrays;
import java.util.List;

public class Alice {

}
```

If five or more classes from the same package are to be imported, a wildcard `*` import should be used to reduce the unnecessary file size.

```java
package com.example;

import java.io.InputStream;
import java.util.*;

public class Alice {
    
}
```

#### Type-level Annotations

Type-level annotations should be placed on the line preceding the class declaration. Type-level annotations should **not** be placed after the modifier when declaring a class. This is to increase clarity.

```java
package com.example;

@Deprecated
public class Alice {
    
}
```

#### Inheritance

Singular extensions and implementations should be placed on the same line as the class declaration.

```java
public class Alice extends Bob implements Person {
    
}
```

If multiple or uncharacteristically long interfaces are required, these may be placed on a separate line if it makes the class declaration easier to read (such as to prevent it running past the line length.)
Any line-breaks should always be placed before a keyword, to make sure that the purpose of the following value is clear.

```java
public class Alice 
    extends Bob 
    implements Person, Employee, Graduate, Thing {
    
}
```

#### Generic Classes

If a generic class has simple or unbounded type parameters these should be placed on the same line as the declaration.
See the section on naming conventions for more information about type parameters.

```java
public class Box<Contents extends Valuable> {
    
}
```

If a generic class has multiple type parameters, and the declaration header becomes too long to be easily legible, line-breaks should be used to separate these.
Line-breaks should be placed after the opening diamond `<` and after every comma.
There should not be a line-break before the closing diamond `>`.

```java
public class Box<
    Material extends Waterproof & Durable,
    Contents extends Valuable & Heavy & Dangerous> {

}
```

### Order of Class Members

Class members should be ordered in a consistent hierarchy, according to the following rules. These rules should be applied in the given order (e.g. the member type trumps the member visibility.)

#### Type
1. Fields
2. Constructors
3. Methods
4. Classes

#### Belonging
1. Static (belonging to class object)
2. Dynamic (belonging to instance)

#### Visibility
1. Public
2. Package-private
3. Protected
4. Private

By applying these rules, the ordering of members should be clear.
A very rudimentary example of correct ordering is shown below.

```java
public class Box {
    
    public static final int NUMBER = 0;
    
    public int first;
    private int second;
    
    public Box(int size) {}
    
    Box() {}
    
    public static void myMethod() {}
    
    public void test() {}
    
    static class Tree {}
    
    class Leaf {}

}
```

### Field Layout

Each field should be defined on its own line with the narrowest appropriate access modifiers.

```java 
private String name;
private int age;
```

The exception to this rule is when declaring multiple fields in one statement. While this is permitted, it is rarely appropriate except for data-oriented classes.

```java 
private String name, surname;
private int length, width, height;
```

If an excessive number of declarations is done in the same statement, these may be placed on multiple lines. The following declarations should be indented for clarity.

```java 
private int first,
    second,
    third,
    fourth,
    fifth;
```

#### Field Annotations

Basic annotations that are augmenting the field itself should be placed above the declaration statement and not after the modifier.

```java 
@Deprecated
private int number;
```

Annotations that are designed to apply to the field's value specifically (such as nullness information) should instead be placed after the modifiers and before the type.

```java 
@Deprecated
private @NotNull String name;
```

**Justification:** Having two places to attach field-target annotations makes it clearer what the annotation is in reference to: the field or its contents specifically. While all field-target annotations are technically targeted to the field itself, many of the most common are in fact in direct reference to the object stored in it. This makes that distinction clear.


#### Field Assignment

If fields can have a common value, this should be assigned in the declaration, rather than the initialiser block or the constructor.

```java 
public int number = 1;
```

This is especially true for final fields, since assigning them during the declaration allows their value to go into the constant pool if they are of a primitive or special type.

```java 
public final int a = 1; // Constant in the pool.
public final int b; // Final but not constant.

{
    b = 2;
}
```

Dynamic constants do not need to be made static (as some other guides suggest) since the value is in the constant pool, so there is no real resource benefit (other than a few bytes saved in memory.)


### Method Layout

Each method should be declared on its own line with the narrowest appropriate access modifiers. Methods should not be declared final unless doing so is necessary. This is in contrast to all other finality rules, as method finality restricts inheritance and so should generally be avoided.

Methods do not need an ending semicolon `;` and the method body brackets should open on the same line as the declaration.

```java 
public void myMethod() {
    // Body code.
}
```

#### Annotations

Method annotations should be used in one of two ways, depending on which is appropriate to the situation.
Annotations that are designed to describe the method itself (such as deprecation warnings, tests, target for JIT, flags for reflection, etc.) should be placed on the preceding line.

```java 
@Test
@Deprecated
@SuppressWarnings("all")
public void myMethod() {
    // Body code.
}
```

Annotations that are designed to apply to the method's return value (such as nullness information) should instead be placed after the modifiers and before the return type.

```java 
public @NotNull String myMethod() {
    // Body code.
}
```

If the annotations of the second kind are cumbersome or unusually long, a line-break is allowed here.
```java 
@Deprecated
public static @NotNull @Finalised @ConstantReturn
Object myMethod() {
    // Body code.
}
```

**Justification:** Having two places to attach method-target annotations makes it clearer what the annotation is in reference to: the method or its result specifically. While all method-target annotations are technically targeted to the method itself, many of the most common are in fact in direct reference to the result. This makes that distinction clear.

#### Generic Methods

If a generic method has simple or unbounded type parameters these should be placed on the same line as the declaration.
See the section on naming conventions for more information about type parameters.

```java 
public <AccessPoint> void myMethod() {
    // Code body.
}
```

If the type parameter declaration is long, a line-break can be placed after the header before the method return type to improve readability.

```java 
public <ClassMember extends Member & AnnotatedElement>
void myMethod() {
    // Code body.
}
```

If a generic method has multiple type parameters, and the declaration header becomes too long to be easily legible, line-breaks should be used to separate these.
Line-breaks should be placed after the opening diamond `<` and after every comma.
There should not be a line-break before the closing diamond `>`.

```java 
public final <
    ClassMember extends Member & AnnotatedElement,
    Material extends Waterproof & Durable,
    Contents extends Valuable & Heavy & Dangerous>
void myMethod() {
    // Code body.
}
```

**Justification:** Clear and neat formatting makes the code faster to read, especially in narrower windows, and makes the type parameter declaration clearer to the reader.

### Brace Arrangement Rules

The opening `{` bracket should always be placed after a space (and its opening action.)
Opening braces should never have a line-break before them.

There are **no** exceptions to this rule.


#### Double-Brace Initialisers

The practice of altering an object at initialisation using a "double-brace initialiser" should be **avoided**.

(Example for reference.)
```java 
new ArrayList<>() {{
    add("hello");
    add("there");
}};
```
It is often used as a time-saving gimmick or to avoid dedicating lines in a constructor to filling a list or map field. While it may appear useful on the surface, it has two unintended consequences.

Firstly, whenever this is used it implicitly creates an extending class. Creating additional classes for no purpose should generally be avoided. 
Secondly, because it is an extending class, the class is functionally nonidentical to the original class, which can cause unintended issues in equality checks. 


#### If or Switch

Switch statements should be preferred over an if/else branch under the following circumstances:
- Your switch statement would have more than three cases.
- A switch statement is applicable to the type.
- The cases can be properly applied to a switch statement.

This is especially true if your switch statement can be fully enclosed (for enums, etc.)

```java 
switch (ordinal) {
    case FIRST:
    case SECOND:
    case THIRD:
}
```

However, it should be noted that switch statements produce a lot more 'compiler garbage' than simple if/else checks, though this is rarely a major concern.

**Justification:**
If `javac` can compile your switch to a `tableswitch` then it may become faster to use as not all of the cases need to be evaluated, as they would in an if/else equality check.
Even a `lookupswitch` could be faster, since the lookup conversion will be more efficient with a large number of cases.


#### Labels and Blocks

Blocks have always been an underused feature of Java. They allow for more delicate and precise flow-control (leading to much shorter and more efficient code in complex loops and conditionals) as well as providing a way to keep code ordered without resorting to comments and extrinsic metadata.

Blocks should always have a preceding label, unless they have a functional clause before them.

Functional clauses:
- `do`
- `while ()`
- `if ()`
- `else`
- `for ()`
- `synchronized ()`

Examples:
```java 
while (true) {
    // 'while' clause serves as label.
}

do {
    // 'do' clause serves as label.
}
    
if (true) {
    // 'if' clause serves as label.
}

label: {
    // No preceding clause, needs a label.
}
```

Labels may also be used as a way to organise or demarcate code. You may, for example, label a conditional or loop clause in order to keep note of its type. This is also wise in nested loops as it allows you to target which to continue or break from.

A very rudimentary of targeted labelling is shown below. By placing the labels before the loop clauses they may be used as targets for the `continue` and `break` instructions.
```java 
count_up: for (int i = 0; i < 10; i++) {
    if (object == null)
        continue count_up;
    count_down: for (int i1 = 10; i1 > 0; i1--) {
        if (object.getClass() == type)
            break count_up;
        else if (thing.getClass() == type)
            break count_down;
        continue count_down;
    }
}
```

**Justification:** Effective use of blocks and labels can increase the readability of code - particularly complex or cryptic code - massively, as well as keeping it more organised and allowing the recycling of variable names.
On top of this, `javac` strips out any unnecessary labels or blocks while compiling, meaning there is no real disadvantage to "over-labelling" code.


### Variable and Parameter Finality

According to rule #3, finality should be used in every appropriate place. This includes using the `final` modifier on method parameters and local variables.

In short methods (under five lines) or in the cases where variables have only one or two uses the finality can be ignored.

In longer methods, or where a variable or parameter is used many times, the variable should be made final.
Obviously, some variables cannot be made final, especially ones that undergo loop reassignment.

**Justification:**
Most users are under the impression that `final` variables have no additional value. However, this is not the case.
Primitives and simple types will be pulled from the constant pool rather than loaded from the local variable pool. On top of this, making variables final can allow for faster and better optimisation by the JIT compiler (detailed in Simmons Jr.'s 'Hardcore Java' Ch.2) and has the added benefit of preventing 'operator errors' such as accidentally reassigning a variable.


## 3. Project Structure Conventions

TODO.

## 4. Testing Conventions

TODO.

## 5. Documentation Conventions

TODO.
