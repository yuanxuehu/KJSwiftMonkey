# KJSwiftMonkey
iOS自动化测试，模拟用户每100毫秒随机触发一个动作（一般是点击事件或Pan手势），OC和Swift均支持

# KJSwiftMonkey 暴力测试专用
## 1、新建一个Target,类型为UI Testing Bundle.一般选Swift版本,把SwiftMonkey.framework倒入到新Target中
## 接入UI测试代码
```
import XCTest
import SwiftMonkey

final class YourProjectUITests: XCTestCase {

    override func setUpWithError() throws {
        // Put setup code here. This method is called before the invocation of each test method in the class.

        // In UI tests it is usually best to stop immediately when a failure occurs.
        continueAfterFailure = false

        // In UI tests it’s important to set the initial state - such as interface orientation - required for your tests before they run. The setUp method is a good place to do this.
    }

    override func tearDownWithError() throws {
        // Put teardown code here. This method is called after the invocation of each test method in the class.
    }

    
    @MainActor
    func testMonkey() throws {
        let application = XCUIApplication()

        // Initialise the monkey tester with the current device
        // frame. Giving an explicit seed will make it generate
        // the same sequence of events on each run, and leaving it
        // out will generate a new sequence on each run.
        let monkey = Monkey(frame: application.frame)
        //let monkey = Monkey(seed: 123, frame: application.frame)

        // Add actions for the monkey to perform. We just use a
        // default set of actions for this, which is usually enough.
        // Use either one of these but maybe not both.


        // Occasionally, use the regular XCTest functionality
        // to check if an alert is shown, and click a random
        // button on it.
        monkey.addXCTestTapAlertAction(interval: 100, application: application)

        // Run the monkey test indefinitely.
        monkey.monkeyAround()
    }
    
    @MainActor
    func testExample() throws {
        // UI tests must launch the application that they test.
        let app = XCUIApplication()
        app.launch()

        // Use XCTAssert and related functions to verify your tests produce the correct results.
    }

    @MainActor
    func testLaunchPerformance() throws {
        // This measures how long it takes to launch your application.
        measure(metrics: [XCTApplicationLaunchMetric()]) {
            XCUIApplication().launch()
        }
    }
}
```

## 2、把SwiftMonkeyPaws文件夹拖入到开发工程中，MonkeyPaws.swift/MonkeyPawDrawer.swift/Configuration.swift/UIColor+MonkeyPaws.swift四个类需要依赖dev目标工程(即需要在Build Phases/Compile Sources中导入)。
### 如果是纯OC项目，需要稍微修改下源代码
```
    // 新增初始化方法：提供给OC调用，因为Swift的结构体等特有类型（比如参数是struct等）不能通过@objc暴露给OC使用
    public init(view: UIView) {
        self.configuration = Configuration()
        self.bezierPathDrawer = MonkeyPawDrawer.monkeyHandPath
        super.init()
        self.view = view

        layer.delegate = self
        layer.isOpaque = false
        layer.frame = view.layer.bounds
        layer.contentsScale = UIScreen.main.scale
        layer.rasterizationScale = UIScreen.main.scale

        view.layer.addSublayer(layer)

//        if tapUIApplication {
            tapUIApplicationSendEvent()
//        }
    }
```

## 3、在Build Setting/Define Modules，确保设置为 YES（适用于 Swift 项目）
## 4、

## 5、在 App 启动时didFinishLaunchingWithOptions，配置

## OC版本
```
#import"[你的项目名]-Swift.h"

self.monkeyPaw = [[MonkeyPaws alloc] initWithView:self.window];
```

## Swift版本
```
import UIKit
import SwiftMonkeyPaws

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
    var paws: MonkeyPaws?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        paws = MonkeyPaws(view: window!)
        return true
    }
}
```


## [SwiftMonkey官网](https://github.com/zalando/SwiftMonkey)
## 用到SwiftMonkey和SwiftMonkeyPaws这两个文件夹📁
