# Objective-C Coding Guidelines
Here, I'll try to outline the coding and style guidelines, along with best practices to adopt when working on an Objective-C based project. Needless to say, I'm just a human and I could miss out on a few things (I'll try not to, though). If you're smart enough to find such cases, don't shy away from showing off by opening an issue or raising a PR.

These guidlines and styles are curated from some amazing sources enlisted below:
* [Cocoa coding guidelines by Apple](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [Ray Wenderlich Objective-C style guide](https://github.com/raywenderlich/objective-c-style-guide)
* [NYTimes Objective-C style guide](https://github.com/NYTimes/objective-c-style-guide)


## Naming
#### Prefixes
The most important benefit of using prefixes is to protect against collisions between symbols defined by third-party developers and those defined by Apple (as well as between symbols in Apple’s own frameworks).
The standard prefix consists of two or three uppercase letters. For example, NS (Foundation), UI (UI Kit), etc.

Use prefixes when naming classes, protocols, functions, constants, and typedef structures. **DO NOT** use prefixes when naming methods; methods exist in a name space created by the class that defines them. Also, don’t use prefixes for naming the fields of a structure.

#### Classes & protocols
Use a prefix for naming your class or protocol. Remember that a class and protocol name should start with a capital letter (post prefixing). Name of a class should contain a noun that clearly indicates what the class (or objects of the class) represent or do. Do not shy away from being descriptive about names. For UI controls related subclasses, it is considered a best practice to include the funtional name (w/o prefix) of the super class in full.

**Good:**
```objc
@interface RKFunctionalViewController : UIViewController
@interface RKFunctionalTabieViewCell : UITableViewCell
```
**Bad:**
````objc
@interface RKFunctionalVC : UIViewController
@interface RKfunctionalTabieViewCell : UITableViewCell
````

#### Delegate Methods
Start the name by identifying the class of the object that’s sending the message. The class name omits the prefix and the first letter is in lowercase.
````objc
- (BOOL)tableView:(NSTableView *)tableView shouldSelectRow:(int)row;
- (BOOL)application:(NSApplication *)sender openFile:(NSString *)filename;
````

A colon is affixed to the class name (the argument is a reference to the delegating object) unless the method has only one argument, the sender.
````objc
- (BOOL)applicationOpenUntitledFile:(NSApplication *)sender;
````

Use “did” or “will” for methods that are invoked to notify the delegate that something has happened or is about to happen.
````objc
- (void)browserDidScroll:(NSBrowser *)sender;
- (NSUndoManager *)windowWillReturnUndoManager:(NSWindow *)window;
````

Instead of "did" or "will", you should prefer "should" for methoda that are invoked to ask the delegate to do something on behalf of another object
````objc
- (BOOL)windowShouldClose:(id)sender;
````
#### Methods
Start the name with a lowercase letter and capitalize the first letter of embedded words. There are two specific exceptions to these guidelines. You may begin a method name with a well-known acronym in uppercase (such as TIFF or PDF)), and you may use prefixes to group and identify [private methods](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-1003829).

In method signatures, there SHOULD be a space after the scope (- or + symbol). There SHOULD be a space between the method segments.

For methods that represent actions an object takes, start the name with a verb:
````objc
- (void)invokeWithTarget:(id)target;
````
Make the word before the argument describe the argument.

**Good**
````objc
- (id)viewWithTag:(NSInteger)aTag;
````
**Bad**
````objc
- (id)taggedView:(NSInteger)aTag;
````

Don’t use “and” to link keywords that are attributes of the receiver.

**Good**
````objc
- (int)runModalForDirectory:(NSString *)path file:(NSString *) name types:(NSArray *)fileTypes;
````
**Bad**
````objc
- (int)runModalForDirectory:(NSString *)path andFile:(NSString *)name andTypes:(NSArray *)fileTypes;
````

If the method describes two separate actions, use “and” to link them.

**Example**
````objc
- (BOOL)openFile:(NSString *)fullPath withApplication:(NSString *)appName andDeactivate:(BOOL)flag;
````

#### Method Arguments
There are a few general rules concerning the names of method arguments:
- As with methods, arguments start with a lowercase letter and the first letter of successive words are capitalized (for example, removeObject:(id)anObject).
- Don’t use “pointer” or “ptr” in the name. Let the argument’s type rather than its name declare whether it’s a pointer.
- Avoid one- and two-letter names for arguments.
- Avoid abbreviations that save only a few letters.
- If you are using pointer object argument, the type name and asterisk (*) denoting the pointer should have a single space between them

#### Categories
Categories are RECOMMENDED to concisely segment functionality and should be named to describe that functionality.

**Good**
````objc
@interface UIViewController (RKCategoryPurpose)
@interface NSString (NSStringEncodingDetection)
````

**Bad**
````objc
@interface RKUserInformation (private)
@interface NSString (RKAdditions)
````
Methods and properties added in categories MUST be named with an app- or organization-specific prefix. This avoids unintentionally overriding an existing method, and it reduces the chance of two categories from different libraries adding a method of the same name. (The Objective-C runtime doesn’t specify which method will be called in the latter case, which can lead to unintended effects.)

**Good**
````objc
@interface NSArray (RKAccessors)
- (id)rk_objectOrNilAtIndex:(NSUInteger)index;
@end
````

````objc
@interface NSArray (RKAccessors)
- (id)objectOrNilAtIndex:(NSUInteger)index;
@end
````

## Spacing
- Method braces and other braces (if/else/switch/while etc.) **MUST** open on the same line as the statement. Braces MUST close on a new line.
````objc
if (user.isHappy) {
    // Do something
}
else {
    // Do something else
}
````

## Error handling
When methods return an error parameter by reference, switch on the returned value, not the error variable.

**Good**
````objc
NSError *error;
if (![self trySomethingWithError:&error]) {
  // Handle Error
}
````
**Bad**
````objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
  // Handle Error
}
````
Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Dot-Notation Syntax
Dot syntax is purely a convenient wrapper around accessor method calls. When you use dot syntax, the property is still accessed or changed using getter and setter methods.  Read more [here](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html)

Dot-notation should **always** be used for accessing and mutating properties, as it makes code more concise. Bracket notation is preferred in all other instances.

**Good**
```objc
NSInteger arrayCount = [self.array count];
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Bad**
```objc
NSInteger arrayCount = self.array.count;
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

[Private properties](#private-properties) should be used in place of instance variables whenever possible. Although using instance variables is a valid way of doing things, by agreeing to prefer properties our code will be more consistent. Also, only expose properties and methods in the header file that can/should be modified from an external inteface or should be inherited when subclassing. This will reduce `#import` overhead in the header file and thus, will reduce the overall app size as well.

Direct access to instance variables that 'back' properties should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**Good**

```objc
@interface RWTTutorial : NSObject

@property (strong, nonatomic) NSString *tutorialName;

@end
```

**Bad**

```objc
@interface RWTTutorial : NSObject {
  NSString *tutorialName;
}
```

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. 

For Example:

````objc
@interface RWTDetailViewController ()
@property (strong, nonatomic) GADBannerView *googleAdView;
@property (strong, nonatomic) ADBannerView *iAdView;
@property (strong, nonatomic) UIWebView *adXWebView;
@end
````

## Code Organization
Use `#pragma mark -` and `#pragma mark` to categorize methods in functional groupings and protocol/delegate implementations.
**Note**: `#pragma mark - XYZ` will show a separator above "XYZ" mark in the document items space (ctrl+6) of your file. Whereas, simply using `#pragma mark XYZ` will NOT show the separator in the document items space.

**Example**
````objc
#pragma mark - Lifecycle

- (instancetype)init {}
- (void)dealloc {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}

#pragma mark - Custom Accessors

- (void)setCustomProperty:(id)value {}
- (id)customProperty {}

#pragma mark - IBActions

- (IBAction)submitData:(id)sender {}

#pragma mark - Public

- (void)publicMethod {}

#pragma mark - Private

- (void)privateMethod {}

#pragma mark - Protocol conformance
#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
````

## Underscores
When using properties, instance variables should always be accessed and mutated using self.. This means that all properties will be visually distinct, as they will all be prefaced with self..

An exception to this: inside initializers, the backing instance variable (i.e. _variableName) should be used directly to avoid any potential side effects of the getters/setters.

Local variables should NOT contain underscores.

# Constants
Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

When defining a string constant to be used as a notification constant or any other purpose in a header file, prefer to use `extern` keyword and assign the actual string value inside the implementation file. This way the data stays more secure and unexposed.

Notifications are identified by global NSString objects whose names are composed in this way:

`[Name of associated class] + [Did | Will] + [UniquePartOfName] + Notification`

**Good**

```objc
static NSString * const RKAboutViewControllerDefaultTitle = @"Coding Guidelines";

static CGFloat const RKImageThumbnailHeight = 50.0;

extern NSString *NSPrintCopies; // Inside implementation, define NSString *NSPrintCopies = @"PrintCopies"

NSApplicationDidBecomeActiveNotification
```

**Bad**

```objc
#define DefaultTitles @"Coding Guidelines"

#define thumbnailHeight 2
```

## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`

**For Example:**

```objc
typedef NS_ENUM(NSUInteger, RKSelectionType) {
  RKSelectionTypeRadio,
  RKSelectionTypeCheckbox,
};
```

```objc
typedef NS_ENUM(NSUInteger, RKSelectionType) {
  RKSelectionTypeRadio = 1,
  RKSelectionTypeCheckbox = 100,
};
```

Older k-style constant definitions should be **avoided** unless writing CoreFoundation C code (unlikely).

**Bad**

```objc
enum GlobalConstants {
  kMaxPinSize = 5,
  kMaxPinCount = 500,
};
```

## Booleans

Objective-C uses `YES` and `NO`.  Therefore `true` and `false` should only be used for CoreFoundation, C or C++ code.  Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**Good**

```objc
if (someObject) {}
if (![anotherObject boolValue]) {}
```

**Bad**

```objc
if (someObject == nil) {}
if ([anotherObject boolValue] == NO) {}
if (isAwesome == YES) {} // Never do this.
if (isAwesome == true) {} // Never do this.
```

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Class Constructor Methods

Where class constructor methods are used, these should always return type of 'instancetype' and never 'id'. This ensures the compiler correctly infers the result type. 

```objc
@interface SomeClass
+ (instancetype)someClassWithFeatureType:(RKFeatureType)type;
@end
```
More information on instancetype can be found on [NSHipster.com](http://nshipster.com/instancetype/).

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**Good**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
CGRect frame = CGRectMake(0.0, 0.0, width, height);
```

**Bad**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
```

## Golden Path

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

**Good**

```objc
- (void)someMethod {
  if (![someOther boolValue]) {
	return;
  }

  //Do something important
}
```

**Bad**

```objc
- (void)someMethod {
  if ([someOther boolValue]) {
    //Do something important
  }
}
```

## Bitmasks

When working with bitmasks, the `NS_OPTIONS` macro MUST be used.

**Example:**

```objc
typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
    NYTAdCategoryAutos      = 1 << 0,
    NYTAdCategoryJobs       = 1 << 1,
    NYTAdCategoryRealState  = 1 << 2,
    NYTAdCategoryTechnology = 1 << 3
};
```

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).
