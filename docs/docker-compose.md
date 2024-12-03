# Docker Compose

## Docker Compose Overview

#### Motivation
By using Docker compose, we can manage multi container environments using a Declarative approach, instead of the traditional imperative approach by using just docker commands.

#### Docker Compose Parts

- **Compose File:** Declare the services in your application
- **Command-line Interface:** The docker compose command line has many of the same commands as docker. You can check out the commands by using ```docker-compose```. You can run the docker compose file by simply using ```docker compose up -d```. Similary, to stop docker compose, just write ```docker compose down```.

## Docker Compose Files

#### YAML
YAML is a data serialization language. YAML files can have the extension ```.yaml``` or ```.yml```.

**YAML Data Types**

- Integers
- Strings
- Null
- Booleans

**YAML Collections: Mappings**

- Mappings: Key-Value pairs.
- Nested Mappings 
- Inline Nested Mappings

**YAML Collections: Sequences**
Sequences are indicated by ```-``` sign. 

**YAML Comments**
Comments start with ```#```.

#### Compose Files

Root elements of file mapping

- version
- services
- volumes
- networks

**Compose File Version:** key: value pair - ```version: '3.8'```

**Compose File Services:** Services in your application. Nested mapping of services under the ```services``` key. Configuration options in a further nested mapping.

**Compose File Service Caveats:** 

- Some configurations only apply to Swarm mode.
- Some are specified on the command-line.

**Compose File Dependencies**

- ```depends_on``` - can use this command to specify what this service depends on
- links

**Compose File Service Configuration**

Compose file key - 

- Specify Image: image
- Specify volume: volumes
- Publish ports on host: ports
- Environment variables: environment
- Logging: logging
- Security options: security_opt

**Compose File Volumes**

- Allows naming and sharing of volumes
- Supports ```external``` volumes

```yaml
volumes:
    named-volume:
    external-volume:
        external: true
```

**Compose File Network**
One network is created by default, which is the ```bridge``` network. It is shown by the ```networks:``` key in the root elements.

```yaml
networks:
    service1:
    service2:
        external: true
```

**Extension Fields**

- Extension fields helps us reuse configuration fragments
- Extension fields are indicated by using root keys beginning with ```x-```
- It uses YAML anchors

Example Extension fields

```yaml
x-logging: #extension field
    &default-logging #using yaml anchors
    options:
    driver:

service:
    web:
        image:
        logging: *default-logging
```
## Compose Command-Line Interface

#### Features

- Run multiple isolated environments on a single host
- Parallel execution model
- Compose file change detection. Everytime you build a container using compose, it stores the container configuration as cache. So the next time you run it, it can run directly from the cache if no changes are made but it can also detect the changes.

#### Commands
```docker-compose [OPTIONS] [COMMAND] [ARGS]```

Compose will use the docker daemon on the host by default.

- Can connect to remote hosts with -H
- Secure remote connections with --tls* options
- The default file for docker-compose is - ```docker-compose.(yml|yaml)```. But you can use the ```-f``` flag to specify alternatives.
- Each isolated application is associated with a project in compose. The project is given a name and that name appears in resources that get created by Compose. You can assign a custom compose name using a ```-p``` flag.

- ```docker-compose up -d``` 
    - Creates network and named volumes
    - Builds, creates, starts, and attaches to service containers.
    - Performs change detection

- ```docker-compose down -d```
    - Removes service containers, named and default networks
    - Leaves volumes and images by default.
