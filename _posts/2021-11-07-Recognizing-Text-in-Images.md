---
layout: post
title: Recognizing Text in Images
category: macOS App Development
---

Add text-recognition features to your app using the Vision framework.

#### What you need to know

-   [Recognizing Text in Images](https://developer.apple.com/documentation/vision/recognizing_text_in_images)
-   [Locating and Displaying Recognized Text](https://developer.apple.com/documentation/vision/locating_and_displaying_recognized_text)

> If not otherwise specified, Vision biases its results toward English. To alter its default behavior, provide an array of supported languages in the request’s recognitionLanguages property. The order in which you provide the languages dictates their relative importance. To recognize traditional and simplified Chinese, specify zh-Hant and zh-Hans as the first elements in the request’s recognitionLanguages property. English is the only other language that you can pair with Chinese.

#### Detect text from image

```swift
import Cocoa
import Vision

class ImageAnalysis {
    static let shared = ImageAnalysis()
    
    // default language
    var languages: [String] = ["zh-Hans"]
    
    func loadImage(_ image: NSImage?, completion: @escaping (_ text: String?) -> Void) {
        guard let image = image?.cgImage(forProposedRect: nil, context: nil, hints: nil) else {
            completion(nil)
            return
        }
        
        
        let request = VNRecognizeTextRequest { request, error in
            if let error = error {
                print("Error detecting text: \(error)")
                completion(nil)
            } else {
                if let result = self.handleDetectionResults(results: request.results) {
                    completion(result)
                } else {
                    completion(nil)
                }
            }
        }
        
        request.recognitionLanguages = languages
        request.recognitionLevel = .accurate
        
        performDetection(request: request, image: image)
    }
    
    private func performDetection(request: VNRecognizeTextRequest, image: CGImage) {
        request.cancel()
        
        let requests = [request]
        let handler = VNImageRequestHandler(cgImage: image, orientation: .up, options: [:])
        
        DispatchQueue.global(qos: .userInitiated).async {
            do {
                try handler.perform(requests)
            } catch {
                print("Error: \(error)")
            }
        }
    }
    
    private func handleDetectionResults(results: [Any]?) -> String? {
        guard let results = results, results.count > 0 else {
            return nil
        }
        
        var output: String = ""
        for result in results {
            if let observation = result as? VNRecognizedTextObservation {
                for text in observation.topCandidates(1) {
                    if !output.isEmpty {
                        output.append("\n")
                    }
                    output.append(text.string)
                }
            }
        }
        return output
    }
    
    func showSupportsLanguages() -> [String] {
        do {
            
            let revision = VNRecognizeTextRequest.supportedRevisions.last
            
            let supportsLanguages = try VNRecognizeTextRequest.supportedRecognitionLanguages(for: .accurate, revision: revision ?? 2)
            return supportsLanguages
        } catch {
            print("error: ", error)
        }
        
        return ["en-US"]
    }
}
```

#### How to use
```swift
// show the supportlanagues
let result = ImageAnalysis.shared.showSupportsLanguages()
print("result: ", result)
/**
* result:
* ["en-US", "fr-FR", "it-IT", "de-DE", "es-ES", "pt-BR", "zh-Hans", "zh-Hant"]
*/

// get the text from image
ImageAnalysis.shared.loadImage(image) { transcript in
    print("transcript: ", transcript)
}

```
