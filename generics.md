# Go2 generics: Extend `type` and `func` keywords

Pros:

* minimal changes to source code parser
* easy to understand when generics are used

Cons:

* unfamiliar generic definition syntax
* adds a new operator `:` and `::` if needed I'm not sure

## The idea

Move the temlate type definition to type keyword.

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


> Russ Cox: The generic dilemma is this: do you want slow programmers, slow compilers and bloated binaries, or slow execution times?

When I use generics I want fast execution time and compile-time type checking. 

I am able to pay I use only manual generics specialization. I will get as I think:
* a little slow programmer
* good enough compiler and binaries


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

@rogpeppe [Problem: What functions do we need to compile?](https://gist.github.com/rogpeppe/9fa9a267472fb80e9ddc4a940aa26e14)

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
