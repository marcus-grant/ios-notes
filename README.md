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




# References
[SwiftDocs:ACL]: https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html "Swift-Docs: Access Control"
[Cocoacast:private-fileprivate]: https://cocoacasts.com/what-is-the-difference-between-private-and-fileprivate-in-swift-3/ "Cocoacasts: Difference between private & file-private"
