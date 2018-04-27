# swift-tensorflow

Dockerized [Swift for TensorFlow](https://github.com/tensorflow/swift).

## Overview

This image will allow you to easily take the official [Swift for TensorFlow](https://github.com/tensorflow/swift) for a test drive without worrying about installing dependencies, changing your path, and interfering with your existing Swift/Xcode config.

## Run
#### Run a REPL

```bash
docker run  --privileged --cap-add sys_ptrace -it --rm zachgray/swift-tensorflow:4.2 swift -I/usr/lib/swift/clang/include
```

and observe the output:

```
Welcome to Swift version 4.2-dev (LLVM 04bdb56f3d, Clang b44dbbdf44). Type :help for assistance.
  1> 
```

Interact with TensorFlow:

```
  1> import TensorFlow
  2> var x = Tensor([[1, 2], [3, 4]])
2018-04-27 04:30:17.505272: I tensorflow/core/platform/cpu_feature_guard.cc:140] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
x: TensorFlow.Tensor<Double> = [[1.0, 2.0], [3.0, 4.0]]
  3> x + x
$R0: TensorFlow.Tensor<Double> = [[2.0, 4.0], [6.0, 8.0]]
  4> :exit
```

#### Run the Interpreter: 

Assuming you've added a swift file ([like this one shown in the official docs](https://github.com/tensorflow/swift/blob/master/Usage.md#interpreter)) in your current directory with the name `inference.swift`:

```bash
docker --rm run -v ${PWD}:/usr/src zachgray/swift-tensorflow:4.2 swift -O /usr/src/inference.swift
```

#### Run the Compiler:

```bash
docker run --rm -v ${PWD}:/usr/src zachgray/swift-tensorflow:4.2 swiftc -O /usr/src/inference.swift
```

## Run with Dependencies (advanced)

Importing third-party packages in the REPL requires a few additional steps, but it's possible if we make use of [SPM](https://swift.org/package-manager/) and a dynamic library.

#### Package Manager Tutorial:

For the sake of simplicity we'll run all of these commands in interactive mode from within the Docker container. Keep in mind that since we've mounted the current directory as a container volume which we're working in, changes here will be reflected in your host filesystem.

Note: if you wanted to run these commands from outside of the container, as we did the previous examples, you'd simple include the following before each `swift` binary interaction: `docker run --rm -v ${PWD}:/usr/src zachgray/swift-tensorflow:4.2`.

1: Start the interactive session:

```bash
docker run --rm -it -v ${PWD}:/usr/src zachgray/swift-tensorflow:4.2 /bin/bash
```

2: Create a library called `example`:

```bash
mkdir TFExample 
cd TFExample 
swift package init --type library
```

3: Add some third-party dependencies to `Package.swift`, and make the library dynamic so we can import it and it's dependencies. Here's an example:

```swift
// swift-tools-version:4.0

import PackageDescription

let package = Package(
    name: "TFExample",
    products: [
        .library(
            name: "TFExample",
            type: .dynamic,    // allow use of this package and it's deps from the REPL
            targets: ["TFExample"]
        )
    ],
    dependencies: [
        .package(url: "https://github.com/ReactiveX/RxSwift.git", "4.0.0" ..< "5.0.0")
    ],
    targets: [
        .target(
            name: "TFExample",
            dependencies: ["RxSwift"]),
        .testTarget(
            name: "TFExampleTests",
            dependencies: ["TFExample"]),
    ]
)
```

4: Now fetch package dependencies:

```bash
swift package update
```

5: Build the package:

```bash
swift build
```

6: Once the build is complete, we will exit our interactive session:

```
exit
```

7: Now fire up the REPL in a similar manner to the examples above, but this time link to the built package:

```bash
docker run --rm --privileged --cap-add sys_ptrace -it -v ${PWD}:/usr/src zachgray/swift-tensorflow:4.2 swift -I/usr/lib/swift/clang/include -I/usr/src/TFExample/.build/debug -L/usr/src/TFExample/.build/debug -lTFExample
```

And now we can import our package dependencies in the REPL session like so:

```
Welcome to Swift version 4.2-dev (LLVM 04bdb56f3d, Clang b44dbbdf44). Type :help for assistance.
  1> import TensorFlow
  2> import RxSwift
  3>  _ = Observable.from([1,2]).subscribe(onNext: { print($0) })
1
2
  4> var x = Tensor([[1, 2], [3, 4]])
2018-04-27 17:13:12.514107: I tensorflow/core/platform/cpu_feature_guard.cc:140] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
x: TensorFlow.Tensor<Double> = [[1.0, 2.0], [3.0, 4.0]]
```

NOTE: the import order is currently important; importing other packages before TensorFlow will result in a runtime error in [swift-lldb](https://github.com/google/swift-lldb/blob/87c3a7891cf2e482c6908f7758c1fc06f531e4c3/source/Plugins/Language/Swift/SwiftFormatters.cpp#L564).

## License

This project is [MIT Licensed](https://github.com/zachgrayio/swift-tensorflow/blob/master/LICENSE).