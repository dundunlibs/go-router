# go-router
Yet another router for Go

## Why go-router?
Go itself is already super fast, so this package doesn't try to solve any performance issues. Instead, it is focused on providing a better and easier way to define routes in your router. This package was inspired by [React Router](https://reactrouter.com/).

go-router is a thin wrapper over `net/http`, it doesn't use any additional packages. So it's very simple and lightweight, no need to learn new thing.

## Installation
```bash
go get -u github.com/dundunlabs/go-router
```

## Usage
```go
package main

import (
	"encoding/json"
	"net/http"

	gorouter "github.com/dundunlabs/go-router"
)

var routes = []gorouter.Route{
	// group of routes
	{
		Path: "/api",
		Middleware: func(next gorouter.HandlerFunc) gorouter.HandlerFunc {
			return func(w http.ResponseWriter, r gorouter.Request) {
				// before
				// ...
				next(w, r)
				// after
				// ...
			}
		},
		Children: []gorouter.Route{
			// static route
			{
				Path:   "/hello",
				Method: http.MethodGet,
				Handler: func(w http.ResponseWriter, r gorouter.Request) {
					w.Write([]byte("hello world!"))
				},
			},
			// dynamic route
			{
				Path:   "/:resource/:id",
				Method: http.MethodGet,
				Handler: func(w http.ResponseWriter, r gorouter.Request) {
					params := r.Params()
					json.NewEncoder(w).Encode(params)
				},
			},
		},
	},
	// wildcard route
	{
		Path:   "/*",
		Method: http.MethodGet,
		Handler: func(w http.ResponseWriter, r gorouter.Request) {
			// do something
		},
	},
}

func main() {
	router := gorouter.New(routes)
	http.ListenAndServe(":8080", router)
}
```
---
Happy coding!
