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