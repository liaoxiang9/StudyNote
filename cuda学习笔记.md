# CUDA学习笔记

## 1. 一个异构应用的组成

- 主机代码
  - 主机代码在CPU上运行， 异构平台上执行的应用通常由CPU初始化。 在设备端加载计算密集型任务之前， CPU代码负责管理设备端的环境、 代码和数据。
- 设备代码
  - 设备代码在GPU上运行。

## 2. 一个典型的CUDA编程结构包括5个步骤

1. 分配GPU内存
2. 从CPU内存中拷贝数据到GPU内存
3. 调用CUDA内核函数来完成程序指定的运算
4. 将数据从GPU内存拷贝到CPU内存
5. 释放GPU内存

## 3. CUDA的内存管理

- 执行GPU内存分配的是cudaMalloc函数， 该函数的原型如下：

```c
cudaError_t cudaMalloc(void **devPtr, size_t size);
```

- 执行GPU内存释放的是cudaFree函数， 该函数的原型如下：

```c
cudaError_t cudaFree(void *devPtr);
```

- 执行从CPU内存到GPU内存的数据拷贝的是cudaMemcpy函数， 该函数的原型如下：

```c
cudaError_t cudaMemcpy(void *dst, const void *src, size_t count, cudaMemcpyKind kind);
```

cudaMemcpy函数的第四个参数kind指定了拷贝的方向， 该参数的取值可以是cudaMemcpyHostToDevice、cudaMemcpyDeviceToHost或cudaMemcpyDeviceToDevice。

- 执行从GPU内存到CPU内存的数据拷贝的是cudaMemcpy函数， 该函数的原型如下：

```c
cudaError_t cudaMemcpy(void *dst, const void *src, size_t count, cudaMemcpyKind kind);
```

- cudaError_t是一个枚举类型， 它的取值包括cudaSuccess、cudaErrorMemoryAllocation、cudaErrorInvalidValue、cudaErrorInvalidDevicePointer、cudaErrorInvalidMemcpyDirection等。可以通过cudaGetErrorString函数来获取错误信息。

```c
const char *cudaGetErrorString(cudaError_t error);
```




-
