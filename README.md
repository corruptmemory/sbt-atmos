sbt-atmos
=========

[sbt] plugin for running [Typesafe Console][console] in development.


Add plugin
----------

This plugin requires sbt 0.12.

Add the sbt-atmos plugin to `project/plugins.sbt`. For example:

```scala
addSbtPlugin("com.typesafe.sbt" % "sbt-atmos" % "0.1.0")
```

Add the sbt-atmos settings to the project. For a `.sbt` build, add a line with:

```scala
atmosSettings
```

**Note:** *These settings need to come after the Akka library dependency
settings, to have the appropriate trace dependencies automatically added based
on the Akka version being used. Otherwise an extra dependency will need to be
added manually.*

For a full `.scala` build, add these settings to your project settings, after
the Akka library dependencies:

```scala
com.typesafe.sbt.SbtAtmos.atmosSettings
```

For example:

```scala
import sbt._
import sbt.Keys._
import com.typesafe.sbt.SbtAtmos.atmosSettings

object SampleBuild extends Build {
  lazy val sample = Project(
    id = "sample",
    base = file("."),
    settings = Defaults.defaultSettings ++ Seq(
      name := "Sample",
      scalaVersion := "2.10.2",
      libraryDependencies += "com.typesafe.akka" %% "akka-actor" % "2.2.0"
    )
  ) settings (atmosSettings: _*)
}
```

A simple [sample Akka project][sample] configured with the sbt-atmos plugin is
included in this repository.


Trace dependencies
------------------

The sbt-atmos plugin will automatically add a library dependency which includes
Aspectj aspects for the Akka dependency being used, providing the settings are
added as described above.

To manually or explicitly specify the trace dependency, select an appropriate
dependency to include from:

```scala
"com.typesafe.atmos" % "trace-akka-2.0.5"           % "1.2.0-M6"
"com.typesafe.atmos" % "trace-akka-2.1.4"           % "1.2.0-M6"
"com.typesafe.atmos" % "trace-akka-2.2.0_2.10"      % "1.2.0-M6"
"com.typesafe.atmos" % "trace-akka-2.2.0_2.11.0-M3" % "1.2.0-M6"
```


Run with Typesafe Console
-------------------------

To run your application with Typesafe Console there are extra versions of the
`run` and `run-main` tasks. These use the same underlying settings for the
regular `run` tasks, and also add the configuration needed to instrument your
application, and start and stop Typesafe Console.

To run the default or discovered main class use:

    atmos:run

To run a specific main class:

    atmos:run-main org.something.MainClass


Trace configuration
-------------------

It's possible to configure which actors in your application are traced, and at
what sampling rates.

The underlying configuration uses the [Typesafe Config][config] library. A
configuration file is automatically created by the sbt-atmos plugin.

There are sbt settings to adjust the tracing and sampling of actors. Trace
configuration is based on actor paths. For example:

```scala
import com.typesafe.sbt.SbtAtmos.Atmos
import com.typesafe.sbt.SbtAtmos.AtmosKeys.{ traceable, sampling }

traceable in Atmos := Seq(
  "/user/someActor" -> true,  // trace this actor
  "/user/actors/*"  -> true,  // trace all actors in this subtree
  "*"               -> false  // other actors are not traced
)

sampling in Atmos := Seq(
  "/user/someActor" -> 1,     // sample every trace for this actor
  "/user/actors/*"  -> 100    // sample every 100th trace in this subtree
)
```

**Note**: The default settings are to collect all traces for all actors.
For applications with heavier loads you should select specific parts of the
application to trace.


More Information
----------------

For more information see the [documentation] for the developer version of
Typesafe Console.


Feedback
--------

We welcome your feedback and ideas for using Typesafe Console for development.
There is a `Send Feedback` link available when running the Typesafe Console web
interface, or you can go directly to [Typesafe Support][support].

You can also use the [Typesafe Console mailing list][email].


License
-------

[Typesafe Console][console] is licensed under the [Typesafe Subscription Agreement][license]
and is made available through the sbt-atmos plugin for development use only.

The code for the sbt-atmos plugin is open source software licensed under the
[Apache 2.0 License][apache].

For more information see [Typesafe licenses][licenses].


Contribution policy
-------------------

Contributions via GitHub pull requests are gladly accepted from their original
author. Before we can accept pull requests, you will need to agree to the
[Typesafe Contributor License Agreement][cla] online, using your GitHub account.


[sbt]: https://github.com/sbt/sbt
[console]: http://typesafe.com/platform/runtime/console
[sample]: https://github.com/typesafehub/sbt-atmos/tree/v0.1.0/sample/abc
[config]: https://github.com/typesafehub/config
[documentation]: http://resources.typesafe.com/docs/console
[support]: http://support.typesafe.com
[email]: http://groups.google.com/group/typesafe-console
[license]: http://github.com/typesafehub/sbt-atmos/blob/master/TypesafeSubscriptionAgreement.md
[apache]: http://www.apache.org/licenses/LICENSE-2.0.html
[licenses]: http://typesafe.com/legal/licenses
[cla]: http://www.typesafe.com/contribute/cla
