---
title: Futtassa az OPC-közzétevő – Azure |} A Microsoft Docs
description: Az OPC Publisher futtatása
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3b386171afc7916e5e803c39a9c7b3520752e6fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603752"
---
# <a name="run-opc-publisher"></a>Az OPC Publisher futtatása

Ez a cikk ismerteti, hogyan futtathat ad hibakeresési az OPC-közzétevő. Kezeli a teljesítménnyel és memóriával kapcsolatos megfontolások is.

## <a name="command-line-options"></a>Parancssori kapcsolók

Az alkalmazás használatának használatával jelenik meg a `--help` parancssori kapcsolót az alábbiak szerint:

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

A tulajdonos IoT Hub kapcsolati karakterláncra általában csak az első alkalmazás megadása. A kapcsolati karakterlánc titkosítása és a platform tárolni. Az újabb fut az alkalmazás beolvassa a kapcsolati karakterláncot a tanúsítványtárolóból. A kapcsolati karakterlánc minden egyes futtatásakor adja meg, ha az eszközt, hogy létrejött az alkalmazás az IoT Hub eszközjegyzékében eltávolítja és újra létre kell hozni.

## <a name="run-natively-on-windows"></a>Windows natív futtatását

Nyissa meg a **opcpublisher.sln** projektre a Visual Studióval, a megoldás létrehozása és közzététele. Az alkalmazást elindíthat az a **céloldali könyvtár** közzétett módon:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Egy saját készítésű tároló használata

Saját tárolót hozhat létre, és indítsa el a következő:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Egy tároló használata a Microsoft Container registryből

Nincs egy előre elkészített tároló elérhető a Microsoft tároló-beállításjegyzékbe. Indítsa el a következő:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Ellenőrizze [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) szeretné megtekinteni a támogatott operációs rendszerek és processzorarchitektúrák. Ha az operációs rendszer és a CPU architektúra támogatott, a Docker automatikusan kiválasztja a megfelelő tárolót.

## <a name="run-as-an-azure-iot-edge-module"></a>Futtatás mint egy Azure IoT Edge-modul

Az OPC-közzétevő készen áll a használatra akkor egy [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) modul. Amikor az IoT Edge-modul az OPC-közzétevő használja, az egyetlen támogatott protokollok a következők átviteli **Amqp_Tcp_Only** és **Mqtt_Tcp_Only**.

OPC Publisher modulként hozzá az IoT Edge-példányban, nyissa meg az Azure Portalon az IoT Hub beállításait, és kövesse az alábbi lépéseket:

1. Lépjen a **IoT Edge** , és hozzon létre vagy válassza ki az IoT Edge-eszközt.
1. Válassza a **Modulok beállítása** lehetőséget.
1. Válassza ki **hozzáadása** alatt **üzembe helyezési modulok** , majd **IoT Edge-modul**.
1. Az a **neve** írja be a következőt **közzétevő**.
1. Az a **rendszerkép URI** mezőben adjon meg `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. A rendelkezésre álló címkék találhat [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Illessze be a következő JSON-kódot a **tároló létrehozása beállítások** mező:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Ez a konfiguráció konfigurálja nevű tároló indítása az IoT Edge **közzétevő** az OPC-közzétevő rendszerkép használatával. A tároló rendszer állomásnevét értékre van állítva **közzétevő**. Az OPC-közzétevő neve a következő parancssori argumentummal: `--aa`. Ezzel a beállítással az OPC-közzétevő megbízik való legközelebbi csatlakozáskor az OPC UA-kiszolgálók tanúsítványait. Az OPC-közzétevő parancssori beállításokat használhat. Az egyetlen megkötés méretétől, a **tároló létrehozása beállítások** IoT Edge által támogatott.

1. Hagyja változatlanul a többi beállítást, és válassza a **Mentés** lehetőséget.
1. Ha meg szeretné feldolgozni az OPC-közzétevő helyileg az IoT Edge-modul kimenete, lépjen vissza a **modulok beállítása** lap. Ezután nyissa meg a **útvonalak megadása** lapra, és adjon hozzá egy új útvonalat, hogy a következőképpen néz ki: a következő JSON-ra:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Térjen vissza a **modulok beállítása** lapon jelölje be **tovább**, amíg el nem éri a konfiguráció utolsó oldalán.
1. Válassza ki **küldés** a konfiguráció küldése az IoT Edge-ben.
1. Ha már elkezdte az IoT Edge az edge-eszköz és a docker-tároló **közzétevő** fut, majd az OPC-közzétevő naplókimenetét megtekinthet használatával `docker logs -f publisher` , vagy a naplófájlban ellenőrizheti. Az előző példában a naplófájl fölött van `d:\iiotegde\publisher-publisher.log`. Is használhatja a [iot-edge-opc-közzétevő-diagnostics eszköz](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Győződjön meg arról, a konfigurációs fájlok érhetők el a gazdagépen

Ahhoz, hogy az IoT Edge modul konfigurációjának fájlokat a gazdafájlrendszerbe elérhető, használja a következő **tároló létrehozása beállítások**. Az alábbi példa használatával Linux rendszerű tárolókat a Windows központi telepítés van:

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

Ezekkel a beállításokkal, az OPC-közzétevő beolvassa a fájlból közzé kell tenni a csomópontok `./pn.json` és a tároló munkakönyvtár beállítása `/appdata` indításkor. Ezekkel a beállításokkal, az OPC-közzétevő beolvassa a fájl `/appdata/pn.json` beolvasni a konfigurációját a tárolóból. Nélkül a `--pf` beállítás, olvassa el az alapértelmezett konfigurációs fájl az OPC-közzétevő próbál `./publishednodes.json`.

A naplófájl, az alapértelmezett név használatával `publisher-publisher.log`, írt `/appdata` és a `CertificateStores` könyvtár is létre ebben a könyvtárban.

Ahhoz, hogy ezeket a fájlokat a gazdafájlrendszerbe érhető el, a tárolókonfigurációt kötési csatlakoztatási kötetet igényel. A `d://iiotedge:/appdata` kötést hozzárendeli a címtár `/appdata`, amely az aktuális munkakönyvtárban tároló indításkor, a gazdagép könyvtárba `d://iiotedge`. Ez a beállítás nélkül a tároló következő elindulásakor a rendszer megőrzi nincs fájladatok.

Ha Windows-tárolók, majd szintaxisa a következő futtatja a `Binds` paraméter nem egyezik. A tároló indításakor, a munkakönyvtárban történik `c:\appdata`. A konfigurációs fájl elhelyezése a címtár `d:\iiotedge`a gazdagépen, adja meg a következő társítás a a `HostConfig` szakaszban:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Ha Linux-tárolók futtat linuxon szintaxisát a `Binds` paraméter újra nem egyezik. A tároló indításakor, a munkakönyvtárban történik `/appdata`. A konfigurációs fájl elhelyezése a címtár `/iiotedge` a gazdagépen, adja meg a következő társítás a a `HostConfig` szakaszban:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Egy tároló használatának szempontjai

Az alábbi szakaszok tartalmazzák, vegye figyelembe, amikor a tárolót használja a következőkre:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Az OPC-közzétevő OPC UA-kiszolgálóhoz való hozzáférés

Alapértelmezés szerint az OPC Publisher OPC UA-kiszolgáló a 62222 portot figyeli. Tegye elérhetővé a bejövő portot egy tárolóban, használja a következő parancsot:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Intercontainer névfeloldás engedélyezése

Ahhoz, hogy a névfeloldás a a tárolón belüli más tárolókba, hozzon létre egy felhasználót docker hídhálózat határozza meg, és csatlakozzon a tárolóhoz a hálózati a `--network` lehetőséget. A név használatával is hozzárendelhet a tárolót a `--name` lehetőséget az alábbiak szerint:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

A tároló már elérhető a név használatával `publisher` által ugyanazon a hálózaton található más tárolóktól.

### <a name="access-other-systems-from-within-the-container"></a>Hozzáférés a más rendszerekkel a tárolón belül

Más tárolók is elérhetik az előző szakaszban ismertetett paraméterekkel. Ha operációs rendszer, amelyen a Docker üzemeltetett DNS engedélyezve van, majd fér hozzá az összes ismert DNS rendszer működik.

NetBIOS-névfeloldás használó hálózatok, engedélyezése más rendszerekhez való hozzáférést a tárolóhoz elindításával a `--add-host` lehetőséget. Ez a beállítás hatékonyan ad hozzá egy bejegyzést a tároló gazdagép fájlt:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Egy állomásnév hozzárendelése

Az OPC-közzétevő használja a tanúsítvány és a végpont létrehozásakor futtató gép állomásnevét. A docker egy véletlenszerű állomásnév úgy dönt, ha egy nem a `-h` lehetőséget. Az alábbi példa bemutatja, hogyan állíthatja be a tároló belső állomásnevét `publisher`:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Kötési csatlakoztatása (megosztott fájlrendszer) használata

A tároló fájlrendszer helyett választhatja a gazdagép konfigurációs adatok tárolására, és a naplófájlok fájlrendszer. Ez a beállítás konfigurálásához használja a `-v` lehetőség a `docker run` kötési csatlakoztatási módban.

## <a name="opc-ua-x509-certificates"></a>Az OPC UA X.509-tanúsítványok

Az OPC UA X.509-tanúsítványokat használ, az OPC UA-ügyfél és kiszolgáló hitelesítéséhez, a kapcsolat létesítéséhez használt és az azok közötti kommunikáció titkosításához. Az OPC-közzétevő OPC UA-verem által karbantartott tanúsítványtárolókat használ minden tanúsítvány kezelésére. Az indítás során az OPC-közzétevő ellenőrzi, hogy maga a tanúsítvány. Van-e a tanúsítványtárolóban nincs tanúsítvány, és a egy nem tartozik átadott a parancssorban, az OPC-közzétevő létrehoz egy önaláírt tanúsítványt. További információkért lásd: a **InitApplicationSecurityAsync** metódus az `OpcApplicationConfigurationSecurity.cs`.

Önaláírt tanúsítványok nem biztosít minden olyan biztonsági azok egy megbízható hitelesítésszolgáltató által nem regisztrált.

Az OPC-közzétevő parancssori kapcsolók biztosítja:

- Az aktuális alkalmazás olyan tanúsítványt használ az OPC-közzétevő CSR-információk lekéréséhez.
- A CA üzembe helyezése az OPC-közzétevő önaláírt tanúsítványt.
- Az OPC-közzétevő üzembe helyezése egy új kulcspárt és a hitelesítésszolgáltató megfelelő önaláírt tanúsítványt.
- Tanúsítványok hozzáadása a megbízható társ vagy a megbízható kibocsátói tanúsítvány tároló.
- Adja hozzá a visszavont tanúsítványok Listáját.
- Távolítsa el a tanúsítványt a megbízható társ vagy a megbízható kiállítók tanúsítványtároló.

Ezek a beállítások lehetővé teszik a fájlok vagy base64-kódolású karakterláncok paramétereket adja át.

Alapértelmezett tároló összes tanúsítvány-tárolóinak típus a fájlrendszer, amely parancssori kapcsolók használatával módosíthatja. Mivel a tároló a fájlrendszer nem biztosítja az állandó tárolóból, ki kell választania egy másik tároló típusa. A Docker használata `-v` csomagtulajdonságokban a tanúsítványt a gazdagép fájlrendszerébe vagy egy Docker-köteten tárolja. Ha egy Docker-kötetet használ, a tanúsítványokat a base64-kódolású karakterláncok adhat át.

A futtatási környezetet érinti, hogyan tanúsítványok megmaradnak. Kerülje az új minden alkalommal, amikor futtatja az alkalmazást:

- Windows natív módon futtatja, nem használhat egy alkalmazás tanúsítványtároló típusú `Directory` a titkos kulcs hozzáférés meghiúsul, mert. Ebben az esetben használja a beállítást `--at X509Store`.
- Linux docker-tárolóban fut, leképezheti a tanúsítványtárolók a gazdagép fájlrendszerébe a docker, futtassa a beállítás `-v <hostdirectory>:/appdata`. Ez a beállítás lehetővé teszi a tanúsítvány állandó alkalmazás frissítési kísérletei során.
- Linux rendszerű futtató docker-tárolót, és szeretné egy X509 használja az alkalmazás tanúsítványának tárolni, használhatja a docker, futtassa a beállítás `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` és az alkalmazás lehetőség `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Teljesítménnyel és memóriával kapcsolatos megfontolások

Ez a szakasz ismerteti a memória és teljesítmény kezelésére vonatkozó beállítások:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parancssori paraméterek vezérlő teljesítmény és a memória

Az OPC-közzétevő futtatásakor kell figyelembe venni a teljesítményre vonatkozó követelmények és a gazdagépen rendelkezésre álló memória-erőforrások.

Memória és teljesítmény egymástól függenek, és mindkettő közzétételéhez konfigurálnia hány csomópontok konfigurációtól függnek. Győződjön meg arról, hogy a következő paraméterek megfelelnek-e a követelményeknek:

- Az IoT Hub küldi időköz: `--si`
- Az IoT Hub-üzenetek mérete (alapértelmezett `1`): `--ms`
- Figyelt elemek várólista-kapacitás: `--mq`

A `--mq` a paraméterrel állítható be a kapacitás, a belső várólista, amely puffereli az összes OPC csomópont értéke változási értesítéseket felső határnál. Ha az OPC-közzétevő nem küld üzeneteket az IoT hub gyors elég, a várólista pufferek az értesítéseket. A paraméter állítja be, amely képes lehet pufferelt értesítések száma. Ha ez a teszt futtatások egyre várólistában lévő elemek száma, majd fenntartása mellett üzenetek elkerülése érdekében el a következőket:

- Csökkentheti az IoT Hub küldési időköz
- Növelje az IoT Hub-üzenetek mérete

A `--si` paraméter arra kényszeríti az OPC-közzétevő küldhet üzeneteket az IoT Hub a megadott időközönként. Az OPC-közzétevő üzenetet küld, amint az üzenet mérete a megadott a `--ms` paraméter elérte vagy által meghatározott, amint az az időköz az `--si` paraméter elérésekor. Az üzenet mérete beállítás letiltásához használja `--ms 0`. Ebben az esetben az OPC-közzétevő használja a legnagyobb lehetséges az IoT Hub üzenet mérete 256 KB-os kötegelt adatokat.

A `--ms` paraméter lehetővé teszi az IoT hubnak küldött üzenetek kötegelt. A protokoll használata esetén meghatározza, hogy egy üzenetet az IoT hub járó többletterhelést magas a tényleges idő a hasznos adatainak küldésére képest. Ha a forgatókönyv késésének lehetővé teszi, amikor az IoT Hub által betöltött adatok, konfigurálja az OPC-közzétevő a legnagyobb üzenet mérete 256 KB-os.

Mielőtt használná az OPC-közzétevő éles forgatókönyvekben, tesztelje a teljesítmény- és memóriahasználat éles körülmények között. Használhatja a `--di` paraméterrel adja meg az időközt (másodpercben), az OPC-közzétevő diagnosztikai adatokat ír az adatbázis.

### <a name="test-measurements"></a>Teszt mérések

A következő példa diagnosztikai értékeit mértékek megjelenítése `--si` és `--ms` közzététele egy OPC közzétételi időköze 1 másodperc 500 csomóponton a paramétereket.  A teszt használja az OPC-közzétevő hibakeresési buildet a Windows 10-es natív módon 120 másodperc. Az IoT Hub protokoll nem az alapértelmezett MQTT protokoll.

#### <a name="default-configuration---si-10---ms-262144"></a>Alapértelmezett konfiguráció (--10 – ms 262144 si)

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

Az alapértelmezett konfiguráció adatokat küld az IoT hub 10 másodpercenként, vagy ha a 256 kB méretű adatot érhető el, hogy az IoT Hub. Ez a konfiguráció egy mérsékelt körülbelül 10 másodperces késleltetéssel, de rendelkezik a legalacsonyabb valószínűségi adatok fenntartása mellett nagy méretű üzenetek mérete miatt. A diagnosztikai kimenetet jeleníti meg, hogy nincsenek elveszett OPC csomópont frissítések: `monitored item notifications enqueue failure: 0`.

#### <a name="constant-send-interval---si-1---ms-0"></a>Állandó küldési időköz (--si 1 – az ms 0)

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

Ha az üzenet mérete 0 értékre van állítva majd az OPC-közzétevő belsőleg kötegeli az adatokat a legnagyobb támogatott az IoT Hub üzenet mérete 256 KB-os használatával. A diagnosztikai kimenetet jeleníti meg, az átlagos mérete 115,019 bájt. Ebben a konfigurációban az OPC-közzétevő nem veszíti el az összes OPC-csomópont értéke frissítéseket, és az alapértelmezett képest még kisebb hálózati késést.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Minden egyes OPC-csomópont érték frissítés küldése (--si 0 – ms 0)

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

Ez a konfiguráció esetében minden egyes OPC-csomópont érték módosítása egy üzenetet az IoT hub küldi. A diagnosztika megjelenítése az átlagos üzenetméret érték 234 bájt, amely kis. Ez a konfiguráció előnye, hogy az OPC-közzétevő nem adja hozzá bármelyik késés. Elveszett OPC-csomópont érték frissítések száma (`monitored item notifications enqueue failure: 44624`) van magas, amely győződjön meg arról, ez a konfiguráció nem alkalmas nagy mennyiségű telemetria közzé kell tenni a forgatókönyvekhez.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximális kötegelés (--0 – ms 262144 si)

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

Ez a konfiguráció a lehető kötegeli az annyi OPC csomópont értéke frissítéseket. Az IoT Hub üzenet maximális mérete 256 kB, ami itt van konfigurálva. Hiba a nem küldési időköz nem kérte, ami azt jelenti, hogy az IoT hub adatok mennyisége határozza meg, a késés. Ez a konfiguráció fenntartása mellett az összes OPC-csomópont értékei minimális valószínűségét rendelkezik, és alkalmas nagy számú csomópont közzététele. Ha használja ezt a konfigurációt, győződjön meg arról, a forgatókönyvnek nincs feltételek nagy a késés bemutatott, ha az üzenet mérete 256 KB-os el.

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése

Az alkalmazás hibakeresése, nyissa meg a **opcpublisher.sln** megoldást a Visual Studióval fájlt, és a Visual Studio hibakereső eszközöket.

Ha az OPC-közzétevő az OPC UA-kiszolgáló eléréséhez, győződjön meg arról, hogy a tűzfal engedélyezi-e a port hozzáférése van szüksége a kiszolgáló figyel. Az alapértelmezett port a következő: 62222.

## <a name="control-the-application-remotely"></a>Az alkalmazás távoli vezérlését

Közzététele a csomópontok konfigurálása elvégezhető az IoT Hub használatával közvetlen metódusokat.

Az OPC-közzétevő valósít meg néhány további IoT Hub közvetlen metódust hívja, olvassa el:

- Általános információk.
- Diagnosztikai adatok OPC munkamenetek, előfizetések és a figyelt elemek.
- Diagnosztikai információ az IoT Hub-üzenetek és események.
- Az Indítási napló.
- A napló a legutóbbi 100 sor.
- Állítsa le az alkalmazást.

Az alábbi GitHub-adattárak eszközöket tartalmaz [közzététele a csomópontok beállítása](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) és [olvassa el a diagnosztikai adatokat](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). A Docker hubon tárolókként mindkét eszközök is érhetők el.

## <a name="use-a-sample-opc-ua-server"></a>Egy olyan OPC UA-kiszolgáló

Ha egy valós OPC UA-kiszolgáló nem rendelkezik, használhatja a [mintát vesz az OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) a kezdéshez. Ez a minta PLC a Docker Hub is érhető el.

Címkék, amelyek véletlenszerű adat és rendellenességeket a címkék létrehozása számos valósítja meg. Ha szeretne további címkeértékeket szimulálása ra is kiterjesztheti a minta.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan futtathat az OPC-közzétevő,-e a javasolt következő lépések megismeréséhez [OPC Ikereszköz](overview-opc-twin.md) és [OPC tároló](overview-opc-vault.md).
