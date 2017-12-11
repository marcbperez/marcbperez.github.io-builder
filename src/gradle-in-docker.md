# Gradle in Docker
*Last edited on 6 August 2017.*

Gradle and Docker can work together to provide an isolated environment to build
software projects. Gradle keeps the build process tied up while Docker and
docker-compose provide an independent and configurable environment to construct,
run and test and deploy the task.

## Setting up Docker

The first step is to set up a Docker image to call Gradle in continuous build
mode. The container will provide an isolated environment and a certain
architecture in where to install the project and its dependencies, and run tests
against the infrastructure. A `Dockerfile` that does exactly this will look like
the next example.

```bash
# Dockerfile

FROM marcbperez/docker-gradle
ADD . /home/builder
WORKDIR /home/builder
CMD gradle --continuous
```

To provide more functionality to the stack, including the following
`docker-compose.yml` manifest. In this example, both files are located in the
root folder of the project.

```yaml
# docker-compose.yml

version: '2'
services:
  builder:
    build: .
    volumes:
      - .:/home/builder
```

## Defining the build

The `gradle --continuous` command looks for a build script called
`build.gradle`, also in the root folder. The script is divided into tasks and
defines how the project will be built. First, it will define the default order
of the build tasks and will set the source folder at `src` and the tests in
`test`.

```javascript
// build.gradle

// Define the execution order of the build tasks.
defaultTasks "wrapper", "watch", "dependencies", "build", "install", "test"

// Get the path and define the source and tests folder.
def path = System.getProperty("user.dir")
def srcDir = "${path}/src"
def testDir = "${path}/test"
```

The next step is to generate the project wrapper and to define its watcher. The
wrapper will enable end users to run the Gradle build and its tasks without
installing Gradle. The watcher will run the build continuously every time a
change is detected in the project source or tests folder.

```javascript
// build.gradle

// Generate the Gradle wrapper.
task wrapper(type: Wrapper) {
  description "Creates the project wrapper."
  gradleVersion = "4.10"
}

// Watch for changes when running in continuous build mode.
task watch(type: Exec) {
  description "Watches for changes."
  inputs.files(srcDir, testDir)
  commandLine "true"
  new ByteArrayOutputStream()
}
```

The project might need system dependencies, in other words, the system will need
to be provisioned and will also need packages for development, such as Spock,
the default testing tool.

```javascript
// build.gradle

// Define which packages will be needed for the build.
def aptPackages = ["libspock-java"]

// Run all tasks with names starting with "dependencies".
task dependencies() {
  description "Installs all dependencies."
  dependsOn {
    tasks.findAll { task ->
      task.name.startsWith("dependencies") && !task.name.equals("dependencies")
    }
  }
}

// Update APT repositories, needed before installing packages.
task aptUpdate(type: Exec) {
  description "Updates APT repository information."
  workingDir path
  commandLine "apt-get"
  args "update"
  new ByteArrayOutputStream()
}

// Install the packages and provision the system.
task dependenciesApt(type: Exec, dependsOn: aptUpdate) {
  description "Installs APT dependencies."
  workingDir path
  commandLine "apt-get"
  args = ["install", "-y"] + aptPackages
  new ByteArrayOutputStream()
}
```

The build script will need three core tasks. `build` will run actions that
compile, `install` will provide the system with those compiled changes and
`test` will orchestrate testing activities, from unit-testing to end-to-end.

```javascript
// build.gradle

// Run all tasks with names starting with "build".
task build() {
  description "Builds all sources."
  dependsOn {
    tasks.findAll { task ->
      task.name.startsWith("build") && !task.name.equals("build")
    }
  }
}

// Run all tasks with names starting with "install".
task install() {
  description "Installs all sources."
  dependsOn {
    tasks.findAll { task ->
      task.name.startsWith("install") && !task.name.equals("install")
    }
  }
}

// Run all tasks with names starting with "test".
task test() {
  description "Tests all sources."
  dependsOn {
    tasks.findAll { task ->
      task.name.startsWith("test") && !task.name.equals("test")
    }
  }
}
```

To keep it simple, this example will only run a set of Spock tests. More
precisely all the `test/*.groovy` files in the project folder. The final loop
on the `build.gradle` the script creates a task prefixed with `test` for every
test found, this way they will be called by the main testing task described
above.

```javascript
// build.gradle

// Create a task, prefixed with "test", for every test file.
fileTree(dir: "${testDir}", include: "**/*.groovy").each { File testFile ->
  def fileName = testFile.getName().split("\\.")[0]

  task "test${fileName}"(type: Exec) {
    description "Runs the ${fileName} case."
    workingDir path
    commandLine "groovy"
    args "-cp", "/usr/share/java/spock-core.jar", testFile
    new ByteArrayOutputStream()
  }
}
```

## And failing first

To get to the point of actually starting to develop new features, the stack
needs to prove it can run a test. An end-to-end test for a terminal based
application would look like `test/AppRuns.groovy` example described here. The
test expects the system to be provisioned with the `app` command, showing a
welcome message.

```javascript
// test/AppRuns.groovy

import spock.lang.*

class AppRunsSpec extends Specification {
  def "Application should show a welcome message"() {
    expect:
      "app".execute().text == "Hello, world!"
  }
}
```

Everything is in place to start developing, there is a first test expecting a
command which does not exist, so the test will fail until something is done. To
watch the build run every time a change is made in the project sources simply
invoke docker-compose. More about [Gradle][gradle-docs], [Docker][docker-docs],
[docker-compose][docker-compose-docs] and [Spock][spock-docs] can be found in
their respective official guides.

```bash
sudo docker-compose up
```

## References

  - Docker Documentation. Docker Compose [online]. Available at
    [https://docs.docker.com/compose/][docker-compose-docs]
    (Accessed 6 August 2017).
  - Docker Documentation. Docker Documentation [online]. Available at
    [https://docs.docker.com/][docker-docs]
    (Accessed 6 August 2017).
  - Gradle. Documentation [online]. Available at
    [https://gradle.org/docs/][gradle-docs]
    (Accessed 6 August 2017).
  - Spock Framework. Spock Framework Reference Documentation. Available at
    [http://spockframework.org/spock/docs/1.1/index.html][spock-docs]
    (Accessed 6 August 2017).

[docker-compose-docs]: https://docs.docker.com/compose/
[docker-docs]: https://docs.docker.com/
[gradle-docs]: https://gradle.org/docs/
[spock-docs]: http://spockframework.org/spock/docs/1.1/index.html
