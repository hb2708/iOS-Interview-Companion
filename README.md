# Questions and answers 
Form the [References](#references) listed at bottom of file.

  __Q1:__ 
  On a UITableViewCell constructor:
  
  __Objective-C__
  
  ```objective-c
  - (id)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier
  ```
  
  __Swift__
  
  ```Swift
  init(style: UITableViewCellStyle, reuseIdentifier: String?)
  ```
  What is the ```reuseIdentifier``` used for?
  
  __A:__
  The ```reuseIdentifier``` is used to indicate that a cell can be re-used in a ```UITableView```. For example when the cell looks the same, but has different content. The ```UITableView``` will maintain an internal cache of ```UITableViewCell```’s with the ```reuseIdentifier``` and allow them to be re-used when ```dequeueReusableCellWithIdentifier:``` is called. By re-using table cell’s the scroll performance of the tableview is better because new views do not need to be created.
# References

In no perticular order

1. [Codemoderator](https://www.codementor.io/ios/tutorial/ios-interview-tips-questions-answers-objective-c "Codemoderator")

2. [Toptal](https://www.toptal.com/ios/interview-questions "Toptal")

3. [Ravi Shankar](http://rshankar.com/ "Ravi Shankar")
