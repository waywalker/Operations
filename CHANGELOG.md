# 2.9.1

1. [[OPR-282](https://github.com/danthorpe/Operations/pull/282)]: Fixes a bug accessing `PHPhotoLibrary` through `Capability.Photos`.
2. [[OPR-285](https://github.com/danthorpe/Operations/pull/285)]: Removes CloudKit files from the watchOS target.

This is a patch release to get these fixes released.

# 2.9.0

1. [[OPR-241](https://github.com/danthorpe/Operations/pull/241)]: Makes change for Xcode 7.3 and Swift 2.2.
2. [[OPR-251](https://github.com/danthorpe/Operations/pull/252)]: Refactors how Capabilities work with their generic registrars.

Got there in the end! Thanks everyone for helping out during the change to Swift 2.2 - by the time Swift 3.0 comes around, I’ll hopefully have a fully automated CI system in place for switching up toolchains/build.

# 2.8.2

1. [[OPR-250](https://github.com/danthorpe/Operations/pull/250)]: Thanks to [@felix-dumit](https://github.com/felix-dumit) for making the cancellation of `GroupOperation` more sensible and consistent. Essentially now the group will cancel after all of its children have cancelled.

This should be the last bug release before v2.9.0 and Swift 2.2 is released.

Also, before v2.10 is released, if you happen to use Operations framework in your app or team, and would agree to having a logo displayed in the README - please [get in touch](https://github.com/danthorpe/Operations/issues/new)!

# 2.8.1

1. [[OPR-245](https://github.com/danthorpe/Operations/pull/247)]: Thanks to [@difujia](https://github.com/difujia) for spotting and fixing a really clear retain cycle in `ComposedOperation`. Good tip - is to remember that an operation will retain its observers, meaning that if an operation owns another operation, *and* acts as its observer, then it will create a retain cycle. The easy fix is to use block based observers, with a capture list of `[unowned self]`.
2. [[OPR-246](https://github.com/danthorpe/Operations/pull/248)]: Another bug fix from [@difujia](https://github.com/difujia) for a race condition when adding operation which have mutual exclusive dependencies. My bad! Thanks Frank!

Just a quick note - these bug fixes are both being released now as 2.8.1 for Swift 2.1 & Xcode 7.2. The same fixes will be pulled into the `development` branch which is shortly going to become Swift 2.2, although it isn't yet. Bear with me - as that should happen over the weekend. 

# 2.8.0
🚀 This will be the last minor release for Swift 2.1.1. From here on development of new features will be in Swift 2.2 😀.

Yet again, this release features more contributors - thanks a lot to [@estromlund](https://github.com/estromlund), [@itsthejb](https://github.com/itsthejb), [@MrAlek](https://github.com/MrAlek) and [@felix-dumit](https://github.com/felix-dumit) for finding bugs and fixing them!

Also, I’m pretty happy to report that adoption and usage of this framework has been seeing somewhat of an uptick! According to the stats on CocoaPods, we’re seeing almost 2,500 downloads/week and over used by over 120 applications 🎉! The support from the Swift community on this has been pretty amazing so far - thanks everyone 😀🙌!

1. [[OPR-233](https://github.com/danthorpe/Operations/pull/223)]: Thanks to [@estromlund](https://github.com/estromlund) & [@itsthejb](https://github.com/itsthejb) for fixing a bug which would have caused retain cycles when using result injection.
2. [[OPR-225](https://github.com/danthorpe/Operations/pull/225)]: Adds a unit test to check that `Operation` calls `finished()`. This was a bit of a followup to the fixes in 2.7.1.
3. [[OPR-208,OPR-209](https://github.com/danthorpe/Operations/pull/209)]: Thanks to [@itsthejb](https://github.com/itsthejb) who remove the `HostReachabilityType` from the arguments of `ReachabilityCondition` which allows it to be more easily consumed. It’s now access via a property in unit tests.
4. [[OPR-210](https://github.com/danthorpe/Operations/pull/210)]: Thanks to [@itsthejb](https://github.com/itsthejb) (again!) for improving the logic for ReachabilityCondition.
5. [[OPR-226](https://github.com/danthorpe/Operations/pull/226)]: Some improvements to the unit tests to fix some failures on development.
6. [[OPR-224](https://github.com/danthorpe/Operations/pull/224)]: Use `.Warning` log severity when logging errors in `Operation`. Thanks again to [@itsthejb](https://github.com/itsthejb) for this one.
7. [[OPR-227](https://github.com/danthorpe/Operations/pull/227)]: Sets the log severity to `.Fatal` for the unit tests.
8. [[OPR-229](https://github.com/danthorpe/Operations/pull/229)]: Thanks to [@estromlund](https://github.com/estromlund) for fixing a bug from 2.7.0 where the automatic result injection was done using a `DidFinishObserver` instead of `WillFinishObserver` which was causing some race conditions.
9. [[OPR-231](https://github.com/danthorpe/Operations/pull/231)]: Removes `self` from the default operation name - which due to the `@autoclosure` nature of the log message could cause locking issues.
10. [[OPR-234](https://github.com/danthorpe/Operations/pull/234)]: Thanks to [@MrAlek](https://github.com/MrAlek) for fixing a bug (causing a race condition) when cancelling a `GroupOperation`.
11. [[OPR-236](https://github.com/danthorpe/Operations/pull/236)]: Thanks to [@felix-dumit](https://github.com/felix-dumit) for fixing a bug where an `AlertOperation` would finish before its handler is called.
12. [[OPR-239](https://github.com/danthorpe/Operations/pull/239)]: Adds `GroupOperationWillAddChildObserver` observer protocol. This is only used by `GroupOperation` and can be use to observer when child operations are about to be added to the group’s queue.
13. [[OPR-235](https://github.com/danthorpe/Operations/pull/235)]: New Observer: `OperationProfiler`.

    An `OperationProfiler` can be added as an observer to an `Operation` instance. It will report a profile result which contains the timings for the lifecycle events of the operation, from created through attached, started to cancelled or finished.
    
    By default, a logging reporter is added, which will print the profile information to the `LogManager`’s logger. This is done like this:
    
    ```swift
    let operation = MyBigNumberCrunchingOperation()
    operation.addObserver(OperationProfiler())
    queue.addOperation(operation)
    ```
    
    However, for customized reporting and analysis of profile results, create the profiler with an array of reporters, which are types conforming to the `OperationProfilerReporter` protocol.
    
    **In most cases doing any kind of profiling of applications in production is unnecessary and should be avoided.**
    
    However, in some circumstances, especially with applications which have very high active global users, it is necessary to gain a holistic view of an applications performance. Typically these measurements should be tied to networking operations and profiling in back end systems. The `OperationProfiler` has deliberately designed with a view of using custom reporters. The built in logging reporter should only really be used as debugging tool during development.
    
    In addition to profiling regular “basic” `Operation` instances. The profiler will also measure spawned operations, and keep track of them from the parent operation’s profiler. Operations can be spawned by calling `produceOperation()` or by using a `GroupOperation`. Regardless, the profiler’s results will reference both as “children” in the same way.
    
    WARNING: Use this feature carefully. *If you have not* written a custom reporter class, **there is no need** to add profilers to operations in production.

# 2.7.1

1. [[OPR-219](https://github.com/danthorpe/Operations/issues/220)]: Fixes an issue after refactoring Operation which would prevent subclasses from overriding `finished(errors: [ErrorType])`.

# 2.7.0
🚀 This release continues the refinement of the framework. Thanks again to everyone who has contributed!

1. [[OPR-152](https://github.com/danthorpe/Operations/issues/152), [OPR-193](https://github.com/danthorpe/Operations/pull/193), [OPR-195](https://github.com/danthorpe/Operations/pull/195), [OPR-201](https://github.com/danthorpe/Operations/pull/201)]: This is a breaking change which significantly improves Operation observers.
    1. Observers can be safely added to an Operation at any point in its lifecycle.
    2. Observers can implement a callback which is executed when there are attached to the operation.
    3. All the block based observers have labels on their arguments.

    Thanks to [@jshier](https://github.com/jshier) for reporting this one.
2. [[OPR-193](https://github.com/danthorpe/Operations/pull/196)]: Thanks to [@seancatkinson](https://github.com/seancatkinson) who made improvements to `UIOperation` making it possible to specify whether the controller should be wrapped in a `UINavigationController`.
3. [[OPR-199](https://github.com/danthorpe/Operations/pull/203)]: Refactored the initializers of `RepeatedOperation` to make it far easier for framework consumers to subclass it - thanks [@jshier](https://github.com/jshier) for reporting this one.
4. [[OPR-197](https://github.com/danthorpe/Operations/pull/198)]: Fixes a bug where errors from nested `GroupOperation`s were not propagating correctly - thanks to [@bastianschilbe](https://github.com/bastianschilbe) for reporting this one.
5. [[OPR-204](https://github.com/danthorpe/Operations/pull/204)]: Fixes a typo in the README - thanks to [@Augustyniak](https://github.com/Augustyniak).
6. [[OPR-214](https://github.com/danthorpe/Operations/pull/214)]: Moves code coverage reporting from Codecov.io to [Coveralls](https://coveralls.io/github/danthorpe/Operations).
7. [[OPR-164](https://github.com/danthorpe/Operations/pull/164)]: Adds initial support for Swift Package Manager - no idea if this actually works yet though.
8. [[OPR-212](https://github.com/danthorpe/Operations/pull/212)]: Removes the example projects from the repo. They are now in the [@danthorpe/Examples](https://github.com/danthorpe/Examples) repo. This was done as a safer/better fix for the issue which was resolved in v2.6.1. Essentially because Carthage now builds *all* Xcode projects that it can finds, it will attempt to build any example projects in the repo, and because Carthage does not have the concept of “local dependencies” these example projects are setup using CocoaPods. And I really don’t like to include the `Pods` folder of dependencies in repositories as it just take longer to checkout. So, this was causing Carthage to exit because it couldn’t build these exampled. So, I’ve moved them to a new repo.
9. [[OPR-216](https://github.com/danthorpe/Operations/pull/216)]: Adds SwiftLint to the project & CI, including fixes for all the issues which were warnings or errors.
10. [[OPR-192](https://github.com/danthorpe/Operations/pull/192)]: Updates the `.podspec` to have more granular dependencies. For users of `CloudKitOperation` this is a breaking change, and you will need to update your `Podfile`:

    ```ruby
    pod ‘Operations/+CloudKit’
    ```

    Thanks to [@itsthejb](https://github.com/itsthejb) for this one.


# 2.6.1

1. [[OPR-205, OPR-206](https://github.com/danthorpe/Operations/pull/206)]: Fixes a mistake where the Cloud Capability was not available on tvOS platform.

2. Temporary work around for an issue with Carthage versions 0.12 and later. In this version, Carthage now builds all Xcode projects it can find, which in this case is 4 projects because there are two examples. Those example projects use CocoaPods to setup their dependency on the Operations framework, using the "development pod" technique. I would prefer to not include their `Pods/` folder in the repo, however, without it, it becomes necessary to run `pod update` before building - which Carthage (reasonably) does not do. Therefore they fail to build and Carthage exits.

# 2.6.0

🚀 This release contains quite a few changes, with over 230 commits with input from 11 contributors! Thanks! 😀🎉

A note on quality: test coverage has increased from 64% in v2.5 to 76%. The code which remains untested is either untestable (`fatalError` etc) or is due for deletion or deprecation such as `AddressBookCondition` etc.

### New Operations

1. [[OPR-150](https://github.com/danthorpe/Operations/pull/150)]: `MapOperation`, `FilterOperation` and `ReduceOperation` *For advanced usage*. 

	These operations should be used in conjunction with `ResultOperationType` which was introduced in v2.5.0. Essentially, given an receiving operation, conforming to `ResultOperationType`, the result of mapping, filtering, or reducing the receiver’s `result` can be returned as the `result` of another operation, which also conforms to `ResultOperationType`. This means that it can be trivial to map the results of one operation inside another.

	It is suggested that this is considered for advanced users only as it’s pretty subtle behavior.

2. [[OPR-154](https://github.com/danthorpe/Operations/pull/154), [OPR-168](https://github.com/danthorpe/Operations/pull/168)]: `RepeatedOperation`

	The `RepeatedOperation` is a `GroupOperation` subclass which can be used in conjunction with a generator to schedule `NSOperation` instances. It is useful to remember that `NSOperation` is a “one time only” class, meaning that once an instance finishes, it cannot be re-executed. Therefore, it is necessary to construct repeatable operations using a closure or generator.
 
	This is useful directly for periodically running idempotent operations. It also forms the basis for operation types which can be retried.
 
	The operations may optionally be scheduled after a delay has passed, or a date in the future has been reached.
 
	At the lowest level, which offers the most flexibility, `RepeatedOperation` is initialized with a generator. The generator (something conforming to `GeneratorType`) element type is `(Delay?, T)`, where `T` is a `NSOperation` subclass, and `Delay` is an enum used in conjunction with `DelayOperation`.
 
	`RepeatedOperation` can also be initialized with a simple `() -> T?` closure and `WaitStrategy`. The strategy offers standardized delays such as `.Random` and `.ExpoentialBackoff`, and will automatically create the appropriate `Delay`. 

	`RepeatedOperation` can be stopped by returning `nil` in the generator, or after a maximum count of operations, or by calling `cancel()`.

	Additionally, a `RepeatableOperation` has been included, which composes an `Operation` type, and adds convenience methods to support whether or not another instance should be scheduled based on the previous instance.

2. [[OPR-154](https://github.com/danthorpe/Operations/pull/154), [OPR-161](https://github.com/danthorpe/Operations/pull/161), [OPR-168](https://github.com/danthorpe/Operations/pull/168)]: `RetryOperation`

	`RetryOperation` is a subclass of `RepeatedOperation`, except that instead of repeating irrespective of the finishing state of the previous instance, `RetryOperation` only repeats if the previous instance finished with errors.

	Additionally, `RetryOperation` is initialized with an “error recovery” block. This block receives various info including the errors from the previous instance, the aggregate errors so far, a `LoggerType` value, plus the *suggested* `(Delay, T?)` tuple. This tuple is the what the `RetryOperation` would execute again without any intervention. The error block allows the consumer to adjust this, either by returning `.None` to not retry at all, or by modifying the return value.

3. [[OPR-160](https://github.com/danthorpe/Operations/pull/160), [OPR-165](https://github.com/danthorpe/Operations/pull/165), [OPR-167](https://github.com/danthorpe/Operations/pull/167)]: `CloudKitOperation` 2.0

	Technically, this work is a refactor of `CloudKitOperation`, however, because it’s a major overhaul it is best viewed as completely new.

	`CloudKitOperation` is a subclass of `RetryOperation`, which composes the `CKOperation` subclass inside a `ReachableOperation`.
	
	`CloudKitOperation` can be used to schedule `CKOperation` subclasses. It supports configuration of the underlying `CKOperation` instance “through” the outer `CloudKitOperation`, where the configuration applied is stored and re-applied on new instances in the event of retrying. For example, below
	
	```swift
    // Modify CloudKit Records
    let operation = CloudKitOperation { CKModifyRecordsOperation() }
    
    // The user must be logged into iCloud 
    operation.addCondition(AuthorizedFor(Capability.Cloud()))
    
    // Configure the container & database
    operation.container = container
    operation.database = container.privateCloudDatabase
    
    // Set the records to save
    operation.recordsToSave = [ recordOne, recordTwo ]
    
    // Set the policy
    operation.savePolicy = .ChangedKeys
    
    // Set the completion
    operation.setModifyRecordsCompletionBlock { saved, deleted in
        // Only need to manage the happy path here
    }
	```
	
	In the above example, all the properties set on `operation` are saved into an internal configuration block. This is so that it in the case of retrying after an error, the same configuration is applied to the new `CKOperation` instance returned from the generator. The same could also be achieved by setting these properties inside the initial block, however the completion block above must be called to setup the `CloudKitOperation` correctly. 
	
	Thanks to `RetryOperation`, `CloudKitOperation` supports some standardized error handling for common errors. For example, if Apple’s CloudKit service is unavailable, your operation will be automatically re-tried with the correct delay. Error handling can be set for individual `CKErrorCode` values, which can replace the default handlers if desired. 
	
	`CKOperation` subclasses also all have completion blocks which receives the result and an optional error. As discussed briefly above, `CloudKitOperation` provides this completion block automatically when the consumer sets the “happy path” completion block. The format of this function is always `set<Name of the CKOperation completion block>()` This means, that it is only necessary to set a block which is executed in the case of no error being received.
	
	`BatchedCloudKitOperation` is a `RepeatedOperation` subclass which composed `CloutKitOperation` instances. It can only be used with `CKOperation` subclasses which have the notion of batched results.
	
	See the class header, example projects, blog posts and (updated) guide for more documentation. This is significant change to the existing class, and should really be viewed as entirely new. Please get in touch if you were previously using `CloudKitOperation` prior to this version, and are now unsure how to proceed. I’m still working on improving the documentation & examples for this class. 

### Examples & Documentation

1. [[OPR-169](https://github.com/danthorpe/Operations/pull/172)]: Last Opened example project

	Last Opened, is the start of an iOS application which will demonstrate how to use the new `CloudKitOperation`. At the moment, it is not exactly complete, but it does show some example. However, the application does not compile until the correct development team & bundle id is set. 

2. [[OPR-171](https://github.com/danthorpe/Operations/pull/171)]: `CloudKitOperation` documentation

	There is now quite a bit of public interface documentation. Still working on updating the programming guide right now.

### Operation Changes

1. [[OPR-152](https://github.com/danthorpe/Operations/pull/156)]: Adding Conditions & Observers

	When adding conditions and observers, we sanity check the state of the operation as appropriate. For adding a Condition, the operation must not have started executing. For adding an Observer, it now depends on the kind, for example, it is possible to add a `OperationDidFinishObserver` right up until the operation enters its `.Finishing` state.

2. [[OPR-147](https://github.com/danthorpe/Operations/pull/157)]: Scheduling of Operations from Conditions

	When an Operation has dependencies and also has Conditions attached which also have dependencies, the scheduling of these dependencies is now well defined. Dependencies from Conditions are referred to as *indirect dependencies* versus *direct* for dependencies added normally.

	The *indirect dependencies* are now scheduled __after__ *all* the direct dependencies finish. See [original issue](https://github.com/danthorpe/Operations/pull/147) and the [pull request](https://github.com/danthorpe/Operations/pull/157) for further explanation including a diagram of the queue.

3. [[OPR-129](https://github.com/danthorpe/Operations/pull/159)]: Dependencies of mutually exclusive Conditions.

	If a Condition is mutually exclusive, the `OperationQueue` essentially adds a lock on the associated `Operation`. However, this previously would lead to unexpected scheduling of that condition had a dependency operation. Now, the “lock” is placed on the dependency of the condition instead of the associated operation, but only if it’s not nil. Otherwise, standard behavior is maintained.

4. [[OPR-162](https://github.com/danthorpe/Operations/pull/162)]: Refactor of `ComposedOperation` and `GatedOperation`

	Previously, the hierarchy of these two classes was all mixed up. `ComposedOperation` has been re-written to support both `Operation` subclasses and `NSOperation` subclasses. When a `NSOperation` (but not `Operation`) subclass is composed, it is scheduled inside its own `GroupOperation`. However, if composing an `Operation` subclass, instead we “produce” it and use observers to finish the `ComposedOperation` correctly.

	Now, `GatedOperation` is a subclass of `ComposedOperation` with the appropriate logic.

5. [[OPR-163](https://github.com/danthorpe/Operations/pull/163), [OPR-171](https://github.com/danthorpe/Operations/pull/171), [OPR-179](https://github.com/danthorpe/Operations/pull/179)]: Refactor of `ReachableOperation`

	`ReachableOperation` now subclasses `ComposedOperation`, and uses `SCNetworkReachablity` callbacks correctly. 

6. [[OPR-187](https://github.com/danthorpe/Operations/pull/187)]: Sanity check `produceOperation()`. Thanks to [@bastianschilbe](https://github.com/bastianschilbe) for this fix. Now the `Operation` must at least have passed the `.Initialized` state before `produceOperation()` can be called.

### Project Configurations

1. [[OPR-182](https://github.com/danthorpe/Operations/pull/184)]: Extension Compatible

	Updates the extension compatible Xcode project. Sorry this got out of sync for anyone who was trying to get it to work!

### Bug Fixes!

1. [[OPR-186](https://github.com/danthorpe/Operations/pull/186), [OPR-188](https://github.com/danthorpe/Operations/pull/188)]: Ensures `UIOperation` finishes correctly.

2. [[OPR-180](https://github.com/danthorpe/Operations/pull/180)]: Completion Blocks.

	Changes in this pull request improved the stability of working with `OperationCondition`s attached to `Operation` instances. However, there is still a bug which is potentially an issue with KVO.
	
	Currently it is suggested that the `completionBlock` associated with `NSOperation` is avoid. Other frameworks expressly forbid its usage, and there is even a Radar from Dave De Long recommending it be deprecated.
	
	The original issue, [#175](https://github.com/danthorpe/Operations/issue/180) is still being tracked.

3. [[OPR-181](https://github.com/danthorpe/Operations/pull/189)]: Fixes a bug in `GroupOperation` where many child operations which failed could cause a crash. Now access to the `aggregateErrors` property is thread safe, and this issue is tested with a tight loop of 10,000 child operations which all fail. Thanks to [@ansf](https://github.com/ansf) for reporting this one.
	
### Thanks!

 I want to say a *huge thank you* to everyone who has contributed to this project so far. Whether you use the framework in your apps (~ 90 apps, 6k+ downloads via CocoaPods metrics), or you’ve submitted issues, or even sent me pull requests - thanks!  
 
 I don’t think I’d be able to find anywhere near the number of edge cases without all the help. The suggestions and questions from everyone keeps me learning new stuff. 

Cheers,
Dan

### What’s next?

I’ve not got anything too major planned right now, except improving the example projects. So the next big thing will probably be Swift 3.0 support, and possibly ditching `NSOperation`.



# 2.5.1
1. [[OPR-151](https://github.com/danthorpe/Operations/pull/151), [OPR-155](https://github.com/danthorpe/Operations/pull/155)]: Fixes a bug where `UserLocationOperation` could crash when the LocationManager returns subsequent locations after the operation has finished.

# 2.5.0

This is a relatively large number of changes with some breaking changes from 2.4.*.

### Breaking changes

1. [[OPR-140](https://github.com/danthorpe/Operations/pull/140)]: `OperationObserver` has been refactored to refine four different protocols each with a single function, instead of defining four functions itself. 

    The four protocols are for observing the following events: *did start*, *did cancel*, *did produce operation* and *did finish*. There are now specialized block observers, one for each event.

    This change is to reflect that observers are generally focused on a single event, which is more in keeping with the single responsibility principle. I feel this is better than a single type which typically has either three no-op functions or consists entirely of optional closures.

    To observe multiple events using blocks: add multiple observers. Alternatively, create a bespoke type to observe multiple events with the same type.

    `BlockObserver` itself still exists, however its usage is discouraged and it will be removed at a later time. It may also be necessary to make syntactic changes to existing code, in which case, I recommend replacing its usage entirely with one or more of `StartedObserver`, `CancelledObserver`, `ProducedOperationObserver` or `FinishedObserver`, all of which accept a non-optional block.

2. [[OPR-139](https://github.com/danthorpe/Operations/pull/139)]: Removed `Capabiltiy.Health`. Because this capability imports HealthKit, it is flagged by the app review team, and an application may be rejecting for not providing guidance on its usage of HealthKit. Therefore, as the majority of apps probably do not use this capability, I have removed it from the standard application framework. It is available as a subspec through Cocoapods:

    ```ruby
    pod 'Operations/+Health'
    ```

### Improvements

1. [[OPR-121](https://github.com/danthorpe/Operations/issues/121),[OPR-122](https://github.com/danthorpe/Operations/pull/122), [OPR-126](https://github.com/danthorpe/Operations/pull/126), [OPR-138](https://github.com/danthorpe/Operations/pull/138)]: Improves the built in logger. So that now:

    1. the message is enclosed in an  `@autoclosure`. 
    2. there is a default/global severity threshold
    3. there is a global enabled setting.

    Thanks to Jon ([@jshier](https://github.com/jshier)) for raising the initial issue on this one.

2. [[OPR-128](https://github.com/danthorpe/Operations/pull/128)]: Improves how code coverage is generated.

    Thanks to Steve ([@stevepeak](https://github.com/stevepeak)) from [Codecov.io](https://codecov.io) for helping with this.

3. [[OPR-133](https://github.com/danthorpe/Operations/issues/133), [OPR-134](https://github.com/danthorpe/Operations/pull/134)]: `DelayOperation` and `BlockOperation` have improved response to being cancelled.

    Thanks to Jon ([@jshier](https://github.com/jshier)) for raising the initial issue on this one.

4. [[OPR-132](https://github.com/danthorpe/Operations/pull/132)]: `BlockObserver` now supports a cancellation handler. However see the notes regarding changes to `OperationObserver` and `BlockObserver` above under breaking changes.

5. [[OPR-135](https://github.com/danthorpe/Operations/issues/135),[OPR-137](https://github.com/danthorpe/Operations/pull/137)]: Result Injection.

    It is now possible to inject the results from one operation into another operation as its requirements before it executes. This can be performed with a provided block, or automatically in the one-to-one, result-to-requirement case. See the [programming guide](https://operations.readme.io/docs/injecting-results) for more information.

    Thanks very much to Frank ([@difujia](https://github.com/difujia)) for the inspiration on this, and Jon ([@jshier](https://github.com/jshier)) for contributing to the discussion.

6. [[OPR-141](https://github.com/danthorpe/Operations/pull/141)]: `Operation` now uses `precondition` to check the expectations of public APIs. These are called out in the function’s documentation. Thanks to the Swift evolution mailing list & Chris Lattner on this one.

7. [[OPR-144](https://github.com/danthorpe/Operations/issues/144), [OPR-145](https://github.com/danthorpe/Operations/pull/145)]: Supports adapting the internal logger to use 3rd party logging frameworks. The example project uses [SwiftyBeaver](https://github.com/SwiftyBeaver/SwiftyBeaver) as a logger. 

    Thanks to Steven ([@shsteven](https://github.com/shsteven)) for raising the issue on this one!

8. [[OPR-148](https://github.com/danthorpe/Operations/pull/148)]: Location operations now conform to `ResultOperationType` which means their result (`CLLocation`, `CLPlacemark`) can be injected automatically into appropriate consuming operations.

### Bug Fixes

1. [[OPR-124](https://github.com/danthorpe/Operations/pull/124)]: Fixes a bug where notification names conflicted. 

    Thanks to Frank ([@difujia](https://github.com/difujia)) for this one.

2. [[OPR-123](https://github.com/danthorpe/Operations/issues/123),[OPR-125](https://github.com/danthorpe/Operations/pull/125), [OPR-130](https://github.com/danthorpe/Operations/pull/130)]: Fixes a bug where a completion block would be executed twice. 

    Thanks again to Frank ([@difujia](https://github.com/difujia)) for raising the issue.

3. [[OPR-127](https://github.com/danthorpe/Operations/issues/127), [OPR-131](https://github.com/danthorpe/Operations/pull/131)]: Fixes a bug where an operation could fail to start due to a race condition. Now, if an operation has no conditions, rather than entering a `.EvaluatingConditions` state, it will immediately (i.e. synchronously) become `.Ready`. 

    Thanks to Kevin ([@kevinbrewster](https://github.com/kevinbrewster)) for raising this issue.

4. [[OPR-142](https://github.com/danthorpe/Operations/pull/142)]: `Operation` now checks the current state in comparison to `.Ready` before adding conditions or operations. This is unlikely to be a breaking change, as it is not a significant difference.

    Thanks to Frank ([@difujia](https://github.com/difujia)) for this one.

5. [[OPR-146](https://github.com/danthorpe/Operations/pull/146)]: Fixes a subtle issue where assessing the readiness could trigger state changes.


Thanks to [@difujia](https://github.com/difujia), [@jshier](https://github.com/jshier), [@kevinbrewster](https://github.com/kevinbrewster), [@shsteven](https://github.com/shsteven) and [@stevepeak](https://github.com/stevepeak) for contributing to this version. :)


# 2.4.1
1. [[OPR-113](https://github.com/danthorpe/Operations/pull/113)]: Fixes an issue where building against iOS 9 using Carthage would unleash a tidal wave of warnings related to ABAddressBook. Thanks to [@JaimeWhite](https://github.com/JamieWhite) for bringing this to my attention!
2. [[OPR-114](https://github.com/danthorpe/Operations/pull/114)]: Reorganized the repository into two project files. One create standard frameworks for applications, for iOS, watchOS, tvOS, and OS X. The other creates Extension API compatible frameworks for iOS, tvOS and OS X. At the moment, if you wish to use an API extension compatible framework with Carthage - this is a problem, as Carthage only builds one project, however the is a Pull Request which will fix this. The issue previously was that the `Operations.framework` products would overwrite each other. I’ve tried everything I can think of to make Xcode produce a product which has a different name to its module - but it won’t let me. So.. the best thing to do in this case is use CocoaPods and the Extension subspec.
3. [[OPR-115](https://github.com/danthorpe/Operations/pull/115)]: Fixes an issue with code coverage after project reorganization.
4. [[OPR-116](https://github.com/danthorpe/Operations/pull/116)]: Fixes a mistake where `aggregateErrors` was not publicly accessible in `GroupOperation`. Thanks to [@JaimeWhite](https://github.com/JamieWhite) for this.

Thanks a lot to [@JaimeWhite](https://github.com/JamieWhite) for helping me find and squash some bugs with this release. Greatly appreciated!  

# 2.4.0
1. [[OPR-108](https://github.com/danthorpe/Operations/pull/108)]: Adds an internal logging mechanism to `Operation`. Output log information using the `log` property  of the operation. This property exposes simple log functions. E.g.

   ```swift
   let operation = MyOperation() // etc
   operation.log.info(“This is a info message.”)
   ```
To use a custom logger, create a type conforming to `LoggerType` and add an instance property to your `Operation` subclass. Then override `getLogger()` and `setLogger(: LoggerType)` to get/set your custom property.

The global severity is set to `.Warning`, however individual operation loggers can override to set it lower, e.g. 

    ```swift
    operation.log.verbose(“This verbose message will not be logged, as the severity threshold is .Warning”)
    operation.log.severity = .Verbose
    operation.log.verbose(“Now it will be logged.”)
    ```
2. [[OPR-109](https://github.com/danthorpe/Operations/pull/109)]: Added documentation to all of the Capability (device permissions etc) functionality. Also now uses Jazzy categories configuration to make the generated documentation more easily navigable. Documentation is hosted on here: [docs.danthorpe.me/operations](http://docs.danthorpe.me/operations/2.4.0/index.html).


# 2.3.0
1. [[OPR-89](https://github.com/danthorpe/Operations/pull/89)]: Adds support (via subspecs) for watchOS 2 and tvOS apps.
2. [[OPR-101](https://github.com/danthorpe/Operations/pull/101)]: Fixes a bug where `ReachableOperation` may fail to start in some scenarios.
3. [[OPR-102](https://github.com/danthorpe/Operations/pull/102)]: Adds more documentation to the finish method of Operation. If it’s possible for an Operation to be cancelled before it’s started, then do not call finish. This is mostly likely a possibility when writing network operations and cancelling groups.
4. [[OPR-103](https://github.com/danthorpe/Operations/pull/103)]: Adds % of code covered by tests to the README. Service performed by CodeCov.
5. [[OPR-104](https://github.com/danthorpe/Operations/pull/104)]: Maintenance work on the CI scripts, which have now moved to using a build pipeline which is uploaded to BuildKite and executed all on the same physical box. See [post on danthorpe.me](http://danthorpe.me/posts/uploading-build-pipelines.html).
6. [[OPR-105](https://github.com/danthorpe/Operations/pull/105)]: Improves the testability and test coverage of the Reachability object.
7. [[OPR-106](https://github.com/danthorpe/Operations/pull/106)]: Adds more tests to the AddressBook swift wrapper, increases coverage of `Operation`, `NegatedCondition` & `UIOperation`.

# 2.2.1
1. [[OPR-100](https://github.com/danthorpe/Operations/pull/100)]: Adds documentation to all “Core” elements of the framework. Increases documentation coverage from 8% to 22%. Still pretty bad, but will get there eventually.

# 2.2.0
1. [[OPR-91](https://github.com/danthorpe/Operations/pull/91), [OPR-92](https://github.com/danthorpe/Operations/pull/92)]: Fixes a bug in AddressBook when building against iOS 9, where `Unmanaged<T>` could be unwrapped incorrectly.
2. [[OPR-93](https://github.com/danthorpe/Operations/pull/93), [OPR-95](https://github.com/danthorpe/Operations/pull/95)]: Adds support for Contacts.framework including `ContactsCondition` plus operations for `GetContacts`, `GetContactsGroup`, `RemoveContactsGroup`, `AddContactsToGroup` and `RemoveContactsFromGroup` in addition to a base contacts operation class. Also included are UI operations `DisplayContactViewController` and `DisplayCreateContactViewController`.
3. [[OPR-97](https://github.com/danthorpe/Operations/pull/97), [OPR-98](https://github.com/danthorpe/Operations/pull/98)]: Refactors how device authorization permissions are checked and requested. Introduces the concept of a `CapabilityType` to govern authorization status and requests. This works in tandem with new operations `GetAuthorizationStatus<Capability>` and `Authorize<Capability>` with an operation condition `AuthorizedFor<Capability>`. The following conditions are now deprecated: `CalendarCondition`, `CloudContainerCondition`, `HealthCondition`, `LocationCondition`, `PassbookCondition`, `PhotosCondition` in favor of `Capability.Calendar`, `Capability.Cloud`, `Capability.Heath`, `Capability.Location`, `Capability.Passbook`, `Capability.Photos` respectively. Replace your condition code like this example:

```swift
operation.addCondition(AuthorizedFor(Capability.Location(.WhenInUse)))
```

# 2.1.0
1. [[OPR-90](https://github.com/danthorpe/Operations/pull/90)]: Multi-platform support. Adds new framework targets to the project for iOS Extension only API framework. This doesn’t have support for BackgroundObserver, or NetworkObserver for example. Use `pod ‘Operations/Extension’` to use it in a Podfile for your iOS Extension target. Also, we have Mac OS X support (no special pod required). And watchOS support - use `pod ‘Operations/watchOS’`.

# 2.0.2
1. [[OPR-87](https://github.com/danthorpe/Operations/pull/87)]: Improves the reliability of the reverse geocoder unit tests.

# 2.0.1
1. [[OPR-62, OPR-86](https://github.com/danthorpe/Operations/pull/86)]: Fixes a bug in Swift 2.0 where two identical conditions would cause a deadlock. Thanks to @mblsha.
2. [[OPR-85](https://github.com/danthorpe/Operations/pull/85)]: Fixes up the Permissions example project for Swift 2.0. Note that YapDatabase currently has a problem because it has some weak links, which doesn’t work with Bitcode enabled, which is default in Xcode 7. This PR just turned off Bitcode, but if you re-run the Pods, then that change will be over-ridden. What you can do instead, if YapDatabase is still not fixed is to use my fork which has a fix on the `YAP-180` branch.

# 2.0.0
This is the Swift 2.0 compatible version.

# 1.0.0
1. [[OPR-79](https://github.com/danthorpe/Operations/pull/79)]: Adds more documentation to the types.
2. [[OPR-83](https://github.com/danthorpe/Operations/pull/83)]: Adds some convenience functions to `NSOperation` and `GroupOperation` for adding multiple dependencies at once, and multiple operations to a group before it is added to a queue.

This is a release for Swift 1.2 compatible codebases.

# 0.12.1
1. [[OPR-74](https://github.com/danthorpe/Operations/pull/74)]: Work in progress on AddressBook external change request. *Warning* so not use this, as I cannot actually get this working yet.
2. [[OPR-75](https://github.com/danthorpe/Operations/pull/75)]: Fixes a serious bug where attempting to create an ABAddressBook after previously denying access executed a fatalError.

# 0.12.0
1. [[OPR-63](https://github.com/danthorpe/Operations/pull/63)]: Speeds up the test suite by 40 seconds.
2. [[OPR-65](https://github.com/danthorpe/Operations/pull/65)]: Adds a generic `UIOperation` class. Can be used to show view controllers, either present modally, show or show detail presentations. It is used as the basis for `AlertOperation`, and the `AddressBookDisplayPersonController`, `AddressBookDisplayNewPersonController` operations.
3. [[OPR-67](https://github.com/danthorpe/Operations/pull/67)]: Adds reverse geocode operations. Supply a `CLLocation` to `ReverseGeocodeOperation` directly. Or use `ReverseGeocodeUserLocationOperation` to reverse geocode the user’s current location. Additionally, `LocationOperation` has been renamed to `UserLocationOperation`.
4. [[OPR-68](https://github.com/danthorpe/Operations/pull/68)]: General improvements to the `AddressBook` APIs including a `createPerson` function, plus addition of missing person properties & labels. Additionally, fixes a bug in setting multi-value string properties.
5. [[OPR-71](https://github.com/danthorpe/Operations/pull/71)]: Updates the unit test scripts to use Fastlane, same as Swift 2.0 branch.

# 0.11.0
1. [[OPR-45](https://github.com/danthorpe/Operations/pull/45), [OPR-46](https://github.com/danthorpe/Operations/pull/46), [OPR-47](https://github.com/danthorpe/Operations/pull/47), [OPR-48](https://github.com/danthorpe/Operations/pull/48), [OPR-49](https://github.com/danthorpe/Operations/pull/49), [OPR-54](https://github.com/danthorpe/Operations/pull/54)]:

Refactor of AddressBook.framework related functionality. The `AddressBookOperation` is no longer block based, but instead keeps a reference to the address book as a property. This allows for superior composition. Additionally there is now an `AddressBookGetResource` operation, which will access the address book, and then exposes methods to read people, and if set, an individual person record and group record.

Additionally, there is now operations for adding/removing a person to a group. Add/Remove groups. And map all the people records into your own type.

Internally, these operations are supported by a Swift wrapper of the AddressBook types, e.g. `AddressBookPerson` etc. This wrapper is heavily inspired by the Gulliver. If you want more powerful AddressBook features, I suggest you checkout that project, and then either subclass the operations to expose Gulliver types, or write a simple protocol extension to get Gulliver types from `AddressBookPersonType` etc etc.

2. [[OPR-57](https://github.com/danthorpe/Operations/pull/57)]: The CloudKitOperation is no longer a GroupOperation, just a standard Operation, which enqueues the `CKDatabaseOperation` onto the database’s queue directly.
3. [[OPR-58](https://github.com/danthorpe/Operations/pull/58)]: Added `ComposedOperation` which is a specialized `GatedOperation` which always succeeds. This is handy if you want to add conditions or observers to an `NSOperation`.
4. [[OPR-60](https://github.com/danthorpe/Operations/pull/60)]: Renamed `NoCancellationsCondition` to `NoFailedDependenciesCondition` which encompasses the same logic, but will also fail if any of the operation’s dependencies are `Operation` subclasses which have failed. In addition, `Operation` now exposes all it’s errors via the `errors` public property.

# 0.10.0
1. [[OPR-14](https://github.com/danthorpe/Operations/pull/14)]: Supports Photos library permission condition.
2. [[OPR-16](https://github.com/danthorpe/Operations/pull/16)]: Supports Health Kit permission condition.

# 0.9.0
1. [[OPR-11](https://github.com/danthorpe/Operations/pull/11)]: Supports Passbook condition.
2. [[OPR-13](https://github.com/danthorpe/Operations/pull/13)]: Supports a EventKit permission condition.
3. [[OPR-17](https://github.com/danthorpe/Operations/pull/17)]: Supports remote notification permission condition.
4. [[OPR-18](https://github.com/danthorpe/Operations/pull/18)]: Supports user notification settings condition.
5. [[OPR-38](https://github.com/danthorpe/Operations/pull/38)]: Adds a `LocationOperation` demo to Permissions.app
6. [[OPR-39](https://github.com/danthorpe/Operations/pull/39)]: Adds a user confirmation alert condition.


# 0.8.0
1. [[OPR-37](https://github.com/danthorpe/Operations/pull/37)]: Creates an example app called Permissions. This is a simple catalogue style application which will be used to demonstrate functionality of the Operations framework.

At the moment, it only shows Address Book related functionality. Including using combinations of `SilentCondition`, `NegatedCondition` and `AddressBookCondition` to determine if the app has already got authorization, requesting authorization and performing a simple ABAddressBook related operation.

Additionally, after discussions with Dave DeLong, I’ve introduced changes to the underlying Operation’s state machine.

Lastly, the structure of `BlockOperation` has been modified slightly to allow the task execution block to pass an error (`ErrorType`) into the continuation block. Because closures cannot have default arguments, this currently means that it is required, e.g. `continueWithError(error: nil)` upon success. 
 

# 0.7.0
1. [[OPR-7](https://github.com/danthorpe/Operations/pull/7)]: Supports a condition which requires all of an operation’s dependencies to succeed.
2. [[OPR-12](https://github.com/danthorpe/Operations/pull/12)]: Adds `LocationOperation` and `LocationCondition`. This allows for accessing the user’s location, requesting “WhenInUse” authorization.
3. [[OPR-36](https://github.com/danthorpe/Operations/pull/36)]: Adds `AddressBookOperation` which allows for access to the user’s address book inside of a handler block (similar to a `BlockOperation`). As part of this, `AddressBookCondition` is also available, which allows us to condition other operation types.


# 0.6.0
1. [[OPR-5](https://github.com/danthorpe/Operations/pull/5)]: Supports silent conditions. This means that if a condition would normally produce an operation (say, to request access to a resource) as a dependency, composing it inside a `SilentCondition` will suppress that dependent operation.
2. [[OPR-6](https://github.com/danthorpe/Operations/pull/r)]: Supports negating condition.
3. [[OPR-30](https://github.com/danthorpe/Operations/pull/30)]: Adds a `LoggingObserver` to log operation lifecycle events.
4. [[OPR-33](https://github.com/danthorpe/Operations/pull/33)]: Adds `GatedOperation` which will only execute the composed operation if the supplied block evaluates true - i.e. opens the gate.
5. [[OPR-34](https://github.com/danthorpe/Operations/pull/34)] & [[OPR-35](https://github.com/danthorpe/Operations/pull/35)]: Adds a `ReachableOperation`. Composing an operation inside a `ReachableOperation` will ensure that it runs after the device regains network reachability. If the network is reachable, the operation will execute immediately, if not, it will register a Reachability observer to execute the operation when the network is available. Unlike the `ReachabilityCondition` which will fail if a host is not available, use `ReachableOperation` to perform network related tasks which must be executed regardless.


# 0.5.0
1. [[OPR-22](https://github.com/danthorpe/Operations/pull/22)]: Supports displaying a `UIAlertController` as a `AlertOperation`.
2. [[OPR-26](https://github.com/danthorpe/Operations/pull/26)]: Adds a Block Condition. This allows an operation to only execute if a block evaluates true.
3. [[OPR-27](https://github.com/danthorpe/Operations/pull/27)]: Fixes a bug where the `produceOperation` function was not publicly accessible. Thanks - @MattKiazyk
4. [[OPR-28](https://github.com/danthorpe/Operations/pull/28)]: Supports a generic `Operation` subclass which wraps a `CKDatabaseOperation` setting the provided `CKDatabase`.
5. [[OPR-29](https://github.com/danthorpe/Operations/pull/29)]: Improves the `CloudCondition.Error` to include `.NotAuthenticated` for when the user is not signed into iCloud.


# 0.4.2 - Initial Release of Operations.
Base `Operation` and `OperationQueue` classes, with the following features.

The project has been developed using Xcode 7 and Swift 2.0, with  unit testing (~ 75% test coverage). It has now been back-ported to Swift 1.2 for version 1.0 of the framework. Version 2.0 will support Swift 2.0 features, including iOS 9 technologies such as Contacts framework etc.

1. Operation types:
1.1. `BlockOperation`: run a block inside an operation, taking advantage of Operation features.
1.2. `GroupOperation`: compose one more operations into a group.
1.3. `DelayOperation`: delay execution of operations on the queue.

2. Conditions
Conditions can be attached to `Operation`s, and optionally introduce new `NSOperation` instances to overcome the condition requirements. E.g. presenting a permission dialog. The following conditions are currently supported:
2.1. `MutuallyExclusive`: for exclusivity of a given kind, e.g. to prevent system alerts presenting at the same time.
2.2. `ReachabilityCondition`: only execute tasks when the device is online.
2.3. `CloudCondition`: authorised access to a CloudKit container. 

3. Observers
Observers can be attached to `Operation`s, and respond to events such as the operation starting, finishing etc. Currently observer types are:
3.1. `BackgroundObserver`: when the app enters the background, a background task will automatically be started, and ended when the operation ends.
3.2. `BlockObserver`: run arbitrary blocks when events occur on the observed operation.
3.3. `NetworkObserver`: updates the status of the network indicator.
3.4. `TimeoutObserver`: trigger functionality if the operation does not complete within a given time interval.

