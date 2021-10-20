---
layout: post
title: What is didSet and willSet
category: Swift
---

The willSet and didSet features are used in the Swift language to monitor property value changes other than initialization.

#### What you need to know

-   [Properties](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)

> You have the option to define either or both of these observers on a property:
> - willSet is called just before the value is stored.
> - didSet is called immediately after the new value is stored.

#### Example

```swift

import UIKit

class Boy: NSObject {
    var firstName: String = ""
    var lastName: String = ""
    
    var fullName: String {
        get {
            "\(firstName) \(lastName)"
        }
    }
    
    var age: Int = 0 {
        // We need to do something before the age property changes
        willSet {
            print("1.newValue: ", newValue)
        }
        
        // We need to do something after the age property change
        didSet {
            print("2.oldValue: ", oldValue)
            
            if age > 6 {
                print("3.hello, Boy")
            }
        }
    }
    
    func toString() -> String {
        return "fullName: \(fullName) " + ", age: \(age) "
    }
}

var boy = Boy()
boy.firstName = "first"
boy.lastName = "last"
boy.age = 10

print(boy.toString())

/**
*1.newValue:  10
*2.oldValue:  0
*3.hello, Boy
*fullName: first last , age: 10 
*/


```