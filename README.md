# Gatling

## START PROJECT

If you use sbt 0.13.13 or later, you can use `sbt new` command for initialize sbt project.

```sh
$ sbt new sbt/scala-seed.g8
....
Minimum Scala build.

name [My Something Project]: hello

Template applied in ./hello
```

reference: [sbt new](https://www.scala-sbt.org/1.x/docs/ja/Hello.html)

## `build.sbt`

`build.sbt` に以下を追記します。

```build.sbt
scalacOptions := Seq(
  "-encoding", "UTF-8", "-target:jvm-1.8", "-deprecation",
  "-feature", "-unchecked", "-language:implicitConversions", "-language:postfixOps"
)

libraryDependencies ++= Seq(
  "io.gatling.highcharts" % "gatling-charts-highcharts" % "2.3.0" % "test",
  "io.gatling"            % "gatling-test-framework"    % "2.3.0" % "test"
)

enablePlugins(GatlingPlugin)
```

> gatling-test-framework は Gatling を実行するためのライブラリっぽいですね。
> gatling-charts-highcharts は HighCharts という JavaScript 製のチャートライブラリをベースにした Gatling 向けのライブラリのようです。結果のレポート表示用だと思います。x

## `plugins.sbt`

`plugins.sbt` に gatling に関する plugin の情報を記載します。

```plugins.sbt
addSbtPlugin("io.gatling" % "gatling-sbt" % "2.2.2")
```

## write Test Screnario

```src/test/scala/mytest/MyBasicSimulation
package mytest

import io.gatling.core.Predef._
import io.gatling.http.Predef._

class MyBasicSimulation extends Simulation {

  val httpProtocol = http
    .baseURL("http://localhost:8080") // Here is the root for all relative URLs
    .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8") // Here are the common headers
    .acceptEncodingHeader("gzip, deflate")
    .acceptLanguageHeader("en-US,en;q=0.5")
    .userAgentHeader("Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:16.0) Gecko/20100101 Firefox/16.0")

  val scn = scenario("My First Scenario") // A scenario is a chain of requests and pauses
    .exec(http("request_1")
      .get("/"))

  setUp(scn.inject(atOnceUsers(1)).protocols(httpProtocol))
}
```

ここでは、http://localhost:8080 にアクセスしてみる。

ここでは ~~テキトーに~~ nginx サーバでも立てて実験してみることにします。
シミュレーションとしては「この nginx サーバの root (`/`) に、ユーザが1人がアクセスしてくるだけ」という簡易的なものです。

```
docker run --rm -p 8080:80 nginx 
```

## execute simulation

```
$ sbt
sbt:hello-sbtproject> 

sbt:hello-sbtproject> gatling:testOnly mytest.MyBasicSimulation
....
Please open the following file: /Users/sudachi/projects/hello-sbtproject/target/gatling/mybasicsimulation-1607150219812/index.html
[info] Simulation MyBasicSimulation successful.
[info] Simulation(s) execution ended.
[success] Total time: 11 s, completed 2020/12/05 15:37:06
```

## Test report 

see `target/gatling`


## Use 

> You can use our Giter8 template to bootstrap a new sbt project:

```
sbt new gatling/gatling.g8
```

* [GITER8 TEMPLATE](https://gatling.io/docs/current/extensions/giter8_template/#g8-template)

## Links

* [SBT PLUGIN](https://gatling.io/docs/current/extensions/sbt_plugin/)
* [負荷試験ツールのGatlingをsbtのタスクから実行する](https://yoshinorin.net/2018/02/28/gatling-using-by-sbt/)
* [gatling/gatling-sbt-plugin-demo - github](https://github.com/gatling/gatling-sbt-plugin-demo)
* [gatling/gatling-sbt-plugin - github](https://github.com/gatling/gatling-sbt-plugin)
