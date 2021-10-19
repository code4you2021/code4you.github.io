---
layout: post
title: How to use ViewBuilder
category: SwiftUI
---

You typically use ViewBuilder as a parameter attribute for child view-producing closure parameters, allowing those closures to `provide multiple child views`. 

#### What you need to know

- 🍻 [ViewBuilder](https://swiftontap.com/viewbuilder)
-   [ViewBuilder](https://developer.apple.com/documentation/swiftui/viewbuilder)
- [Init with @Binding variables](https://stackoverflow.com/questions/56973959/swiftui-how-to-implement-a-custom-init-with-binding-variables)


#### Why use it
@ViewBuilder is a great tool for encapsulating reusable view logic. The great thing about it is that it strips your logic code from your view. It makes the code much more maintainable and readable.

#### Preview
![wdNSKx](https://cdn.jsdelivr.net/gh/code4you2021/oss@main/uPuc/wdNSKx.gif)

#### Fixed structure
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

The view file, `Content.swift`:

![py13k5](https://cdn.jsdelivr.net/gh/code4you2021/oss@main/uPuc/py13k5.png)

```swift
import SwiftUI

struct Content: View {
    var body: some View {
        VStack(alignment:.center) {
            Image("1").resizable()
                .aspectRatio(contentMode: .fit)
                .cornerRadius(6)
                .padding(.trailing,10)
            
            Text("Dune(2021)")
                .font(.system(size: 18))
                .fontWeight(.semibold)
                .foregroundColor(.black)
            
            Spacer()
            
        }.frame(height: 250)
    }
}

struct Content_Previews: PreviewProvider {
    static var previews: some View {
        Content()
    }
}
```
The view file, `Detail.swift`:

![xmbZMp](https://cdn.jsdelivr.net/gh/code4you2021/oss@main/uPuc/xmbZMp.png)

```swift
import SwiftUI

struct Detail: View {
    var body: some View {
        HStack {
            Image("1").resizable()
                .aspectRatio(contentMode: .fit)
                .cornerRadius(10)
                .padding(.trailing,10)
            
            VStack(alignment:.leading) {
                
                Text("Dune(2021)")
                    .font(.system(size: 18))
                    .fontWeight(.bold)
                    .foregroundColor(.black)
                
                HStack {
                    ForEach(0 ..< 5) { item in
                        Image(systemName: "star.fill")
                            .resizable()
                            .aspectRatio(contentMode: .fit)
                            .frame(width:10,height: 10)
                            .foregroundColor(Color.yellow)
                    }
                }.padding(0).offset(y: -5)
                
                Text("Director: Denis Villeneuve")
                    .font(.subheadline)
                    .foregroundColor(Color(#colorLiteral(red: 0.501960814, green: 0.501960814, blue: 0.501960814, alpha: 1)))
                
                Text("Feature adaptation of Frank Herbert's science fiction novel, about the son of a noble family entrusted with the protection of the most valuable asset and most vital element in the galaxy.").font(.system(size: 14))
                    .lineLimit(3)
                    .lineSpacing(6)
                    .padding(.top, 4)
                    .foregroundColor(Color(#colorLiteral(red: 0.2549019754, green: 0.2745098174, blue: 0.3019607961, alpha: 1)))
                    
                
                Spacer()
                
            }
            
            Spacer()
            
        }.frame(height: 130)
    }
}
```
The view file, `CustomView.swift`:
```swift
import SwiftUI

struct CustomView<Content: View, Detail:View>: View {
    let content: Content
    let detail: Detail

    @Binding var showDetail: Bool

    init(
        showDetail: Binding<Bool>,
        @ViewBuilder content: () -> Content,
        @ViewBuilder detail: () -> Detail) {

        self.content = content()
        self.detail = detail()
        
        // Please note here that it is easy to overlook
        self._showDetail = showDetail
    }

    var body: some View {
        if showDetail {
            detail
        } else {
            content
        }
    }
}
```

The main file, `ContentView.swift`:
```swift
import SwiftUI

struct ContentView: View {

    @State var showDetail = false

    var body: some View {

        VStack {

            CustomView(showDetail: $showDetail) {
                Content()
            } detail: {
                Detail()
            }

            Button(action: {
                showDetail.toggle()
            }, label: {
                Text("Change Style")
            })
        }.frame(maxWidth: .infinity, maxHeight: .infinity)
            .padding()

    }
}
```