[![FIWARE Banner](https://fiware.github.io/tutorials.Media-Streams/img/fiware.png)](https://www.fiware.org/developers)

[![FIWARE Media Streams](https://img.shields.io/badge/FIWARE-Media_Streams-4f3495.svg)](https://www.fiware.org/developers/catalogue/)
[![Documentation](https://readthedocs.org/projects/fiware-tutorials/badge/?version=latest)](https://fiware-tutorials.readthedocs.io/en/latest)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Kurento 6.7.1](https://img.shields.io/badge/Kurento-6.7.1-4f3495.svg)](http://doc-kurento.readthedocs.io/)


This is an Introductory Tutorial to [FIWARE Kurento](http://kurento.readthedocs.io/)  - a media
server generic enabler used for the analysis and enhancement of video streams via [WebRTC](https://webrtc.org/).
The tutorial discusses the architecture of stream orientated systems, and demonstrates the key concepts behind
the use of video streams, by discussing code written in Node.js. Alternative code examples,written in Java and
client-side JavaScript are also available.

The tutorial introduces a series of exercises which can be run directly from within a [Docker](https://www.docker.com) container, no HTTP calls are required.



# Contents

TO DO ...


# What are Media Streams?

> "Thank You Mario, But Our Princess is in Another Castle."
>
> — Toad (Super Mario Bros.)


[WebRTC](https://webrtc.org/) is a set of protocols enabling direct peer-to-peer Real-Time Communications (RTC) between
browsers or mobile applications.- this would allow a user to make a direct video call to a person in another remote location.
However the type of applications which can be created using direct one-to-one communication are limited, and the scope of
WebRTC can be enhanced by introducing an intellegent middleware application between the two clients.

The FIWARE **Kurento** generic enabler is a WebRTC Media Server. Each client makes a direct connection to the server, and
the server intercepts the stream of data and passes the communication on to another client or clients. This model enables
additional features such as group communition and broadcasting, but also means that is possible to process and interpret the
media stream as it is received enabling transcoding, recording, mixing or object detection.



# Prerequisites

## Docker

To keep things simple both components will be run using [Docker](https://www.docker.com). **Docker** is a
container technology which allows to different components isolated into their respective environments.

* To install Docker on Windows follow the instructions [here](https://docs.docker.com/docker-for-windows/)
* To install Docker on Mac follow the instructions [here](https://docs.docker.com/docker-for-mac/)
* To install Docker on Linux follow the instructions [here](https://docs.docker.com/install/)

**Docker Compose** is a tool for defining and running multi-container Docker applications. A
[YAML file](https://raw.githubusercontent.com/Fiware/tutorials.Entity-Relationships/master/docker-compose.yml) is used
configure the required services for the application. This means all container services can be brought up in a single
command. Docker Compose is installed by default as part of Docker for Windows and  Docker for Mac, however Linux users
will need to follow the instructions found  [here](https://docs.docker.com/compose/install/)

## Cygwin

We will start up our services using a simple bash script. Windows users should download [cygwin](http://www.cygwin.com/) to provide a
command line functionality similar to a Linux distribution on Windows.


# Start Up

To start the installation, do the following:

```console
git clone git@github.com:Fiware/tutorials.Media-Streams.git
cd tutorials.Media-Streams
git submodule update --init --recursive

./services create
```

>**Note** The initial creation of Docker images can take up to three minutes


Thereafter, all services can be initialized from the command line by running the [services](https://github.com/Fiware/tutorials.Media-Streams/blob/master/services) Bash script provided within the repository:

```console
./services <command>
```

Where `<command>` will vary depending upon the exercise we wish to activate.

>:information_source: **Note:** If you want to clean up and start over again you can do so with the following command:
>
>```console
>./services stop
>```
>



# Architecture

This application will only make use of one FIWARE component - the [Kurento Media Server](http://kurento.readthedocs.io/)).
Usage of the Media server alone is insufficient for an application to qualify as *“Powered by FIWARE”*.

The overall architecture will consist of the following elements:

* One **FIWARE Generic Enabler**:
  * FIWARE [Kurento](http://kurento.readthedocs.io/) acts as a Media Server which will intercept [WebRTC](https://webrtc.org/) traffic

* One **Client Application**:
  *  Which will send a [WebRTC](https://webrtc.org/) media stream and display results (either received processed video or detected events )

Since all interactions between the elements are initiated by HTTP requests, the entities can be containerized and run from exposed ports.

The specific architecture of each section of the tutorial is discussed below.



# Introduction to Kurento

The goal of this section is to provide you with a simple getting started guide on how to install and use the Kurento Media Server.
All the code Node.js for the demo can be found within the `nodejs` folder within the GitHub repository.[Media Streams](https://github.com/Fiware/tutorials.Media-Streams/tree/master/kurento-examples). Alternative `client-side-javascript` and `java` examples are also available.

## Kurento - Example Usage

### Service Health


### Getting Started

### Object Detection

### Filters





Other examples are available in the `nodejs` folder, which are beyond the scope of this tutorial. For more information, please refer to the [official Kurento documentation](http://doc-kurento.readthedocs.io/).





# Next Steps

Want to learn how to add more complexity to your application by adding advanced features?
You can find out by reading the other [tutorials in this series](https://fiware-tutorials.readthedocs.io/en/latest)

---

## License

[MIT](LICENSE) © FIWARE Foundation e.V.

