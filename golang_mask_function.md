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
	sub := content[0 : len(content)-int(math.Floor(float64(len(content))*howMany))]
	masker = strings.Repeat(masker, int(math.Floor(float64(len(content))*howMany)))
	return strings.Join([]string{sub, masker}, "")
}
```

Output:
```shell
085*********
```
