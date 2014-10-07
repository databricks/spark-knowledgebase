# Missing Dependencies in Jar Files

By default, maven does not include dependency jars when it builds a target.  When running a Spark job, if the Spark worker machines don't contain the dependency jars - there will be an error that a class cannot be found.

The easiest way to work around this is to create a *shaded* or *uber* jar to package the dependencies in the jar as well.  

It is possible to opt out certain dependencies from being included in the uber jar by marking them as ```<scope>provided</scope>```.  Spark dependencies should be marked as provided since they are already on the Spark cluster.  You may also exclude other jars that you have installed on your worker machines.

Here is an example Maven pom.xml file that creates an uber jar with all the code in that project and includes the common-cli dependency, but not any of the Spark libraries.:

```xml
<project>
    <groupId>com.databricks.apps.logs</groupId>
    <artifactId>log-analyzer</artifactId>
    <modelVersion>4.0.0</modelVersion>
    <name>Databricks Spark Logs Analyzer</name>
    <packaging>jar</packaging>
    <version>1.0</version>
    <repositories>
        <repository>
            <id>Akka repository</id>
            <url>http://repo.akka.io/releases</url>
        </repository>
    </repositories>
    <dependencies>
        <dependency> <!-- Spark -->
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.10</artifactId>
            <version>1.1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency> <!-- Spark SQL -->
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-sql_2.10</artifactId>
            <version>1.1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency> <!-- Spark Streaming -->
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-streaming_2.10</artifactId>
            <version>1.1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency> <!-- Command Line Parsing -->
            <groupId>commons-cli</groupId>
            <artifactId>commons-cli</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                    <finalName>uber-${project.artifactId}-${project.version}</finalName>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```
