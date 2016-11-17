# Swift, Java, Node.js, 或者Ruby？Swift 在服务器端的优势

使用 Swift 作为服务端编程语言不仅让 client 和 server 端使用相同的编程语言成为可能，而且还可以复用 API 和代码。

这个 [try!Swift](https://www.tryswiftnyc.com/) 课程将向你介绍应用程序开发中一个新的 client 和 server 的交互模型，并且向你展示怎样迅速构建一个 client 和 server 端都使用 Swift 语言写成的 App。

## 介绍

我是 Chris Bailey。我和 Robert Dickerson 效力于 Swift@IBM 工程师团队。在这里，我们将要谈谈有关使用 Swift 来构建一个端到端应用程序。现在，作为其中的一部分，我们将会尝试并达成两件事：

- 首先，解释一下为什么 Swift 作为 server 端编程语言会和它在 client 端的表现同样优秀。
- 第二，更加有趣，重要并且充满希望的是，我会给你一个我们构建的真实的应用程序例子，并让你学会如何自己做一个。

## 现代应用程序设计

举个例子，你有一个终端用户，他想要构建一个面向 client 的应用程序，这个 client 端你会用 Swift 来实现。这时你很可能要用 Java、Node.js 或者 Ruby 来创建一套后端服务应用。接着，你可能还要集成一些第三方服务，也许是 Elasticsearch，Twitter，Twilio，AWS 或者一些 NoSQL 数据库。你或许还会与一些现有的基于 DB2，Oracle 或是 MySQL 的数据库系统打交道。

这将让你尝试并相信，与其仅仅用 Swift 开发面向用户的客户端 App，倒不如也将它用于 server 的开发。这将带给你一个统一的开发体验，你可以使用相同的 IDE 并且在 client 和 server 端共享代码。

## 服务端的 Swift

那真是太棒了，但是为什么是 Swift 呢？在 server 上使用 Swift 有什么特别的优势吗？其中之一就是它性能确实高。我们在9个方面有一组公开的基准测试，在一个被称为 Alioth 的集群上运行这个 [“Benchmarks Game.”](http://benchmarksgame.alioth.debian.org/) 这些基准测试横跨全部编程语言。他们把这些语言运行在一台搭载了四个 CPU 的 Linux 机器上。

这个特殊的基准测试被称为 [spectral norm](http://benchmarksgame.alioth.debian.org/u64q/performance.php?test=spectralnorm)，它只是精密的数学运算。它尝试消耗全部的 CPU 资源，在这种情况下这个基准测试使用派发并运行在这台机器全部的四个 CPU 上。

现在，Swift 在4秒内完成了任务。Java 的完成时间几乎一样，4.3秒。所以，在这项任务中，Swift 和 Java 的性能在四通道 Linux 笔记本上几乎是相同的。下面可以看一下 Node.js，Node.js 花了15.8秒，大概是 Swift 和 Java 的四倍。如果你用 Ruby 来运行个什么东西的话，它的速度是及其慢的。Ruby 不是一个为了计算而设计的语言，主要原因是 Ruby 并没有像 Node.js 和 Java 那样的即时编译类型的编译器。

由于性能上的原因，Swift 被用作 server 端的编程语言确实很有趣。它在内存的使用上同样优秀。看一下对应的基准测试，在常驻内存使用量上（在基准测试中需要使用的全部物理内存），Swift 需要15MB。然而 Java 需要32MB，所以 Swift 让你花一半的内存量就能达到跟 Java 相同的性能。Node.js 所需的内存比 Java 小一点，但仍然比 Swift 多很多。然而 Ruby 所需的内存就更多了。

## Swift 在云服务中的优势

如果你将应用运行在云上的话，这会非常有趣，因为大部分的云服务是根据你所使用的内存来收费的。所以你将要买一个256MB的 container，或者是512MB的 container 外加免费的 CPU。这就意味着在云服务上运行 Swift 语言是一个合适的选择，因为它兼顾了高性能和低内存占用。你可以花相同的资金来获取更多的 container，基于此你就可以运行更多的应用程序实例。因此，在云服务和 server 上面，Swift 是不错的选择。

对于开发者而言，Swift 也很优秀，因为它可以轻易的被叫做「同构开发」。目的就是你作为一个构建 Swift 工程的开发者。你可以创建一个既有 client 又有 server 组件的工程。你可以在同一个工程中编译它们，并且可以为它们写相同的代码，事实上，它们之间可以共享组件。接着你可以同时把应用部署到 client 和 server 上面，并且任何数据库和数据存储都使用相同的 connector。同时，你当然可以生成像是 [Swagger](http://swagger.io/) 那样的 API 文档，因为你可能想要在一个客户端或者是安卓客户端上都可以很好的浏览它。当你写第一个类的时候，你就是创建了一个基于 Swift 的 client 和 server 应用程序。

使用同样的工具链是一个副作用；你可以用 Xcode 处理任何事。你可以使用相同的构建系统，同样的基础设施。你可以在任何地方使用相同的工具和技术。

所以是什么让这一切成为可能的呢？

## 这些是怎么发生的？

我们是如何将 Swift 运行在 server 上的？第一件我们必须要做的事是让语言可以在上面运行。Swift 在2015年底开源时，Linux 版本并没有完成。我们有 Swift 的 runtime 和标准库，一小部分 foundation，还有一个未编译的 dispatch 版本，而且没有经过任何的测试。

现在呢，我们有一个能工作的 foundation 版本和一个彻底工作的 dispatch 版本，并且这一切都基于 Swift 3 的工具链。这样我们在 client 和 server 之间就拥有了统一的平台。下一步你需要一个web框架，IBM 已经做好了，叫做 [Kitura](https://github.com/IBM-Swift/Kitura)。外面还有许多其他的框架。

接下来你要添加一堆必要的东西，以便让你的 server 端的代码能够跑起来。首先你需要一个网络库。如果你需要 HTTPS 或是安全的 web sockets 的话你还需要一个安全相关的库，你还需要一些 HTTP 解析器。

我们添加了三个额外的库来做那些事。接下来我们把我们的 web 框架添加进来，当你写你的 client app 的时候你可以把里面使用的库来和 server 端共享。

我们尝试让 Kitura 去使用标准组件。我们使用 dispatch 来实现并发，这样做的原因是：

- dispatch 是一个非常好的库。
- 当我们发现性能问题时，我们在 dispatch 里面解决了，剩下的社区可以沿用。

当什么人发现 dispatch 中有问题的时候，我们也可以从中获益。所以越多用户的贡献组件或是库，分享就越多，质量会变的更高并且让所有人都受益。

作为驱使我们使用标准组件的一部分，在过去的数个月里，我们和 Apple 以及社区中的其他人一起工作，把这些之前提到的组件变成标准组件：Networking, Security, 和 HTTP parsing。使它们成为 Swift runtime 的一部分并且创建一套服务端 API 组件。

## Swift 3.0 + Kitura

由于 Swift 3.0 作为 Swift 在 server 上一个稳定版本的发布，加上像 Kitura 这样的 web 框架，使用 Swift 部署你的 iOS app 的后端成为了一个真实的选项，这带给你可共享代码的全部好处。

接下来我的同事 Robert 会向你展示如何在一个应用中来使用它。

## Blitter - 社交网络后端实例

在这个例子中，我们将会用 Swift 3 来构建一个社交网络后端，你可以在第28页幻灯片中看到一个例子。这是一个典型的后端应用，开启一个 HTTP server，建立一个路由来处理收到的请求，让它匹配一些正则表达式，来将请求路由到合适的 handler 中。

我们也可以插入许多中间件，举个例子，你可以使用一个我们已经建立好的 Facebook 证书的中间件，它可以拦截请求，验证 OAuth token，并给你返回一个 username。我们有许多闭包形式的 handler，当 router 找到一条匹配规则时就会调用它们。我们也会使用一个 Cassandra 后端，因为它是一个性能非常高的数据库。

我将会告诉你如何：

- 设置工程和相关依赖
- 设置路由规则
- 增加 Facebook 身份认证
- 建立数据库模型
- 处理请求

你们当中大多数都没有在命令行中玩过 Swift 3，我想给你们展示一个例子，当你安装Xcode Beta 8 或者在 Linux 上安装 Swift 工具链的时候会发生什么。

## 创建样板文件

如果你创建一个新的文件夹，你可以用 Swift package manager 来保存 Swift 的 package，它可以帮你创建一些样板代码，所以你准备好开始了：

    $ ~/> mkdir Blitter && cd Blitter
	$ ~/Blitter/> swift package init

    Creating library package: Blitter
    Creating Package.swift
    Creating .gitignore
    Creating Sources/
    Creating Sources/Blitter.swift
    Creating Tests/
    Creating Tests/LinuxMain.swift
    Creating Tests/BlitterTests/
    Creating Tests/BlitterTests/BlitterTests.swift


我们可以生成一个 Hello World App，还有一个用来添加测试用例的源文件。你们当中的大部分人可能都想要使用 Xcode 来进行开发，你可以用 Swift package manager 把这个 project 转变成一个 Xcode project：

    $ ~/Blitter/> swift package generate-xcodeproj
	$ ~/Blitter/> open Blitter.xcodeproj


我喜欢用 Xcode 来开发，在开发后端代码的时候你可以用它单步调试，增加断点，类型检查，代码覆盖，性能分析，所有这些工具都可以工作在在后端 Swift 上。

## 增加依赖

你可以通过 Package.swift 文件来指定依赖：

```
// Package.swift
import PackageDescription

let package = Package(
    name: "TwitterClone",
    dependencies: [
        .Package(url: "https://github.com/IBM-Swift/Kassandra", majorVersion: 0, minor: 1)
        .Package(url: "https://github.com/IBM-Swift/Kitura.git", majorVersion: 0, minor: 28)
        .Package(url: "https://github.com/IBM-Swift/SwiftyJSON.git", majorVersion: 0, minor: 14)
        ]
)
```

你不需要再使用 Carthage 或者 Cocoapods。你可以指定一个 Github URL 还有 打在这些仓库上的 tag 所表示 major 和 minor 版本号，Swift package manager 会自动下载这些依赖。

## 设置路由

下一步，你怎样设置你的路由？

```swift
router.get("/") { request, response, next throws in

    // Get my Feed here

}

router.get("/:user") { request, response, next throws in

    // Get user bleets here
    let user = request.parameters["user"]
    
}

router.post("/") { request, response, next throws in

    // Add a Bleet here
    
}
```

如果你使用过 Express.js 或者 Sinatra，我们使用与之非常相似的语法来注册更新 GETs，POSTs，PUTs这些请求。你得到一个 request-response 对象，一个能让你切换到下一个中间件的 function。如果你想传入一个 URL 参数，你可以用 /:user 来表示，后面我们就可以在参数里取到 user 了。

通常我喜欢把这些逻辑全部封装在一个 controller 中，所以我建立了一个 BlitterControler 类：
```swift
public class BlitterController {
    
    let kassandra = Kassandra()
    public let router = Router()

    public init() {

        router.get("/", handler: getMyFeed)
        router.get("/:user", handler: getUserFeed)
        router.post("/", handler: bleet)
        router.put("/:user", handler: followAuthor)
    }
}
```

我创建了数据库驱动，路由和注册。这些路由都在我的构造函数中。

## 增加 Facebook 身份认证

我们该如何来添加 Facebook 的身份认证？我们有一个 **Credentials** 模块：

```swift
import Credentials
import CredentialsFacebook

let credentials = Credentials()
credentials.register(CredentialsFacebook())
```

我们创建一个 Credentials() 实例，然后到前面把 Facebook credentials 添加到中间件中。我们也会支持 GitHub 和其他一些认证方式。

如果你想要这些中间件来拦截请求，你得向路由注册这个 credential 的中间件。举个例子，当我作为一个作者想来发一条内容，我会向一个路由路径发起一个 POST 请求，然后中间件会检查我们的 username 是什么，然后将用这个用户：

```swift
router.post("/", middleware: credentials)

router.post("/") { request, response, next in}
    /// ...
    let profile = request.userProfile
    let userId = profile.id // "robert dickerson"
    let userName = profile.displayName // "Robert F. Dickerson"
    /// ...
}
```

## 建立模型和数据库

一个 **Bleet** 拥有以下属性，我每次写一条新的，所有的粉丝将会得到一份拷贝：

```swift
struct Bleet {
    
    var id             : UUID?
    let author         : String
    let subscriber     : String
    let message     : String
    let postDate     : Date

}
```

我们的数据模型就是这样。一条 **Bleet** 有 **message** 和 **postDate**。关键是我们使用了 **Foundation**，这真的很棒。注意，你再也不用添加这个 **NS** 前缀了，他们已经改了名字。date 和 UUID 在 Linux 上用起来很好。

你在写后端代码的时候，经常会用 JSON 来组织大量的数据，将一个结构体转换成字符串对这个概念非常有用。我们定义了一个协议来确保这种转换是可行的：

```swift
typealias StringValuePair = [String : Any]

protocol StringValuePairConvertible {
    var stringValuePairs: StringValuePair {get}
}
```

如果一个对象可以转换成字符串值，那么这种对象所组成的集合也应该同样可以进行这种转换：

```swift
extension Array where Element : StringValuePairConvertible {
    var stringValuePairs: [StringValuePair] {
        return self.map { $0.stringValuePairs }
    }
}
```

我们可以将这个字符串值转换方法添加到 **Bleet** 中，我们就可以很容易饿将它转换成 JSON：

```swift
extension Bleet: StringValuePairConvertible {
    var strongValuePairs: StringValuePair {
        var result = StringValuePair()

        result["id"]            = "\(self.id!)"
        result["author"]         = self.author
        result["subscriber"]     = self.subscriber
        result["message"]         = self.message
        result["postdate"]         = "\(self.postDate)"

        return result
    }
}
```

我们该如何给现有的结构体增加一种能力，可以将它们持久化或者说保存到数据库中？通过使用我们开发的 Cassandra 驱动，来扩展我们的 **Bleet**，使之具有 **Model** 行为。然后我们可以指定表名让 bleets 存储在那里，你将免费得到一套持久化函数：

```swift
import Kassandra

extension Bleet : Model {
    static let tableName = "bleet"

    // other mapping goes here
}
```

举个例子，当我创建一个新的 **Bleet**，接着我可以链接数据库并调用 **.save()** 方法。数据持久化会非常简单：

```swift
let bleet = Bleet(    id             : UUID(),
					 author         : "Robert",
                    		 subscriber     : "Chris",
                    		 message     : "I love Swift!",
                    		 postDate     : Date()
                )

try kassandra.connect(with: "blitter") { _ in bleet.save() }
```

也就是说，我们有一份订阅者清单的话，你想要为他们中的每个人都创建一个新的 bleet 并存储到数据库中。你可以将 subscriber map 到 bleet 的构造器中，然后你得到一个新的列表，接着你就可以愉快的调用 save 方法进行持久化了：

```swift
// Get the subscribers ["Chris", "Ashley", "Emily"]
let newbleets = [Bleet] = subscribers.map {
    return Bleet(    id             : UUID(),
                    author         : userID,
                    subscriber     : $0,
                    message     : message,
                    postDate     : Date())
}

newbleets.forEach { $0.save() { _ in } }
```

你也可以在 map 的结尾处链式调用 forEach，来使代码写在一行当中。

对于异步错误处理，你常常会看到 APIs 会给你返回一个 value 还有 error，它们之间是互斥的：

```swift
func doSomething(oncompletion: (Stuff?, Error?) -> Void) {
    
}
```

我推荐你利用一些语言特性来确保这种互斥，像这样：

```swift
enum Result<T> {
    case success(T)
    case error(Error)

    var value: T? {
        switch self {
        case .success (let value): return value
        case .error: return nil
        }
    }

    // Do same for error
}
```

这里你有一个表示结果的范型枚举，在成功时会得到一个对象，失败时得到一个错误。当你想取返回值时，刚好有个错误，那么它将返回一个 nil，反之会得到这个值。

我们怎样获取一个 bleets 列表？我们可以连接数据库，拉取所有的 bleets，如果遇到错误，我们可以用 error 选项的参数来调用 **onComplete**。如果你取到了结果，那么你就可以用它们来创建 bleets，接着调用 **success** handler：

```swift
func getBleets(oncomplete: Result<[Bleet]>) -> Void) {
    try kassandra.connect(with: "blitter") { _ in
        Bleet.fetch() { bleets, error in

            if let error = error {
                oncomplete( .error(error))
            }

            let result = bleets.flatMap() { Bleet.init(withValuePair:)}
            oncomplete( .success(result) )
        }

    }
}
```

我们还可以处理条件判断。如果你想要根据作者的名字来获取 bleets，我们可以利用 Swift 重载运算符这个特性，用 **==** 得到的结果作为条件判断适用于我们的 query：

```swift
Bleet.fetch(predicate: "author" == user,
            limit: 50) { bleets. error in

        ///
}
```

## 处理请求

最后，我想要展示给你如何来处理请求：

```swift
getBleets { result in
    
    guard let bleets = result.value else {
        response.status(.badRequest).send()
        response.next()
        return
    }

    response.status(.OK)
        .send(json: JSON(bleets.stringValuePairs))
        response.next()
    }
}
```

当我们取到 bleets 返回时，我们得到一个结果。我们检查发现结果是成功的，如果是失败的话我们会在 response type 后面添加一个 bad request。这是一个基本的 500 错误。如果是成功的，状态码就是 200。我们就是通过这个 **.status()** 方法来设置状态码的。

接着当你准备好序列化一个 JSON 时，你就可以调用 **.send()** 方法来发送这个 JSON。我们已经为 JSON 类型设置好了 MIME type，并且可以用这个字符串值还原回我们的字典。使用 [SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON)，让字典转换成一个 **JSON** 对象，它可以序列化成一个字符串。

常常遇到一种情况是，一个哥们儿想用 JSON 的形式给服务器发送一条消息，然后我们想能够从中获取一些信息。

这种情况下，我们可以给 router request 对象加个扩展，检查一下有没有 body，如果有的话再检查一下它是不是 JSON，然后返回这个 JSON：

```swift
extension RouterRequest {
    
    var json: Result<JSON> {

        guard let body = self.body else {
            return .error(requestError("No body in the message"))
        }

        guard case let .json(json) = body else {
            return .error(requestError("Body was not formed as JSON"))
        }

        return json
    }
}
```

当我们准备要存储这个 bleet 时，我们就可以接着从这个 JSON 中获取 message 并且保存：

```swift
let userID = authenticate(request: request)

let jsonResult = request.json
guard let json = jsonResult.result else {
    response.status(.badRequest)
    next()
    return
}

let message = json["message"].stringValue

// Save the Bleets with author matching userID
```

## 结尾

如果你对其他使用 **Kitura** 的例子感兴趣，我们已经用多种数据库构建了一个 to-do list app，比如 MongoDB 和 Redis。当然我们也做了一个与之相匹配的 iOS app。

如果你对一个更有扩展性的例子也感兴趣，你可以用 AngularJS，我们有一个 BluePic 网站例子用的是 Kitura，CouchDB，对象存储，还有 Watson（沃森）。我们有一个 iOS 端，还有一个基于 AngularJS 的前端。

非常感谢。我希望你们下载 Xcode 8 Beta 和最新的工具链，来玩一下 Kitura。

## 资源

- [IBM-Swift/Kitura](https://github.com/IBM-Swift/Kitura)
- [IBM-Swift/Blitter](https://github.com/IBM-Swift/Blitter)
- [IBM-Swift/BluePic](https://github.com/IBM-Swift/BluePic)
- [Alioth - Benchmarks Game](http://benchmarksgame.alioth.debian.org/)
- [Alioth - Benchmarks Game, Spectral Norm Test](http://benchmarksgame.alioth.debian.org/u64q/performance.php?test=spectralnorm)
- [Swagger](http://swagger.io/)
- [SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON)


[点击阅读原文](https://realm.io/news/tryswift-chris-robert-end-to-end-application-development-swift-backend/)



