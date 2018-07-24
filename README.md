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

- [What are Media Streams?](#what-are-media-streams)
  * [The teaching goal of this tutorial](#the-teaching-goal-of-this-tutorial)
- [Prerequisites](#prerequisites)
  * [Docker](#docker)
  * [Cygwin](#cygwin)
- [Start Up](#start-up)
- [Architecture](#architecture)
  * [Kurento Configuration](#kurento-configuration)
  * [Application Server Configuration](#application-server-configuration)
- [Connecting to a Media Sever](#connecting-to-a-media-sever)
  * [Hello World - Start Up](#hello-world---start-up)
    + [Service Health](#service-health)
    + [Running the Example](#running-the-example)
  * [Analyzing the Code](#analyzing-the-code)
    + [Back-End - WebSocket Connection](#back-end---websocket-connection)
    + [Back-End - Connecting to Kurento](#back-end---connecting-to-kurento)
    + [Back-End - Creating a Media Pipeline](#back-end---creating-a-media-pipeline)
    + [Front-End - JavaScript on the rendered page](#front-end---javascript-on-the-rendered-page)
- [Altering Media Streams](#altering-media-streams)
  * [Altering Media Streams - Start Up](#altering-media-streams---start-up)
    + [Running the Example](#running-the-example-1)
  * [Analyzing the Code](#analyzing-the-code-1)
- [Raising Context Events](#raising-context-events)
  * [Raising Context Events - Start Up](#raising-context-events----start-up)
    + [Running the Example](#running-the-example-2)
  * [Analyzing the Code](#analyzing-the-code-2)
- [Next Steps](#next-steps)


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

## The teaching goal of this tutorial

The goal of this tutorial is to provide you with a simple getting started guide on how to install and use the **Kurento** Media Server. For this purpose a simple Node.js Express application will be created. The emphasis will be on how to integrate
**Kurento** as a generic enabler within the FIWARE system and alter context.

The intention here is not to teach users how manipulate media streams using Node - indeed any language could have been chosen. It is merely to show how a sample programming language could be used analyze and alter a media stream to potentially raise
events and alter the context of a product *"powered by FIWARE"*.

All the code for the demo can be found within the `nodejs` folder within the [kurento-examples](https://github.com/Fiware/tutorials.Media-Streams/tree/master/kurento-examples) directory. Alternative `client-side-javascript` and `java` examples are also available. Obviously, your choice of programming language will depend upon your own business needs - when reading the code below please keep this in mind and substitute Node.js with your own programming language as appropriate.

Additional non-context related **Kurento** examples exist, which are beyond the scope of this tutorial. For more information, please refer to the [official Kurento Tutorial Documentation](https://doc-kurento.readthedocs.io/en/stable/user/tutorials.html).
The **Kurento** Media Server is a stand-alone product and can also be used outside of the FIWARE ecosystem as a generic media server.


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
  * FIWARE [Kurento](http://kurento.readthedocs.io/) acts as a Media Server which will intercept [WebRTC](https://webrtc.org/) traffic, additional processing will be added as necessary

* One **Application Server** (Examples) which:
  * Displays a web-page, allowing a user to switch on their camera and interact.
  * Sends a [WebRTC](https://webrtc.org/) media stream and displays results (either received processed video or detected events )

Since all interactions between the elements are initiated by HTTP requests, the entities can be containerized and run from exposed ports.


![](https://fiware.github.io/tutorials.Media-Streams/img/architecture.png)

The specific architecture of each section of the tutorial is discussed below.

## Kurento Configuration

```yaml
  kurento:
    image: fiware/stream-oriented-kurento
    hostname: kurento
    container_name: fiware-kurento
    expose:
      - "8888"
    ports:
      - 8888:8888
    networks:
      - default
```

The `kurento` container is listening on a single port:

* Port `8888` is exposed for Websocket communications - and also so that we can check the connection


## Application Server Configuration

```
  kurento-examples:
    image: fiware/kurento-examples
    container_name: examples-kurento
    depends_on:
      - kurento
    build:
      context: ../kurento-examples
      dockerfile: Dockerfile
    ports:
      - 8443:8443
    networks:
      - default
    environment:
      - "MEDIA_SERVER_HOST=kurento"
      - "MEDIA_SERVER_PORT=8888"
      - "APP_SERVER_HOST=kurento-examples"
      - "APP_SERVER_PORT=8443"
      - "TUTORIAL_NAME=${TUTORIAL_NAME}"
```

The `kurento-examples` container is a web app server listening on a single port:

* Port `8443` is exposed for HTTPS traffic so we can display the webpage

The `kurento-examples` container is driven by environment variables as shown:

| Key |Value|Description|
|-----|-----|-----------|
|MEDIA_SERVER_HOST|`kurento`| Hostname of the Media Server receives the media stream |
|MEDIA_SERVER_PORT|`8888`|Port used by the Media Server used websocket traffic |
|APP_SERVER_HOST|`kurento-examples`| Hostname of the App Server to display the web page (and receive event callbacks)|
|APP_SERVER_PORT|`8443`| Port used by the App Server  |
|TUTORIAL_NAME| none| The name of the example to run |


# Connecting to a Media Sever

Before describing a proper context-related example, we will first examine a minimal set-up "Hello World" example
to check that we are able to connect to the **Kurento** media server and send and receive a video stream. This
is a very simple [WebRTC](https://webrtc.org/) application implementing a WebRTC loopback. A media stream is generated
from a web-cam. It is displayed on the browser but also sent to the **Kurento** media server where it is redirected
un-altered back to the client application.

![](https://fiware.github.io/tutorials.Media-Streams/img/hello-world.png)

The net result is that two identical video elements are displayed on the same screen (with a minimal lag between them).
This is not a useful application in itself, but will demonstrate that the media server can be accessed and is
capable of streaming video.

## Hello World - Start Up

To start the system with the simplest integration of **Kurento**, run the following command:

```console
./services hello-world
```

### Service Health

You can check if the Kurento Media Server is running by making an HTTP request to the exposed port `8888`

```console
curl -iX GET \
  http://localhost:8888
```

The expected response is :

```
HTTP/1.1 426 Upgrade Required
Server: WebSocket++/0.7.0
```

The response code  `426` indicates that the **Kurento** Media Server is responding on the given port, but will not respond successfully to HTTP requests. The Media Server will only respond successfully to WebSocket traffic.


>**What if I get a `Failed to connect to localhost port 8888: Connection refused` Response?**
>
> If you get a `Connection refused` response, the Media Server cannot be found where expected
> for this tutorial  - you will need to substitute the URL and port in each cUrl command with the
> corrected IP address. All the cUrl commands tutorial assume that the Media Server is available on `localhost:8888`.
>
>Try the following remedies:
> * To check that the docker containers are running try the following:
>
>```
>docker ps
>```
>
>You should see two containers running. If the Media Server is not running, you can restart the containers as necessary.
>This command will also display open port information.
>

### Running the Example

To run the example, open a WebRTC compatible browser at `https://localhost:8443` and accept HTTPS traffic to open
the page. The application consists of a single HTML web page containing two HTML5 `<video>` tags: one showing
the local stream (as captured by the local webcam) and the other showing the remote stream sent by the media server
back to the client. Click on the start button and the  same video will be displayed in both `<video>` elements.

![](https://fiware.github.io/tutorials.Media-Streams/img/hello-world-screenshot.png)

You  can check that the remote stream has been re-directed by bringing down the Media Server:

```console
docker stop fiware-kurento
```

The image on the right will then freeze.

You can restart the Kurento Media Server by running:

```console
docker start fiware-kurento
```

## Analyzing the Code

The code under discussion can be found within the `kurento-hello-world` directory within the Git Repository
The main script of this demo is called `server.js`. For simplicity, all URLs have been hard-coded and error
handling has been removed from the code snippets under discussion to make them easier to read.




### Back-End - WebSocket Connection

Dynamic communication between the Front-End rendered web-page and the Back-End application server occurs using [WebSockets](https://www.html5rocks.com/en/tutorials/websockets/basics/). The code handling the connection at the server
can be seen below:

```javascript
var ws = require('ws');

var wss = new ws.Server({
    server : server,
    path : '/helloworld'
});

wss.on('connection', function(ws) {
    var sessionId = null;
    var request = ws.upgradeReq;
    var response = {
        writeHead : {}
    };

    sessionHandler(request, response, function(err) {
        sessionId = request.session.id;
    });

    ws.on('error', (error) => {
        stop(sessionId);
    });

    ws.on('close', () => {
        stop(sessionId);
    });

    ws.on('message', (_message) => {
        var message = JSON.parse(_message);

        switch (message.id) {
        case 'start':
            sessionId = request.session.id;
            start(sessionId, ws, message.sdpOffer, function(error, sdpAnswer) {
                ws.send(JSON.stringify({
                    id : 'startResponse',
                    sdpAnswer : sdpAnswer
                }));
            });
            break;

        case 'stop':
            stop(sessionId);
            break;

        case 'onIceCandidate':
            onIceCandidate(sessionId, message.candidate);
            break;
        }

    });
});
```

After the initial connection `wss.on('connection', ...)`,  there are several message types to handle, the main two
message types to handle are `start`  which then makes a connection to the **Kurento** Media Server (see  `start()`
function below) and `onIceCandidate` (see `onIceCandidate()` function below). Stopping, closing the connection and
error handling are handled in a standard manner and are not discussed here.

### Back-End - Connecting to Kurento

Kurento offers a well-defined [WebSocket API](http://kurento.github.io/doc-kurento/) -
To establish a WebSocket connection, the client needs to send a WebSocket handshake request
to the `/kurento` endpoint, and the media server will return a WebSocket handshake response.

Programmatically, we need create an instance of the `KurentoClient` in the
application server. This is created using the Environment Variables values specified.
For example, if the Kurento is hosted at  `kurento-server`  and listening on port `8888`.

```javascript
const kurento = require('kurento-client');

function getKurentoClient(callback) {
    if (kurentoClient !== null) {
        return callback(null, kurentoClient);
    }

    kurento('ws://kurento-server:8888/kurento', (error, _kurentoClient) => {
        kurentoClient = _kurentoClient;
        callback(null, kurentoClient);
    });
}
```

The `KurentoClient` provides a simplied [interface](https://doc-kurento.readthedocs.io/en/stable/features/kurento_client.html)
for  manipulating Media Elements and Media Pipelines - a Node.js `npm` library and Java `jar` file are available.


### Back-End - Creating a Media Pipeline

To operate on the incoming video we will need to create a media pipeline - this is a processing widget
where the source generated by one element is used at the input for another. Pipeline elements can be
chained together and therefore represent a series of operations on the media stream.

In the Hello World example we will need a single `WebRtcEndpoint` that connects back to itself (i.e. in loopback)

These functions are called in the `start` function, which is fired when the `start` message is received:

```javascript
function start(sessionId, ws, sdpOffer, callback) {
    getKurentoClient((error, kurentoClient) => {
    	// Create a Media Pipeline
        kurentoClient.create('MediaPipeline', (error, pipeline) => {
            createMediaElements(pipeline, ws, (error, webRtcEndpoint) => {
                if (candidatesQueue[sessionId]) {
                    while(candidatesQueue[sessionId].length) {
                        const candidate = candidatesQueue[sessionId].shift();
                        webRtcEndpoint.addIceCandidate(candidate);
                    }
                }
                // Connect it back on itself (i.e. in loopback)
                connectMediaElements(webRtcEndpoint, (error) => {
                    webRtcEndpoint.on('OnIceCandidate', function(event) {
                        const candidate = kurento.getComplexType('IceCandidate')(event.candidate);
                        ws.send(JSON.stringify({
                            id : 'iceCandidate',
                            candidate : candidate
                        }));
                    });

                    webRtcEndpoint.processOffer(sdpOffer, (error, sdpAnswer) => {
                        sessions[sessionId] = {
                            'pipeline' : pipeline,
                            'webRtcEndpoint' : webRtcEndpoint
                        }
                        return callback(null, sdpAnswer);
                    });

                    webRtcEndpoint.gatherCandidates((error) => {
                    	if (error) {
                            return callback(error);
                        }
                    });
                });
            });
        });
    });
}
```
Where `createMediaElements()` and `connectMediaElements()` are the following callback functions:

```javascript
function createMediaElements(pipeline, ws, callback) {
    pipeline.create('WebRtcEndpoint', (error, webRtcEndpoint) => {
        if (error) {
            return callback(error);
        }

        return callback(null, webRtcEndpoint);
    });
}
```
```javascript
 function connectMediaElements(webRtcEndpoint, callback) {
    webRtcEndpoint.connect(webRtcEndpoint, (error) => {
        if (error) {
            return callback(error);
        }
        return callback(null);
    });
}
```

The connection between media elements is negotiated by exchanging [ICE](https://tools.ietf.org/html/rfc5245.html) candidates between the WebRTC peers - this can be achieved using the following boilerplate to use and store candidates:

```javascript
let candidatesQueue = {};

[...]

function onIceCandidate(sessionId, _candidate) {
    let candidate = kurento.getComplexType('IceCandidate')(_candidate);

    if (sessions[sessionId]) {
        let webRtcEndpoint = sessions[sessionId].webRtcEndpoint;
        webRtcEndpoint.addIceCandidate(candidate);
    }
    else {
        if (!candidatesQueue[sessionId]) {
            candidatesQueue[sessionId] = [];
        }
        candidatesQueue[sessionId].push(candidate);
    }
}
```

### Front-End - JavaScript on the rendered page

The connection and rendering of the video streams on the rendered web page is achieved using the JavaScript
[WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) helped along with
a client-side JavaScript library - `kurento-utils.js`  used to  simplify the WebRTC interaction with the
application server. The complete JavaScript can be found in `static/js/index.js`.


```javascript
var ws = new WebSocket('wss://' + location.host + '/helloworld');
var videoInput = document.getElementById('videoInput');
var videoOutput = document.getElementById('videoOutput');
var webRtcPeer = null;
```
```javascript
function start() {
    var options = {
      localVideo: videoInput,
      remoteVideo: videoOutput,
      onicecandidate : onIceCandidate
    }

    webRtcPeer = kurentoUtils.WebRtcPeer.WebRtcPeerSendrecv(options, function(error) {
        if(error) return onError(error);
        this.generateOffer(onOffer);
    });
}

function onIceCandidate(candidate) {
   sendMessage({
      id : 'onIceCandidate',
      candidate : candidate
   });
}

function onOffer(error, offerSdp) {
	sendMessage({
		id : 'start',
		sdpOffer : offerSdp
	});
}
```

Whenever a WebSocket message is received, either starting communication, changing ICE candidates or an error condition,
an appropriate action is taken.

```javascript
ws.onmessage = function(message) {
	var parsedMessage = JSON.parse(message.data);

	switch (parsedMessage.id) {
	case 'startResponse':
		webRtcPeer.processAnswer(message.sdpAnswer);
		break;
	case 'iceCandidate':
		webRtcPeer.addIceCandidate(parsedMessage.candidate)
		break;
	case 'error':
		// Error Handling
		break;
	}
}

```

A fuller description of the code can be found within the Kurento documenation.


# Altering Media Streams

In order to alter context, we will need to be able to process the media stream. This second example
builds on the previous WebRTC loopback video communication but also analyzes and
alters the media stream by detecting faces and placing a hat over detected faces. This is an example of a
Computer Vision and Augmented Reality filter.

![](https://fiware.github.io/tutorials.Media-Streams/img/magic-mirror.png)

##  Altering Media Streams - Start Up

To start the system with an example of **Kurento** altering a media stream, run the following command:

```console
./services magic-mirror
```

### Running the Example

To run the example, open a WebRTC compatible browser at `https://localhost:8443` and accept HTTPS traffic to open
the page. The application consists of a single HTML web page containing two HTML5 `<video>` tags: one showing
the local stream (as captured by the local webcam) and the other showing the remote stream sent by the media server
back to the client. Click on the start button and the  modified video will be displayed in on the right.

![](https://fiware.github.io/tutorials.Media-Streams/img/magic-mirror-screenshot.png)


## Analyzing the Code

The code under discussion can be found within the `kurento-magic-mirror` directory within the Git Repository



# Raising Context Events

A media stream can also be analyzed and used to raise context-related events. The final example of
this tutorial adds a vehicle number plate detector filter element to the WebRTC video communication

![](https://fiware.github.io/tutorials.Media-Streams/img/plate-detector.png)

The events raised could be associated to context entities, and therefore be suitable for integration within the FIWARE
ecosystem. For example if **Kurento** was attached to a security camera, code you be added to send a PATCH request to
the Orion Context Broker to update the context of entity **Camera X** to show that a vehicle registration plate
`XXX-xxx-XXX` was detected at time `yyy-yyy-yyy`.

## Raising Context Events  - Start Up

To start the system with an example of **Kurento** raising events, run the following command:

```console
./services plate-detection
```

### Running the Example


To run the example, open a WebRTC compatible browser at `https://localhost:8443` and accept HTTPS traffic to open
the page. The application consists of a single HTML web page containing two HTML5 `<video>` tags: one showing
the local stream (as captured by the local webcam) and the other showing the remote stream sent by the media server
back to the client. Click on the start button and the  same video will be displayed in both `<video>` elements.

Select the image of a Vehicle registration plate from the list below and display it on your phone and see if the
plate is detected


* [Argentina](https://fiware.github.io/tutorials.Media-Streams/img/vrn-argentina.jpg)
* [Botswana](https://fiware.github.io/tutorials.Media-Streams/img/vrn-botswana.jpg)
* [Australia (NSW)](https://fiware.github.io/tutorials.Media-Streams/img/vrn-new-south-wales.jpg)
* [Australia (WA)](https://fiware.github.io/tutorials.Media-Streams/img/vrn-western-australia.jpg)
* [Canada](https://fiware.github.io/tutorials.Media-Streams/img/vrn-new-brunswick.jpg)
* [Finland](https://fiware.github.io/tutorials.Media-Streams/img/vrn-finland.jpg)
* [India](https://fiware.github.io/tutorials.Media-Streams/img/vrn-india-kolkata.jpg)
* [Russia](https://fiware.github.io/tutorials.Media-Streams/img/vrn-russia.jpg)
* [Sweden](https://fiware.github.io/tutorials.Media-Streams/img/vrn-sweden.jpg)

Further Vehicle Registration Plate images are available on [Wikipedia](https://en.wikipedia.org/wiki/Vehicle_registration_plate)


![](https://fiware.github.io/tutorials.Media-Streams/img/plate-detector-screenshot.png)


For example with the vehicle registration plate displayed in the screenshot above, the following output is obtained.

```
License plate detected --8886AJR
```

The reliability of detection will depend on the camera and filter used.


## Analyzing the Code

The code under discussion can be found within the `kurento-platedetector` directory within the Git Repository




# Next Steps

Want to learn how to add more complexity to your FIWARE application by adding advanced features?
You can find out by reading the other [tutorials in this series](https://fiware-tutorials.readthedocs.io/en/latest)

For more ideas about the capabilities of the **Kurento** media server, please read the offical [Kurento Tutorial Documentation](https://doc-kurento.readthedocs.io/).

---

## License

[MIT](LICENSE) © FIWARE Foundation e.V.

The Program includes additional submodules which were obtained under license:

* [kurento-example/nodejs](https://github.com/Kurento/kurento-tutorial-node) -  © [Kurento](http://kurento.org) **Apache 2.0 license**

The Vehicle Registration Plate Images are public domain or have been obtained from Wikipedia Commons under license:

*  Argentina  © [Quilmeño89](https://commons.wikimedia.org/wiki/User:Quilme%C3%B1o89) **Creative Commons Attribution-Share Alike 4.0 International**
* Australia  © [EurovisionNim](https://commons.wikimedia.org/wiki/User:EurovisionNim) **Creative Commons Attribution-Share Alike 4.0 International**
* Finland  © [Krokodyl](https://commons.wikimedia.org/wiki/User:Krokodyl) **Creative Commons Attribution 2.5 Generic**
* India  © [Biswaruo Ganguly](https://commons.wikimedia.org/wiki/User:Gangulybiswarup) **Creative Commons Attribution 3.0 Unported**
* Russia  © [Krokodyl](https://commons.wikimedia.org/wiki/User:Krokodyl) **Creative Commons Attribution 2.5 Generic**
* Sweden  © [Lalpino](https://commons.wikimedia.org/wiki/User:Lalpino) **Creative Commons Attribution-Share Alike 4.0 International**






