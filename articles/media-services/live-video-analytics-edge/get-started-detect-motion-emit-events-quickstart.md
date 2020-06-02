---
title: Ismerkedés a Live Video Analytics szolgáltatással IoT Edge-Azure-ban
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el az élő videók elemzését IoT Edgeon, és hogyan észlelhető a mozgás egy élő videó streamben.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262013"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Gyors útmutató: első lépések – élő videó-elemzés IoT Edge

Ez a rövid útmutató végigvezeti azon lépéseken, amelyekkel megkezdheti az élő videók elemzését IoT Edgeon. Egy Azure-beli virtuális gépet használ IoT Edge eszközként és szimulált élő videó streamként. A telepítés lépéseinek elvégzése után egy szimulált élő videó streamet futtathat egy olyan adathordozó-gráfon keresztül, amely az adott stream bármely mozgását észleli és jelenti. Az alábbi ábra az adott adathordozó-gráf grafikus ábrázolását mutatja be.

![Élő videó-elemzés a mozgásészlelés alapján](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen az [Azure IoT Tools bővítménnyel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* A hálózat, amelyhez a fejlesztői számítógép csatlakozik, engedélyezni kell a AMQP protokollt az 5671-as porton keresztül (így az Azure IoT-eszközök kommunikálhatnak az Azure IoT Hub).

> [!TIP]
> Előfordulhat, hogy a rendszer a Docker telepítésére kéri az Azure IoT Tools bővítmény telepítésekor. Nyugodtan hagyja figyelmen kívül.

## <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása

Ehhez az oktatóanyaghoz a következő Azure-erőforrások szükségesek.

* IoT Hub
* Tárfiók
* Azure Media Services fiók
* Linux virtuális gép az Azure-ban, [IoT Edge futtatókörnyezettel](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) telepítve

Ebben a rövid útmutatóban azt javasoljuk, hogy az Azure-előfizetésében fent említett Azure-erőforrások üzembe helyezéséhez használja az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg a következő címet: https://shell.azure.com.
1. Ha első alkalommal használja a Cloud Shellt, a rendszer megkéri, hogy válasszon egy előfizetést a Storage-fiók létrehozásához és a fájlok megosztásához Microsoft Azure. Válassza a "tároló létrehozása" lehetőséget, és hozzon létre egy Storage-fiókot az Cloud Shell-munkamenet adatainak tárolásához. Ez a Storage-fiók a Azure Media Services-fiókkal való használatra létrehozott parancsfájltól elkülönítve jelenik meg.
1. A rendszerhéj ablakának bal oldali legördülő menüjében válassza a "bash" lehetőséget a környezetében.

    ![Környezeti választó](./media/quickstarts/env-selector.png)

1. Futtassa a következő parancsot:

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Ha a parancsfájl sikeresen befejeződik, az előfizetésben említett összes erőforrást látnia kell. A szkript kimenetének részeként létrejön egy erőforrások táblázata, amely kilistázza az IoT hub nevét. Keresse meg a **"Microsoft. Devices/IotHubs"** típusú erőforrástípust, és jegyezze fel a nevet. Erre a következő lépésben lesz szükség. A szkript a ~/clouddrive/LVA-Sample/könyvtárban is létrehoz néhány konfigurációs fájlt – ezekre később szüksége lesz a rövid útmutatóban.

## <a name="deploy-modules-on-your-edge-device"></a>Modulok üzembe helyezése a peremhálózati eszközön

Futtassa a következő parancsot Cloud Shell

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

A fenti parancs a következő modulokat telepíti a peremhálózati eszközre (a Linux rendszerű virtuális gépre):

* Élő videó-elemzés a IoT Edgeon (modul neve: "lvaEdge")
* RTSP-szimulátor (modul neve: "rtspsim")

Az RTSP szimulátor modul szimulál egy élő videó streamet egy olyan tárolt videofájl használatával, amelyet a rendszer a peremhálózati eszközre másolt, amikor futtatta az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). Ebben a szakaszban már telepítve vannak a modulok, de nincsenek aktív adathordozó-diagramok.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Az Azure IoT Tools bővítmény konfigurálása a Visual Studio Code-ban

Indítsa el a Visual Studio Code-ot, és kövesse az alábbi utasításokat az Azure-IoT Hub az Azure IoT Tools bővítmény használatával való kapcsolódáshoz.

1. Navigáljon az Explorer lapra a Visual Studio Code-ban a **View**  >  **Explorerben** vagy egyszerűen nyomja meg a (CTRL + SHIFT + E) billentyűkombinációt.
1. Az Explorer lapon kattintson a bal alsó sarokban található "Azure IoT Hub" elemre.
1. Kattintson a további beállítások ikonra a helyi menü megjelenítéséhez, majd válassza a "IoT Hub a kapcsolódási karakterlánc beállítása" lehetőséget.
1. Megjelenik egy beviteli mező, majd adja meg IoT Hub kapcsolódási karakterláncát. A IoT Hubhoz tartozó kapcsolatok karakterláncát lekérheti a Cloud Shell ~/clouddrive/LVA-Sample/appSettings.JSON.
1. Ha a sikeres kapcsolatok megjelennek, megjelenik a peremhálózati eszközök listája. Legalább egy, "LVA-Sample-Device" nevű eszköznek kell lennie.
1. Mostantól kezelheti IoT Edge eszközeit, és használhatja az Azure IoT Hubt a helyi menüben.
1. A peremhálózati eszközön üzembe helyezett modulokat a "LVA-Sample-Device" alatt található modulok csomópont kibontásával tekintheti meg.

    ![LVA – minta – eszköz csomópont](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Közvetlen metódusok használata

A modul segítségével közvetlen metódusok meghívásával elemezheti az élő videó streameket. A [IoT Edge élő videó-elemzések közvetlen módszereinek](direct-methods.md) olvasása a modul által biztosított közvetlen módszerek megismeréséhez. 

### <a name="invoke-graphtopologylist"></a>GraphTopologyList meghívása
Ez az összes gráf- [topológiát](media-graph-concept.md#media-graph-topologies-and-instances) enumerálja a modulban.

1. Kattintson a jobb gombbal a "lvaEdge" modulra, és válassza a helyi menü "a modul közvetlen metódusának meghívása" elemét.
1. Ekkor megjelenik a Visual Studio Code ablakának felső sarkában látható pop-beviteli mező. Írja be a "GraphTopologyList" kifejezést a Szerkesztés mezőbe, majd nyomja le az ENTER billentyűt.
1. Ezután másolja és illessze be az alábbi JSON-adattartalmat a Szerkesztés mezőbe, majd nyomja le az ENTER billentyűt.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    Néhány másodpercen belül megjelenik a kimeneti ablak a Visual Studio Code felugró ablakban a következő választal

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    A fenti válasz várható, mivel nem jött létre gráf-topológia.
    

### <a name="invoke-graphtopologyset"></a>GraphTopologySet meghívása

A GraphTopologyList meghívásakor megjelenő lépések használatával meghívhatja a GraphTopologySet-t, hogy a következő JSON- [t használja a](media-graph-concept.md#media-graph-topologies-and-instances) hasznos adattartalomként.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```


A fenti JSON-tartalom egy olyan gráf-topológia létrehozását eredményezi, amely három paramétert határoz meg (amelyek közül kettő alapértelmezett értékkel rendelkezik). A topológia egyetlen forrás (RTSP-forrás) csomóponttal, egy processzorral (mozgásérzékelő processzor) és egy fogadó (IoT Hub fogadó) csomóponttal rendelkezik.

Néhány másodpercen belül a következő válasz jelenik meg a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

A visszaadott állapot 201, ami azt jelzi, hogy egy új topológia lett létrehozva. Próbálkozzon a következő lépésekkel:

* Indítsa újra a GraphTopologySet, és figyelje meg, hogy a visszaadott állapotkód 200. Az 200-as állapotkód azt jelzi, hogy egy meglévő topológiát sikerült frissíteni.
* Indítsa újra a GraphTopologySet, de módosítsa a leíró karakterláncot. Figyelje meg, hogy a válaszban szereplő állapotkód 200, és a Leírás frissítve lett az új értékre.
* Hívja meg a GraphTopologyList az előző szakaszban leírtak szerint, és figyelje meg, hogy most már megtekintheti a "MotionDetection" topológiát a visszaadott adattartalomban.

### <a name="invoke-graphtopologyget"></a>GraphTopologyGet meghívása

Most hívja meg a GraphTopologyGet-t a következő hasznos adattartalommal

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Néhány másodpercen belül a következő választ kell látnia a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Jegyezze fel a következőt a válasz adattartalomban:

* Az állapotkód 200, ami sikeresnek jelez.
* A hasznos adatok "létrehozva" és "lastModified" időbélyegzővel rendelkeznek.

### <a name="invoke-graphinstanceset"></a>GraphInstanceSet meghívása

Ezután hozzon létre egy gráf-példányt, amely a fenti gráf-topológiára hivatkozik. Ahogy az [itt](media-graph-concept.md#media-graph-topologies-and-instances)is látható, a Graph instances lehetővé teszi, hogy több kameráról származó élő videó streameket elemezzen ugyanazzal a gráf-topológiával.

Hívja meg a Direct metódus GraphInstanceSet a következő adattartalommal.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Vegye figyelembe a következőket:

* A fenti hasznos adat meghatározza azt a topológiai nevet (MotionDetection), amelyhez a példányt létre kell hozni.
* A hasznos adatok a "rtspUrl" paraméter értékét tartalmazzák, amely nem rendelkezett alapértelmezett értékkel a gráf-topológia hasznos adataiban.

Néhány másodpercen belül a következő válasz jelenik meg a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Jegyezze fel a következőt a válasz adattartalomban:

* Az állapotkód 201, amely azt jelzi, hogy egy új példány lett létrehozva.
* Az állapot "inaktív", ami azt jelzi, hogy a Graph-példány létrejött, de nincs aktiválva. További információ: [Media Graph-állapotok](media-graph-concept.md).

Próbálkozzon a következő lépésekkel:

* GraphInstanceSet meghívása ugyanazzal a hasznos adattartalommal, és vegye figyelembe, hogy a visszaadott állapotkód mostantól 200.
* Ismételje meg a GraphInstanceSet, de egy másik leírással, és figyelje meg, hogy a válasz adattartalmát a frissített leírás tartalmazza, ami azt jelzi, hogy a Graph-példány frissítése sikeresen megtörtént.
* Hívja meg a GraphInstanceSet, de módosítsa a nevet a "Sample-Graph-2" névre, és figyelje meg a válasz adattartalmát. Vegye figyelembe, hogy létrejön egy új gráf-példány (azaz az állapotkód 201).

### <a name="invoke-graphinstanceactivate"></a>GraphInstanceActivate meghívása

Most aktiválja a Graph-példányt – amely elindítja az élő videó áramlását a modulon keresztül. Hívja meg a Direct metódus GraphInstanceActivate a következő adattartalommal.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Néhány másodpercen belül a következő választ kell látnia a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

A válasz adattartalomban szereplő 200-as állapotkód azt jelzi, hogy a Graph-példány sikeresen aktiválva lett.

### <a name="invoke-graphinstanceget"></a>GraphInstanceGet meghívása

Most hívja meg a Direct metódus GraphInstanceGet a következő adattartalommal:

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Néhány másodpercen belül a következő választ kell látnia a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Jegyezze fel a következőt a válasz adattartalomban:

* Az állapotkód 200, ami sikeresnek jelez.
* Az állapot "aktív", ami azt jelzi, hogy a Graph-példány már "aktív" állapotban van.

## <a name="observe-results"></a>Eredmények megfigyelése

A fenti példában létrehozott és aktivált Graph-példány a mozgásészlelési processzor csomóponttal észleli a mozgást a beérkező élő videó streamben, és eseményeket küld a IoT Hub fogadó csomópontnak. Ezek az események ezután továbbítva lesznek a IoT Edge hubhoz, amely már megfigyelhető. Ehhez kövesse az alábbi lépéseket.

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az Azure IoT Hub a bal alsó sarokban.
2. Bontsa ki az eszközök csomópontot.
3. A jobb gombbal a LVA-minta-eszközre kattint, és a "beépített figyelési esemény figyelése" lehetőséget választotta.

![IOT hub-események figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)

A kimeneti ablakban a következő üzenetek jelennek meg:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Vegye figyelembe a következőket a fenti üzenetben

* Az üzenet "Body" szakaszt és egy "applicationProperties" szakaszt tartalmaz. Ha szeretné megismerni, hogy mit jelentenek ezek a csoportok, olvassa el [IoT hub üzenet létrehozása és olvasása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)című cikket.
* a applicationProperties "subject" a MediaGraph azon csomópontjára hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet a mozgásészlelési processzorból származik.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy elemzési esemény.
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* a "Body" az elemzési eseményre vonatkozó információkat tartalmaz. Ebben az esetben az esemény egy következtetési esemény, ezért a törzs "Timestamp" és "következtetéseket" tartalmaz.
* a "következtetések" szakasz azt jelzi, hogy a "type" a "Motion", és további információkat tartalmaz a "Motion" eseményről.

Ha a MediaGraph-t egy kis ideig futtatja, a következő üzenet jelenik meg a kimeneti ablakban is:

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Vegye figyelembe a következőket a fenti üzenetben

* a "tárgy" a applicationProperties azt jelzi, hogy az üzenet a Media Graph RTSP-forrás csomópontjában lett létrehozva.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy diagnosztikai esemény.
* a "Body" a diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az esemény MediaSessionEstablished, és így a törzs.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>További közvetlen metódusok meghívása a tisztításhoz

A közvetlen metódusok meghívásával inaktiválhatja és törölheti a Graph-példányt (ebben a sorrendben).

### <a name="invoke-graphinstancedeactivate"></a>GraphInstanceDeactivate meghívása

Hívja meg a Direct metódus GraphInstanceDeactivate a következő adattartalommal.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Néhány másodpercen belül a következő választ kell látnia a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Az 200-es állapotkód azt jelzi, hogy a Graph-példány inaktiválása sikeres volt.

Próbálkozzon a következő lépésekkel.

* Hívja meg a GraphInstanceGet a korábbi szakaszokban jelzett módon, és figyelje meg az "állapot" értéket.

### <a name="invoke-graphinstancedelete"></a>GraphInstanceDelete meghívása

A közvetlen metódus GraphInstanceDelete meghívása a következő adattartalommal

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Néhány másodpercen belül a következő választ kell látnia a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

A válaszban szereplő 200-as állapotkód azt jelzi, hogy a gráf példányának törlése sikerült.

### <a name="invoke-graphtopologydelete"></a>GraphTopologyDelete meghívása

Hívja meg a Direct metódus GraphTopologyDelete a következő adattartalommal:

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Néhány másodpercen belül a következő választ kell látnia a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Az 200-es állapotkód azt jelzi, hogy a gráf topológiáját sikerült törölni.

Próbálkozzon a következő lépésekkel.

* Hívja meg a GraphTopologyList, és figyelje meg, hogy nincsenek gráf-topológiák a modulban.
* Hívja meg a GraphInstanceList ugyanazzal a hasznos adattartalommal, mint a GraphTopologyList, és figyelje meg, hogy a rendszer nem sorolja fel a diagramokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az ebben a rövid útmutatóban létrehozott erőforrásokat.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan rögzíthet videót a Live Video Analytics használatával IoT Edge
* További információ a diagnosztikai üzenetekről.
