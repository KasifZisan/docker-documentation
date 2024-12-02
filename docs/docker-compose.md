# Docker Compose

## Docker Compose Overview

#### Motivation
By using Docker compose, we can manage multi container environments using a Declarative approach, instead of the traditional imperative approach by using just docker commands.

#### Docker Compose Parts
- Compose File: Declare the services in your application

- Command-line Interface: The docker compose command line has many of the same commands as docker. You can check out the commands by using ```docker-compose```. You can run the docker compose file by simply using ```docker compose up -d```. Similary, to stop docker compose, just write ```docker compose down```.

## Docker Compose 

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