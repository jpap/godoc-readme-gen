<!-- DO NOT EDIT. -->
<!-- Automatically generated with https://go.jpap.org/godoc-readme-gen -->

# GoDoc README Markdown Generator

# Install

```shell
go install go.jpap.org/godoc-readme-gen
```
# Overview

Automatically generate a Markdown README for your Go project.

This tool creates a GitHub-flavored README.md using the same format as godoc.
It includes the package summary and generates badges for pkg.go.dev and
Travis CI.

This is a fork of James Frasche's project, found at
https://github.com/jimmyfrasche/autoreadme.

## What It Does
By default, `godoc-readme-gen` will read the Go package in the working
directory, and generate a `README.md` file.  If the README already exists, it
will not be overwritten without the `-f` flag.  You can specify the path to
the package directory as the final argument to the tool.

A template for the README is specified by the `-template` flag, and by
default it looks for a file named `.README.template.md` in the package
directory.  If the default template is not found, or an alternate is not
provided, the default template is used.

To view the default template, pass the `-print-template` function to dump it
to stdout.  You might redirect this output to a file so you may use it as the
basis for creating your own custom template.

## Lists and Bullets
Paragraphs that start with the text "1. ", "2. ", etc. are automatically
turned into lists by Markdown.  Paragraphs between list items are
automatically indented so that they appear as part of the same list item.
Similarly for bullets, that are paragraphs that start with the text "* ".

We assume the list and/or bullets continue until the end of the text section
(that is, until the next heading or end of document).  But sometimes you may
wish to "terminate" a list/bullet before then: to do this, insert a pseudo
heading "..." before the next paragraph.  The ellipses will not be inserted
into the README file.

The following example illustrates this concept:

```
// Example List
//
// 1. Apple
//
// An Apple a day keeps the doctor away.
//
// 2. Pear
//
// Not to be confused with "pair".
//
// ...
//
// This trailing paragraph is not indented, as it is not considered to be
// part of the above list.
```

## Automating README Generation
To track changes in your godoc, and ensure that your README is always kept up
to date, we recommend adding a `//go:generate` line to your Go package so
that you can easily re-generate the README via the `go generate` command-line
tool.

If you have one or more sub-packages in your project, you can add similar
`//go:generate` lines to each, and then regenerate all of the READMEs by
running `go generate ./...` from the top-level directory.

We recommend placing your high-level godoc comments in a separate project
file `ϟdocϟ.go` and a `//go:generate` line beneath the `package` declaration
as follows:

```go
// Package demo shows how you might structure a "ϟdocϟ.go" file.
package demo // import "corp.example.com/demo"

//go:generate godoc-readme-gen -f -title "Demo Usage of godoc-read-me-gen"
//  To install: `go install go.jpap.org/godoc-readme-gen`
```

Naming this file with the given Unicode "ϟ" character ensures `go generate`
generates your README **last**, because it processes files in sorted order.
This is important because `godoc-readme-gen` requires your Go project to
build without error (so it can parse the source code).  If other generators
have not yet run, or require regeneration (e.g. out-of-date `stringer`
files), your source code might not "compile" and `godoc-readme-gen` will
fail, stopping `go generate` from running the other generators.

Unfortunately Go source filenames are restricted to being ASCII or Unicode
letters, and limited to ASCII punctuation when using modules.  The allowed
punctuation characters "compare before" all of the ASCII letters, so we are
then forced to use a Unicode letter that always "compares after" all of the
ASCII letters.  We choose the ancient Greek letter koppa "ϟ" for this
purpose, because it "compares after" all Greek characters too!

## Examples
Create a README.md for the package in directory a/b/c, with `.Title` template
variable set to "A Great Package":

```
godoc-readme-gen -title "A Great Package" a/b/c
```

Overwrite the README.md in the current directory:

```
godoc-readme-gen -f
```

Copy the built-in template to a file for the creation of a new template:

```
godoc-readme-gen -print-template > .README.template.md
```

Generate using a custom template:

```
godoc-readme-gen -template path/to/my/readme.template.md
```

## Template Variables
The following variables are available in custom templates:

`.Name` Package name.

`.Title` The -title flag value, or package name if not provided.

`.Doc` Package-level documentation.

`.Synopsis` The first sentence from the .Doc variable.

`.ImportPath` Package import path.

`.RepoPath` The import path without the first path component. For example,
the import github.com/golang/go is represented as "golang/go".  This is
typically the path within the repo of the package.

`.Bugs` A []string of all bugs as per godoc.

`.Commands` A []string of import paths of all main packages.  In addition to
the directory provided to the tool, we also check cmd/* directories for
additional main packages.

`.Library` True if the package is not a main package.

`.Today` The current date in YYYY.MM.DD format.

`.Travis` True if there is a `.travis.yml` file in the package directory.

`.Examples` a map of Example with all examples from `*_test.go` files. These
can be used to include selective examples into the README.  The Example
struct has the following fields:

```
.Name    Name of the example
.Code    Rendered example code similar to godoc
.Output  Example output, if any
```




