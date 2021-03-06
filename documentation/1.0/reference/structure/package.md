---
layout: reference
title: Packages
tab: documentation
unique_id: docspage
author: Tom Bentley
milestone: Milestone 1
---

# #{page.title}

In Ceylon, a *package* is a collection of top-level declarations
contained in one or more 
[compilation units](../compilation-unit) together with an 
optional [package descriptor](#descriptor).

## Usage 

An simple example [package descriptor](#descriptor):

<!-- check:none -->
<!-- try: -->
    shared package com.example.foo.bar;
    
This would be in the source file 
`<source-dir>/com/example/foo/bar/package.ceylon` where `<source-dir>` is the
directory containing ceylon source code, conventionally `source`.

## Description

### Members

A package's members are the top-level declarations contained in its 
[compliation unit(s)](../compilation-unit).

### Descriptor

The 
[package descriptor](\#{site.urls.apidoc_current}/descriptor/class_Package.html) 
holds metadata about the package and is declared in a source file called
`package.ceylon` in the package being described. Here's an example

<!-- check:none -->
<!-- try: -->
    doc "An example package"
    shared package com.example.foo.bar;
    
The `package` declaration may be preceeded by [annotations](../annotation), including:

* [`shared`](#{site.urls.apidoc_current}/#shared) 
  to allow the package to be visible outside its containing module,
* [`doc`](#{site.urls.apidoc_current}/#doc) 
  to let you to specify package-level documentation,
* [`by`](#{site.urls.apidoc_current}/#by) 
  to document the package's author or authors. 

The package declaration consists of the `package` keyword followed by the 
package name.

It is common not to have a package descriptor if the package is not `shared`.

## See also

* Packages are contained in [modules](../module)
* Packages contains [compilation units](../compilation-unit)
* `ceylon doc` documentation of the 
  [Package](\#{site.urls.apidoc_current}/descriptor/class_Package.html) 
  type (the type of the package descriptor)
