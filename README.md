# Fullstack Squash

This term describes cleaning up and shortening a domain class and its related classes, e.g "I'm doing a fullstack squash of the Foo class". Very often I have removed around 30%-50% of a class's code and shortened the average line length dramatically while incurring very low risk. This makes the code easier to read, maintain and extend. I'm using the case of a C# 5.0 project with NUnit and ASP.NET WebForms, though the process is generally applicable.

## Candidate classes
- `Foo` - domain class
- `TestFoo` - test fixture
- `FooHelper` - test helper class
- `FooEditor.aspx.cs, FooEditor.aspx`- web classes<sup>1</sup>

## Process

The process has two changesets. Most changes are made in the first. Reordering class contents is done in the second.

### First changeset

In all candidate classes:
- Remove and sort usings
- Remove property backing fields if access is not an issue (default values can be set in constructors)
- Condense `public Bar Foo { get; set; }` on single line unless this overruns guideline
- Use `var` in cases where explicit typing adds no new information<sup>2</sup>
- Remove single-use variables and methods unless they're super long
- Remove unused methods and variables (but make sure they aren't being used by reflection!)
- Factor out null/empty object validations<sup>3</sup>
- Use context to shorten names, e.g. in `ChocolateAndBananaIceCreamHelper`, a variable can just be `iceCream`, and in `ChocolateAndBananaIceCreamEditor.aspx`, `chocolateAndBananaIceCreamDropDownList` can just be `iceCreamDropdown`!
- Use the brace constructor e.g. `Foo { Bar = bar }` or Builder classes<sup>4</sup>

In TestFoo:
- Inherit from a custom base fixture (provides common domain objects and helper methods)
- Remove common objects, use those from base fixture (this is sometimes not possible due to cross-test interference)
- Remove unnecessary setups/teardowns
- Replace `fixtureFoo` with `foo` - if there is already a `foo` local var in a test, rename it to `testFoo`
- Replace `ExpectedException` with `Assert.Throws<FooException>()`<sup>5</sup>
- Use builder classes and extenders to make complex object construction and assertion more readable, eg. `new FooBuilder().withBar(bar).create()`<sup>6</sup>

### Second changeset

- Reorder class contents: constants, fields, constructors, properties, methods
- Apply secondary ordering: public, internal, protected, private.

### Footnotes

1. Changes to web page files must be kept to a minimum to reduce risk as they use loose binding.
2. Use `var` in the case `Foo foo = new Foo()`, in the case `Foo foo = Something()` since foo is obviously a Foo, but NOT in the case `Foo result = Something()` since it won't be obvious what `result` is.
3. Replace long-form string validation with `string.IsNullOrWhiteSpace()` and replace long-form null checking with use of a Validator class, e.g. `Validator.IsNull(foo, "Foo")` will throw with message "Foo cannot be null".
4. Some don't like to use the brace constructor since breakpoints can't be set on each assignment. If the object has a large number of properties being set by functions, a Builder class can be used instead of the braces to shorten the code.
5. `ExpectedException` was removed from later versions of NUnit. Also, `Assert.Throws<FooException>()` is much more powerful and can be made less verbose by using a helper method containing it
6. Builders can be reused: set one up and call `create()` using the same builder in multiple tests.
