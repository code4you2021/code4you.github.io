---
layout: post
title: How to use ViewBuilder
category: SwiftUI
---

You typically use ViewBuilder as a parameter attribute for child view-producing closure parameters, allowing those closures to `provide multiple child views`. 

#### What you need to know

- 🍻 [ViewBuilder](https://swiftontap.com/viewbuilder)
-   [ViewBuilder](https://developer.apple.com/documentation/swiftui/viewbuilder)

#### Why use it
@ViewBuilder is a great tool for encapsulating reusable view logic. The great thing about it is that it strips your logic code from your view. It makes the code much more maintainable and readable.

#### How to use it

##### Fixed structure
In most cases the code is as follows, you can try adding some properties.
```swift
import SwiftUI

struct CustomView<Content: View>: View {
    let content: Content

    init(@ViewBuilder content: () -> Content) {
        self.content = content()
    }

    var body: some View {
        content

        /*
        * or what you want to do
        * content
        *    .padding()
        *    .background(Color.blue)
        */
    }
}
```

##### Example


To do...