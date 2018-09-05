# DRAFT

This proposal has no description yet. 


```go
// Print prints the elements of a slice.
// It should be possible to call this with any slice value.
func(TypeAlias) Print(s []TypeAlias) { // Just an example, not the suggested syntax.
	for _, v := range s {
		fmt.Println(v)
	}
}

func main() {
    data := []int{1,2}
    Print:(int)(data) // manual function specialization
    Print::(data) // automatic compile time function specialization
}

```

```go
func(SrcType, DstType) Convert(src SrcType) DstType {
    //...
}

// specialization
func:(int, float64) Convert(src int) float64 {
    //...
}
```

```go
type(Element) List struct {
	next *List(Element)
	val  Element
}

func (l *List) Add(e Element) {
    //...
}

func main() {
    el := &Element:(sql.NullString)
    // ...
}
```

```go
type(Target) Ptr *Target
```


## Type contracts

```go
contract stringer(x T) {
	var s string = x.String()
}

func(T stringer) Stringify(s []T) (ret []string) {
	for _, v := range s {
		ret = append(ret, v.String()) // now valid
	}
	return ret
}
```
