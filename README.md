# Logback layout for Google Structured logging

## Why?

- Google indexes logs - easies and faster to filter logs.
  See [Using the Logs Explorer](https://cloud.google.com/logging/docs/view/logs-viewer-interface)
- Aggregating logs by labels
- Log metrics
- Proper logging levels in the UI

See [structured logs](https://cloud.google.com/logging/docs/structured-logging).

## Getting started

Maven:

```xml

<dependencies>
    <dependency>
        <groupId>io.github.aaabramov</groupId>
        <artifactId>glogging-core</artifactId>
        <version>0.0.2</version>
    </dependency>


    <dependency>
        <groupId>io.github.aaabramov</groupId>
        <artifactId>glogging-gson</artifactId>
        <!-- OR -->
        <!--<artifactId>glogging-jackson</artifactId>-->
        <version>0.0.2</version>
    </dependency>
</dependencies>
```

Gradle:

```groovy
implementation 'io.github.aaabramov:glogging-core:0.0.2'
implementation 'io.github.aaabramov:glogging-gson:0.0.2'
// OR 
// implementation 'io.github.aaabramov:glogging-jackson:0.0.2'
```

Sbt:

```sbt
libraryDependencies ++= Seq(
  "io.github.aaabramov" % "glogging-core",
  "io.github.aaabramov" % "glogging-gson"
  //  OR
  //  "io.github.aaabramov" % "glogging-jackson"
).map(_ % "0.0.2")
```

## Configuration example:

```xml

<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="io.github.aaabramov.glogging.GoogleLayout">

                <!-- You have a choice which JSON encoder to use. Or create your own via implementing JsonEncoder interface -->
                <json>io.github.aaabramov.glogging.JacksonEncoder</json>

                <!-- OR -->
                <!-- <json>io.github.aaabramov.glogging.GsonEncoder</json> -->

                <!-- Optionally append "${prefix}/loggerName" labels -->
                <appendLoggerName>true</appendLoggerName>

                <!-- Optionally configure prefix for labels -->
                <prefix>com.yourcompany</prefix>

                <!-- Provide message pattern you like. -->
                <!-- Note: there is no need anymore to log timestamps & levels to the message. Google will pick them up from specific fields. -->
                <pattern>%message %xException{10}</pattern>
            </layout>
        </encoder>
    </appender>

    <appender name="ASYNCSTDOUT" class="ch.qos.logback.classic.AsyncAppender">
        <appender-ref ref="STDOUT"/>
    </appender>

    <!--  Configure logging levels  -->
    <logger name="com.github" level="DEBUG"/>

    <root level="DEBUG">
        <appender-ref ref="ASYNCSTDOUT"/>
    </root>
</configuration>
```

## Example output:

```
{"timestamp":{"seconds":1629642099,"nanos":659000000},"severity":"DEBUG","message":"debug","labels":{"io.github.aaabramov/name":"Andrii","io.github.aaabramov/loggerName":"io.github.aaabramov.glogging.App"}}
{"timestamp":{"seconds":1629642099,"nanos":659000000},"severity":"INFO","message":"info","labels":{"io.github.aaabramov/name":"Andrii","io.github.aaabramov/loggerName":"io.github.aaabramov.glogging.App"}}
{"timestamp":{"seconds":1629642099,"nanos":659000000},"severity":"WARN","message":"warn","labels":{"io.github.aaabramov/name":"Andrii","io.github.aaabramov/loggerName":"io.github.aaabramov.glogging.App"}}
{"timestamp":{"seconds":1629642099,"nanos":661000000},"severity":"ERROR","message":"error java.lang.RuntimeException: BOOM\n\tat io.github.aaabramov.glogging.App.main(App.java:22)","labels":{"io.github.aaabramov/name":"Andrii","io.github.aaabramov/loggerName":"io.github.aaabramov.glogging.App"}}
{"timestamp":{"seconds":1629642102,"nanos":661000000},"severity":"DEBUG","message":"debug","labels":{"io.github.aaabramov/name":"Andrii","io.github.aaabramov/loggerName":"io.github.aaabramov.glogging.App"}}
{"timestamp":{"seconds":1629642102,"nanos":661000000},"severity":"INFO","message":"info","labels":{"io.github.aaabramov/name":"Andrii","io.github.aaabramov/loggerName":"io.github.aaabramov.glogging.App"}}
{"timestamp":{"seconds":1629642102,"nanos":661000000},"severity":"WARN","message":"warn","labels":{"io.github.aaabramov/name":"Andrii","io.github.aaabramov/loggerName":"io.github.aaabramov.glogging.App"}}
{"timestamp":{"seconds":1629642102,"nanos":661000000},"severity":"ERROR","message":"error","labels":{"io.github.aaabramov/name":"Andrii","io.github.aaabramov/loggerName":"io.github.aaabramov.glogging.App"}}
```

## Releasing

```bash
mvn versions:set -DnewVersion=X.Y.Z
mvn versions:commit
mvn clean javadoc:jar source:jar verify gpg:sign deploy
```
