# CORS ego's middleware

[![Build Status](https://travis-ci.org/go-ego/cors.svg)](https://travis-ci.org/go-ego/cors)
[![codecov](https://codecov.io/gh/go-ego/cors/branch/master/graph/badge.svg)](https://codecov.io/gh/go-ego/cors)
[![Go Report Card](https://goreportcard.com/badge/github.com/go-ego/cors)](https://goreportcard.com/report/github.com/go-ego/cors)
[![GoDoc](https://godoc.org/github.com/go-ego/cors?status.svg)](https://godoc.org/github.com/go-ego/cors)
[![Join the chat at https://gitter.im/go-ego/ego](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/go-ego/ego)

Ego middleware/handler to enable CORS support, based on [gin-cors](https://github.com/gin-contrib/cors).

## Usage

### Start using it

Download and install it:

```sh
$ go get github/go-ego/cors
```

Import it in your code:

```Go
import "github.com/go-ego/cors"
```

### Canonical example:

```Go
package main

import (
	"time"

	"github.com/go-ego/cors"
	"github.com/go-ego/ego"
)

func main() {
	router := ego.Default()
	// CORS for https://foo.com and https://github.com origins, allowing:
	// - PUT and PATCH methods
	// - Origin header
	// - Credentials share
	// - Preflight requests cached for 12 hours
	router.Use(cors.New(cors.Config{
		AllowOrigins:     []string{"https://foo.com"},
		AllowMethods:     []string{"PUT", "PATCH"},
		AllowHeaders:     []string{"Origin"},
		ExposeHeaders:    []string{"Content-Length"},
		AllowCredentials: true,
		AllowOriginFunc: func(origin string) bool {
			return origin == "https://github.com"
		},
		MaxAge: 12 * time.Hour,
	}))
	router.Run()
}
```

### Using DefaultConfig as start point

```Go
func main() {
	router := ego.Default()
	// - No origin allowed by default
	// - GET,POST, PUT, HEAD methods
	// - Credentials share disabled
	// - Preflight requests cached for 12 hours
	config := cors.DefaultConfig()
	config.AllowOrigins = []string{"http://google.com"}
	config.AddAllowOrigins("http://facebook.com")
	// config.AllowOrigins == []string{"http://google.com", "http://facebook.com"}

	router.Use(cors.New(config))
	router.Run()
}
```

### Default() allows all origins

```Go
func main() {
	router := ego.Default()
	// same as
	// config := cors.DefaultConfig()
	// config.AllowAllOrigins = true
	// router.Use(cors.New(config))
	router.Use(cors.Default())
	router.Run()
}
```
