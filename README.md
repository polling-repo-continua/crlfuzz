# crlfuzz

[![License](https://img.shields.io/badge/license-MIT-_red.svg)](https://opensource.org/licenses/MIT)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/dwisiswant0/crlfuzz/issues)

A fast tool to scan CRLF vulnerability written in Go

<img src="https://user-images.githubusercontent.com/25837540/90128972-fc3bdf00-dd91-11ea-8c3b-0d6f4e8c6ba3.png" height="350">

---

## Resources

- [Installation](#installation)
	- [from Binary](#from-binary)
	- [from Source](#from-source)
	- [from GitHub](#from-github)
- [Usage](#usage)
	- [Basic Usage](#basic-usage)
	- [Flags](#flags)
	- [Target](#target)
		- [Single URL](#single-url)
		- [URLs from list](#urls-from-list)
		- [from Stdin](#from-stdin)
	- [Method](#method)
	- [Data](#data)
	- [Adding Headers](#adding-headers)
	- [Using Proxy](#using-proxy)
	- [Concurrency](#concurrency)
	- [Silent](#silent)
	- [Verbose](#verbose)
	- [Version](#version)
	- [Library](#library)
- [Help & Bugs](#help--bugs)
- [License](#license)
- [Version](#version)

## Installation

### from Binary

The installation is easy. You can download a prebuilt binary from [releases page](https://github.com/dwisiswant0/crlfuzz/releases), unpack and run! or with

```bash
▶ curl -sSfL dw1.io/crlfuzz.sh | sh -s -- -b /usr/local/bin
```

### from Source

If you have go1.13+ compiler installed and configured:

```bash
▶ GO111MODULE=on go get -v dw1.io/crlfuzz/cmd/crlfuzz
```

In order to update the tool, you can use `-u` flag with go get command.

### from GitHub

```bash
▶ git clone https://github.com/dwisiswant0/crlfuzz
▶ cd crlfuzz/cmd/crlfuzz
▶ go build .
▶ mv crlfuzz /usr/local/bin
```

## Usage

### Basic Usage

Simply, CRLFuzz can be run with:

```bash
▶ crlfuzz -u "http://target"
```

### Flags

```bash
▶ crlfuzz -h
```

This will display help for the tool. Here are all the switches it supports.

| Flag             	| Description                                    	|
|------------------	|------------------------------------------------	|
| -u, --url        	| Define single URL to fuzz                      	|
| -l, --list       	| Fuzz URLs within file                          	|
| -X, --method     	| Specify request method to use _(default: GET)_ 	|
| -d, --data       	| Define request data                            	|
| -H, --header     	| Pass custom header to target                   	|
| -x, --proxy      	| Use proxy to fuzz                              	|
| -c, --concurrent 	| Set the concurrency level _(default: 20)_      	|
| -s, --silent     	| Silent mode                                    	|
| -v, --verbose    	| Verbose mode                                   	|
| -V, --version    	| Show current CRLFuzz version                   	|
| -h, --help       	| Display its help                               	|

### Target

You can define a target in 3 ways:

#### Single URL

```bash
▶ crlfuzz -u "http://target"
```

#### URLs from list

```bash
▶ crlfuzz -l /path/to/urls.txt
```

#### from Stdin

In case you want to chained with other tools.

```bash
▶ subfinder -d target -silent | httpx -silent | crlfuzz
```

### Method

By default, CRLFuzz makes requests with `GET` method.
If you want to change it, you can use the `-X` flag.

```bash
▶ crlfuzz -u "http://target" -X "GET"
```

### Data

If you want to send a data request using POST, DELETE. PATCH or other methods, you just need to use `-d` flag.

```bash
▶ crlfuzz -u "http://target" -X "POST" -d "data=body"
```

### Adding Headers

May you want to use custom headers to add cookies or other header parts.

```bash
▶ crlfuzz -u "http://target" -H "Cookie: ..." -H "User-Agent: ..."
```

### Using Proxy

Using a proxy, this can also be useful if Google or other engines meet Captcha.

```bash
▶ crlfuzz -u "http://target" -x http://127.0.0.1:8080
```

### Concurrency

Concurrency is the number of fuzzing at the same time. Default value CRLFuzz provide is 20, you can change it by using `-c` flag.

```bash
▶ crlfuzz -l /path/to/urls.txt -c 25
```

### Silent

If you activate this silent mode with the `-s` flag, you will only see vulnerable targets.

```bash
▶ crlfuzz -l /path/to/urls.txt -s | tee vuln-urls.txt
```

### Verbose

Unlike silent mode, it will display error details if there is an error with the `-v` flag.

```bash
▶ crlfuzz -l /path/to/urls.txt -v
```

### Version

To display the current version of CRLFuzz with the `-V` flag.

```bash
▶ crlfuzz -V
```

### Library

You can use CRLFuzz as a library.

```go
package main

import (
	"fmt"

	"dw1.io/crlfuzz/pkg/crlfuzz"
)

func main() {
	target := "http://target"
	method := "GET"

	// Generates a potentially CRLF vulnerable URLs
	for _, url := range crlfuzz.GenerateURL(target) {
		// Scan againts target
		vuln, err := crlfuzz.Scan(url, method, "", []string{}, "")
		if err != nil {
			panic(err)
		}

		if vuln {
			fmt.Printf("VULN! %s\n", url)
		}
	}
}
```

## Help & Bugs

If you are still confused or found a bug, please [open the issue](https://github.com/dwisiswant0/crlfuzz/issues). All bug reports are appreciated, some features have not been tested yet due to lack of free time.

## License

**CRLFuzz** released under MIT. See `LICENSE` for more details.

## Version

**Current version is 0.0.2** and still development.