# Static Code Analysis

[![Build Status](https://travis-ci.org/Monits/static-code-analysis-plugin.svg?branch=development)](https://travis-ci.org/Monits/static-code-analysis-plugin)
[![Download](https://api.bintray.com/packages/monits/monits-android/static-code-analysis-plugin/images/download.svg) ](https://bintray.com/monits/monits-android/static-code-analysis-plugin/_latestVersion)

Static Code Analysis wraps around Checkstyle, Findbugs, PMD and CPD, offering new features
and extensions to the encapsulated plugins, making it easier to use them and providing
better results with minimum effort.

Out of the box, with just applying the plugin you get:
 * Improved build performance. Findbugs and PMD will ignore changes to autogenerated
Android files (``R.class``, ``Manifest.class`` and ``BuildConfig.class``)
 * Android Lint task is reconfigured to detect when it's up-to-date and cache results
 * Better PMD and Findbugs analysis, since both will depend on *mockableAndroidJarTask*,
and look into Android's SDK classes when performing analysis.
 * More detectors, by automatically including:
   * [fb-contrib](https://github.com/mebigfatguy/fb-contrib)
   * [Monits Findbugs](https://github.com/Monits/findbugs-plugin)
 * Automatically use the latest available version of each tool compatible with the
used Gradle version. Just updating Gradle will introduce newer tools.
 * Checkstyle and Findbugs support remote file configuration, which they normally don't.
 * Easy configuration through DSL.
 * Easily and reliably include [custom Android Lint rules](http://tools.android.com/tips/lint-custom-rules) accross teams and CI servers.
 * Backporting of fixes and improvements from later Gradle versions (in case you can't update)
 * Fully compatible with the use of Android's build cache.

# Adding it to your project

We are on [the Grade Plugin Portal](https://plugins.gradle.org/plugin/com.monits.staticCodeAnalysis), so you can simply do:

```
plugins {
  id 'com.monits.staticCodeAnalysis' version '2.6.6'
}
```

or, you could also do

```
buildscript {
  repositories {
    maven {
      url 'https://plugins.gradle.org/m2/'
    }
  }
  dependencies {
    classpath 'com.monits:static-code-analysis-plugin:2.6.6'
  }
}

apply plugin: 'com.monits.staticCodeAnalysis'
```

or, directly from jcenter

```
buildscript {
  repositories {
    jcenter()
  }
  dependencies {
    classpath 'com.monits:static-code-analysis-plugin:2.6.6'
  }
}

apply plugin: 'com.monits.staticCodeAnalysis'
```

The plugin is compatible with Gradle 2.3+, Gradle 3.0+ and Gradle 4.0+. We are commited to supporting at least the last 2 major gradle versions.

It supports all versions of the Android plugin from 1.1.0 onwards, up to 3.0.0-beta4.


## DSL

Configuring Static Code Analysis is very simple and intuitive thanks to its DSL. You can choose
which encapsulated plugin to run and set its configuration files. Here is a quick example

```
staticCodeAnalysis {
    findbugs = true
    checkstyle = true
    pmd = true
    cpd = true
    androidLint = true // Since 2.2.0

    ignoreErrors = true // Since 1.3.0

    // default rules
    findbugsExclude = "$project.rootProject.projectDir/config/findbugs/excludeFilter.xml"
    checkstyleRules = 'https://raw.githubusercontent.com/Monits/static-code-analysis-plugin/staging/defaults/checkstyle/checkstyle-cache.xml'
    pmdRules = [ 'https://raw.githubusercontent.com/Monits/static-code-analysis-plugin/staging/defaults/pmd/pmd.xml',
        'https://raw.githubusercontent.com/Monits/static-code-analysis-plugin/staging/defaults/pmd/pmd-android.xml' ]

    // Since 2.2.0
    androidLintConfig = 'https://raw.githubusercontent.com/Monits/static-code-analysis-plugin/staging/defaults/android/android-lint.xml'

    // Since 2.0.0
    sourceSetConfig {
        test { // or the name of any other sourceset
            // use a more relaxed ruleset
            checkstyleRules = 'config/checkstyle/test-checkstyle.xml'
            findbugsExclude = 'config/findbugs/test-findbugs.xml'
            pmdRules = [ 'config/pmd/test-pmd.xml',
                'https://raw.githubusercontent.com/Monits/static-code-analysis-plugin/staging/defaults/pmd/pmd-android.xml' ]
        }
    }
}
```

There are things to consider though, like running plugins are always set to ``true`` by default.
All configurations values in the example are the default ones, but you must take notice of their types;
``findbugsExclude`` and ``checkstyleRules`` are a ``String`` (Note: for remote files, they must
begin with "http://" or "https://", else it will be considered local) and ``pmdRules`` is a
collection of ``String``.

Rules used by PMD, Findbugs and Checkstyle can be overriden per-sourceset under the ``sourceSetConfig`` block.

To include custom lint rules, you can simply include the jars as dependencies under `androidLint`.
For instance, you could include [Monits' Android Linters](https://github.com/monits/android-linters) by adding:

```
dependencies {
    androidLint 'com.monits:android-linters:1.+'
}
```

## Tasks

The plugin will add the following tasks:

* `cpd`
* `checkstyle`, depends on:
  * a `checkstyleSourcesetname` task per source-set (`checkstyleMain`, `checkstyleTest`, so on).
* `findbugs`, depends on:
  * a `findbugsSourcesetname` task per source-set (`findbugsMain`, `findbugsTest`, so on).
* `pmd`, depends on:
  * a `pmdSourcesetname` task per source-set (`pmdMain`, `pmdTest`, so on).

All tasks, are hooked to be run as part of the `check` task of the Java Plugin.

# Contributing

As always feel free to contribute in any shape or form, we look forward to your feedback!.

## Suppressing warnings

This is **not** the trick to break the rules but this **is** the way you should face those **few exceptions** to the rules.
Since every tool has its own mechanism, you should refer to its documentation:

- Android Lint: [Documentation](http://tools.android.com/tips/lint/suppressing-lint-warnings)
- Checkstyle: [Documentation](http://checkstyle.sourceforge.net/config_annotation.html#SuppressWarnings)
- CPD: [Documentation](https://pmd.github.io/latest/pmd_userdocs_cpd.html#suppression) 
- Findbugs: [Annotations documentation](http://findbugs.sourceforge.net/manual/annotations.html); @SupressFBWarnings [javadoc](http://findbugs.sourceforge.net/api/edu/umd/cs/findbugs/annotations/SuppressFBWarnings.html) 
- PMD: [Documentation](https://pmd.github.io/latest/usage/suppressing.html)


# Copyright and License
Copyright 2010-2017 Monits S.A.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use
this work except in compliance with the License. You may obtain a copy of the
License at:

http://www.apache.org/licenses/LICENSE-2.0

