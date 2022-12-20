---
author: dschaaff
categories:
- java
- springboot
- techology
- docker
comments: true
date: "2019-01-19T00:00:00Z"
slug: easy-integrations-tests-for-java-with-the-maven-docker-plugin
tags:
- springboot
- docker
- devops
title: Easy Integrations Tests for Java with the Maven Docker Plugin
---

Traditionally it has been a pain to manage the infrastructure necessary for running integration tests within a CI/CD pipeline. Several years ago I accomplished this with an RDS instance for the database in AWS dedicated solely to the test environment. The problem is that multiple tests running at the same time would cause conflicts as they inserted and removed data in the database. At the time I set a lock in Jenkins to only allow one service to utilize the test database at a time, but this was far from ideal. Thankfully there are a lot of good options for solving this problem. I’m particularly fond of using the Docker plugin for Maven to handle this when dealing with Java-based applications.

I'm currently taking advantage of this plugin to accomplish two things.

## Building the Docker Image for the App

This is how I got started with the Docker plugin for maven.

```xml
<plugin>
  <groupId>io.fabric8</groupId>
  <artifactId>docker-maven-plugin</artifactId>
  <version>0.26.0</version>
  <configuration>
    <verbose>true</verbose>
    <images>
    <image>
      <name>registry/foo-bar:%l</name>
      <build>
        <dockerFileDir>${project.basedir}</dockerFileDir>
        <tags>
          <tag>${project.artifactId}-${project.version}</tag>
        </tags>
      </build>
    </image>
  </images>
</configuration>
</plugin>
```

This configuration adds the `docker:build` and `docker:push` goals to maven for handling the building and pushing of the app's docker image.
I'll likely write more about some cool features this allows at a later time.

## Integration Testing

The best part of using this plugin is how it simplifies running integration tests.

I create a [Maven profile](https://maven.apache.org/guides/introduction/introduction-to-profiles.html) called build. Within that build profile, the Docker plugin is configured to 
bring up dependent services via Docker containers for the integration test phase of the Maven build pipeline.

```xml
<profiles>
  <profile>
    <id>build</id>
    <build>
      <plugins>
        <plugin>
          <groupId>io.fabric8</groupId>
          <artifactId>docker-maven-plugin</artifactId>
          <version>0.26.0</version>
          <configuration>
            <verbose>true</verbose>
            <images>
              <image>
                <alias>mysql</alias>
                <name>mysql:5.6</name>
                <run>
                  <env>
                    <MYSQL_USER>db_user</MYSQL_USER>
                    <MYSQL_PASSWORD>password</MYSQL_PASSWORD>
                    <MYSQL_ROOT_PASSWORD>root</MYSQL_ROOT_PASSWORD>
                    <MYSQL_DATABASE>test_db</MYSQL_DATABASE>
                  </env>
                  <wait>
                    <log>MySQL init process done. Ready for start up.</log>
                    <time>80000</time>
                  </wait>
                  <ports>
                    <port>mysql.port:3306</port>
                  </ports>
                </run>
              </image>
              <image>
                <alias>redis</alias>
                <name>redis:5</name>
                <run>
                  <wait>
                    <log>Ready to accept connections</log>
                  </wait>
                  <ports><port>redis.port:6379</port></ports>
                </run>
              </image>
            </images>
          </configuration>
          <executions>
            <execution>
              <id>start</id>
              <phase>initialize</phase>
              <goals>
                <goal>start</goal>
              </goals>
            </execution>
            <execution>
              <id>stop</id>
              <phase>post-integration-test</phase>
              <goals>
                <goal>stop</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
      </plugins>
    </build>
  </profile>
</profiles>
```

This configuration starts a MySQL container with the configured environment variables and waits till the log line
`MySQL init process done. Ready for start up.` appears in stdout to continue. The ports section confgures the ports
for the container. What this config does is dynamically bind `3306` in the container to an available host port and sets the host port number
in the property `mysql.port`.

```xml
<ports>
  <port>mysql.port:3306</port>
</ports>
```

The app's properties are configured to use that port for the jdbc connection.

```text
spring.datasource.url=jdbc:mysql://localhost:@mysql.port@/test_db
```

The Redis container's port is handled exactly the same way.

```xml
<ports>
  <port>redis.port:6379</port>
</ports>
```

```text
redis.port=@redis.port@
```

The last part of the configuration bind's the start of the containers to Maven's initialization phase and 
the stop of the containers to Maven's post-integration-test phase.

```xml
 <executions>
  <execution>
    <id>start</id>
    <phase>initialize</phase>
    <goals>
      <goal>start</goal>
    </goals>
  </execution>
  <execution>
    <id>stop</id>
    <phase>post-integration-test</phase>
    <goals>
      <goal>stop</goal>
    </goals>
  </execution>
</executions>
```

## Putting it All Together

In the end, we can now run integrations tests in the build tool of choice by simply calling
`mvn verify -P build`. All the build machines require is a proper JDK, Maven, and Docker.
Tests will run with a clean database and Redis instance and then everything will be torn down at the end of the 
integration tests. Multiple builds can run the tests at the same time without stepping on each other.

```bash
[INFO] --- docker-maven-plugin:0.26.0:start (start) @ foo-bar ---
[INFO] DOCKER> [mysql:5.6] "mysql": Start container bd5d25d42d37
[INFO] DOCKER> Pattern 'MySQL init process done. Ready for start up.' matched for container bd5d25d42d37
[INFO] DOCKER> [mysql:5.6] "mysql": Waited on log out 'MySQL init process done. Ready for start up.' 12697 ms
[INFO] DOCKER> [redis:5] "redis": Start container 8c8b49646958
[INFO] DOCKER> Pattern 'Ready to accept connections' matched for container 8c8b49646958
[INFO] DOCKER> [redis:5] "redis": Waited on log out 'Ready to accept connections' 512 ms
...
[INFO] Results:
[INFO]
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0
[INFO]
[INFO]
[INFO] --- maven-jar-plugin:3.1.0:jar (default-jar) @ foo-bar ---
[INFO] Building jar: /Users/danielschaaff/development/foo-bar/foo-bar.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:2.1.1.RELEASE:repackage (repackage) @ foo-bar ---
[INFO] Replacing main artifact with repackaged archive
[INFO]
[INFO] --- docker-maven-plugin:0.26.0:stop (stop) @ foo-bar ---
[INFO] DOCKER> [redis:5] "redis": Stop and removed container 8c8b49646958 after 0 ms
[INFO] DOCKER> [mysql:5.6] "mysql": Stop and removed container bd5d25d42d37 after 0 ms
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  40.827 s
[INFO] Finished at: 2019-01-19T08:40:56-08:00
[INFO] ------------------------------------------------------------------------
```