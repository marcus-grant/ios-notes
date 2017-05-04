# ios-notes
My haphazard collection of ios/swift/objc based notes


## Access Control in Swift

`private`
- most exclusive keyword to specify access
- restricts use of an entitity to **only the enclosing declaration of an entity**
    - ie a private var of a class can't be accessed by anything but that class, not even extensions of that class
    - Use to hide implementation details details of a specific functionality when those details only get used inside a single declaration
    - From a [Cocoacasts post][Cocoacast:private-fileprivate] the code below will give compiler errors for trying to access a private var from an extension
```swift
class NotesViewController: UIViewController {
    private var dataSource = [String]()
}

extension NotesViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, 
                    numberOfRowsInSection section: Int) -> Int {
        return dataSource.count
}
```

`file-private`
- Only permits access to an entitity from **within the same source**
- New in Swift 3 to provide an option between `private` and `internal` in access
- The above code example for `private` that wouldn't compile would compile if the var was `file-private` instead
- If the extension however was moved outside the same source file it wouldn't compile
- Use to hide implementation or data within a file

`internal`
- Excludes access to **entities inside the same module** only
- No access outside the module the `internal` entity was defined
- From [Apple Swift Docs: Access Control][SwiftDocs:ACL]
> Useful for defining an app's or framework's internal structure

`public`
- Only used for class definitions after Swift 3
- Before Swift 2 it meant the defined **entity, not just class** could:
    - Be accessed by any exterior swift module
    - Subclassed by any swift module or file
    - Overriden by any swift module or file
- **After Swift 3**
    - Can only be subclassed within the same module as the `public` class
    - Class members with `public` access or any more exclusive level can be **overriden ONLY within the same module**
    - Can be accessed by any module or file that imports the module the `public` entitty was defined

`open`
- New in swift 3
- `open` in Swift 3 == `public` in Swift 2
- Distinguishes permissions to subclass & override vs. simply accesss to it
    - `open` allows you complete access to an entity so long as its module is included
    - `public` only **permits access**, but **not overrides or subclassing**
- From the Apple Swift Docs on [access controls][SwiftDocs:ACL]
> Marking a class as open explicitly indicates that you’ve considered the impact of code from other modules using that class as a superclass, and that you’ve designed your class’s code accordingly.

## Classes & Custom Types
From Apple [SwiftDocs][SwiftDocs:ACL]:
> Access levels in Swift follow an overall guiding principle: No entity can be defined in terms of another entity that has a lower (more restrictive) access level.

> For example:

> A public variable cannot be defined as having an internal, file-private, or private type, because the type might not be available everywhere that the public variable is used.
> A function cannot have a higher access level than its parameter types and return type, because the function could be used in situations where its constituent types are not available to the surrounding code.
> The specific implications of this guiding principle for different aspects of the language are covered in detail below.

- For example, a public class, can only have members that are public or more exclusive entities
- `internal` classes can have only `internal`, `file-private` and `private` entities

From [SwiftDocs][SwiftDocs:ACL]:
```swift
public class SomePublicClass {                  // explicitly public class
    public var somePublicProperty = 0            // explicitly public class member
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
}
 
 class SomeInternalClass {                       // implicitly internal class
    var someInternalProperty = 0                 // implicitly internal class member
    fileprivate func someFilePrivateMethod() {}  // explicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
 }
  
  fileprivate class SomeFilePrivateClass {        // explicitly file-private class
    func someFilePrivateMethod() {}              // implicitly file-private class member
    private func somePrivateMethod() {}          // explicitly private class member
  }
   
   private class SomePrivateClass {                // explicitly private class
       func somePrivateMethod() {}                  // implicitly private class member
   }
```

## Functions
- The **implied access level** of a function is implied semantically based off the **most restrictive access level of all parameters and returns**
- If an **implied access level** of a function is **too restrictive**, explicitly **state its access level** with a prefix of the access keywords
- You **must** explicitly **define access level of a function** if **most restrictive parameter or return is more restrictive than function declaration**

## Tuples
- Implied to **have access of most restrictive component**
- Can't be explicity given any access level besides what is implied by its components

## Enums
- Can't specify different access level to individual enum cases
- All enum cases have same access as enum definition 
- Raw & Associated values must have the same access as the enum they get used in

## Subclasses
- Cannot have less restrictive access than its superclass
- Overriding & Subclassing is allowed depending on the class that is being attempted to subclass or override
    - i.e. you can't subclass a `private-file` class outside that class's source file, but you can within it

## Variables, Constants, Properties, Subscripts
- Cannot be more less restrictive than its containing type/class
    - Makes sense, because you're violating a class/type's access level if its properties are less restrictive
- If a variable, property, constant, or subscript is assigned based on a type, that type needs to be exactly as restrictive
--  **WAIT WHAT?!** from [Swift Docs][SwiftDocs:ACL]:
> if a constant, variable, property, or subscript makes use of a private type, the constant, variable, property, or subscript must also be marked as private:
`private var privateInstance = somePrivateClass()` why have a keyword explicitly specify the access if it must have the same kind as the assigning type?

## Getters/Setters
- Implied to have the same access as the type/class that of the constant/property/subscript/variable it belongs to
- Can explicitly specify a higher restriction than its associated type/class
- get & set can have different, but again, lower than their types restrictions


### Default Access
- Most entities get `internal` level access as they are defined
- 


# Miscelaneous

## Discardable Results in Swift 3
- Before swift 3, functions didn't necessarily need to return a function and not even a warning would show if called a returnable function without assigning it to something.
- Before swift 3, if you wanted a warning when this happens you had to prepend `@warn_unused_result`
- In Swift 3+, this paradigm was flipped
- Now a function that disregards the return gives a warning
- Quiet this warning with a prepended `@discardableResult` before the function definition
[From @patrickbdev on medium][DiscardableResult]
> This behaviour has been flipped around in Swift 3 where now any unused returned results generate a warning. If you’re designing an API and certain function return values should more so be treated as side effects you can annotate the function with @discardableResult


# References
[SwiftDocs:ACL]: https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html "Swift-Docs: Access Control"
[Cocoacast:private-fileprivate]: https://cocoacasts.com/what-is-the-difference-between-private-and-fileprivate-in-swift-3/ "Cocoacasts: Difference between private & file-private"
[DiscardableResult]: http://bit.ly/2qJW73R "Swift 3: Discardable Result on Medium"
