# 10条小建议让你成为更好的 Swift 开发者

所以，你沐浴在 Swift 中已经好几个月了。现在，你想要成为一个更好的 Swift 开发者了吗？干吻，不是湿吻？我这边有一个美妙的想法送给你。

格式上我很抱歉，我试着用尽可能少的行数来让它简单明了。请随意复制并粘贴到 Playground 中来检查所有代码是否可以工作。👊如果不行，请在下面评论。我会尽快改正。

我也会在每一部分的下面嵌入 Youtube 课程作为解释说明。

我说太多了，让我们开始吧。

## 1. Extension

#### Ex）求一个数的平方
```swift
// Okay Version
func square(x: Int) -> Int { return x * x }
var squaredOFFive = square(x: 5)
square(x:squaredOFFive) // 625
```
当我们对 5 做两次平方时我们创建了一个无用的变量，我们需要让自己不喜欢打字。
```swift
// Better Version
extension Int { 
 var squared: Int { return self * self }
}
5.squared // 25
5.squared.squared // 625
```
[Extension 课程](https://www.youtube.com/watch?v=4pPtLjkF0HE&index=14&list=PL8btZwalbjYlRZh8Q1VK80Ly0YsZ7PZxx)

## 2. Generics

#### Ex）打印一个数组中的所有元素
```swift
// Bad Code
var stringArray = ["Bob", "Bobby", "SangJoon"]
var intArray = [1, 3, 4, 5, 6]
var doubleArray = [1.0, 2.0, 3.0]
func printStringArray(a: [String]) { for s in a { print(s) } }
func printIntArray(a: [Int]) { for i in a { print(i) } }
func printDoubleArray(a: [Double]) {for d in a { print(d) } }
```
太多无用的方法。让我们只创建一个。
```swift
// Awesome Code
func printElementFromArray<T>(a: [T]) {
 	for element in a { print(element) } 
 }
```
[Generic 课程](https://www.youtube.com/watch?v=nhxw7eaY-go)

## 3. For 循环 vs While 循环

#### Ex）打印 5 次「Count」
```swift
// Okay Code
var i = 0
while 5 > i {
	print("Count")
	i += 1 
}
```
你声明了一个 i 变量来确保在打印有受限的数字时你的电脑不会坏掉。

听着，越多变量 → 越耗费记忆力 → 头越痛 → bug 越多 → 生活中问题越多。

想一想[蝴蝶效应](https://en.wikipedia.org/wiki/Butterfly_effect)。
```swift
// Better Code
for _ in 1...5 { print("Count") }
```
[For 循环课程](https://www.youtube.com/watch?v=nhxw7eaY-go)

## 4. Optional 解包

#### Ex）guard let vs if let
我们来写一个程序来欢迎新用户。
```swift
var myUsername: Double?
var myPassword: Double?
// Hideous Code
func userLogIn() {
 if let username = myUsername {
  if let password = myPassword {
   print("Welcome, \(username)"!)
  }
 }
}
```
你看到[金字塔末日](https://en.wikipedia.org/wiki/Pyramid_of_doom_%28programming%29)了吗？这些是让人难以忍受的嵌套代码。永远不要这样做。摧毁坏代码，写一个好的。
```swift
// Pretty Code
func userLogIn() {
 	guard let username = myUsername, let password = myPassword else { return } 
 	print("Welcome, \(username)!") 
 }
```
差别很大。如果 username 或者 password 是一个 nil 值，这个好的实现将会调用 return 来提前退出这个方法。反之，它将打印欢迎语。没有金字塔。🤘

## 5. 计算型属性 vs 方法

#### Ex）计算圆的直径
```swift
// 💩 Code
func getDiameter(radius: Double) -> Double { return radius * 2}
func getRadius(diameter: Double) -> Double { return diameter / 2}
getDiameter(radius: 10) // return 20
getRadius(diameter: 200) // return 100
getRadius(diameter: 600) // return 300
```
你创建了两个互斥的方法。这糟透了。让我们把 radius 和 diameter 联系起来。
```swift
// Good Code
var radius: Double = 10
var diameter: Double {
	get { return radius * 2}
	set { radius = newValue / 2} 
} 
radius // 10
diameter // 20
diameter = 1000 
radius // 500
```
现在，radius 和 diameter 变量互相依赖对方。
越多联系 → 越少敲额外代码 → 越少写错 → bug 越少 → 生活中问题越少。💅

## 6. 利用枚举实现类型安全

#### Ex）售票
```swift
// Simply Bad

switch person {
 	case "Adult": print("Pay $7")
 	case "Child": print("Pay $3")
 	case "Senior": print("Pay $4")
 	default: print("You alive, bruh?") 
 }
```
“Adult”, “Child”, “Senior” → 你在硬编码。每一个 case 你都逐字敲打这些字符串值。这可不行。我解释过当你写太多会发生什么。我们不喜欢写太多。
```swift
// Beautiful Code
enum People { case adult, child, senior }
var person = People.adult
switch person {
 	case .adult: print("Pay $7")
 	case .child: print("Pay $3")
 	case .senior: print("Pay $4")
}
```
你将不会再写错，因为“.adult”, “.child”, “.senior”会高亮它们自己。如果 switch 遇到一个在枚举中并未定义的量，Xcode 会在左侧抛出一个红色的错误 😡。我只是找不到更好的 emoji 了。

[Switch 课程](https://www.youtube.com/watch?v=Q-X3Xbr6LP8&list=PL8btZwalbjYlRZh8Q1VK80Ly0YsZ7PZxx&index=6)
[Enum 课程](https://www.youtube.com/watch?v=DHCgaQ5GeR4&index=8&list=PL8btZwalbjYlRZh8Q1VK80Ly0YsZ7PZxx)

## 7. 空合并

#### Ex）用户选择 Twitter 主题颜色
```swift
// Long Code
var userChosenColor: String? 
var defaultColor = "Red"
var colorToUse = ""
if let Color = userChosenColor { 
	colorToUse = Color 
} else { 
	colorToUse = defaultColor 
}
```
太长了。让我们减减肥。
```swift
// Concise AF
var colorToUse = userChosenColor ?? defaultColor
```
上面这个代码，如果 `userChosenColor` 是 nil，它会选择 `defaultColor`。反之，选择 `userChosenColor`。

## 8. 条件合并

#### Ex）如果你有尖细的头发，要增加长度
```swift
// Simply Verbose
var currentHeight = 185
var hasSpikyHair = true
var finalHeight = 0
if hasSpikyHair { 
	finalHeight = currentHeight + 5
} else { 
 	finalHeight = currentHeight 
}
```
太长了，做一下减法。
```swift
// Lovely Code
finalHeight = currentHeight + (hasSpikyHair ? 5 : 0)
```
上面这个代码，当 hasSpikeHair 是 true 时，最终长度加 5，反之加 0.

## 9. 函数式编程

#### Ex）获得偶数
```swift
// Imperative (a.k.a boring)
var newEvens = [Int]()
for i in 1...10 {
	if i % 2 == 0 { newEvens.append(i) } 
}
print(newEvens) // [2, 4, 6, 8, 10]
```
我不需要看全部的过程。我来重新看你的 for 循环是怎么写的这存粹是浪费时间。让我们直截了当点吧，看代码。
```swift
// Declarative 😎
var evens = Array(1...10).filter { $0 % 2 == 0 } 
print(evens) // [2, 4, 6, 8, 10]
```
函数时编程真是非同凡响。
函数式编程使你看上去聪明些。

## 10. 闭包和函数
```swift
// Normal Function 
func sum(x: Int, y: Int) -> Int { return x + y }
var result = sum(x: 5, y: 6) // 11
```
你无需牢记函数名和参数名，你只需要一个。
```swift
// Closure
var sumUsingClosure: (Int, Int) -> (Int) = { $0 + $1 }
sumUsingClosure(5, 6) // 11 
```
[闭包课程](https://www.youtube.com/watch?v=fzqRas21_BM)

[点击阅读原文](https://medium.com/ios-geek-community/10-tips-to-become-better-swift-developer-a7c2ab6fc0c2#.6c5vz72rr)

