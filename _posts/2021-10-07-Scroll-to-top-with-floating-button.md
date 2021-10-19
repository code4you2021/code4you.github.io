---
layout: post
title: Scroll to top with floating button
category: SwiftUI in Action
---

Here is possible alternate solution in `Xcode 12 / iOS 14 (SwiftUI 2.0)` that can be used in same scenario when controls for scrolling is outside of scrolling area (because SwiftUI2 `ScrollViewReader` can be used only inside ScrollView).

#### Preview

![w8g6oP](https://cdn.jsdelivr.net/gh/code4you2021/oss@main/uPic/w8g6oP.gif)

What's learned from books is superficial after all. It's crucial to have it personally tested somehow.

#### What you need to know

- 🍻[ScrollViewReader](https://swiftontap.com/ScrollViewProxy)
- 🍻[overlay](https://swiftontap.com/view/overlay(_:alignment:))
- 🍻[GeometryReader](https://swiftontap.com/geometryreader)
- 🍻[GeometryProxy](https://swiftontap.com/geometryproxy)

#### Let's get started

`ContentView.swfit`:

```swift
import SwiftUI

struct ContentView: View {
    @State private var starY: CGFloat = 0.0
    @State private var offetY: CGFloat = 0.0
    @State private var showButton = false

    var body: some View {
        ScrollViewReader(content: { scrollProxy in
            ScrollView(.vertical, showsIndicators: false, content: {
                VStack {
                    ForEach(getData()?.articles ?? [], id: \.urlToImage) { article in
                        NewsCell(article: article)
                    }
                }
                .id("scrollBox1")
                .overlay(
                    /**
                     *  Use the overlay modifier to get the minY value of the VStack through the GeometryReader
                     */
                    GeometryReader(content: { proxy -> Color in

                        DispatchQueue.main.async {
                            let minY = proxy.frame(in: .global).minY
                            
                            // get start postion
                            if self.starY == 0 {
                                self.starY = minY
                            }
                            
                            // get the offset postion
                            let offsetY = minY - self.starY
                            
                            /**
                            *  Scroll down and the value gets smaller
                            *  Scroll up and the value gets biger
                            */
                            print("minY: ", minY, "offsetY: ", offsetY)
                            /*
                             minY:  64.00000000000045 offsetY:  0.0
                             minY:  63.500000000000455 offsetY:  -0.5
                             minY:  62.500000000000455 offsetY:  -1.5
                             minY:  62.000000000000455 offsetY:  -2.0
                             minY:  61.500000000000455 offsetY:  -2.5
                             minY:  61.000000000000455 offsetY:  -3.0
                             minY:  60.500000000000455 offsetY:  -3.5
                             minY:  60.000000000000455 offsetY:  -4.0
                             minY:  59.000000000000455 offsetY:  -5.0
                             minY:  58.500000000000455 offsetY:  -5.5
                             minY:  57.500000000000455 offsetY:  -6.5
                             minY:  57.000000000000455 offsetY:  -7.0
                             minY:  56.500000000000455 offsetY:  -7.5
                             minY:  56.000000000000455 offsetY:  -8.0
                             minY:  55.500000000000455 offsetY:  -8.5
                             minY:  55.000000000000455 offsetY:  -9.0
                             minY:  54.500000000000455 offsetY:  -9.5
                             minY:  54.000000000000455 offsetY:  -10.0
                             */
                            
                            if -1 * offsetY >= 600 {
                                self.showButton = true
                            } else {
                                self.showButton = false
                            }

                            print("abs", abs(offsetY))
                        }

                        return Color.clear
                    })
                )
                .frame(maxWidth: .infinity, maxHeight: .infinity)

            })
                .padding(.top, getSafeArea().top)
                .overlay(
                    VStack {
                        Spacer()
                        HStack {
                            Spacer()
                            Button(action: {
                                withAnimation(.linear) {
                                    scrollProxy.scrollTo("scrollBox1", anchor: .top)
                                }
                            }, label: {
                                Image(systemName: "arrow.up.to.line")
                                    .resizable()
                                    .aspectRatio(contentMode: .fit)
                                    .foregroundColor(.white)
                                    .frame(width: 20, height: 20, alignment: .center)
                            }).frame(width: 50, height: 50, alignment: .center)
                                .background(Color(#colorLiteral(red: 0.9043928981, green: 0.2968245745, blue: 0.5311032534, alpha: 1)))
                                .cornerRadius(100)
                                .padding(.bottom, getSafeArea().bottom)
                                .padding(.trailing, getSafeArea().bottom)
                                .opacity(self.showButton ? 1 : 0)
                                .animation(.easeInOut)
                        }
                    }
                )

        })
            .padding()
            .background(Color(#colorLiteral(red: 0.8039215803, green: 0.8039215803, blue: 0.8039215803, alpha: 1)))
            .edgesIgnoringSafeArea(.all)
    }
}

extension ContentView {
    func readFile(forName name: String) -> Data? {
        do {
            if let bundlePath = Bundle.main.path(forResource: name, ofType: "json"),
               let jsonData = try String(contentsOfFile: bundlePath).data(using: .utf8)
            {
                return jsonData
            }
        } catch {
            print(error)
        }
        return nil
    }

    func getData() -> News? {
        guard let data = readFile(forName: "data") else {
            return nil
        }

        guard let result = try? JSONDecoder().decode(News.self, from: data) else {
            return nil
        }

        return result
    }
}

// extending view to get saferarea...
extension View {
    func getSafeArea() -> UIEdgeInsets {
        return UIApplication.shared.windows.first?.safeAreaInsets ?? UIEdgeInsets(top: 0, left: 0, bottom: 0, right: 0)
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

`News.swift` is a model:

```swift
import Foundation
struct News: Codable {
    let articles: [Article]
}
// MARK: - Article
struct Article: Codable, Hashable {
    let title: String
    let urlToImage: String
}
```

`NewsCell.swift` is the ScrollView's item:

```swift
import SwiftUI
import Kingfisher

struct NewsCell: View {
    var article:Article
    var body: some View {
        VStack {
            Text("\(article.title)")
                .font(.system(size: 22, weight: .medium, design: .rounded))
            KFImage.url(URL(string: "\(article.urlToImage)")!)
                .resizable()
                .aspectRatio(contentMode: .fit)
                .cornerRadius(10)
        }.padding()
        .background(Color(#colorLiteral(red: 0.999904573, green: 1, blue: 0.9998808503, alpha: 1)))
        .cornerRadius(16)
    }
}
```

`data.json` is the mock data:
```json
{
  "articles": [
    {
      "source": {
        "id": null,
        "name": "Bolsamania.com"
      },
      "author": "Nieves Amigo",
      "title": "El Ibex afronta un arranque semanal tranquilo mientras el PIB chino decepciona",
      "description": "El Ibex y el resto de plazas del Viejo Continente apuntan a un comienzo de semana tranquilo. Los futuros europeos caen un leve 0,05% mientras que la jornada comienza con importantes referencias en China.",
      "url": "https://www.bolsamania.com/noticias/cronica-ibex/ibex-arranque-semanal-tranquilo-pib-chino-decepciona--8231899.html",
      "urlToImage": "https://img2.s3wfg.com/web/img/images_uploaded/7/2/ep_valores_del_ibex_35_en_el_palacio_de_la_bolsa.jpg",
      "publishedAt": "2021-10-18T05:33:58Z",
      "content": "El Ibex y el resto de plazas del Viejo Continente apuntan a un comienzo de semana tranquilo. Los futuros europeos caen un leve 0,05% mientras que la jornada comienza con importantes referencias en Ch… [+3265 chars]"
    },
    ... and so on
    ]
}    
```