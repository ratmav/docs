go
==

## version management

* *nix: install [asdf](https://asdf-vm.com/)
    * [asdf go plugin](https://github.com/kennyp/asdf-golang): `asdf plugin-add golang https://github.com/kennyp/asdf-golang.git`

## module management

run the following when imports are modified.

```shell
$ go mod init example.com/hello # run once when creating a new project/module. you may need to use the full path to the repo, for example "example.com/foo/bar/hello.git"
$ go mod vendor # creates the vendor folder for dependencies.
$ go mod download # downloads module dependencies, including indirects, to the vendor folder.
$ go mod tidy # cleans up modules based on current imports. run after importing new external packages.
$ go mod verify # confirms all modules are downloaded, checksums are correct, no missing packages, etc.
$ go clean --modcache # run when you want to wipe out all local vendored modules. probably a good idea to manually remove the vendor folder first.
```

### updating vendored modules

typically, you just `go get -u the.domain/foo/bar/baz/package_name` to install or update a vendored package. by default, `go mod` will attempt to pull **all** modules down, which you might not want or need (like in a ci/cd container). in that case, when you _just_ want to pull down a single specific package, use the `-f` flag: `go get -f -u the.domain/foo/bar/baz/package_name`.

### using local modules

edit `go.mod` to point at your module's local working copy:

```go
module example.com/localmodexample

go 1.13

require (
   example.org/hello v0.0.0
   example.org/utils v0.0.0

)

replace (
   example.org/hello => ./hello
   example.org/utils => ./utils
)
```

## pre-push checks

```shell
$ go fmt
$ go test
$ go vet
$ golint # or whatever linter the project is using.
```

## tools

some packages you may find useful; don't add these to `go.mod`:

* [delve](https://github.com/go-delve/delve)
    * `go get github.com/go-delve/delve/cmd/dlv`
* [gore](https://github.com/motemen/gore)
    * `go get -u github.com/motemen/gore/cmd/gore`

## snippets

### pretty print json from struct

```go
import "fmt"

type Foo struct {
	ID  string    `json:"id"`
	Woo string    `json:"woo"`
	Lad string    `json:"lad"`
}

newFoo := Foo{}
newFoo.ID = "D34DB33F"
newFoo.Woo = "goodTimes.jpg"
newFoo.Lad = "Ric Flair"

foo, _ := json.MarshalIndent(newFoo, "", "  ")
fmt.Printf("%s\n", foo)
```

### fp shenanigans

there may or may not be a handy way to hang this on appropriate types (int, maps, etc.), to enable things like:

```go
var foo []int{1, 2, 3}
foo.filter(isEven)
```

#### integer `filter`

```go
package main

import (
	"fmt"
)

func isEven(element int) bool {
	if element%2 == 0 {
		return true
	}
	return false
}

/*
 note: i indexes the starting array, j indexes the final array. the trick here is that
       the element at i is overwritten with the element at j, pushing the filtered elements
       to the front of the array, x. when the last element in x is reached, all of the
       filtered elements are at the front of x, and j is the position of the last valid
       element, so slicing x at the j index returns the filtered array.

*/
func filter(x []int, ff func(int) bool) []int {
	// initialize j
	j := 0

	// iterate over the array
	for i := 0; i < len(x); i++ {

		// test each element using the function parameter ff.
		if (ff(x[i])) {
		        // if the filter function returns true, overwrite x at position j with said element
			x[j] = x[i]

			// increment j
			j++
		}
	}

	// slice x up to the j index (half open range), and overwrite x with the slice.
	x = x[:j]
        return x
}

// given an array of integers, filter odd integers in place.
func main() {
	var x = []int{90, 15, 81, 87, 47, 59, 81, 18, 25, 40, 56, 8}

	fmt.Println(filter(x, isEven))
}
```

#### integer `map`

```go
func square(num int) int {
    return num * num
}

func mapper(f func(int) int, alist []int) []int {
    var a = make([]int, len(alist), len(alist))
    for index, val := range alist {

        a[index] = f(val)
    }
    return a
}

func main() {
    alist := []int{4, 5, 6, 7}
    result := mapper(square, alist)
    fmt.Println(result)

}
```
