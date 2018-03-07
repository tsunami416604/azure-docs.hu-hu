---
title: "Az Azure IoT Suite csatlakoztatott gyári OPC Publisherrel |} Microsoft Docs"
description: "Hogyan hozza létre és telepítheti a csatlakoztatott gyári OPC publisher hivatkozás végrehajtása."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 934f4deb3139c136c871ab0125ba45267e1d1b05
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2018
---
# <a name="opc-publisher-for-azure-iot-edge"></a>Az Azure IoT peremhálózati OPC közzétevője

Ez a cikk ismerteti, hogyan OPC Publisher hivatkozás végrehajtása használandó. A referencia-megvalósítási Azure IoT Edge böngésző számára a használatát mutatja be:

- Kapcsolódás a meglévő OPC EE-kiszolgálókhoz.
- JSON-kódolású telemetriai adatokat ezekről a kiszolgálókról közzététele a OPC EE *Pub/Sub* formátumú, egy JSON-adattartalmat, az Azure IoT Hub használatával. Az átviteli protokollokat, amely támogatja az Azure IoT peremhálózati használhatja.

Ez az alkalmazás referencia tartalmazza:

- Egy OPC EE *ügyfél* meglévő OPC EE-kiszolgálók a hálózaton történő kapcsolódáshoz.
- Egy OPC EE *server* , melyekkel kezelheti a közzétett mi 62222 portot figyel.

Az alkalmazás a .NET Core segítségével van megvalósítva, és a .NET Core által támogatott platformokon futtatható.

A közzétevő újrapróbálkozási logika valósítja meg, ha végpontok kapcsolatok létesítése. A közzétevő végpontokat a tárolás során is garantálják életben kérések megadott számát belül válaszol vár. Az újrapróbálkozási logika lehetővé teszi, hogy a közzétevő körülmények például áramkimaradás OPC EE-kiszolgáló észlelni.

Az egyes különálló közzétételi intervallumát OPC EE-kiszolgálóhoz létrehoz egy különálló előfizetés, amelyben a közzétételi intervallumát minden csomópont frissítése.

Hálózati terhelés csökkentése érdekében a közzétevő támogatja az IoT-központ küldött adatok kötegelés. A kötegelt küldi el az IoT-központ csak akkor, ha a konfigurált csomag méretet.

Ez az alkalmazás OPC alapjainak OPC EE hivatkozást verem használ, és ezért licencelési korlátozások érvényesek. Látogasson el a http://opcfoundation.github.io/UA-.NETStandardLibrary/ OPC EE dokumentációk és licencelési időszakonként.

A OPC Publisher forráskódját található a [Azure IoT peremhálózati OPC közzétevője](https://github.com/Azure/iot-edge-opc-publisher) GitHub-tárházban.

## <a name="prerequisites"></a>Előfeltételek

Az alkalmazás létrehozásához szüksége a [.NET Core SDK 1.1.](https://docs.microsoft.com/dotnet/core/sdk) az operációs rendszerhez.

## <a name="build-the-application"></a>Az alkalmazás létrehozása

### <a name="as-native-windows-application"></a>Natív Windows-alkalmazás

Nyissa meg a `OpcPublisher.sln` projektre a Visual Studio 2017 és által F7 elérte-e a megoldás felépítéséhez.

### <a name="as-docker-container"></a>Docker-tároló

Az alkalmazás a Windows Docker-tároló létrehozásához használja a `Dockerfile.Windows` konfigurációs fájlt.

Az alkalmazás egy Linux Docker-tároló létrehozásához használja a `Dockerfile` konfigurációs fájlt.

A fejlesztői számítógépén a tárház gyökérkönyvtárában írja be a következő parancsot a konzol:

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

A `-f` választás, `docker build` nem kötelező, és alapértelmezés szerint a rendszer használni a `Dockerfile` konfigurációs fájlt.

Docker is lehetővé teszi a közvetlenül a git-tárház létrehozásához. A Linux Docker-tároló, a következő paranccsal hozhat létre:

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>A OPC EE-csomópontok közzététele

Konfigurálásához OPC EE csomópontok kell rendelkeznie az értékekre közzétett Azure IoT Hub JSON formátumú konfigurációs fájl létrehozása. Az alapértelmezett név a konfigurációs fájl `publishednodes.json`. Az alkalmazás frissíti, és menti ezt a konfigurációs fájlt használja a OPC EE server módszereket **PublishNode** vagy **UnpublishNode**.

A konfigurációs fájl szintaxisa a következő:

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

### <a name="command-line-options"></a>Parancssori kapcsolók

Az alkalmazás teljes használat megjelenítéséhez használja a `--help` parancssori kapcsolót. Az alábbi példában a parancs szerkezete:

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname` a használandó OPC EE-alkalmazás neve van. Ez a paraméter megadása kötelező. Az alkalmazás nevét a közzétevő regisztrálja az IoT-központ eszközbeállításjegyzékben is szolgál.

`IoT Hubconnectionstring` az IoT-központ tulajdonos kapcsolati karakterlánc. Ez a paraméter nem kötelező megadni.

A következő beállításokat támogatja:

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
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
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

A következő környezeti változók segítségével szabályozhatja az alkalmazást:
- `_HUB_CS`: az IoT-központ tulajdonos kapcsolati karakterlánc beállítása
- `_GW_LOGP`: Beállítja azt a fájlnevet a naplófájl használata
- `_TPC_SP`: tárolási tanúsítványok megbízható állomások beállítása
- `_GW_PNFP`: állítja a közzétételi konfigurációs fájl nevét

Parancssori argumentumok környezetiváltozó-beállításainak felülbírálhatja.

Általában állítsa be az IoT-központ tulajdonos kapcsolati karakterlánc csak az alkalmazás első elindítása. A kapcsolati karakterlánc titkosítása és a platform tanúsítványtároló tárolja.

A további hívások a kapcsolati karakterlánc platform tanúsítványtároló olvassa, illetve fel újra. A kapcsolati karakterláncot ad meg minden egyes start, ha az eszközt az IoT-központ eszközbeállításjegyzékben eltávolítása és minden alkalommal, amikor újból.

### <a name="native-on-windows"></a>A natív Windows rendszeren

Az alkalmazás futtatási Windows rendszeren, nyissa meg a `OpcPublisher.sln` projektre a Visual Studio 2017, a megoldás felépítéséhez, és tegye közzé. Az alkalmazás indítható a **célkönyvtár** a közzétett:

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>Önálló beépített tároló használata

Az alkalmazás futtatásához egy önálló beépített tárolóban, felépítéséhez, és indítsa el a saját tároló:

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>A hub.docker.com tároló használata

Nincs egy előre elkészített tároló elérhető DockerHub. Indítsa el, futtassa a következő parancsot:

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>Fontos, amikor tároló használatával

#### <a name="access-to-the-publisher-opc-ua-server"></a>A közzétevő OPC EE-kiszolgáló eléréséhez

A közzétevő OPC EE-kiszolgáló alapértelmezés szerint 62222 porton figyel. A bejövő portot, a tárolóban lévő teszi közzé, kell használnia a `docker run` beállítás `-p`:

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>Intercontainer névfeloldás engedélyezése

Ahhoz, hogy a névfeloldás a tárolóban más tárolókhoz, kell:

- Hozzon létre egy docker felhasználói híd hálózatot.
- A tároló csatlakozni a hálózati használatával a `--network`lehetőséget.
- Rendelje hozzá a tároló neve using a `--name` lehetőséget.

A következő példa bemutatja a konfigurációs beállításokról:

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

A tároló most már elérhető egyéb tárolók a hálózaton a név használatával `publisher`.

#### <a name="assign-a-hostname"></a>Az állomásnév hozzárendelése

Közzétevő használja a tanúsítvány- és végpont létrehozásakor futtató számítógép állomásneve. Docker úgy dönt, kivéve, ha beállítása egy-egy véletlenszerű állomásnevet a `-h` lehetőséget. Itt egy példa a tárolót, hogy a belső állomásnevét beállítása `publisher`:

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>A kötés használatával csatlakoztatja a (megosztott fájlrendszer)

Bizonyos esetekben érdemes konfigurációs adatait, írási vagy olvasási naplófájlokat, helyét a gazdagépen, a tároló fájlrendszerben használata helyett. Ez a viselkedés beállításához használja a `-v` lehetőség a `docker run` a bind csatlakoztatási módban. Példa:

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>X509 áruház tanúsítványok

X509 tárolására tanúsítványok nem működik a bind csatlakoztatások, mert az elérési útját a tároló engedélyeinek kell lenniük `rw` a tulajdonos. Ehelyett kell használnia a `-v` lehetőség a `docker run` a kötet módban.

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése

### <a name="native-on-windows"></a>A natív Windows rendszeren

Nyissa meg a `OpcPublisher.sln` a Visual Studio 2017 le és indítsa el az alkalmazás hibakeresésének F5 elérte-e.

### <a name="in-a-docker-container"></a>A docker-tároló

A Visual Studio 2017 támogatja az egy Docker-tároló a hibakeresési alkalmazásokat `docker-compose`. Azonban ez a módszer nem teszi lehetővé parancssori paraméterek.

Hibakeresés, amely VS2017 támogatja a másik lehetőség az keresztül hibakeresési `ssh`. A docker build konfigurációs fájlt használhat `Dockerfile.ssh` egy engedélyezett SSH-tároló létrehozása a tárház gyökérkönyvtárában található:

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

Most elindíthatja a tárolót, hogy a közzétevő debug:

```cmd/sh
docker run -it publisherssh
```

A tárolóban, el kell indítania a **ssh** démon manuálisan:

```cmd/sh
service ssh start
```

Ezen a ponton létrehozhat egy **ssh** felhasználóként munkamenet `root` jelszóval `Passw0rd`.

A tároló az alkalmazás hibakeresése előkészítéséhez végezze el az alábbi kiegészítő lépéseket:

1. A gazdagép-oldali, hozzon létre egy `launch.json` fájlt:

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. A projekt buildjének elkészítéséhez, és tegye közzé az Ön által választott könyvtár.

1. Használjon például egy eszköz `WinSCP` másolni a közzétett fájlokat a könyvtárba `/root/publisher` a tárolóban. Ha egy másik címtárhoz használatát választja, frissítse a `cdw` tulajdonságot a `launch.json` fájlt.

Most elindíthatja hibakeresés a Visual Studio parancssori ablakban az alábbi paranccsal:

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>További lépések

A javasolt következő lépésre megtudhatja, hogyan [egy átjáró a Windows vagy Linux az előre konfigurált csatlakoztatott gyári megoldás üzembe helyezéséhez](iot-suite-connected-factory-gateway-deployment.md).