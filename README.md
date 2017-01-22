# Questions and answers 
Form the [References](#references) listed at bottom of file.

  __Q1:__
  On a UITableViewCell constructor:
  
  __Objective-C__
  
  ```objective-c
  - (id)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier
  ```
  
  __Swift__
  
  ```swift
  init(style: UITableViewCellStyle, reuseIdentifier: String?)
  ```
  
  What is the ```reuseIdentifier``` used for?
  
  __A1:__
  The ```reuseIdentifier``` is used to indicate that a cell can be re-used in a ```UITableView```. For example when the cell looks the same, but has different content. The ```UITableView``` will maintain an internal cache of ```UITableViewCell```’s with the ```reuseIdentifier``` and allow them to be re-used when ```dequeueReusableCellWithIdentifier:``` is called. By re-using table cell’s the scroll performance of the tableview is better because new views do not need to be created.
  
__Q2:__
Explain the difference between atomic and nonatomic synthesized properties?

__A2:__
Atomic and non-atomic refers to whether the setters/getters for a property will atomically read and write values to the property. When the atomic keyword is used on a property, any access to it will be “synchronized”. Therefore a call to the getter will be guaranteed to return a valid value, however this does come with a small performance penalty. Hence in some situations nonatomic is used to provide faster access to a property, but there is a chance of a race condition causing the property to be nil under rare circumstances (when a value is being set from another thread and the old value was released from memory but the new value hasn’t yet been fully assigned to the location in memory for the property).

__Q3:__
Explain the difference between copy and retain?

__A3:__
Retaining an object means the retain count increases by one. This means the instance of the object will be kept in memory until it’s retain count drops to zero. The property will store a reference to this instance and will share the same instance with anyone else who retained it too. Copy means the object will be cloned with duplicate values. It is not shared with any one else.

__Q4:__
What is method swizzling in Objective C and why would you use it?

__A4:__
Method swizzling allows the implementation of an existing selector to be switched at runtime for a different implementation in a classes dispatch table. Swizzling allows you to write code that can be executed before and/or after the original method. For example perhaps to track the time method execution took, or to insert log statements

```objective-c
#import "UIViewController+Log.h"
@implementation UIViewController (Log)
    + (void)load {
        static dispatch_once_t once_token;
        dispatch_once(&once_token,  ^{
            SEL viewWillAppearSelector = @selector(viewDidAppear:);
            SEL viewWillAppearLoggerSelector = @selector(log_viewDidAppear:);
            Method originalMethod = class_getInstanceMethod(self, viewWillAppearSelector);
            Method extendedMethod = class_getInstanceMethod(self, viewWillAppearLoggerSelector);
            method_exchangeImplementations(originalMethod, extendedMethod);
        });
    }
    - (void) log_viewDidAppear:(BOOL)animated {
        [self log_viewDidAppear:animated];
        NSLog(@"viewDidAppear executed for %@", [self class]);
    }
@end

```

__Q5:__
What’s the difference between not-running, inactive, active, background and suspended execution states?

__OR__

What are the different app states?

__A5:__

* __Not running:__ The app has not been launched or was running but was terminated by the system.

* __Inactive:__ The app is running in the foreground but is currently not receiving events. (It may be executing other code though.) An app usually stays in this state only briefly as it transitions to a different state.

* __Active:__ The app is running in the foreground and is receiving events. This is the normal mode for foreground apps.

* __Background:__ The app is in the background and executing code. Most apps enter this state briefly on their way to being suspended. However, an app that requests extra execution time may remain in this state for a period of time. In addition, an app being launched directly into the background enters this state instead of the inactive state.

* __Suspended:__ The app is in the background but is not executing code. The system moves apps to this state automatically and does not notify them before doing so. While suspended, an app remains in memory but does not execute any code. When a low-memory condition occurs, the system may purge suspended apps without notice to make more space for the foreground app.

__Q6:__
What is a category and when is it used?

__A6:__
A category is a way of adding additional methods to a class without extending it. It is often used to add a collection of related methods. A common use case is to add additional methods to built in classes in the Cocoa frameworks. For example adding async download methods to the ```UIImage``` class.

__Q7:__
Can you spot the bug in the following code and suggest how to fix it?

```objective-c
@interface MyCustomController : UIViewController  

@property (strong, nonatomic) UILabel *alert;  

@end  

@implementation MyCustomController  

- (void)viewDidLoad {
     CGRect frame = CGRectMake(100, 100, 100, 50);
     self.alert = [[UILabel alloc] initWithFrame:frame];
     self.alert.text = @"Please wait...";
     [self.view addSubview:self.alert];
      dispatch_async(
        dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0),
        ^{
           sleep(10);
           self.alert.text = @"Waiting over";
        }
    ); 
}  

@end  
```

__A7:__
All UI updates must be done on the ___main thread___. In the code above the update to the alert text may or may not happen on the main thread, since the global dispatch queue makes no guarantees . Therefore the code should be modified to always run the UI update on the main thread

```objective-c
dispatch_async(		
    dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0),
    ^{
      sleep(10);
      dispatch_async(dispatch_get_main_queue(), ^{
         self.alert.text = @"Waiting over";
      });
}); 
```

__Q8:__
What is the difference between ```viewDidLoad``` and ```viewDidAppear```? Which should you use to load data from a remote server to display in the view?

__A8:__
```viewDidLoad``` is called when the view is loaded, whether from a Xib file, storyboard or programmatically created in ```loadView```. ```viewDidAppear``` is called every time the view is presented on the device. Which to use depends on the use case for your data. If the data is fairly static and not likely to change then it can be loaded in ```viewDidLoad``` and cached. However if the data changes regularly then using ```viewDidAppear``` to load it is better. In both situations, the data should be loaded asynchronously on a background thread to avoid blocking the UI.

__Q9:__
What considerations do you need when writing a ```UITableViewController``` which shows images downloaded from a remote server?

__A9:__
This is a very common task in iOS and a good answer here can cover a whole host of knowledge. The important piece of information in the question is that the images are hosted remotely and they may take time to download, therefore when it asks for “considerations”, you should be talking about:

* Only download the image when the cell is scrolled into view, i.e. when ```cellForRowAtIndexPath``` is called.
* Downloading the image asynchronously on a background thread so as not to block the UI so the user can keep scrolling.
* When the image has downloaded for a cell we need to check if that cell is still in the view or whether it has been re-used by another piece of data. If it’s been re-used then we should discard the image, otherwise we need to switch back to the main thread to change the image on the cell.

Other good answers will go on to talk about offline caching of the images, using placeholder images while the images are being downloaded.

__Q10:__
What is a protocol, how do you define your own and when is it used?

__A10:__
A protocol is similar to an interface from Java. It defines a list of required and optional methods that a class must/can implement if it adopts the protocol. Any class can implement a protocol and other classes can then send messages to that class based on the protocol methods without it knowing the type of the class.

```objective-c
@protocol MyCustomDataSource
- (NSUInteger)numberOfRecords;
- (NSDictionary *)recordAtIndex:(NSUInteger)index;
@optional
- (NSString *)titleForRecordAtIndex:(NSUInteger)index;
@end
```

A common use case is providing a DataSource for ```UITableView``` or ```UICollectionView```.

# References

In no perticular order

1. [Codemoderator](https://www.codementor.io/ios/tutorial/ios-interview-tips-questions-answers-objective-c "Codemoderator")

2. [Toptal](https://www.toptal.com/ios/interview-questions "Toptal")

3. [Ravi Shankar](http://rshankar.com/ "Ravi Shankar")
