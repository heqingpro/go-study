### golang 性能调优

#### 一 调优介绍

##### 1、常规调优范围

- cpu： 程序对cpu的使用情况 - 使用时长，占比等

- 内存： 程序对cpu的使用情况 - 使用时长，占比，内存泄露等。如果在往里分，程序堆、栈使用情况

- I/o：IO的使用情况 - 哪个程序IO占用时间比较长
  
  （Linux 调优工具： top，dstat，perf ）

##### 2、golang 程序调优常规范围

- **goroutine**：go的协程使用情况，调用链的情况
- **goroutine leak**：goroutine泄露检查
- **go dead lock**：死锁的检测分析
- **data race detector**：数据竞争分析，其实也与死锁分析有关

##### 3、golang 性能调试优化方法

- **Benchmark**：**基准测试**，对特定代码的运行时间和内存信息等进行测试

- **Profiling**：**程序分析**，程序的运行画像，在程序执行期间，通过采样收集的数据对程序进行分析

- **Trace**：**跟踪**，在程序执行期间，通过采集发生的事件数据对程序进行分析
  
  （profiling 分析没有时间线，trace 分析有时间线）

#### 二、pprof 简介

Golang 官方提供的性能调优工具，可以对程序进行性能分析，并可视化数据，看起来相当的直观。

##### 1、[runtime/pprof](https://golang.org/pkg/runtime/pprof/)

采集程序运行数据进行性能分析，一般用于后台工具型应用，这种应用运行一段时间就结束。

##### 2、[net/http/pprof](https://golang.org/pkg/net/http/pprof/)

对 runtime/pprof 的二次封装，一般是服务型应用。比如 web server ，它一直运行。这个包对提供的 http 服务进行数据采集分析

#### 三、 runtime/pprof

##### 1、开启采集

- 写入文件：pprof.StartCPUProfile(w io.Writer) pprof.StopCPUProfile()
- 开启测试： go test -cpuprofile cpu.prof -memprofile mem.prof -bench .
- http server： go tool pprof $host/debug/pprof/profile

##### 2、分析数据

- 命令行交互分析：go tool pprof democpu.pprof
- 图形可视化：谷歌火焰图