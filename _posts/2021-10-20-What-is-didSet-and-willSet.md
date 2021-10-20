---
layout: post
title: What is didSet and willSet
category: Swift
---

The willSet and didSet features are used in the Swift language to monitor property value changes other than initialization.

#### Example

```swift

import UIKit

class Boy: NSObject {
    var firstName: String = ""
    var lastName: String = ""
    
    var fullName:String {
        get {
            return "\(firstName) \(lastName)"
        }
    }
    
    var age:Int = 0 {
        // Something needs to be done before the age property changes
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
}

var boy = Boy()
boy.age = 10

/**
*1.newValue:  10
*2.oldValue:  0
*3.hello, Boy
*/


```