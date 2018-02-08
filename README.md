# Fullstack Squash

This term describes cleaning up and shortening a C# domain class and its related classes, e.g "I want to do a fullstack squash of the Foo class". On some older projects, I have found that I can eliminate around 30%-50% of a class's code and shorten the average line length dramatically. This makes the code easier to read, maintain and extend. Ultimately I want this process to be succinct and low-risk. This process focuses on C# projects using NUnit and ASP.NET WebForms.

## Candidate classes
- Domain class (Foo)
- Test Fixture (TestFoo) - NUnit test class
- Test Helper (FooHelper)
- Web page + code-behind  (FooList.aspx, FooList.aspx.cs, FooEditor.aspx.cs, Foo.aspx...) (In a very limited sense! Risky to change these classes due to loose bindings.)
- There will be tiny changes in ancillary classes but these should be kept to a minimum due to the possibility of merge conflicts.

## Process

In all candidate classes:
- Remove and sort usings
- Remove backing fields for simple property get;set; (default values can be set in constructors)
- Property get;set; on single line
- Use "var" in the case of "Foo foo = new Foo()"
- Use "var" in the case of "Foo foo = Something()" since foo is obviously a Foo
- Order methods according to convention
-- Constants
-- Fields
-- Constructors
-- Properties
-- Methods
- Within each of the above order by convention
-- Public
-- Internal
-- Protected
-- Private
- Remove single-use variables (debatable - can be helpful when debugging)
- Remove single-use methods (unless they're super long)
- Remove unused methods and variables (but be certain that they aren't being used by reflection!)
- Replace long-form string validation with string.IsNullOrWhiteSpace
- Replace long-form validation & throwing with use of a Validator class, e.g. Validator.IsNull(foo, "Foo") will throw with message "Foo cannot be null"
- Use context to shorten names, e.g. inside class "ChocolateAndBananaIceCreamHelper", a local variable can simply be "iceCream", and inside "ChocolateAndBananaIceCreamEditor.aspx", the control "chocolateAndBananaIceCreamDropDownList" can just be "iceCreamDropdown"!
- Use default brace constructor where possible e.g. Foo { Bar = bar } (some don't like to do this since breakpoints can't be set on each assignment)

In TestFoo:
- Inherit from super SuperTestFixture (super that loads/creates the system's most common domain objects and also has some commonly used helper methods)
- Remove unnecessary objects from child test fixture where possible, use the super's objects instead (this is sometimes not possible if it makes other test using the super's objects fail)
- Remove unnecessary setups/teardowns if they aren't doing anything
- Replace "fixtureFoo" with "foo" - if there is already "foo" in a test, rename it to "testFoo"
- Replace ExpectedException with Assert.Throws(). ExpectedException was removed from later versions of NUnit. Also, Assert.Throws() is much more powerful and can be made less verbose by using a helper method containing it
- Use builders/extenders to make complex object construction and assertion more readable eg.  new FooBuilder().setBar(bar).create()
