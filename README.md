# Your own Go Playground

The [Go Playground][2] is a pretty good way to learn about [Go][1] programs
without actually installing [Go][1]. You can set up identical playgrounds
easily using the [golang/playground][3] repository at Github.

## What's wrong with the Go Playground?

Nothing really. The official [Go Playground][1] runs on a [sandbox][5], which
is the recommened way of doing things like executing code from untrusted
sources, since it minimizes the risk of crackers abusing the system.

However, if you ever need to showcase features that require network, a real
filesystem, CGO or any kind of thing not supported or actively restricted by
[NaCL](https://developer.chrome.com/native-client) you will be out of luck.

This alternate Playground offers you more flexibility on that front, you can
choose when to use the sandbox and when not to.

## So this is like the Go Playground_

Yes, it's actually a fork of it. You can see a live example of this
playground here: https://play.golang.mx.

## webapp

This is like the
[play.golang.org](https://github.com/golang/playground/tree/master/app) web
app, except that it:

* Does not depend on [appengine](https://cloud.google.com/appengine/docs/go/reference).
* Uses [boltdb](https://github.com/boltdb/bolt) to save data.
* Can be configured to communicate with any other Go Playground service (the
  one that compiles and runs Go code), including the official one.

See a live example of this playground at
[https://play.golang.mx/](https://play.golang.mx/).

You can build and run a Go Playground like this:

```
cd webapp
go build
./webapp -allow-share
# 2016/01/03 08:26:23 Serving Go playground at :3000...
```

This will create a local server that uses the official Go Playground service to
build and run Go code. See the main page at http://127.0.0.1:3000.

![screen shot 2016-01-03 at 8 12 25 am](https://cloud.githubusercontent.com/assets/385670/12079146/1de8c24a-b1f4-11e5-87b9-10f0a22054e5.png)

Love those nice [live
examples](https://golang.org/pkg/strings/#example_Contains) on the golang.org
site? You can also run them using this Playground:

![screen shot 2016-01-03 at 8 12 50 am](https://cloud.githubusercontent.com/assets/385670/12079219/9fd19f14-b1f6-11e5-949e-f36561a7f0ff.png)

See a local example at http://127.0.0.1:3000/example or a live one at
https://play.golang.mx/example.


If you're into [Docker][4] you can create and run a container like this:

```
cd webapp
make docker
make run
```

See the available commands with `-h`:

```
./webapp -h
  -allow-share
        Allow storing and sharing snippets.
  -db string
        Database file. (default "playground.db")
  -disable-cache
        Disable cache.
  -h    Show help.
  -l string
        Listen address. (default ":3000")
  -o string
        Access-Control-Allow-Origin header. (default "*")
  -s string
        Sandbox service URL. (default "https://play.golang.org/compile?output=json")
```

## Too complicated, I came here for the embedded examples

If you're only insterested on embedding Go examples that run on the official
sandbox you can code something based on this snippet:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />

    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.8.2/jquery.min.js"></script>
    <link href="//play.golang.mx/static/example.css" rel="stylesheet" type="text/css" />
    <script src="//play.golang.mx/static/playground.js"></script>
    <script src="//play.golang.mx/static/snippets.js"></script>

    <script>
    goPlaygroundOptions({
      'shareRedirect': '//play.golang.mx/p/',
      'shareURL': '//play.golang.mx/share',
      'compileURL': '//play.golang.mx/compile',
      'fmtURL': '//play.golang.mx/fmt',
      'shareOpenNewWindow': true
    });
    </script>
  </head>
  <body>
    <textarea class="go-playground-snippet" data-title="Example (Hello Playground!)" autocorrect="off" autocomplete="off" autocapitalize="off" spellcheck="false">
package main

import (
&#09;"fmt"
)

func main() {
&#09;fmt.Printf("Hello Playground!")
}</textarea>
  </body>
</html>
```

## sandbox

If you don't want to use the Go Playground service for building and running Go
code you can also run your own service.

This sandbox is based on the original sandbox, it includes the same
restrictions we have in the original (NaCL, no network access, no real
filesystem, fake time, etc.).

The main difference is in [this
handler](https://github.com/xiam/go-playground/blob/62d009ae93973b450ac72d52d78bbab780803090/sandbox/sandbox.go#L51)
that now accepts form-data as well as JSON formatted data.

Build and run the sandbox like this:

```
cd sandbox
make docker
make run
```

You can point the Go Playground web app to this service using the `-s`
parameter:

```
cd webapp
./webapp -allow-share -s "http://localhost:8080/compile?output=json"
```

## unsafebox

I basically took all the security measures out of the playground and now I
offer you a dumbed down version of it which actually does not sandbox anything
and will put your life at risk.

This is a unrestricted linux/amd64 installation, you should not really use this
box unless you're absolutely sure you know what you're doing and you're aware
that no matter what you do, people will try to abuse the system and they will
probably succeed.

Ok, now that you're warned, you can (but shouldn't) build and run this
dangerous box like this:

```
cd unsafebox
make docker
make run
```

You can point the Go Playground web app to this service using the `-s`
parameter:

```
cd webapp
./webapp -allow-share -s "http://localhost:8080/compile?output=json"
```

[1]: https://www.golang.org/
[2]: https://play.golang.org/
[3]: https://github.com/golang/playground
[4]: https://www.docker.com/
[5]: https://en.wikipedia.org/wiki/Sandbox_(computer_security)