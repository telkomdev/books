## Golang simple mask function

```go
package main

import (
	"fmt"
	"math"
	"strings"
)

func main() {
	fmt.Println(Mask(0.75, "085662772142", "*"))
}

func Mask(howMany float64, content, masker string) string {
	if len(content) <= 0 {
		return ""
	}
	sub := content[0 : len(content)-int(math.Floor(float64(len(content))*howMany))]
	masker = strings.Repeat(masker, int(math.Floor(float64(len(content))*howMany))-1)
	return strings.Join([]string{sub, masker, content[len(content)-1 : len(content)]}, "")
}
```

Output:
```shell
085********2
```
