# Go Style Guide

## Introduction

This document presents a set of team-approved conventions for writing Go
code. The goal of setting standards for code formatting and organization
is maintainable code. When code is readable, consistent, predictable, and understandable
it is by nature more maintainable. By producing more maintainable code, we thus produce
code that is more easily changed, more readily refactored, and less prone to common errors
and bugs.

## Rules

* Use UTF-8 encoding
* Use Unix-style line endings.
* Avoid inline or end-of-line comments

```go
// BAD
foo := 6 // set foo equal to 6 (also avoid useless comments...)

// REALLY BAD
for i := 0; /* iterate over i... */ i < 10; i++ {
	// Do something with i
}

// GOOD
// Be ready for foo
foo := 6
```

* Delete trailing whitespace.
* Don't include spaces after `(`, `[` or before `]`, `)`.

```go
// BAD
fmt.Println( foo )
first = list[ 0 ]

// GOOD
fmt.Println(foo)
first = list[0]
```

* Use one empty line between functions.
* Use spaces around operators, after commas, and after colons (Exception:  Do
  not put a space between the `:` and `=` of a short declaration.)
* Try to declare variables at the top of the function/scope in which they
  are used.  Ex:

```go
// BAD
func foo() {
	var bar int
	bar = 0

	// Stuff happens here...

	baz := 1

	// More stuff happens here...
}

// GOOD
func foo() {
	bar := 0
	baz := 1

	// Stuff happens here...
}
```

* `go fmt` is your friend.  Run it against all of your Go code before commiting.
  The majority of the popular editors for Go include plugins for this.  Use them.
  See [here](https://go.dev/wiki/IDEsAndTextEditorPlugins) for more details.
  Your favorite editor probably has a plugin...

* Keep the imports grouped by type and in the following order. Separate each group
  with an empty line:
    * Go libraries
    * External libraries
    * Private libraries
    * Project libraries

```go
// BAD
// All imports are grouped together, with no spacing.
import (
	"fmt"
	"github.com/Sirupsen/logrus"
	"github.com/gorilla/handlers"
	"github.com/gorilla/mux"
	"log"
	"net/http"
	"os"
	"github.com/team/project/api"
	"github.com/team/gocore/configuration"
	"github.com/team/project/models"
	"github.com/team/project/utilities"
	"github.com/team/gocore/validation"
)

// ALSO BAD
// Only internal libraries are broken out, external libraries still mingled.
import (
	"fmt"
	"github.com/Sirupsen/logrus"
	"github.com/gorilla/handlers"
	"github.com/gorilla/mux"
	"log"
	"net/http"
	"os"

	"github.com/team/project/api"
	"github.com/team/gocore/configuration"
	"github.com/team/project/models"
	"github.com/team/project/utilities"
	"github.com/team/gocore/validation"
)

// STILL BAD
// Libraries are broken out into groups, but not in the right order.
import(
	"fmt"
	"log"
	"net/http"
	"os"

	"github.com/team/project/api"
	"github.com/team/project/models"
	"github.com/team/project/utilities"

	"github.com/Sirupsen/logrus"
	"github.com/gorilla/handlers"
	"github.com/gorilla/mux"

	"github.com/team/gocore/configuration"
	"github.com/team/gocore/validation"
)

// GOOD
import(
	"fmt"
	"log"
	"net/http"
	"os"

	"github.com/Sirupsen/logrus"
	"github.com/gorilla/handlers"
	"github.com/gorilla/mux"

	"github.com/team/gocore/configuration"
	"github.com/team/gocore/validation"

	"github.com/team/project/api"
	"github.com/team/project/configuration"
	"github.com/team/project/models"
	"github.com/team/project/utilities"
)
```

## Namespacing

* We use the following format for Go functions:
	* api package namespace: {Base Name}{Verb}
        * Base Name is always the name of the struct it will be verbing
        * Verb
            *  Allowed Verbs: Create, Get, Delete, Update, Index
            *  Base Name is always plural in conjunction with Index

```go
package api

func UserCreate() {
	...
}

func UserGet() {
	...
}

func UsersIndex() {
	...
}
```

* models package: {Verb}{Base Name}{(s)}
    * Base Name is always the name of the struct it will be verbing
    * Verb
        *  Allowed Verbs: Create, Get, Delete, Update

```go
package models

//Maps to UserCreate() in the api package
func CreateUser() {
	...
}

//Maps to UserGet() in the api package
func GetUser() {
	...
}

//Maps to UsersIndex() in the api package
func GetUsers() {
	...
}
```

* The one exception to this rule is {Base Name}Index maps to Get{Base Name}s.
  (This is mostly for legacy reasons, and we should try to avoid this in the
  future.)

## Guides

* Generally strive for short functions (5 lines is optimal). If you can't see your
  whole function on one screen, the function is too long.
* If you break up an argument list, keep the arguments on their own lines and
  closing parenthesis on its own line.
* The compiler will complain, but remember to add the trailing comma to the last
  parameter line.  It is required by golang.
* On multi-line function calls, put the first parameter on its own line, not on
  the same line as the function call. Ex:

```go
// BAD
func doSomething(param1, param2) {
	// something is done here...
}

doSomething("one",
	        "two")

// GOOD
func doSomething(param1, param2) {
	// something is done here...
}

doSomething(
	"one",
	"two",
)
```

* In the same vein, in function definitions that span more than one line, also put
  the first parameter on the first line, not on the same line as the function name. Ex:

```go
// BAD
func doSomething(param1,
	                 param2) {
  // something is done here...
}

// GOOD
func doSomething(
	param1,
	param2,
) {
	// something is done here...
}
```

* Again, `go fmt` is your friend here.  Running it will prevent you from making a lot
  of these types of style errors in the first place.

### Tests

When writing tests, try to make use of the [testify](https://godoc.org/github.com/stretchr/testify)
library as much as is practical.  It makes for easier-to-read test code, and has a
number useful utility methods.  Some advice though:

* Avoid over-use of `require.*`.  Only use `require` methods if you want to quit
  the test completely on failure.  Otherwise stick to `assert`, which allows the
  rest of your test to complete.
* When checking for errors, or lack of errors, use the `NoError` or `Error` methods
  instead of just checking for `nil`.
* There are a *lot* of utility methods in the library, don't be afraid to explore
  it.
