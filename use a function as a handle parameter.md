# Use a function as a handle parameter

The handle declaration seems like a function declaration. 
Why not use pure Go functions with specific execution flow to control error checks. 

Pros:

* The error handlers is reusable 
* Dynamic error handlers 
* No breaking changes into the current draft

## Examples

### The not found handler
```go
func NotFoundHandler(thing, id string) func(error) error {
	return func (err error) error {
		if err = sql.ErrNoRows {
			return fmt.Errorf("%s not found by id %s", thing, id)
		}
		return err
	}
}

func GetUser(id string) (*User, error) {
	handle err {
	   return nil, err
	}
	handle NotFoundHandler("user", id)
	user := check FindUserByID(id)
	// ....
}
```

An equivalent implementation on Go 1.11
```go

func NotFoundHandler(thing, id string) func(error) error {
  return func (err error) error {
    if err = sql.ErrNoRows {
      return fmt.Errorf("%s not found by id %s", thing, id)
    }
    return err
  }
}

func GetUser(id string) (*User, error) {
	handle1 := func(err error) (*User, error) {
		return nil, err
	}
	handle2 := NotFoundHandler("user", id)
	user, err := FindUserByID(id)
	if err != nil {
		return handle1(handle2(err))
	}
	// ....
}
```

### The default behavior

I think for function without return values we need to throw a panic. The next code will throw a panic if ReadAll returns an error.

```go 
func main() {
	check ioutil.ReadAll(os.Stdin)
}
```

An equivalent implementation on Go 1.11
```go 
func main() {
  hendle := func(err error) {
    panic(err)
  }
  _, err := ioutil.ReadAll(os.Stdin)
  if err != nil {
	  handle(err)
  }
}
```

To prevent a default behavior we can add some standard handlers like LogHandler, FatalHandler. 
The next code will call os.Exit(1)  if ReadAll returns an error.
```go 
func main() {
  hendle FatalHandler
  check ioutil.ReadAll(os.Stdin)
}
```
The next code will call log.Println(err) if ReadAll returns an error.
```go 
func main() {
  hendle LogHandler
  check ioutil.ReadAll(os.Stdin)
}
```

