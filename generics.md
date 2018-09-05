# DRAFT: An alternative generics syntax

This proposal has no description yet. 

The basic idea is using generics as a function. For example, we have a type description in AST representation with not existing types TypeA and TypeB. We can transform this AST to another AST by type substitution like a template function.

```
// function for AST transformation
func transform(ast AST, ...types) AST

// get specialized AST
newAST$int$string := transform(AST, int, string)
```

In this way, we can cache concreate type AST like templates.

## Syntax examples

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
	next *List:(Element)
	val  Element
}

func (l *List) Add(e Element) {
    //...
}

func main() {
    list := &List:(sql.NullString)
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
