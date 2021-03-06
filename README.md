# Rate limiting for go-redis

[![Build Status](https://travis-ci.org/go-redis/redis_rate.svg?branch=master)](https://travis-ci.org/go-redis/redis_rate)
[![GoDoc](https://godoc.org/github.com/go-redis/redis_rate?status.svg)](https://godoc.org/github.com/go-redis/redis_rate)

This package is based on [rwz/redis-gcra](https://github.com/rwz/redis-gcra) and implements [GCRA](https://en.wikipedia.org/wiki/Generic_cell_rate_algorithm) for rate limiting based on Redis. The code requires Redis version 3.2 or newer since it relies on [replicate_commands](https://redis.io/commands/eval#replicating-commands-instead-of-scripts) feature.

```go
package redis_rate_test

import (
	"fmt"
	"time"

	"github.com/go-redis/redis/v7"
	"github.com/go-redis/redis_rate/v8"
)

func ExampleNewLimiter() {
	rdb := redis.NewClient(&redis.Options{
		Addr: "localhost:6379",
	})
	_ = rdb.FlushDB().Err()

	limiter := redis_rate.NewLimiter(rdb, &redis_rate.Limit{
		Burst:  10,
		Rate:   10,
		Period: time.Second,
	})
	res, err := limiter.Allow("project:123")
	if err != nil {
		panic(err)
	}
	fmt.Println(res.Allowed, res.Remaining)
	// Output: true 9
}
```
