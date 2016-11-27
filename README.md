# Interoperability

Swift is designed to work seamlessly with Objetive-C. You can use Objective-C in Swift and Swift APIs in Objetive-C.

Interoperability is the ability to interface between Swift and Objective-C in either direction, letting you access and use pieces of code written in one language in a file of the other language. 

### Importing 

#### Importing Objective-C into Swift

If you want to access an Objective-C object in Swift, you must use a bridging header file. You can create this header file yourself, but the easiest way to generate the file is to let Xcode do it. The first time you try to mix languages in a project, Xcode will ask you if you want to create a briging header. 

![Create Bridging Header](https://developer.apple.com/library/prerelease/content/documentation/Swift/Conceptual/BuildingCocoaApps/Art/bridgingheader_2x.png)

If you select `Create Bridging Header`, Xcode will automatically generate the header file with the following naming convention: `{Your Project Name}-Bridging-Header.h`

Every time you want to use one of your Objective-C classes in Swift, just import it into this Bridging Header file.  

#### Importing Swift into Objective-C

If you want to access a Swift file in Objective-C, you must import your project's Swift target header file. This is a file that Xcode will generate and manage for us so we will never see it or modify it. The file you need to import is `{Your Project Name}-Swift.h` where `{Your Project Name}` matches your project's name exacly. 

All you have to do is import this file into your Objective-C .m file `#import "{Your Project Name}-Swift.h"` and you will have access to all your Swift files. To avoid cyclical references, don’t import Swift code into an Objective-C header (.h) file, always import into an implementation (.m) file.


### Nullability and Optionals

In Objective-C, we work with pointers to objects that could be NULL (nil) at any time. In Swift, everything is guaranteed to have a value (no be NULL) and we use optional types to indicate NULL.

In Objective-C, if we want to explicity specify if a property or paramter can be NULL or not, we have to use the following annotations:

Function parameter: `_Nonnull`, `_Nullable`
Property: `nonnull`, `nullable`

It is common to wrap a class' interface inside the `NS_ASSUME_NONNULL` macros, so that we only have to declare what can be `nullable`, or 'optional'.

Example:

```objective-c
NS_ASSUME_NONNULL_BEGIN

@interface Person : NSObject

@property (nonatomic, copy) NSString *name;
@property (nonatomic, copy, nullable) NSString *address;
@property (nonatomic, copy, nullable) NSArray<Cat *> *pets;

- (instancetype)initWithName:(NSString *)name address:(NSString * _Nullable)address NS_DESIGNATED_INITIALIZER;

@end

NS_ASSUME_NONNULL_END
```

Swift would interpret it like this:

```Swift
open class Person : NSObject {
    
    open var name: String
    open var address: String?
    open var pets: [Cat]?

    public init(name: String, address: String?)
}
```

### Initializers 

Designated initializers are the primary initializers for a class. A designated initializer fully initializes all properties introduced by that class and calls an appropriate superclass initializer to continue the initialization process up the superclass chain.

![Initialzers](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Art/initializerDelegation01_2x.png)

In swift, all initialzers are designated by default, and we sepcify convenience initialzers using the `convenience` keyword:

```swift
convenience override init() {
    self.init(ownerName: nil)
}
```

In Objecive-C we have to specify designated initalizers using the `NS_DESIGNATED_INITIALIZER` macro: 

```objective-c
- (instancetype)initWithName:(NSString *)name NS_DESIGNATED_INITIALIZER;
```

### Importing Frameworks

No matter what language your framework is built in, you import it into Swift using the `import` statment. And in Objective-C you can use either `#import` or `@import`