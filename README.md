# Questions and answers 
Form the [References](#references) listed at bottom of file.

  __Q1:__
  On a UITableViewCell constructor:
  
  __Objective-C__
  
  ```
  - (id)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier
  ```
  
  __Swift__
  
  ```
  init(style: UITableViewCellStyle, reuseIdentifier: String?)
  ```
  
  What is the ```reuseIdentifier``` used for?
  
  __A:__
  The ```reuseIdentifier``` is used to indicate that a cell can be re-used in a ```UITableView```. For example when the cell looks the same, but has different content. The ```UITableView``` will maintain an internal cache of ```UITableViewCell```’s with the ```reuseIdentifier``` and allow them to be re-used when ```dequeueReusableCellWithIdentifier:``` is called. By re-using table cell’s the scroll performance of the tableview is better because new views do not need to be created.
  
__Q2:__
Explain the difference between atomic and nonatomic synthesized properties?

__A:__
Atomic and non-atomic refers to whether the setters/getters for a property will atomically read and write values to the property. When the atomic keyword is used on a property, any access to it will be “synchronized”. Therefore a call to the getter will be guaranteed to return a valid value, however this does come with a small performance penalty. Hence in some situations nonatomic is used to provide faster access to a property, but there is a chance of a race condition causing the property to be nil under rare circumstances (when a value is being set from another thread and the old value was released from memory but the new value hasn’t yet been fully assigned to the location in memory for the property).

__Q3:__
Explain the difference between copy and retain?

__A:__
Retaining an object means the retain count increases by one. This means the instance of the object will be kept in memory until it’s retain count drops to zero. The property will store a reference to this instance and will share the same instance with anyone else who retained it too. Copy means the object will be cloned with duplicate values. It is not shared with any one else.






# References

In no perticular order

1. [Codemoderator](https://www.codementor.io/ios/tutorial/ios-interview-tips-questions-answers-objective-c "Codemoderator")

2. [Toptal](https://www.toptal.com/ios/interview-questions "Toptal")

3. [Ravi Shankar](http://rshankar.com/ "Ravi Shankar")
