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

We may think about it as a code generation. We know the package of the generic type and can generate code for template specialization like on the next example. 

```go
package list // github.com/my/list/list.go
// also module github.com/my/list/v3
type(Element) List struct {
	// ...
}

// end of list.go

// another code
import "github.com/my/list"
import "labix.org/v2/mgo/bson"
func foo() {
	data := &list.List:(bson.M){}
	//...
}
```

When the compiler works it is able to create a virtual file github.com/my/list/list$[labix.org/v2/mgo/bson.M].go and compiles it. Like the next code

```go
// github.com/my/list/list$[labix.org/v2/mgo/bson.M].go
package list 
import "labix.org/v2/mgo/bson"

type List$[labix.org/v2/mgo/bson.M] struct {
	val  bson.M
	// ...
}

```


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
    list := &List:(sql.NullString){}
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

## Other talks

[Problem: What functions do we need to compile?](https://gist.github.com/rogpeppe/9fa9a267472fb80e9ddc4a940aa26e14)

I think we should block automatic type specialization in templates declaration.

```go
package main
import "fmt"

func main() {
	foo::(43)
	foo::("hello")
}

func(T) foo(x T) {
	bar::(&x) // it's wrong
	bar::(make(chan T)) // it's wrong

	bar:(*T)(&x) // it's good
	bar:(chan T)(make(chan T)) // it's good
}

func(T) bar(x T) {
	fmt.Println(x)
}
```