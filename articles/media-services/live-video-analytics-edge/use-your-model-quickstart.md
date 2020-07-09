---
title: Élő videó elemzése saját modell használatával – Azure
description: Ebben a rövid útmutatóban a számítógép jövőképét fogja alkalmazni az élő videó hírcsatornájának (szimulált) IP-kamerából való elemzéséhez.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: e19c5bd6b44f9a4dd225f6a849ee9aaf62fa6273
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027664"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-model"></a>Rövid útmutató: élő videó elemzése saját modell használatával

Ez a rövid útmutató bemutatja, hogyan használható a Live Video Analytics a IoT Edge egy élő videó-hírcsatorna (szimulált) IP-kamerából való elemzéséhez. Megtudhatja, hogyan alkalmazhatja a Computer látási modellt az objektumok észlelésére. Az élő video-hírcsatornában található keretek egy részhalmazát a rendszer egy következtetési szolgáltatásnak továbbítja. Az eredményeket a rendszer IoT Edge hubhoz továbbítja. 

Ez a rövid útmutató egy Azure-beli virtuális gépet használ IoT Edge eszközként, és szimulált élő videó streamet használ. Ez a C# nyelven írt mintakód alapján készült, amely az [észlelési és a kibocsátási események](detect-motion-emit-events-quickstart.md) gyors üzembe helyezésére épül. 

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetést tartalmazó Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , ha még nem rendelkezik ilyennel.
* [Visual Studio Code](https://code.visualstudio.com/)a következő kiterjesztésekkel:
    * [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Ha nem fejezte be a [mozgás észlelése és az események kibocsátása](detect-motion-emit-events-quickstart.md) című rövid útmutatót, akkor ügyeljen arra, hogy [Azure-erőforrásokat állítson be](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Az Azure IoT-eszközök telepítésekor előfordulhat, hogy a rendszer a Docker telepítésére kéri. A promptot figyelmen kívül hagyhatja.

## <a name="review-the-sample-video"></a>A minta videó áttekintése
Az Azure-erőforrások beállításakor a rendszer átmásolja az autópálya-forgalom rövid videóját az Azure-ban lévő, IoT Edge eszközként használt linuxos virtuális gépre. Ez a rövid útmutató egy élő stream szimulálására használja a videofájl használatával.

Nyisson meg egy alkalmazást, például a [VLC Media Playert](https://www.videolan.org/vlc/). Válassza a CTRL + N billentyűkombinációt, majd illessze be a [videóra](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) mutató hivatkozást a lejátszás elindításához. Itt láthatja az autópálya-forgalomban mozgó számos jármű felvételét.

Ebben a rövid útmutatóban élő videó-elemzéseket fog használni a IoT Edgeon az objektumok, például a gépjárművek és a személyek észleléséhez. A kapcsolódó következtetési eseményeket közzé kell tenni IoT Edge hubhoz.

## <a name="overview"></a>Áttekintés

![Áttekintés](./media/quickstarts/overview-qs5.png)

Ez az ábra azt mutatja be, hogyan áramlik be a gyors útmutatóban szereplő jelek. Az [Edge-modulok](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) egy valós idejű Streaming Protocol-(RTSP-) kiszolgálót üzemeltető IP-kamerát szimulálnak. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a képkockákat a [frame rate szűrő processzor](media-graph-concept.md#frame-rate-filter-processor) -csomópontjára küldi. Ez a processzor korlátozza a [http-bővítmény processzor](media-graph-concept.md#http-extension-processor) -csomópontját elérő video stream képkockasebességét. 

A HTTP-bővítmény csomópont egy proxy szerepét játssza le. A képkockákat a megadott képtípusra konvertálja. Ezt követően továbbítja a képet a REST-ben egy másik Edge-modulba, amely egy HTTP-végpont mögötti AI-modellt futtat. Ebben a példában az Edge-modul a [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) modell használatával készült, amely számos típusú objektumot képes érzékelni. A HTTP-bővítmény processzor-csomópontja összegyűjti az észlelés eredményeit, és közzéteszi az eseményeket a [IoT hub](media-graph-concept.md#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket [IoT Edge hubhoz](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Ebben a rövid útmutatóban a következőket fogja megtekinteni:

1. A Media Graph létrehozása és üzembe helyezése.
1. Az eredmények értelmezése.
1. Erőforrások karbantartása.



## <a name="create-and-deploy-the-media-graph"></a>A Media Graph létrehozása és üzembe helyezése
    
### <a name="examine-and-edit-the-sample-files"></a>A mintaadatok vizsgálata és szerkesztése

Az előfeltételek részeként letöltötte a mintakód egy mappába. A mintaadatok vizsgálatához és szerkesztéséhez kövesse az alábbi lépéseket.

1. A Visual Studio Code-ban lépjen az *src/Edge*elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz üzembe helyezési jegyzékére hivatkozik. Tartalmaz néhány helyőrző értéket. A *. env* fájl tartalmazza a változók értékeit.

1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a *appsettings.js* fájlt, és néhány további fájlt:

    * ***C2D-Console-app. csproj*** – a Project fájl a Visual Studio Code-hoz.
    * ***operations.json*** – a program futtatásához használni kívánt műveletek listája.
    * ***Program.cs*** – a minta programkódja. Ez a kód:

        * Betölti az alkalmazás beállításait.
        * Közvetlen metódusokat hív meg, amelyeket az élő videó Analytics IoT Edge modulban tesz elérhetővé. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md)meghívásával.
        * Szünetelteti, hogy megvizsgálhatja a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez.


1. Szerkessze a *operations.js* fájlt:
    * Módosítsa a Graph-topológiára mutató hivatkozást:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

    * A (z) alatt `GraphInstanceSet` szerkessze a gráf topológiájának nevét, hogy az megfeleljen az előző hivatkozásban szereplő értéknek:

      `"topologyName" : "InferencingWithHttpExtension"`

    * `GraphTopologyDelete`A alatt szerkessze a nevet:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése

1. Kattintson a jobb gombbal a fájlon a *src/Edge/deployment.yolov3.template.js* elemre, majd válassza a **IoT Edge üzembe helyezési jegyzék előállítása**lehetőséget.

    ![IoT Edge üzembe helyezési jegyzék előállítása](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    A manifest-fájl *deployment.yolov3.amd64.js* a *src/Edge/config* mappában jön létre.

1. Ha befejezte a [mozgás észlelése és a kibocsátó események](detect-motion-emit-events-quickstart.md) rövid útmutatóját, ugorja át ezt a lépést. 

    Ellenkező esetben a bal alsó sarokban található **Azure IOT hub** panel közelében válassza a **További műveletek** ikont, majd válassza a **IoT hub a kapcsolatok karakterláncának beállítása**lehetőséget. A karakterláncot a *appsettings.js* fájlból másolhatja. Vagy a megfelelő IoT hub a Visual Studio Code-ban való konfigurálásának biztosításához használja az [IoT hub kiválasztása parancsot](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![IoT Hub-kapcsolatok karakterláncának beállítása](./media/quickstarts/set-iotconnection-string.png)

1. Kattintson a jobb gombbal *az src/Edge/config/deployment.yolov3.amd64.js* elemre, és válassza a **központi telepítés létrehozása egyetlen eszközhöz**lehetőséget. 

    ![Központi telepítés létrehozása egyetlen eszközhöz](./media/quickstarts/create-deployment-single-device.png)

1. Amikor a rendszer rákérdez a IoT Hub eszköz kiválasztására, válassza a **LVA-Sample-Device**elemet.
1. Körülbelül 30 másodperc elteltével frissítse az Azure IoT Hubt az ablak bal alsó sarkában. A peremhálózati eszköz mostantól a következő központilag telepített modulokat mutatja:

    * A **lvaEdge** nevű Live Video Analytics-modul
    * Az **rtspsim** modul, amely egy RTSP-kiszolgálót szimulál, és élő videó-hírcsatorna forrásaként működik
    * A **yolov3** modul, amely a yolov3 objektum-észlelési modell, amely a számítógépekre vonatkozó jövőképet alkalmaz a lemezképekre, és több objektumtípust ad vissza.
 
      ![A peremhálózati eszközön üzembe helyezett modulok](./media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Felkészülés az események figyelésére

Kattintson a jobb gombbal a Live Video Analytics-eszközre, és válassza a **figyelés beépített esemény végpontjának elindítása**lehetőséget. Erre a lépésre szüksége lesz a Visual Studio Code **kimeneti** ablakának IoT hub eseményeinek figyeléséhez. 

![Figyelés indítása](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>A minta program futtatása

1. A hibakeresési munkamenet indításához válassza az F5 billentyűt. A **terminál** ablakban láthatók a kinyomtatott üzenetek.
1. A kód *operations.js* a közvetlen metódusok hívásával indul el `GraphTopologyList` `GraphInstanceList` . Ha az előző rövid útmutatók befejezése után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. A folytatáshoz válassza az ENTER billentyűt.

   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

    A **terminál** ablak a közvetlen metódusok következő készletét jeleníti meg:

     * `GraphTopologySet`Az előzőt használó hívás`topologyUrl`
     * A `GraphInstanceSet` következő törzset használó hívás:

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               }
             ]
           }
         }
         ```

     * A `GraphInstanceActivate` gráf-példányt és a videó folyamatát indító hívás
     * Egy második hívás, `GraphInstanceList` amely azt mutatja, hogy a Graph-példány fut állapotban van
1. A **terminál** ablakban lévő kimenet szünetelteti a `Press Enter to continue` kérdést. Ne jelölje be az ENTER billentyűt. Görgessen felfelé, és tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a **kimeneti** ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulban az IoT hub-ra küld üzenetet. A rövid útmutató következő szakasza ezeket az üzeneteket tárgyalja.
1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a **terminál** ablakába, és válassza az ENTER billentyűt. 

    A hívások következő sorozata törli az erőforrásokat:
      * A Graph- `GraphInstanceDeactivate` példány inaktiválására irányuló hívás.
      * A `GraphInstanceDelete` példány törlésére irányuló hívás.
      * A `GraphTopologyDelete` topológia törlésére irányuló hívás.
      * Egy végső hívás, `GraphTopologyList` amely azt jelzi, hogy a lista üres.

## <a name="interpret-results"></a>Eredmények értelmezése

A Media Graph futtatásakor a HTTP-bővítmény processzor-csomópontjának eredményei áthaladnak a IoT Hub fogadó csomóponton az IoT hubhoz. A **kimeneti** ablakban látható üzenetek egy `body` szakaszt és egy `applicationProperties` szakaszt tartalmaznak. További információ: [IoT hub üzenetek létrehozása és olvasása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

A következő üzenetekben az élő videó elemzési modulja az alkalmazás tulajdonságait és a törzs tartalmát határozza meg. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a kapcsolat sikeres, a következő esemény lesz kinyomtatva. Az esemény típusa: `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Ebben az üzenetben a következő információkat láthatja:

* Az üzenet egy diagnosztikai esemény. `MediaSessionEstablished`azt jelzi, hogy az RTSP-szimulátorhoz csatlakoztatott RTSP-forrás csomópont (a tárgy) megkezdte a (szimulált) élő adatcsatorna fogadását.
* A `applicationProperties` ben `subject` azt jelzi, hogy az üzenet a Media Graph RTSP-forrás csomópontjában lett létrehozva.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* A `eventTime` jelzi az esemény időpontját.
* A `body` diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.

### <a name="inference-event"></a>Következtetési esemény

A HTTP-bővítmény processzor-csomópontja a yolov3 modulból származó következtetéseket fogad. Ezután az eredményeket a IoT Hub fogadó csomóponton keresztül származtatott eseményként bocsátja ki. 

Ezekben az eseményekben a típus úgy van beállítva, `entity` hogy jelezze, hogy egy entitás, például egy autó vagy egy teherautó. Az `eventTime` érték az a UTC-idő, amikor az objektum észlelve lett. 

A következő példában két autót észleltek ugyanabban a képkockában, különböző megbízhatósági szinttel.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

Az üzenetekben figyelje meg a következő adatokat:

* A alkalmazásban a `applicationProperties` `subject` diagram topológiájában lévő csomópontra hivatkozik, amelyről az üzenet létrejött. 
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy elemzési esemény.
* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett.
* A `body` szakasz az elemzési eseményre vonatkozó információkat tartalmaz. Ebben az esetben az esemény egy következtetési esemény, így a törzs tartalmaz egy `inferences` adathalmazt.
* A `inferences` szakasz azt jelzi, hogy az a `type` `entity` . Ez a szakasz az entitással kapcsolatos további információkat tartalmaz.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha más gyors útmutatókat szeretne kipróbálni, tartsa meg a létrehozott erőforrásokat. Ellenkező esetben lépjen a Azure Portalra, nyissa meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben ezt a rövid útmutatót futtatta, és törölje az összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Megkeresheti az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőséggel](https://www.onvif.org/conformant-products/) rendelkező termékek oldalon. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.
* Az Azure Linux rendszerű virtuális gépek helyett AMD64-vagy x64-es Linux-eszközt használjon. Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. A [Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)című témakör útmutatását követheti. Ezután regisztrálja az eszközt az Azure IoT Hub az [első IoT Edge modul üzembe helyezése virtuális Linux-eszközre](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)című témakör útmutatását követve.

