# Snek

[![cacois](https://circleci.com/gh/cacois/snek.svg?style=svg)](https://app.circleci.com/pipelines/github/cacois/snek)

**Snek** is a very simple Go environment variable-based config management system, designed for docker/kubernetes use cases. Very small fangs.

## Why?

Go has some great config management tools, like [Viper](https://github.com/spf13/viper) - powerful, but complex. Managing precedence between config sources, many file formats, aliases - sometimes you need something simpler. 

Like many developers, I find myself working in a Docker/Kubernetes world more often than not. So, who needs config files? I just want environment variables and/or a [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/). This simplifies config management from within my app - all I really need is something that looks for my config values in environment variables, and allows me to set default values in case the environment variables aren't set. Fin.

I'm tired of writing this (admitttedly simple) logic into each of my apps, so here we are. :)

## Usage

Start by importing the library:

```go
import (
	"github.com/cacois/snek"
)
```

Snek is really simple. You can do two things. First, if you want to, you can set default values for a particular configuration parameter:

```go
snek.Default("MY_ENV_VAR", "mydefaultvalue")
```

I tend to put a big list of these in a file called `config.go`, because 1) its extremely convenient to be able to see the entire application config in a single place, and 2) I like to have default values for all config parameters - its handy when running the app in a default development mode:

config.go:
```go
package main

func init() {
    snek.Default("CONFIG_VAL_1", "value1")
    snek.Default("CONFIG_VAL_2", "value2")
    snek.Default("CONFIG_VAL_3", "value3")
    ...
}
```

Second, you can read your configuration values from environment variables:

```go
value := snek.Get("MY_ENV_VAR")
```

This will first look for and return the value of the environment variable `MY_ENV_VAR`. If this environment variable has not been set, snek will look for any default value you defined for `MY_ENV_VAR` and return that. If neither has been set, it will return an empty string.

One more option is to use `snek.GetOrError()` instead of `snek.Get()`:

```go
value, err := snek.GetOrError("MY_ENV_VAR")
```

This function behaves the exact same way as `snek.Get()`, except instead of returning an empty string when neither the specified environment variable or a default value has been defined, it throws an error. 

