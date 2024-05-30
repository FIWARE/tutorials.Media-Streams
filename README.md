[![FIWARE Banner](https://fiware.github.io/tutorials.Media-Streams/img/fiware.png)](https://www.fiware.org/developers)

[![FIWARE Media Streams](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/media-streams.svg)](https://github.com/FIWARE/catalogue/blob/master/processing/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.Media-Streams.svg)](https://opensource.org/licenses/MIT)
[![Kurento 6.7.1](https://img.shields.io/badge/Kurento-6.7.1-4f3495.svg)](http://doc-kurento.readthedocs.io/)
[![Support badge](https://img.shields.io/badge/tag-fiware-orange.svg?logo=stackoverflow)](https://stackoverflow.com/questions/tagged/fiware)

This is an Introductory Tutorial to [FIWARE Kurento](http://kurento.readthedocs.io/) - a media server generic enabler
used for the analysis and enhancement of video streams via [WebRTC](https://webrtc.org/). The tutorial discusses the
architecture of stream orientated systems, and demonstrates the key concepts behind the use of video streams, by
discussing code written in Node.js. Alternative code examples,written in Java and client-side JavaScript are also
available.

The tutorial introduces a series of exercises which can be run directly from within a [Docker](https://www.docker.com)
container, no HTTP calls are required.


# Start-Up

## NGSI-v2 Smart Supermarket

**NGSI-v2** offers JSON based interoperability used in individual Smart Systems. To run this tutorial with **NGSI-v2**, use the `NGSI-v2` branch.

```console
git clone https://github.com/FIWARE/tutorials.Media-Streams.git
cd tutorials.Media-Streams
git checkout NGSI-v2

./services create
./services start
```

| [![NGSI v2](https://img.shields.io/badge/NGSI-v2-5dc0cf.svg)](https://fiware-ges.github.io/orion/api/v2/stable/) | :books: [Documentation](https://github.com/FIWARE/tutorials.Media-Streams/tree/NGSI-v2) |
| --- | --- |


<!--
## NGSI-LD Smart Farm

**NGSI-LD** offers JSON-LD based interoperability used for Federations and Data Spaces. To run this tutorial with **NGSI-LD**, use the `NGSI-LD` branch.

```console
git clone https://github.com/FIWARE/tutorials.Media-Streams.git
cd tutorials.Media-Streams
git checkout NGSI-LD

./services create
./services start
```

| [![NGSI LD](https://img.shields.io/badge/NGSI-LD-d6604d.svg)](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.08.01_60/gs_cim009v010801p.pdf) | :books: [Documentation](https://github.com/FIWARE/tutorials.Media-Streams/tree/NGSI-LD) | <img  src="https://cdn.jsdelivr.net/npm/simple-icons@v3/icons/postman.svg" height="15" width="15"> [Postman Collection](https://fiware.github.io/tutorials.Media-Streams/ngsi-ld.html) |
| --- | --- | --- |

-->

---

## License

[MIT](LICENSE) © 2018-2024 FIWARE Foundation e.V.




