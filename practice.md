# Practice

*Pieces of a good answer: correctness, meaningful explanation, attention to details*

> **A.** Imagine that you are doing a code review for the following piece. What notes would you make? 
>
> _note: ITTest1Worker class interface is described below the sample. Assume that ITTest1Worker is a part of some third-party library, so **is not a target to change.**_

```objc
@interface ITTest1ViewController ()
@property (nonatomic, strong) ITTest1Worker *worker;
@property (nonatomic, strong) NSArray *testItems;
@end

@implementation ITTest1ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.worker = [ITTest1Worker new];
    self.worker.completion = ^(BOOL success, NSArray *items, NSError *error) {
        if (!error) {
            _testItems = items;
        }
        else {
            NSLog(@"Error occurred: %@", error.localizedDescription);
        }
    };
    [self.worker start];
}

@end
```

```objc
@interface ITTest1Worker : NSObject
@property (nonatomic, copy) void(^completion)(BOOL success, NSArray *items, NSError *);

/** Performs some job in the background and calls 'completion' block afterwards. */
- (void)start;
@end
```
<br/>
> **B.** This code is supposed to show a message, then wait for 10 seconds and show another message. Does it work as intended? What would you propose to change here?

```objc
@interface ITTest2ViewController ()
@property (strong, nonatomic) UILabel *alert;
@end

@implementation ITTest2ViewController

- (void)viewDidLoad {
    CGRect frame = CGRectMake(100, 100, 100, 50);
    self.alert = [[UILabel alloc] initWithFrame:frame];
    self.alert.text = @"Please wait...";
    [self.view addSubview:self.alert];
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        sleep(10);
        self.alert.text = @"Waiting over";
    });
}

@end
```
<br/>
> **C.** The following example will display a sequence of numbers in console output. What can you tell about this sequence? Will the order of displayed numbers always be the same?

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    NSLog(@"1");
    
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSLog(@"2");
        
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"4");
        });
        NSLog(@"5");
    });
    NSLog(@"3");
}
```
<br/>
> **D.** What would you change in the following example to make sure both long jobs are executed *in parallel*? The method should still log message *3* after first two.
>
> _note: ITTest4Worker class interface described below the sample. Assume that ITTest4Worker is a part of some third-party library, so **is not a target to change.**_

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    NSLog(@"View did load.");
    
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        ITTest4Worker *worker = [ITTest4Worker new];
        
        // 1.
        [worker doLongJob1];
        NSLog(@"Long job 1 is finished.");
        
        // 2.
        [worker doLongJob2];
        NSLog(@"Long job 2 is finished.");
        
        // 3.
        NSLog(@"Both long jobs are finished!");
    });
}
```
```objc
@interface ITTest4Worker : NSObject
- (void)doLongJob1;
- (void)doLongJob2;
@end
```
<br/>
> **E.** Please describe, what will be logged to console output in lines *2* and *3*. 

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    NSArray *initialNumbers = @[@1, @11, @32, @11, @55, @32];
    NSLog(@"Initial numbers count: %d", (int)initialNumbers.count); // 1.
    
    NSArray *numbers = [[NSSet setWithArray:initialNumbers] allObjects];
    NSLog(@"Numbers count: %d", (int)numbers.count); // 2.
    
    
    NSMutableArray *mutableNumbers = [numbers mutableCopy];
    for (NSNumber *n in mutableNumbers) {
        if (n.integerValue % 10 == 1) {
            [mutableNumbers removeObject:n];
        }
    }
    
    NSArray *finalNumbers = [mutableNumbers copy];
    NSLog(@"Final numbers count: %d", (int)finalNumbers.count); // 3.
}
```
