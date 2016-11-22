# 用 Swift 协议来让 Table View 代码变干净

从我实现第一个 `UITableViewDataSource` 协议的时候开始，编写正确的 `UITableView` 代码是我一直所追求的。不同于 delegation 部分，带有 Cell 复用的 `UITableViewDataSource` 部分经常使我困惑不已，自从我开始做 iOS 开发后就是这样。我简直不能理解。

7年来，在写了大量 `UITableView` 以后，我已经想出一个解决办法，它将帮助我和我的 iOS 开发同事，给我们带来希望，让我们能够写出干净的、可维护的 `UITableView` 代码，同时把样板代码减少到最低限度。

我敢保证，通过引入3个协议，你的上百行的 `cellForRowAtIndexPath` 代码实现将会减少到很少的行数，并且帮助你在正确的级别上进行关注点分离。事实上当我们完成后，你不再必须去重新写一个 `UITableViewDataSource`，而且为你的 table views 添加新的 Cell 和 Section 将会比以往更简单。

这篇文章假定你使用 Swift 和 Storyboards，但大部分代码都可以转换成 Objective-C，也可以兼容旧版的 nib 文件。

文中展示的代码可以在这个[Github仓库](https://github.com/jayway/TableViewCompatible)找到。

## 使用协议来帮你做正确的事儿

Swift 中的协议比 Objective-C 中的协议强大很多。但对于我来说，使用协议的主要原因仍然事一样的：传达做什么事情的方法（如果你愿意，也可以是实施一种模式），它让测试变的简单，最后也提供了一个通常比子类化更好的选择。

首先我来讲一下如何引入两个协议来帮你把 table view 的代码写的干净，可维护，并灵活。两个协议分别是：

- `TableViewCompatible`
- `Configurable`

### `TableViewCompatible`协议

通常一个 table view 显示一些由特定规则排序好的模型数据。实现方法通常是这样的，有一个模型对象的数组，然后当 `cellForRowAtIndexPath:` 方法被调用时，通过一个指定的 identifier 从 table view 中取出一个 cell，用 index path 参数从数组中找出模型对象，最后根据模型对象所需要的属性来配置这个 cell。

这种方法带来的问题是，view，model 和 `UITableViewDataSource` 的实现耦合的非常紧密（多半还有 `UIViewController` 的实例）。所有的配置和布局都发生在 data source 中，经常基于硬编码的 index path，四处移动 cell，或是增加具有不同布局方式的 cell 类型，这些都可能让代码变的复杂，难以阅读，并且难以维护。

我们真正想要的是让 data source 无需关心 cell 是如何显示和布局的。由于 `UITableViewCells` 的复用性质，将所有可能的 cell 实例组成一个数组存放在 data source 中是不可行的。所以我们需要一个方法来将 cell 的状态处理和配置分离到某些 view model 中。

一个 view model 本质上是一个携带 view 配置逻辑的对象。如果我们为所有想要在一个 table view 中显示的 model 对象创建 view model，并且让这个 view model 处理 cell 的创建和配置，我们所有的 data source 必须要做的就是维护 view model 中数据结构。

`TableViewCompatible` 协议会帮你做这些事儿，所以让我们瞧一瞧它长什么样儿：

```swift
protocol TableViewCompatible {

    var reuseIdentifier: String { get }
    
    func cellForTableView(tableView: UITableView, atIndexPath indexPath: IndexPath) -> UITableViewCell
    
}
```

如果你让你的模型对象遵从这个协议，你所有的 data source 必须要做的就是，找出模型对象并且调用它的 `cellForTableView:atIndexPath:` 方法。

让我们看一下是怎样实现的：

```swift
class MyModel: TableViewCompatible {
    
    var reuseIdentifier: String {
        return "MyModelCellIdentifier"
    }

    var title: String
    
    init(title: String) {
        self.title = title
    }
    
    func cellForTableView(tableView: UITableView, atIndexPath indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: self.reuseIdentifier, for: indexPath)
        cell.textLabel?.text = self.title
        return cell
    }
}
```

Data source 的实现是像这样：

```swift
class MyDataSource: UITableViewDataSource {
    
    var data = [MyModel]()
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return data.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let model = data[indexPath.row]
        return model.cellForTableView(tableView: tableView, atIndexPath: indexPath)
    }
}
```

现在，它看起来不那么糟糕了，而且将配置 cell 的职责转移给模型对象，这个实现实际上开辟出其他一些好处。

看一下定义在 data source 上面的 data 属性。目前它只支持 `MyModel` 类型的对象。假如我们让它支持所有遵从 `TableViewCompatible` 协议的对象会怎么样？

那将会是下面这个样子：

```swift
class MyDataSource: UITableViewDataSource {
    
    var data = [TableViewCompatible]()

    ...

}
```

我们现在有一个能够支持任何你能想到的 `UITableViewCell` 的 table view，只要模型对象遵从 `TableViewCompatible` 协议就行。很优雅，不是吗？

这也意味着你可以使数组中不同类型的 cell 变换位置然后 reload 这个 table view 就 ok 了，你不用修改 `cellForRowAtIndexPath` 中的任何一行代码。

但是你可能会问，section 该怎么办。在我们探讨这个问题之前，让我们先来看一下把布局的代码移到 view 当中。

### `Configurable` 协议

对一个 iOS 开发者而言，相对于 framework 所提供的布局方式较为单一的 cell 来说，我们经常会自定义一些布局更为复杂的 cell。这意味着我们需要基于这些我们想要展示的不同的模型对象以及它们的状态来写我们的布局代码。过去我们将这些代码放在 `cellForRowAtIndexPath` 中，但自从我们试着帮助未来的自己，让我们来看看是否我们可以做的更好。

布局代码实际上是属于一个 view 而不是一个 model。我希望这对我们所有人都是毫无疑问的，对吧？

帮助我们回忆起，毕竟我们仅仅是人类，让我们来引入另一个协议： `Configurable`。

```swift
protocol Configurable {
    
    associatedtype T
    var model: T? { get set }
    func configureWithModel(_: T)
    
}
```

每当你创建一个 `UITableViewCell` 子类，让它遵从 `Configurable` 协议，并且把布局代码放在 `configureWithModel:` 中。一个简单的用来展示 `MyModel` 对象的 `UITableViewCell` 的实现是这样的：

```swift
class MyModelCell: UITableViewCell, Configurable {
    
    @IBOutlet var titleLabel: UILabel!
    
    var model: MyModel?
    
    func configureWithModel(_ model: MyModel) {
        self.model = model
        self.titleLabel.text = model.title
    }
    
}
```

当把布局代码放在 `UITableViewCell` 子类中后，你拥有了正确的关注点分离。`MyModel` 可以更新去支持 `Configurable` 协议：

```swift
class MyModel: TableViewCompatible {
    
    var reuseIdentifier: String {
        return "MyModelCellIdentifier"
    }

    var title: String
    
    init(title: String) {
        self.title = title
    }
    
    func cellForTableView(tableView: UITableView, atIndexPath indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: self.reuseIdentifier, for: indexPath) as! MyModelCell
        cell.configureWithModel(self)
        return cell
    }
}
```

> 注释：转换成 `MyModelCell` 时我使用了隐式解包的可选值。通常我不推荐这样做，但这种情况下，如果解析失败，那这是一个编码错误，这时 crash 应该越快越好。当忘记链接到 `IBOutlet` 时也是同样的情况。

如果你需要改变 cell 的样式，你在 cell 的类中来做。如果你不再需要这个 cell 了，你仅仅需要把这个 cell 删除，并且让你的 `TableViewCompatible` 对象返回一个不同的 cell 或者简单的把它从 data 数组中移除即可；其他任何地方都无需改变。

### 一个关联类型要注意的地方

Swift 已经提供了很多新工具供我们玩耍。其中之一就是带有关联类型的协议（有些人喜欢管它叫 [PATs](https://www.natashatherobot.com/swift-what-are-protocols-with-associated-types/)）。如果你没有听说过 PATs，我可以推荐你看这篇  [Natasha The Robot 的 Blog](https://www.natashatherobot.com/swift-what-are-protocols-with-associated-types/) 还有 [Alexis Gallagher 的视频](https://www.youtube.com/watch?v=XWoNjiSPqI8) 。关于 Swift 的面向协议编程我推荐看一看 [WWDC2015 中的讲解](https://developer.apple.com/videos/play/wwdc2015/408/)。

不过，通过在 `Configurable` 协议中声明了一个关联类型，我们可以确保在实现时仅支持一个指定类型的模型对象，并且报错会从运行时转移到编译时。

## 中场休息

我们来总结一下：通过引入两个简单的协议，我强迫自己应用一个模式，这帮助我把关注点分离到正确的对象中。现在我的 `UITableViewDataSource` 代码基本上由可复用的样板代码组成，并且当我创建一个 cell 的时候，我会按照下面3步进行：

1. 创建一个遵从 `TableViewCompatible` 协议的 view model（或者让一个已经存在的 model 遵从这个协议）。
2. 创建一个遵从 `Configurable` 协议的 `UITableViewCell` 子类。
3. 把 model 添加到 data 数组，在索引时它会被展示在 table view 中。

现在 data source 不需要再关心它要展示那种类型的 cell，也不需要关心它们如何布局。

## Sections

所以 section 要怎么搞？很好，基本上你可以按你的想法来操作 section，但我有一种让我越来越满意的方法，我已经在过去的 3～4 个工程中使用（拆台者：这并不是很难）。

让我们引入一个协议 `TableViewSection`：

```swift
protocol TableViewSection {
    
    var sortOrder: Int { get set }
    var items: [TableViewCompatible] { get set }
    var headerTitle: String? { get set }
    var footerTitle: String? { get set }
    
    init(sortOrder: Int, items: [TableViewCompatible], headerTitle: String?, footerTitle: String?)
    
}
```

你很可能已经想到这个主意，让你的 data source 依赖 `TableViewSection` 对象数组。所以我们提供一个 `TableViewSection` 的真实实现，叫做 `MyTableViewSection`：

```swift
class MyTableViewSection: TableViewSection {
    
    var sortOrder: Int = 0
    var items: [TableViewCompatible]
    var headerTitle: String?
    var footerTitle: String?
    
    init(sortOrder: Int, items: [TableViewCompatible], headerTitle: String? = nil, footerTitle: String? = nil)  {
        self.sortOrder = sortOrder
        self.items = items
        self.headerTitle = headerTitle
        self.footerTitle = footerTitle
    }
    
}
```

... `MyDataSource` 像这样实现：

```swift
class MyDataSource: UITableViewDataSource {
    
    var sections = [TableViewSection]() {
        didSet {
            sections.sort {
                $0.sortOrder < $1.sortOrder
            }
        }
    }
    
    func numberOfSections(in tableView: UITableView) -> Int {
        return sections.count
    }
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return sections[section].items.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let model = sections[indexPath.section].items[indexPath.row]
        return model.cellForTableView(tableView: tableView, atIndexPath: indexPath)
    }
    
    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        return sections[section].headerTitle
    }
    
    func tableView(_ tableView: UITableView, titleForFooterInSection section: Int) -> String? {
        return sections[section].footerTitle
    }
}
```

> 注意到 `didSet` 中的排序操作了吗？我并不真的推荐这样做。你可能想要在其他什么地方来处理排序。事实上，要小心 `didSet`。使用时要当心（副作用，你懂的）。

## 结尾

我们都想做正确的事。不是去找简单的办法以避免麻烦；投机取巧。但有些时候真实世界会让你做你并不情愿做的事。这就是为什么你要调整你自己，正确的方法实际上也是简单的方法。我发现对于这件事来说协议是个完美的工具。

通过引入 3 个协议：
- `TableViewCompatible` （让你的 model 或者 view model 遵从这个协议）
- `Configurable` （让你的 `UITableViewCell` 子类来遵从这个协议）
- `TableViewSection` （让你的 `UITableViewDataSource` 依赖遵从这个协议的对象的数组）

然后当我们让 `UITableViewDataSource` 依赖一个属性：
```swift
var sections: [TableViewSection]
```
我现在被迫做正确的事，并且这样做实际上让我对处理更改布局这样的任务像插入、移动或是删除 cell 一样简单，因为我必须关心的事情仅仅是更新我的 model （在这里就是 `sections` 属性）来体现这些更新。

### 最后关于 Xcode snippets 的一些事儿

Xcode 中的 Snippets 是一个好工具，它让我们写一些样板代码变的非常快。用上面 3 个协议，很容易创建 snippets 来让我们简单快捷的写一个新的 cell。我已经做出两个 snippets，一个用来创建一个带有 cell model 的 `UITableViewCell` 子类，另一个用来创建 `UITableViewDataSource` 代码。我已经把他们上传到 git 仓库。你可以把它们放在这个文件夹里：

	~/Library/Developer/Xcode/UserData/CodeSnippets/
	
重启 Xcode。`cell` 和 `datasource` 两个自动补全快捷方式各自都可以在任何 Swift 顶层域里面触发。

如果你有什么意见或者建议来改善它，我非常乐意倾听。

在下一篇文章里我将会告诉你怎么通过使用协议扩展和默认实现来减轻 view controller 中大量的 table view 样板代码。我也会告诉你怎样把 `UITableViewDelegate` 代码分离到处理性能的新协议 `UITableViewDataSourcePrefetching` 中。

[点击阅读原文](https://blog.jayway.com/2016/11/15/clean-table-view-code-using-swift-protocols/)


