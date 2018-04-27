# swift-tensorflow

Dockerized Swift for TensorFlow.

## Running

#### Pull

```bash
docker pull zachgrayio/swift-tensorflow
```

#### Run a REPL

```bash
docker run  --privileged --cap-add sys_ptrace -it --rm zachgray/swift-tensorflow swift -I/usr/lib/swift/clang/include
```

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

## License

This project is [MIT Licensed](https://github.com/zachgrayio/swift-tensorflow/blob/master/LICENSE)