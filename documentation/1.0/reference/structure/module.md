---
layout: reference
title: Modules
tab: documentation
unique_id: docspage
author: Tom Bentley
milestone: Milestone 1
doc_root: ../../..
---

# #{page.title}

In Ceylon, a *module* is a collection of [packages](../package) together with a 
[module descriptor](#descriptor) that serves as a unit of distribution.

## Usage 

An example module descriptor:

<!-- check:none -->
<!-- try: -->
    module com.example.foo '1.2.0' {
        import com.example.bar '3.4.1';
    }
    
This would be in the source file 
`<source-dir>/com/example/foo/module.ceylon` where `<source-dir>` is a
directory containing ceylon source code, conventionally `source`.

## Description

### Member packages

The names of the packages in a module must begin with the name of the module,
so for example in a module called `com.example.foo` all the the package names
must begin `com.example.foo.`.

### Descriptor

The module descriptor
holds metadata about the module and is declared in a source file called
`module.ceylon` in the base package of the module (that is the package whose 
name is the same as the module name). Here's an example:

<!-- check:none -->
<!-- try: -->
    doc "An example module."
    module com.example.foo '1.2.0' {
        import com.example.bar '3.4.1';
        import org.example.whizzbang '0.5';
    }

The `module` declaration may be preceeded by [annotations](../annotation), including:

* [`doc`](#{site.urls.apidoc_current}/#doc) 
  to let you to specify module-level documentation,
* [`license`](#{site.urls.apidoc_current}/#license) 
  to let you specify the module's license,
* [`by`](#{site.urls.apidoc_current}/#by) 
  to document the module's author or authors. 

The module declaration itself starts with the `module` keyword followed by the 
module name and version and a block of other declarations.

Each dependency of the module needs to be declared with an `import` declaration
specifying the module name of the dependency and its version. 

The `import` declarations can also be annotated. Two annotations particularly 
worth noting are:

* [`shared`](#{site.urls.apidoc_current}/#shared) to mark the 
  imported module as also being exported to clients of this module. If your 
  modules uses types from an imported modules in its API then the compiler 
  requires you to mark that module `shared` in your module descriptor.
* [`optional`](#{site.urls.apidoc_current}/#optional) to mark
  the imported module as being optional.

### Distribution

Modules are distributed in `.car` files, which are essentially `.jar` files 
with a different extension, and with a [module descriptor](#descriptor).

Modules are kept in a [module repository](../../repository). The list of module 
repositories to use is passed to 
[`ceylon compile`](#{page.doc_root}/reference/tool/ceylon/subcommands/ceylon-compile.html), 
[`ceylon run`](#{page.doc_root}/reference/tool/ceylon/subcommands/ceylon-run.html), and 
[other tools](#{page.doc_root}/reference/#tools)

## See also

* Modules contain [packages](../package)
* [Module repositories](../../repository)
* `ceylon doc` documentation of the 
  [Module](#{site.urls.apidoc_current}/descriptor/class_Module.html) 
  type (the type of the module descriptor)
