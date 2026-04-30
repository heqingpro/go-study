### golang 常见面试题

#### 1、三个协程依次打印0 - 100

```go
package main

import (
    "fmt"
    "sync"
)
var wg sync.WaitGroup
var aa chan int
var bb chan int
var cc chan int 

func a() {
    defer wg.Done()
    for {
        if val, ok := <- aa; ok {
            fmt.Println("a:",val)
            if val == 100 {
                close(aa)
                close(bb)
                close(cc)
                fmt.Println("close a -")
                return
            }
            val ++
            bb <- val
        } else {
            fmt.Println("close a")
            return
        }
    }
}

func b() {
    defer wg.Done()
    for {
        if val, ok := <- bb; ok{
            fmt.Println("b:",val)
            if val == 100 {
                close(aa)
                close(bb)
                close(cc)
                fmt.Println("close b -")
                return
            }
            val++
            cc <- val
        } else {
            fmt.Println("close b")
            return

        }
    }
}

func c() {
    defer wg.Done()
    for {
        if val, ok := <- cc; ok{
            fmt.Println("c:", val)
            if val == 100 {
                close(aa)
                close(bb)
                close(cc)
                fmt.Println("close c -")
                return
            }
            val++
            aa <- val
        } else {
            fmt.Println("close c")
            return
        }
    }
}
func main() {
    wg.Add(3)
    aa = make(chan int, 0)
    bb = make(chan int, 0)
    cc = make(chan int, 0)
    go a()
    go b()
    go c()
    aa <- 0
    wg.Wait()
}
```
