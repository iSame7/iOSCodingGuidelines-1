# Objective-C Coding Guidelines

## Misc

* Take compiler warnings seriously and help eliminate them from the project. The number of warnings should never be higher after you’ve committed something to trunk.


## Naming Conventions
### Classes
#### Class Names
All UIKit-derived Classes should be named by removing the prefix of their parent class (i.e. "UI") and replacing it by one ore more **meaningful** and **straightforward** words.

<table>
  <tr>
      <th>Purpose</th>
        <th>Parent</th>
        <th>Class Name</th>
        <th>OK?</th>
    </tr>
    <tr>
      <td>Article View Controller</td>
      <td>UIViewController</td>
      <td>ArticleViewController</td>
      <td>YES</td>
    </tr>
    <tr>
      <td>Article View Controller</td>
      <td>UIViewController</td>
      <td>ArticleController</td>
      <td>NO</td>
    </tr>
    <tr>
      <td>Account Infos View</td>
      <td>UIView</td>
      <td>MyAccountInfosView</td>
      <td>NO</td>
    </tr>
    <tr>
      <td>Account Infos View</td>
      <td>UIView</td>
      <td>AccountInfosView</td>
      <td>YES</td>
    </tr>
    <tr>
      <td>Account Infos View</td>
      <td>UIView</td>
      <td>AccountInformationsView</td>
      <td>YES</td>
    </tr>
</table>

All derived classes in general should follow the same pattern.

All model classes names should be consistent with the API and/or the shop naming, adding to it only the **Z** prefix . For example, even if we talk about products with PM and onSite, the model class representing a so-called product should be named ZArticle[…] (ZArticleConfig, ZArticleSimple, etc.).

#### Interface File Structure
The main goal when writing a .h file is to keep it as small as possible in terms of actual code lines. To do so, these rules are to follow:

* Don't put anything private in it (properties, methods, protocols conformity, etc.)
* Don't declare instance variables in it
* Don't put any kind of implementation in it (like C functions)
* Don't put anything useless in it (prefer @class statements to imports, etc.)
* If possible, declare public properties as readonly
* Put comments to explain usage if necessary, not to explain implementation
* Only list implemented protocols in the header if it makes sense for inheritance. Otherwise, put them into the class extension
* Mark your designated initializer using `__attribute__((objc_designated_initializer))` (`NS_DESIGNATED_INITIALIZER` once it's made available)

Every time it's possible, structure your class properties and methods using **explicit** pragma marks that tell their "context" and will be reused in the implementation file.

Readonly properties should be explicitly marked as such.

Any interface file should be structured like this:

```
// n lines header with at least the name of the author

[#imports]

[constants]

[@class statements]

[@protocol statements]

@interface [CLASS]: [PARENT] [<protocols>]

[block of public properties and methods declaration 1]
[block of public properties and methods declaration 2]

@end

[public class-related C functions declaration]
```

#### Implementation File Structure
The main goal when writing a .m file is to make it as readable and understandable as possible. To do so, these rules are to follow:

- Put all your private stuff in it
- Use the class extension pattern(`@interface [CLASS] ()`) to explicitly declare any private property, method or protocol conformity
- Remove all methods and comments written by the system
- Clean your code from any unused lines (methods, statements, etc.)
- Avoid duplicating code (of course…), for example by using constants instead of writing the same float value several times, or by having a look at categories like `UIColor+ZALAdditions` or `UIFont+Zalando`
- Do not use instance variables. Use @property. Any imagined performance gains are negligible and a consistent style is more important.
 - Do not use _ivar (unless overwriting a setter of course or in initializers to avoid any side effects of custom getters/setters). self.variableName is the preferred style.
- Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.
- Using constants to explain contextual code. For example prefer the first example below to the second:

```
//Example 1
switch (indexPath.section) {
  case ZALVariantArticlesSection:
    // Do A
  break;
  case ZALRecommendedArticlesSection:
    // Do B
  break;
  default:
    // Do C
  break;
}
//Example 2
if (indexPath.section == 1) {
  // Do A
}
else if (indexPath.section == 2) {
  // Do B
}
else {
  // Do C
}
```

Any implementation file should be structured like this:

```
// n lines header with at least the name of the author

[#imports]

[constants]

[private class-related C functions declaration]

@interface [CLASS]() [<protocols>]

[block of private properties and methods declaration 1]
[block of private properties and methods declaration 2]

@end

@implementation [CLASS]

[blocks of superclass properties and methods overriding 1]
[blocks of superclass properties and methods overriding 2]

[block of public properties and methods implementation 1]
[block of public properties and methods implementation 2]

[block of private properties and methods implementation 1]
[block of private properties and methods implementation 2]

[block of protocol 1 methods implementation]
[block of protocol 2 methods implementation]

@end

[class-related C functions implementation]
```

### Organize code with #pragma marks

Use them to divide code into logical sections. But do so sparingly. If the class conforms to any protocols, group all of those methods together. Otherwise, just use common sense. Follow the below structure as a general guide

```

- (instancetype)init {}
- (void)dealloc {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}

- (void)setCustomProperty:(id)value {}
- (id)customProperty {}

- (IBAction)submitData:(id)sender {}

- (void)publicMethod {}

- (void)privateMethod {}

#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

- (void)someDelegateMethod() {}
```

### Categories
Naming wise, prefix categories with the ubiquitous `ZAL` prefix. Method names should use a three letter with underscore prefix `zal_methodName` as per [Apple`s](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html#//apple_ref/doc/uid/TP40011210-CH6-SW4) recommendations. We’ll illustrate the point with an addition to NSDate.

* File name `NSDate+ZALAdditions`
* Interface `@interface NSDate (ZALAdditions)`
* Method `+(NSDate *)zal_dateFromISO8601String:(NSString *)ISO8601String;`

### Class Members
```
TODO
- Classes (controllers, views & models)
- Class members
- Protocols
- Categories
- .h structure
- .m structure
- What about that "Z" prefix?
```
#### Organizing imports

* Imports are sorted alphabetically within a group with framework imports at the bottom (again in alphabetical order). (vim command: `sort i`)


```
#import "MyClass.h"
#import "AEvertyhingElse.h"
#import "BEvertyhingElse.h"
#import <AFramework.h>

```

### Use and Naming of Constants
Use `static` and `const` for all constants and do not use `#define` statements. If they are for class internal use, only declare them in the .m file. If they do not need to be exposed, only define your constant in the implementation file.
Declaring it `static` means that the variable is local to the translation unit it’s defined in and in fact using both `static` and `const` means the compiler won’t create a symbol and replace all uses of the constant just as  a `#define` would!
Prefix internal constants with the `k` prefix:
```
static NSString *const kStaticString = @"something static"
static const int kNumberOfCategorySections = 3;
```
If you need to define a global constant, prefix it with the three-letter ZAL prefix and continue naming them using title case. The first letter should be capitalized too. It should be declared as `extern` in a .h file and receive its actual value in the .m file:

* AppDelegate.h
*
```
extern NSString *const ZALGlobalError;
extern const NSTimeInterval ZALGlobalTimeInterval;
```
* AppDelegate.m
*
```
NSString *const ZALGlobalError = @"de.zalando.iphone.error";
const NSTimeInterval ZALGlobalTimeInterval = 0.4;
```

### Blocks
The use of blocks is highly encouraged and should be your first port of call for any delayed (asynchronous) tasks.
However, there seems to be a lot of confusion as to the use of weak and strong references in combination with blocks.
Probably the best way to think about the problem is to rather focus on retain cycles (what we’re trying to prevent by passing in a weak reference to self) and not so much on the weak or strong keywords.
There’s nothing inherent to blocks that creates a retain cycle. This is perfectly fine:

```
[anArray enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
  [self doWhatever:obj];
};
```
So the block retains self but self doesn’t retain the block and all will be good.

Problems only start arising if your self has a strong reference to the block and that block calls something on self, e.g.:

```
@property(strong) void(^aBlock)(id obj, NSUInteger idx, BOOL *stop);
…
[self setABlock:^(id obj, NSUInteger idx, BOOL *stop) {
  [self doWhatever:obj];
}];
```
In this example self now retains the block and the block retains self. Voilà: a memory leak.
The solution in this case is to pass in a weak reference to self to the block:

```
__weak TheClass *weakSelf = self;
[self setABlock:^(id obj, NSUInteger idx, BOOL *stop) {
  [weakSelf doWhatever:obj];
}];
```
It’s also often not necessary to create a strong reference to the weakSelf inside the block unless you absolutely must make sure that something is called on self. Most of the time it’s not though.
Final parting note: Don’t create typedefs for void blocks. Apple was so gracious as to provide us with a type for that: `dispatch_block_t`

## Coding Guidelines
* Use categories for colors and fonts: `UIColor+ZALAdditions` and UIFont+Zalando. They provide a lovely semantic layer on top and make replacing colors/fonts a breeze as well
* TODOs and FIXMEs are treated as compiler warnings. If you add one please actually go in and do or fix the issue or annotate the line in such a way that someone else can take care of the issue. The goal is 0 warnings.
* Feel free to refactor old code. The best way to do this is to create a test that verifies current behavior. Sometimes there are strange implementation details that work around other bugs that are outside of the direct scope (they should be commented of course).
* Dot-notation should be used for accessing and changing properties, bracket notation in all other cases.
* Mark your designated initializer with `__attribute__((objc_designated_initializer))`. Xcode 6 will add `NS_DESIGNATED_INITIALIZER` but until that time…
* Use the `NS_ENUM` and `NS_OPTIONS` macros to create enums and bitmasks respectively. They provide hints to the compiler for type-checking and switch statement completeness and in the case of `NS_OPTIONS` tell the compiler how values can be combined with bitmask |.
* Only ever use `YES` and `NO` for boolean values (no `true` or `false` or what else you might come across) and don't directly compare values to booleans in your if-blocks. A boolean @property should also use a custom getter `@property(getter=isEditable)editable;` to make it KVO compliant.
* make use of the `__unused` attribute to help the compiler optimize method calls. Mostly makes sense in delegate method calls that provide more context than you might need.

```
TODO
- Classes only instantiable from NIB files (views with initWithFrame: failing to instantiate subviews)
- Autolayout
- localization
- Utils and Categories already available and what to use them for
```

## Network Calls
All network calls are encapsulated inside `ZHTTPSessionManager` which in turn inherits from `AFHTTPSessionManager`.
The structure for all network calls should follow this pattern:
```
-(NSURLSessionDataTask *)loadSomeResourceWithParam:(NSString *)aParam number:(NSNumber *)aNumber completion:(BOOL success, id JSON, NSError *error);
```
Thusly:

* Pass in any parameters the task needs to construct a URI
* Use a completion block that returns success `YES`|`NO`, the raw JSON and an optional NSError
* The `ZHTTPSessionManager` uses `AFJSONResponseSerializer` as response serializer so the JSON returned by the call is already an NSArray or NSDictionary. We use [Mantle](https://github.com/MantleFramework/Mantle) to turn those raw data types into proper objects. It’s the job of the caller to create that object. Do use Mantle, do not take shortcuts and use the NSDictionary directly. And learn how Mantle works to properly deserialize nested object structures
* The `ZHTTPSessionManager` will initialize the network call immediately before returning the `NSURLSessionDataTask`. You can ignore the return type for most calls unless you have some special case where it makes sense to cancel the call.
* The `ZHTTPSessionManager` will construct the proper URI for its call. Do not pass in any prepared strings or parts of URIs.

## Mantle for JSON
As was alread alluded to in the **Network Calls** section, we use [Mantle](https://github.com/MantleFramework/Mantle) to turn JSON into proper objects. Any class that is a model should use a Z-prefix as class name and inherit from `MTLModel` as well as conform to the `MTLJSONSerializing` protocol:

```
typedef NS_ENUM(NSUInteger, ZOrderType) {
  ZOrderTypeWeb,
  ZOrderTypeApp
};

@interface ZOrder : MTLModel <MTLJSONSerializing>

@property(nonatomic, readonly, strong) NSString *id;
@property(nonatomic, readonly, strong) NSArray *history;
@property(nonatomic, readonly) ZOrderType orderType;

@end
```

Mantle models can be nested infinitely deep and to make this magic work only rely on some naming conventions inside the class implementation:

```
@implementation ZOrder

+ (NSDictionary *)JSONKeyPathsByPropertyKey {
    return @{
      @"id" : @"id",
      @"history: : @"history",
      @"orderType" : @"orderType"
    };
}

+ (NSValueTransformer *)historyJSONTransformer {
    return [NSValueTransformer mtl_JSONArrayTransformerWithModelClass:ZOrderHistory.class];
}

+ (NSValueTransformer *)orderTypeJSONTransformer {
    NSDictionary *orderTypes = @{
            @"web" : @(ZOrderTypeWeb),
            @"app" : @(ZOrderTypeApp)
    };

    return [MTLValueTransformer reversibleTransformerWithForwardBlock:^(NSString *string) {
        return orderTypes[string];
    }                                                    reverseBlock:^(NSNumber *state) {
        return [[orderTypes allKeysForObject:state] lastObject];
    }];
}

@end
```

Note:

* The JSONKeyPathsByPropertyKey method is required by the protocol but the implementation itself is optional. It may return `nil` unless you want to use it to rename some properties returned from the server to make them more compliant with Objective-C naming conventions (such as `imageUrl` vs `imageURL`) or the name would not compile such as variables prefixed with `new`
* The transformation relies on naming conventions, so the array history in the above example is automatically transformed because of the method name `historyJSONTransformer`. Because of this magic though, the methods are not recognized as being used. Use `#pragma clang diagnostic push / pop` to ignore the warning
* Transform all enumerated types into proper enums, again with a  custom transformer as shown in the above example.
* It’s good practice to mark your properties as `readonly` in the public interface (and no need to mark them `readwrite` in the class extension). Immutable data structures are your friend.
