---
title: Ready, Set, GO!
---

You might have heard of [Go](http://golang.org/) (or golang, as it were) by now. It's the primary technology that underlies [Docker](docker.io), lots of companies have been switching their APIs over to it, and it's created some traction in the open source community. As we continue to build out Charityware, we are choosing which services need which language to best work within our architecture.

We're big fans of The JavaScript &trade; and most of stack is written in Javascript of one variation or another. JavaScript is a beautiful but oft-misunderstood language. Lots of people from the older styles and worlds of software development like to think of it as a toy, but it's far from that. But I digress -- not looking to get into a language war here.

As much as we love JavaScript, we understand it's not always the perfect solution for a problem. It suits most of our needs for asynchronous, event-driven programs that scale easily and allow for high developer productivity. But when it came to writing some of our front-facing services that tend to require heavier utilization of container resources, we wondered if there was an alternative.

One of our primary aims is to create services that scale easily but can also utilize the lowest end of available cloud resources out there. Without getting caught up in the "optimize first" fallacy, we want to ensure we're squeezing as much performance out of our machines as we can.

So we started to look around and see what our options might be. We weren't really wanting to hop into the python or rails worlds, as fun as they are/can be. We also weren't set on switching entirely away from JavaScript unless there was a clearly superior alternative for our situation and scale.

As it turns out, we happen to have been hearing a lot about Go due to the curious phenomenon of JS/Node developers either switching over to Go (a lá [TJ Holowaychuk](https://medium.com/@tjholowaychuk/farewell-node-js-4ba9e7f3e52b)) or dabbling in it from time to time. So, we thought "what the heck? Let's give it a shot."

##Go, A Way
We first started dipping into the syntax and characteristics of Go and it didn't feel quite right. It felt very C/C++, but with less syntax  gobbledygook. There were other differences, including pointers (but the sane kind), compilation, these great things called slices (associative arrays that can you can grow), and other small quirks.

Dependencies are definitely handled differently in Go; you have to set a GOPATH environment variable on your system and there doesn't seem to be any npm-esque grand repository. It's not terribly difficult to install things, but ease-of-availability for tools & dependency management does seem to be an area where the Go community will grow in the future.

But, all that notwithstanding, we kept learning and toiling away, determined to give Go a chance. And we found several things that made us smile and start to think "this isn't so bad after all." For one, unused variables/packages are an error in Go. I know that's not the sexiest of features (like concurrency), but it showed a thoughtfulness in language design that pleasantly surprised us and we started to see elsewhere, too.

There's also the lovely `go get <git url>` command that you use to install things. Perhaps it's the epic readability and sense of the command, but I found myself thinking "that's perfect!" I suppose it's not a characteristic of the language, but delightful nonetheless.

The syntax of Go is definitely a welcome improvement to some of the other C-style languages; it tends to help you create code that's more readable on the whole, even though it takes some initial getting used-to. For example, there's the short assignment operator `:=`, which does allocation and assignment in one fell swoop.

There are also slices, structs, receivers, and other unique/new-ish things in Go that make thinking through things easier on the whole. We could spend a long time going over the subtleties of Go; check out the end of the article for some further reading.

Here's some example code in Go:
{% highlight go linenos %}
//Every program has to have a package

package main

// Importing is really straightforward
import 'fmt'

func main() {
    fmt.Printf('Charityware super cool\n')
}

// Receivers allow us to use methods more easily and work with
// pointers automatically

func (c *Circle) area() float64 {
  return math.Pi * c.r*c.r
}

// Multiple return values
func learnMultiple(x, y int) (sum, prod int) {
    return x + y, x * y // Return two values.
}
{% endhighlight %}

###Let's Get to Wrk
There's also one more thing about Go we *really* like: speed. It's fast. You'd expect that with a compiled language, but we were still pleasantly surprised even *with that expectation*. Add that to the ways that it's designed to help facilitate use with large teams and a lot of it's improvements over other compiled languages and you have a pretty sweet engine to run your API.

How fast is it, exactly? One of the tools we use to test our service performance locally is [wrk](https://github.com/wg/wrk). It's a great way to generate a lot of stress on a server locally or in staging. Make sure to use it with er, wisdom ;)

Without getting into really serious benchmarking, we ran a couple tests on our Go-driven service and some one our existing API. We ran each test for 30s, with 100 open connections and 100 threads dedicated to the test. Everything was local. We know, we know, it's really not a real-world test. But with this we just wanted to see what the "best of all possible worlds" scenario was for each different technology. The results blew us away:

**[Express](expressjs.com) API running w/ Mongodb & Redis locally:**

`$ wrk http://localhost:8080/give/foo/bar -c100 -t100 -d30s`

37324 requests in 30.10s, 24.56MB read

Requests/sec:   1239.81
Transfer/sec:    835.42KB

**Go running [Gin](https://github.com/gin-gonic/gin):**

`$ wrk http://localhost:8080/ -c100 -t100 -d30s`

715042 requests in 30.06s, 130.93MB read

Requests/sec:  23789.21
Transfer/sec:   4.36MB

<br/>
**715,042** requests vs **37,324** requests

We were able to get a quote from Go after the tests:

<!-- <img width="100%" src="/content/images/2015/07/is8K8zY.gif" /> -->
![](https://s3-us-west-2.amazonaws.com/charityware-resources/blogs/engineering/images/is8K8zY.gif)


##More Resources
- [Learnxinyminutes](http://learnxinyminutes.com/docs/go/)
- [Golang-book](https://www.golang-book.com/books/intro/1)
- [awesome-go](https://github.com/avelino/awesome-go)
