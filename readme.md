###### **1** pprof
package pprof serves via its HTTP server runtime profiling data in the format expected by the pprof visualization tool.
if graphviz is installed,graph can be showed in browser;
demo code

`package main

import (
"net/http"
_ "net/http/pprof"
)

func main() {
http.ListenAndServe(":9999", nil)
}`

use the pprof tool to look at a 30-second CPU profile:
`go tool pprof -http=:1234 http://:9999/debug/pprof/profile?seconds=30
`

to look at the heap profile:
`go tool pprof -http=:1234 http://:9999/debug/pprof/heap
`

to look at the goroutine blocking profile, after calling runtime.SetBlockProfileRate in your program
`go tool pprof -http=:1234 http://:9999/debug/pprof/block
`

to look at the goroutine mutexes profile, after calling runtime.SetMutexProfileFraction in your program:
`go tool pprof -http=:1234 http://:9999/debug/pprof/mutex
`

###### **2** when concatenating string,strings.Builder.WriteString is more efficient than plus +. demo
`func builderConcat(n int, str string) string {
var builder strings.Builder
for i := 0; i < n; i++ {
builder.WriteString(str)
}
return builder.String()
}`

###### **3** Concurrency
1)
sync.Pool's purpose is to cache allocated but unused items for later reuse, relieving pressure on the garbage collector. That is, it makes it easy to build efficient, thread-safe free lists. However, it is not suitable for all free lists.
New,Get,Put,demo:

```
package main
import (
	"fmt"
	"sync"
	"time"
)
var bytePool = sync.Pool{
	New: func() interface{} {
		b := make([]byte, 1024)
		return &b
	},
}
func main() {
	a := time.Now().Unix()
	for i := 0; i < 1000000000; i++ {
		obj := make([]byte, 1024)
		_ = obj
	}
	b := time.Now().Unix()

	for j := 0; j < 1000000000; j++ {
		obj := bytePool.Get().(*[]byte)
		_ = obj
		bytePool.Put(obj)
	}
	c := time.Now().Unix()
	fmt.Println("without pool ", b-a, "s")
	fmt.Println("with    pool ", c-b, "s")
}
```

2)atomic.Value provides an atomic load and store of a consistently typed value.It's more efficient than using sync.Mutex.Key methods is Load and Store.


