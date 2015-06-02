# Swift Coding guidelines
<!-- Guidelines adadpted from 
https://github.com/raywenderlich/swift-style-guide, 
https://github.com/github/swift-style-guide and
 current code practice -->
## General

 * Spaces, not tabs. Width=4
 * End files with newline
 * Don’t leave trailing whitespace.
 	* Not even leading indentation on blank lines.
 * Use semicolons only when you use multiple statements in one line (although it is not recommended to use multiple statements in one line)
 * Prefer `let` to `var` whenever possible

## Classes

 * Prefer structs over classes for things that do not have an identity.
	 Unless you require functionality that can only be provided by a class (like identity or deinitializers), implement a struct instead.
	
	Note that inheritance is (by itself) usually not a good reason to use classes, because polymorphism can be provided by protocols, and implementation reuse can be provided through composition.
  * Prefer implicit getters for read-only properties and subscripts.

```

 // Good
 
 var myProperty: Int {
 	return 42
 }
 
 subscript(index: Int) -> T {
 	return values[index]
 }
 
 // Bad
 
 var myBadProperty: Int {
 	get {
 		return 0
 	}
 }
 
 subscript(index: Int) -> T {
 	get {
 		return values[index]
 	}
 }
 
``` 
 
  * Associate colon with identifier or dictionary key
 

```
 
 class MyClass: MySuperClass { ... }
 
 let containerWidth: CGFloat = 10
 
 func countItems(type: MyClass) -> MySuperClass {...}
 
 var myDict: [String: AnyObject]
 
 var fruits = ["apple": 3, "banana": 2, "peach": 3]
 
```
 
 
 * Only explicitly refer to `self` when required. When accessing properties or methods on `self`, don't use `self` by default:
 
 
```
 
 private class Log {
 
 	var events = [Event]()
 	
 	func clean() {
 		recordMessage("cleaning log") // implicit call to self.recordMessage()
 		events = [] 
 	}
 	
 	func recordMessage(message: String) {
 		println(message)
 	}
 	
 	func replace(events: [Event]) {
 		self.events = events // self.events is required here
 	}
 	
 }
 
```

 * Prefer closures to small delegates. Use delegates when there are too many closures to be added.
 
 
```

// Good

class MyView: UIView {

	var onButtonPress: (() -> ())?
	
	@IBAction func handleButtonPress(sender: AnyObject) {
		onButtonPress?()
	}
	
}

class MyController: UIViewController {

	var myView: MyView!
	
	override func viewDidLoad() {
		super.viewDidLoad()
		myView = MyView()
		myView.onButtonPress = {
		    [unowned self] in
		    println("Callback from \(self.view)")
			println("Detected button tap")
		}
		myView.center = view.center
		view.addSubview(myView())
	}
	
}

```

Not preferred

```
// Bad

protocol MyViewDelegate {
	func myViewDidPressButton(view: MyView)
}

class MyView: UIView {

	var delegate: MyViewDelegate?
	
	@IBAction func handleButtonPress(sender: AnyObject) {
		delegate?.myViewDidPressButton(self)
	}
	
}

class MyController: UIViewController, MyViewDelegate {

	var myView: MyView!
	
	override func viewDidLoad() {
		super.viewDidLoad()
		myView = MyView()
		myView.delegate = self
		myView.center = view.center
		view.addSubview(myView())
	}
	
	// MARK: MyViewDelegate
	
	func myViewDidPressButton(view: MyView) {
		println("Detected button tap")
	}
	
}
```

## Code organization

 * Use marks to organize code (`// MARK: DelegateName`)
 
 * Make an empty line after class declaration and before closing brace for readability
 

```
 
 class Fruit {
 
 	func description() -> String {
 		return "Tasty"
 	}
 	
 }
 
```
 
  * Prefer type inference when possible
  
  
```
  
  // Good
  let letters = ["a", "b", "c"]
  
  
  // Bad
  let letters: [String] = ["a", "b", "c"]
  
```
  
  * Prefer shortcut versions of type declarations over full generic syntax:
  
 
```
  
  // Good
  var articles: [ZArticleResult]
  var configurations: [String: Configuration]
  
  // Bad
  var articles: Array<ZArticleResult>
  var configurations: Dictionary<String, Configuration>
  
```
  
  
## Naming

  * Use descriptive names with camel case for classes, methods, variables. Sometimes we use abbreviations: `vc` for `viewController`, `navController` for `navigationController`

 * Do not use class prefixes
 * use camelCase for variable names, CamelCase for classes, protocols, and constants
  * Use UpperCamelCase for enumeration values: 

```
enum Shape {
  case Rectangle
  case Square
  case Triangle
  case Circle
}
```

## Control flow
 * `else` starts on the same line as closing brace

```
// Good

if everything.isOk {
	drinkBeer()
} else {
	drinkMoreBeer()
}

// Bad

if everything.isOk 
{
	drinkBeer()
}
else {
	drinkMoreBeer()
}
	
```
 * Prefer `if let` to force-unwrapping optionals, since latter is prone to runtime errors.
 

```

 if let foo = bar {
   // use foo
 }

```

 Alternatively, sometimes we can use Optional Chaining:
 

```

 foo?.callIfFooIsNotNil()

```

 * Prefer `for-in` style of `for` loop over the `for-condition-increment` style
 

```

 // Good
 
 for _ in 0..<3 {
 	println("Hello there")
 }
 
 for (index, article) in enumerate(articles) {
 	println("\(index). \(article.label)")
 }
 
 // Bad
 
 for var i = 0; i < 3; i++ {
 	println("Hello there")
 }
 
 for var i = 0; i < articles.count; i++ {
 	let article = articles[i]
 	println("\(i). \(article.label)")
 }
 
```
 
## Comments

 * Use comments only when they are absolutely necessary. Explain _why_ are you doing something, and not _what_ are you doing in code. Prefer descriptive names and refactoring to writing a comment.

## Closures

 * Prefer trailing closure syntax
 * Use type inferencing for closure parameters:
 
 
```
 
 UIView.animateWithDuration(1.0, animations: {
 	self.view.alpha = 0
 }) {
 	finished in
 	self.view.removeFromSuperview()
 }
 
```
 
 * When issuing network calls, use `[weak self]` in completion handlers, since the hosting object may be deallocated when closure is called. Use `[unowned self]` when you are 100% sure `self` will be present at time of closure execution.
 
 * Use natife Swift struct initializers and helper functions instead of CGGeometry functions.
 

```

 let bounds = CGRect(x: 0, y: 0, width: 200, height: 100)
 let center = CGPoint(x: bounds.midX, y: bounds.midY)
 let rect = CGRect(origin: bounds.origin, size: CGSize(width: bounds.width, height: 150))

```

## Don't do that
<!-- http://ericasadun.com/2015/05/05/swift-dont-do-that/ -->
* Don't add code cruft. Avoid parentheses around conditions in if-statements or with the `return` keyword. Don't add semicolons except where syntactically demanded in statements or to separate statements on the same line. 
* Don't be unnecessarily verbose. Use inferred `get` clauses (for properties), anonymous arguments (e.g. `$0`, skipping the type in bits for closures), etc to clean up your code.
* Don't use unnecessary variables. The wildcard expression (`_`) ignores values during assignments. Use it to skip items that are not referenced in the called scope, e.g. `let (status, _) = GetInfo()` or `for _ in 1...5 { //do something 5 times }`