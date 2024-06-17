<h2>Run/debug the Spring application in native (no JVM) mode inside docker container</h2>
Currently spring native build based on buildpacks (perhaps plugin should be more investigated)

1. configure pom.xml
   1. configure `org.graalvm.buildtools`
```xml
            <plugin>
                <groupId>org.graalvm.buildtools</groupId>
                <artifactId>native-maven-plugin</artifactId>
                <configuration>
                    <classesDirectory>${project.build.outputDirectory}</classesDirectory>
                    <metadataRepository>
                        <enabled>true</enabled>
                    </metadataRepository>
                    <requiredVersion>17</requiredVersion>
                    <imageName>springDemo</imageName>
                </configuration>
            </plugin>
```
   2. configure execution phase
```xml
    <profiles>
        <profile>
            <id>native</id>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.graalvm.buildtools</groupId>
                        <artifactId>native-maven-plugin</artifactId>
                        <configuration>
                            <debug>true</debug>
                        </configuration>
                        <executions>
                            <execution>
                                <id>build-native</id>
                                <goals>
                                    <goal>compile-no-fork</goal>
                                </goals>
                                <phase>package</phase>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>
```
2. Create a maven task with 
   - Run: -P native package
   - Working directory:spring-graalvm-demo
   - Run on:  
     - click on "Manage targets..." link
     - click on "+"->select "Docker"->enter path to the Dockerfile, if not present: src\main\docker\Dockerfile
     - click Create 
   - select the created target in the "Run on" field (it is not selected automatically)
3. Save the created maven run configuration
4. Run this maven configuration (it takes time)
5. Create a "GraalVM Native Image" run configuration
   - "Executable" field: target\springDemo
   -  "Symbol file" field: target\springDemo.debug
6. Run Debug   

Result: 
-we have Debug view opened
-if we set the breakpoint on the home() endpoint then we need to send a http request to the app. The application port we can find on the "GraalVm on Docker console" tab, for example: "Application port  is bounded to local port 62724". So, we go to the page http://localhost:62724 and the debug stops on this breakpoint.