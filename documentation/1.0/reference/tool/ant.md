---
layout: reference
title: Ceylon Ant tasks
tab: documentation
unique_id: docspage
author: Tom Bentley
doc_root: ../../..
---

# #{page.title}

## Usage 

Before using any of the Ceylon [Ant](http://ant.apache.org) 
tasks they need to be declared using a `<typedef>`:

<!-- lang: xml -->
    <property name="ceylon.ant.lib" value="${ceylon.home}/lib/ceylon.ant.jar"/>
    
    <target name="ceylon-ant-taskdefs">
      
      <!-- Create a path to the ceylon-ant.jar --> 
      <path id="ant-tasks">
        <pathelement location="${ceylon.ant.lib}"/>
      </path>
    
      <!-- use a typedef to define all the ceylon tasks together -->
      <typedef resource="com/redhat/ceylon/ant/antlib.xml" classpathref="ant-tasks"/>
      
    </target>

**Note:** Prior to M5 we used a `<taskdef>`, but such usage is now deprecated.

### Common nested elements

#### `<reposet>`

<!-- m5 -->

A `<reposet>` element contains a number of `<repo>` and/or `<reposet>` elements. 
It can be defined at the 
top level, and then used by reference in `<ceylon-*>` tasks so you don't have 
to repeat the same list of repositories all the time. 

Here's an example from the `build.xml` for the Ceylon SDK:

<!-- lang: xml -->
    <reposet id="reposet.compile.test">
        <repo url="${out.repo}"/>
        <repo url="test-deps"/><!-- Needed for h2.jar dep of test.ceylon.dbc -->
    </reposet>
    
    <!-- ... -->
    
    <target name="compile-test-jvm" depends="compile-jvm">
        
        <ceylon-compile executable="${ceylon.executable}"
            src="test-source"
            out="${test.repo}"
            verbose="${ceylon.verbosity}"
            encoding="UTF-8">
            <reposet refid="reposet.compile.test"/> 
            <moduleset refid="modules.test.jvm"/>
        </ceylon-compile>
        
    </target>


#### `<moduleset>`

A `<moduleset>` element contains a number of `<sourcemodules>` and/or `<module>`
elements. It can be defined at the 
top level, and then used by reference in `<ceylon-*>` tasks so you don't have 
to repeat the same list of modules all the time. 

A `<sourcemodules>` element simply includes all the ceylon modules in a given 
source directory, as specified by its `dir` attribute. This saves you having 
to explicitly list all the modules to be compiled, you can instead just 
compile all the modules in a given directory.

Here's an example using `<moduleset>`/`<sourcemodules>`from the 
`build.xml` for the Ceylon SDK:

<!-- lang: xml -->
    <moduleset id="modules.sdk.jvm">
        <sourcemodules dir="source"/>
    </moduleset>
    
    <!-- ... -->
    
    <target name="compile-jvm">
        
        <ceylon-compile executable="${ceylon.executable}"
            verbose="${ceylon.verbosity}"
            encoding="UTF-8">
            <moduleset refid="modules.sdk.jvm"/>
        </ceylon-compile>
        
    </target>

A `<module>` element must specify a name, and may specify a version. 
If the relevant ceylon task don't require a version it will be ignored. 

Here's an example using `<moduleset>`/`<module>`from the 
`build.xml` for the Ceylon SDK:

<!-- lang: xml -->
    <moduleset id="modules.sdk.js">
        <module name="ceylon.test"/>
        <module name="ceylon.collection"/>
        <module name="ceylon.json"/>
    </moduleset>

    <!-- ... -->
    
    <target name="compile-js">
        
        <ceylon-compile-js executable="${ceylon.executable}"
            verbose="${ceylon.verbosity}"
            encoding="UTF-8">
            <moduleset refid="modules.sdk.js"/>
        </ceylon-compile-js>
        
    </target>


## See also

* The [`<ceylon-compile>`](../ant-ceylon-compile) task
* The [`<ceylon-doc>`](../ant-ceylon-doc) task
* The [`<ceylon-run>`](../ant-ceylon-run) task
* The [`<ceylon-compile-js>`](../ant-ceylon-compile-js) task
* The [`<ceylon-run-js>`](../ant-ceylon-run-js) task
