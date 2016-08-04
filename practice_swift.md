# Practice

*Pieces of a good answer: correctness, meaningful explanation, attention to details*

> **A.** Imagine that you are doing a code review for the following piece. What notes would you make? 
>
> _note: ITTest1Worker class interface is described below the sample. Assume that ITTest1Worker is a part of some third-party library, so **is not a target to change.**_

```swift
class ITTest1ViewController: UIViewController {
    var worker: ITTest1Worker!
    var items: [Item1] = []
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        worker = ITTest1Worker()
        worker.completion = { (success, items, error) in
            if let error = error {
                print("Error happened: \(error)")
            }
            else {
                self.items = items
            }
        }
        worker.start()
    }
}
```

<br/>
> **B.** This code is supposed to show a message, then wait for 10 seconds and show another message. Does it work as intended? What would you propose to change here?

```swift
class ITTest2ViewCongroller: UIViewController {
    var alert: UILabel!
    
    override func viewDidLoad() {
        let frame = CGRect(x: 100, y: 100, width: 50, height: 50)
        alert = UILabel(frame: frame)
        alert.text = "Please wait..."
        view.addSubview(alert)
        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)) { 
            sleep(10)
            self.alert.text = "Waiting over"
        }
    }
}
```
<br/>
> **C.** The following example will display a sequence of numbers in console output. What can you tell about this sequence? Will the order of displayed numbers always be the same?

```swift
class ITTest3ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        print("1")
        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)) { 
            print("2")
            dispatch_async(dispatch_get_main_queue()) {
                print("4")
            }
            print("5")
        }
        print("3")
    }
}
```

<br/>
> **D.** What would you change in the following example to make sure both long jobs are executed *in parallel*? The method should still log message *3* after first two.
>
> _note: ITTest4Worker class interface described below the sample. Assume that ITTest4Worker is a part of some third-party library, so **is not a target to change.**_

```swift
class ITTest4ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)) {
            let worker = ITTest4Worker()
            
            worker.doLongJob1()
            print("Long job 1 is finished.")
            
            worker.doLongJob2()
            print("Long job 2 is finished.")
            
            print("Both long jobs are finished.")
        }
    }
}
```
