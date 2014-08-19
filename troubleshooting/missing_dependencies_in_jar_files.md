# Missing Dependencies in Jar Files

By default, maven does not include dependency jars when it builds a target.  When running a Spark job, if the Spark workers don't contain the dependency jars - there
will be an error.  One workaround is to create a shaded or uber jar with the dependencies.  Add this to your Maven pom.xml file:

```xml
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
```
