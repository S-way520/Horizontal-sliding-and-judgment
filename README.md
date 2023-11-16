# Horizontal-sliding-and-judgment
Horizontal sliding and judgment: slide to the nearest content grid.
# The first part

https://github.com/S-way520/Horizontal-sliding-and-judgment/assets/95877651/bf3fc252-5663-43d0-a20b-62aa37b408d5

# The second part
Code file 1
```swift
import SwiftUI
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```
Code file 2
```swift
import SwiftUI
import Combine
struct ContentView: View {
    @StateObject var scrollViewHelper = ScrollViewHelper()
    @State var x: CGFloat = 0
    var body: some View {
        ScrollViewReader { scrollValue in
            ScrollView(.horizontal) {
                    HStack(spacing: 20) {
                        ForEach(0...100, id: \.self) { i in
                            Rectangle()
                                .frame(width: 350, height: 200)
                                .foregroundColor(.green)
                                .padding(.horizontal, 15)
                                .overlay(Text("\(i)"))
                        }
                    }.background(GeometryReader {
                        Color.clear.preference(key: ViewOffsetKey.self,
                                               value: -$0.frame(in: .named("scroll")).origin.x)
                    })
            }
            .coordinateSpace(name: "scroll")
            .onPreferenceChange(ViewOffsetKey.self) {
                scrollViewHelper.currentOffset = $0
                x = $0
                print("offset >> \($0)")
            }
            .onReceive(scrollViewHelper.$offsetAtScrollEnd) {
                print("offset >> \((Int(($0 + 200) / 400)))")
                scrollValue.scrollTo(Int(($0 + 200) / 400), anchor: .leading)
            }
        }
    }
}
class ScrollViewHelper: ObservableObject {
    @Published var currentOffset: CGFloat = 0
    @Published var offsetAtScrollEnd: CGFloat = 0
    private var cancellable: AnyCancellable?
    init() {
        cancellable = AnyCancellable($currentOffset
            .debounce(for: 0.2, scheduler: DispatchQueue.main)
            .dropFirst()
            .assign(to: \.offsetAtScrollEnd, on: self))
    }
}
struct ViewOffsetKey: PreferenceKey {
    static var defaultValue = CGFloat.zero
    static func reduce(value: inout CGFloat, nextValue: () -> CGFloat) {
        value += nextValue()
    }
}
```
