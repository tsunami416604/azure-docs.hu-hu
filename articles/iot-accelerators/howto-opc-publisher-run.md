---
title: Az OPC Publisher futtatása - Azure | Microsoft dokumentumok
description: Ez a cikk az OPC Publisher futtatását és hibakeresését ismerteti. A teljesítmény- és memóriaszempontokat is figyelembe veszi.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4f5d57bab51d537b64ce4b800737219663c3d7a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198786"
---
# <a name="run-opc-publisher"></a>Az OPC Publisher futtatása

Ez a cikk az OPC Publisher hirdetéshiba-keresési hibakeresési futtatását ismerteti. A teljesítmény- és memóriaszempontokat is figyelembe veszi.

## <a name="command-line-options"></a>Parancssori kapcsolók

Az alkalmazás használata `--help` a parancssori kapcsolóval a következőképpen jelenik meg:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Általában csak az alkalmazás első futtatásakor adja meg az IoT Hub tulajdonosának kapcsolati karakterláncát. A kapcsolati karakterlánc titkosítva van, és a platformtanúsítvány-tárolóban tárolódik. A későbbi futtatások során az alkalmazás beolvassa a kapcsolati karakterláncot a tanúsítványtárolóból. Ha minden futtatáskor megadja a kapcsolati karakterláncot, az IoT Hub-eszköz beállításjegyzékében az alkalmazáshoz létrehozott eszközt eltávolítja, majd újra létrehozza.

## <a name="run-natively-on-windows"></a>Natív futtatás Windows rendszeren

Nyissa meg az **opcpublisher.sln** projektet a Visual Studio alkalmazással, hozza létre a megoldást, és tegye közzé. Az alkalmazást a következő **célkönyvtárban** indíthatja el:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Saját készítésű tároló használata

Készítsd el a saját tároló, és indítsa el az alábbiak szerint:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Tároló használata a Microsoft Container Registry rendszerből

Van egy előre összeállított tároló a Microsoft Container Registry. Indítsa el a következőképpen:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

A [docker hubon](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) megtekintheti a támogatott operációs rendszereket és processzorarchitektúrákat. Ha az operációs rendszer és a CPU architektúra támogatott, a Docker automatikusan kiválasztja a megfelelő tárolót.

## <a name="run-as-an-azure-iot-edge-module"></a>Futtatás Azure IoT Edge-modulként

Az OPC Publisher készen áll az [Azure IoT](https://docs.microsoft.com/azure/iot-edge) Edge-modulként való használatra. Ha az OPC Publishert IoT Edge modulként használja, az egyetlen támogatott átviteli protokoll a **Amqp_Tcp_Only** és **a Mqtt_Tcp_Only.**

Ha az OPC-közzétevőt modulként szeretné hozzáadni az IoT Edge-telepítéshez, nyissa meg az IoT Hub beállításait az Azure Portalon, és hajtsa végre az alábbi lépéseket:

1. Nyissa meg az **IoT Edge-et,** és hozza létre vagy válassza ki az IoT Edge-eszközt.
1. Válassza **a Modulok beállítása**lehetőséget.
1. Válassza **a Hozzáadás** **lehetőséget a Telepítési modulok,** majd az **IoT Edge-modul csoportban.**
1. A **Név** mezőbe írja be a **publisher**t.
1. A **Kép URI** mezőjébe írja be a`mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Az elérhető címkéket a [Docker Hubon](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) találja
1. Illessze be a következő JSON-t a **Tároló létrehozási beállításai** mezőbe:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Ez a konfiguráció úgy konfigurálja az IoT Edge-et, hogy elindítson egy **közzétevő** nevű tárolót az OPC Publisher-lemezkép használatával. A tároló rendszerének állomásneve **közzétevőre**van állítva. Az OPC Publisher neve a következő `--aa`parancssori argumentummal történik: . Ezzel a beállítással az OPC Publisher megbízik az OPC UA-kiszolgálók tanúsítványaiban, amelyekhez csatlakozik. Az OPC Publisher parancssori kapcsolóit tetszőlegesen használhatja. Az egyetlen korlátozás az IoT Edge által támogatott **tárolólétrehozási beállítások** mérete.

1. Hagyja változatlanul a többi beállítást, és válassza a **Mentés** lehetőséget.
1. Ha az OPC-közzétevő kimenetét helyileg szeretné feldolgozni egy másik IoT Edge-modullal, lépjen vissza a **Modulok beállítása** lapra. Ezután lépjen az **Útvonalak megadása** lapra, és vegyen fel egy új útvonalat, amely a következő JSON-hoz hasonlóan néz ki:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. A **Modulok beállítása** lapon válassza a **Tovább**lehetőséget, amíg el nem éri a konfiguráció utolsó oldalát.
1. Válassza **a Küldés** lehetőséget, ha a konfigurációt az IoT Edge-nek szeretné elküldeni.
1. Amikor elindította az IoT Edge-et a peremhálózati eszközön, és a docker-tároló **közzétevője** `docker logs -f publisher` fut, az OPC Publisher naplókimenetét a naplófájl használatával vagy ellenőrzésével is megnézheti. Az előző példában a naplófájl felett `d:\iiotegde\publisher-publisher.log`van. Használhatja az [iot-edge-opc-publisher-diagnostics eszközt](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)is.

### <a name="make-the-configuration-files-accessible-on-the-host"></a>A konfigurációs fájlok elérhetővé tétele az állomáson

Ha az IoT Edge modul konfigurációs fájljait elérhetővé szeretné tenni a gazdafájlrendszerben, használja a következő **Tárolólétrehozási beállításokat.** A következő példa a Linux-tárolók windowsos használatával történő központi telepítésre mutat:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Ezekkel a beállításokkal az OPC Publisher beolvassa `./pn.json` azokat a csomópontokat, amelyeket közzé kell tennie a fájlból, és a tároló munkakönyvtára `/appdata` indításkor be van állítva. Ezekkel a beállításokkal az `/appdata/pn.json` OPC Publisher beolvassa a fájlt a tárolóból a konfiguráció leolvasásához. A `--pf` beállítás nélkül az OPC Publisher megpróbálja `./publishednodes.json`elolvasni az alapértelmezett konfigurációs fájlt.

Az alapértelmezett nevet `publisher-publisher.log`használó naplófájl a `/appdata` programba kerül, és a könyvtár is ebben a `CertificateStores` könyvtárban jön létre.

Ahhoz, hogy ezeket a fájlokat elérhetővé tegye a gazdafájlrendszerben, a tároló konfigurációja kötéscsatlakoztatási kötetet igényel. A `d://iiotedge:/appdata` kötés a `/appdata`könyvtárat , amely a tároló indításakor `d://iiotedge`az aktuális munkakönyvtár, a gazdakönyvtárhoz rendeli. E beállítás nélkül a következő tároló indításakor a rendszer nem őriz meg fájladatokat.

Ha Windows-tárolókat futtat, akkor a `Binds` paraméter szintaxisa eltérő. A tároló indításakor `c:\appdata`a munkakönyvtár . Ha a konfigurációs fájlt `d:\iiotedge`az állomás könyvtárába szeretné `HostConfig` helyezni, adja meg a következő leképezést a szakaszban:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Ha Linux-tárolókat futtat Linux-on, `Binds` a paraméter szintaxisa ismét más. A tároló indításakor `/appdata`a munkakönyvtár . Ha a konfigurációs fájlt `/iiotedge` az állomás könyvtárába szeretné `HostConfig` helyezni, adja meg a következő leképezést a szakaszban:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Figyelembe vett szempontok a tároló használatakor

A következő szakaszok néhány olyan dolgot sorolnak fel, amelyeket a tároló használatakor szem előtt kell tartani:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Hozzáférés az OPC Publisher OPC UA kiszolgálójához

Alapértelmezés szerint az OPC Publisher OPC UA kiszolgálója a 62222-es porton figyel. A bejövő port tárolóban való közzétételéhez használja a következő parancsot:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Tárolók közötti névfeloldás engedélyezése

Ha engedélyezni szeretné a névfeloldást a tárolón belül más tárolókhoz, hozzon létre `--network` egy felhasználói definiáló docker-hidat, és csatlakoztassa a tárolót ehhez a hálózathoz ezzel a beállítással. Rendeljen a tárolóhoz `--name` egy nevet is az alábbi beállítással:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

A tároló most már elérhető `publisher` a név használatával más tárolók ugyanazon a hálózaton.

### <a name="access-other-systems-from-within-the-container"></a>Hozzáférés más rendszerekhez a tárolón belülről

Más tárolók az előző szakaszban leírt paraméterekkel érhető el. Ha az operációs rendszer, amelyen a Docker található, a DNS engedélyezve van, akkor a DNS által ismert összes rendszer elérése működik.

A NetBIOS-névfeloldást használó hálózatokban engedélyezze a hozzáférést `--add-host` más rendszerekhez a tároló elindításával. Ez a beállítás hatékonyan hozzáad egy bejegyzést a tároló gazdafájljába:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Állomásnév hozzárendelése

Az OPC Publisher annak a számítógépnek a gazdagépnevét használja, amelyen fut a tanúsítvány és a végpont létrehozásához. A Docker véletlenszerű állomásnevet választ, ha nincs `-h` beállítva a beállítás. A következő példa bemutatja, hogyan állíthatja `publisher`be a tároló belső állomásnevét a következőre:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Kötéscsatolók használata (megosztott fájlrendszer)

A tárolófájlrendszer használata helyett választhatja a gazdafájlrendszert a konfigurációs adatok és a naplófájlok tárolására. A beállítás konfigurálásához `-v` használja `docker run` a kötéscsatlakoztatási módban való beállítást.

## <a name="opc-ua-x509-certificates"></a>OPC UA X.509 tanúsítványok

Az OPC UA X.509-es tanúsítványokat használ az OPC UA-ügyfél és kiszolgáló hitelesítésére, amikor kapcsolatot létesítenek, és titkosítják a köztük lévő kommunikációt. Az OPC Publisher az OPC UA verem által fenntartott tanúsítványtárolókat használja az összes tanúsítvány kezeléséhez. Indításkor az OPC Publisher ellenőrzi, hogy van-e saját tanúsítványa. Ha nincs tanúsítvány a tanúsítványtárolóban, és a parancssorban nincs egy átadott tanúsítvány, az OPC Publisher önaláírt tanúsítványt hoz létre. További információt az **InitApplicationSecurityAsync** metódusban talál a alkalmazásban. `OpcApplicationConfigurationSecurity.cs`

Az önaláírt tanúsítványok nem nyújtanak biztonságot, mivel nincsenek megbízható hitelesítésszolgáltató által aláírva.

Az OPC Publisher parancssori beállításokat biztosít a következőkhöz:

- Az OPC Publisher által használt aktuális alkalmazástanúsítvány CSR-adatainak beolvasása.
- Opc kiadó kiépítése hitelesítésszolgáltatóaláírással.
- Opc Publisher kiépítése új kulcspárral és megfelelő hitelesítésszolgáltatóaláírási tanúsítvánnyal.
- Tanúsítványok hozzáadása megbízható társ- vagy megbízható kiállítói tanúsítványtárolóhoz.
- Visszavont tanúsítványok listájának hozzáadása.
- Tanúsítvány eltávolítása a megbízható társ- vagy megbízható kibocsátói tanúsítványtárolóból.

Mindezek a beállítások lehetővé teszik, hogy fájlok vagy base64 kódolású karakterláncok használatával adja át a paramétereket.

Az összes tanúsítványtároló alapértelmezett tárolótípusa a fájlrendszer, amelyet parancssori beállításokkal módosíthat. Mivel a tároló nem biztosít állandó tárolót a fájlrendszerben, másik tárolótípust kell választania. A Docker-beállítással `-v` megőrizte a tanúsítványtárolókat a gazdafájlrendszerben vagy egy Docker-köteten. Ha docker-kötetet használ, a tanúsítványok base64 kódolású karakterláncok használatával.

A futásidejű környezet befolyásolja a tanúsítványok megőrzésének módját. Ne hozzon létre új tanúsítványtárolókat az alkalmazás minden egyes futtatásakor:

- A Windows natív módon futó alkalmazástanúsítvány-tároló nem `Directory` használható, mert a személyes kulcshoz való hozzáférés sikertelen. Ebben az esetben használja `--at X509Store`a lehetőséget.
- Linux docker-tárolóként futtatva leképezheti a tanúsítványtárolókat a `-v <hostdirectory>:/appdata`gazdafájlrendszerhez a docker futtatási lehetőséggel. Ez a beállítás a tanúsítványt az alkalmazások között állandóvá teszi.
- Linux os docker-tárolóként fut, és egy X509-tárolót szeretne használni `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` az alkalmazástanúsítványhoz, használja a docker futtatási lehetőséget és az alkalmazás beállítást`--at X509Store`

## <a name="performance-and-memory-considerations"></a>A teljesítményre és a memóriára vonatkozó szempontok

Ez a szakasz a memória és a teljesítmény kezelésére szolgáló lehetőségeket ismerteti:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parancssori paraméterek a teljesítmény és a memória szabályozásához

Az OPC Publisher futtatásakor tisztában kell lennie a teljesítménykövetelményekkel és a gazdagépen rendelkezésre álló memória-erőforrásokkal.

A memória és a teljesítmény egymástól függ, és mindkettő a közzétételre konfigurált csomópontok konfigurációjától függ. Győződjön meg arról, hogy a következő paraméterek megfelelnek az Ön igényeinek:

- Az IoT Hub időközt küld:`--si`
- IoT Hub-üzenet `1`mérete (alapértelmezett):`--ms`
- Figyelt elemek várólista kapacitása:`--mq`

A `--mq` paraméter a belső várólista kapacitásának felső határát szabályozza, amely puffereli az összes OPC-csomópont értékváltozási értesítését. Ha az OPC Publisher nem tud elég gyorsan üzeneteket küldeni az IoT Hubnak, ez a várólista puffereli az értesítéseket. A paraméter beállítja a pufferelhető értesítések számát. Ha azt látja, hogy a várólistában lévő elemek száma növekszik a tesztfuttatások során, akkor az üzenetek elvesztésének elkerülése érdekében:

- Az IoT Hub küldési időközének csökkentése
- Az IoT Hub-üzenetek méretének növelése

A `--si` paraméter kényszeríti az OPC Publisher-t, hogy a megadott időközönként üzeneteket küldjön az IoT Hubnak. Az OPC Publisher akkor küld üzenetet, amikor `--ms` eléri a paraméter által megadott üzenetméretet, `--si` vagy amint a paraméter által megadott időköz elérésekor eléri. Az üzenetméret beállítás letiltásához használja a használatát. `--ms 0` Ebben az esetben az OPC Publisher a lehető legnagyobb 256 kB-os IoT Hub-üzenetméretet használja az adatok kötegeléséhez.

A `--ms` paraméter lehetővé teszi az IoT Hubnak küldött üzenetek kötegelését. A használt protokoll határozza meg, hogy az IoT Hubnak küldött üzenet többletterhelése magas-e a hasznos adat küldésének tényleges idejéhez képest. Ha a forgatókönyv lehetővé teszi a késést, amikor az Adatok at IoT Hub, konfigurálja az OPC Publisher a legnagyobb 256 kB-os üzenetméretet használja.

Mielőtt éles környezetben használná az OPC Publishert, tesztelje a teljesítményt és a memóriahasználatot éles környezetben. A `--di` paraméter segítségével megadhatja azt az időközt másodpercben, amíg az OPC Publisher diagnosztikai adatokat ír.

### <a name="test-measurements"></a>Vizsgálati mérések

A következő példadiagnosztika `--si` különböző `--ms` értékekkel rendelkező méréseket és 500 csomópontot közzéíró paramétereket jelenít meg 1 másodperces OPC közzétételi időközzel.  A teszt 120 másodpercig natív módon használta az OPC Publisher hibakeresési buildjét a Windows 10 rendszeren. Az IoT Hub protokoll volt az alapértelmezett MQTT protokoll.

#### <a name="default-configuration---si-10---ms-262144"></a>Alapértelmezett konfiguráció (--si 10 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Az alapértelmezett konfiguráció 10 másodpercenként küldi az adatokat az IoT Hubnak, vagy ha 256 kB adat áll rendelkezésre az IoT Hub számára a betöltéshez. Ez a konfiguráció körülbelül 10 másodperces mérsékelt késést ad hozzá, de a nagy üzenetméret miatt a legkisebb az adatvesztés valószínűsége. A diagnosztikai kimenet azt mutatja, hogy nincsenek `monitored item notifications enqueue failure: 0`elveszett OPC-csomópontfrissítések: .

#### <a name="constant-send-interval---si-1---ms-0"></a>Állandó küldési időköz (--si 1 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Ha az üzenet mérete 0, akkor az OPC Publisher belsőleg kötegeli az adatokat a legnagyobb támogatott IoT Hub-üzenetméret, azaz 256 kB használatával. A diagnosztikai kimenet azt mutatja, hogy az üzenet átlagos mérete 115 019 bájt. Ebben a konfigurációban az OPC Publisher nem veszíti el az OPC-csomópont értékfrissítéseit, és az alapértelmezetthez képest alacsonyabb késést tartalmaz.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Minden OPC-csomópontérték frissítésének elküldése (--si 0 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Ez a konfiguráció minden OPC-csomópont érték módosítása egy üzenetet az IoT Hub. A diagnosztika azt mutatja, hogy az üzenet átlagos mérete 234 bájt, ami kicsi. Ennek a konfigurációnak az az előnye, hogy az OPC Publisher nem ad hozzá késést. Az elveszett OPC-csomópont értékfrissítések`monitored item notifications enqueue failure: 44624`( ) száma magas, amelyek ezt a konfigurációt alkalmatlanná teszik a nagy mennyiségű telemetriai adatokközzétételét eredményező forgatókönyvek esetében.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximális kötegelés (-si 0 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Ez a konfiguráció a lehető legtöbb OPC-csomópont-értéket frissíti. Az IoT Hub-üzenetek maximális mérete 256 kB, amely itt van konfigurálva. Nincs kért küldési időköz, ami azt jelenti, hogy az IoT Hub betöltési adatai nak mennyisége határozza meg a késést. Ez a konfiguráció rendelkezik a legkisebb valószínűséggel az OPC-csomópontok értékeinek elvesztésére, és alkalmas nagyszámú csomópont közzétételére. Ha ezt a konfigurációt használja, győződjön meg arról, hogy a forgatókönyv nem rendelkezik olyan feltételekkel, ahol nagy késés, ha az üzenet mérete 256 kB nem éri el.

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése

Az alkalmazás hibakereséséhez nyissa meg az **opcpublisher.sln** megoldásfájlt a Visual Studióval, és használja a Visual Studio hibakereső eszközeit.

Ha az OPC-közzétevő ben az OPC UA-kiszolgálót kell elérnie, győződjön meg arról, hogy a tűzfal engedélyezi a hozzáférést ahhoz a porthoz, amelyet a kiszolgáló figyel. Az alapértelmezett port: 62222.

## <a name="control-the-application-remotely"></a>Az alkalmazás távoli vezérlése

A csomópontok közzétételre konfigurálása az IoT Hub közvetlen metódusokkal végezhető el.

Az OPC Publisher néhány további IoT Hub-közvetlen metódushívásolvasást valósít meg:

- Általános információk.
- Diagnosztikai információk az OPC-munkamenetek, előfizetések és figyelt elemek.
- Az IoT Hub-üzenetek és -események diagnosztikai információi.
- Az indítási napló.
- A napló utolsó 100 sora.
- Állítsa le az alkalmazást.

A következő GitHub-adattárak olyan eszközöket tartalmaznak, [amelyekkel konfigurálhatja a csomópontokat](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) a diagnosztikai információk közzétételére és [olvasására.](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) Mindkét eszköz tárolóként is elérhető a Docker Hubban.

## <a name="use-a-sample-opc-ua-server"></a>OpC UA-kiszolgáló minta használata

Ha nem rendelkezik valódi OPC UA-kiszolgálóval, a [kezdéshez használhatja a minta OPC UA PLC-t.](https://github.com/Azure-Samples/iot-edge-opc-plc) Ez a minta PLC a Docker Hubon is elérhető.

Számos címkét valósít meg, amelyek véletlenszerű adatokat és anomáliákat. Kiterjesztheti a mintát, ha további címkeértékeket kell szimulálnia.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az OPC Publisher futtatását, az ajánlott következő lépések az [OPC Twin](overview-opc-twin.md) és [az OPC Vault](overview-opc-vault.md)megismerése.
