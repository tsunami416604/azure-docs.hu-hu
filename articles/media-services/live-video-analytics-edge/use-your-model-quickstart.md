---
title: Élő videó elemzése saját modell használatával – Azure
description: Ebben a rövid útmutatóban a Computer víziót fogja alkalmazni, hogy elemezze az élő videó-hírcsatornát egy (szimulált) IP-kameráról.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261971"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Gyors útmutató: élő videó elemzése saját modellel

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Live Video Analytics szolgáltatást a IoT Edgeon az élő videó-hírcsatorna egy (szimulált) IP-kamerából történő elemzéséhez egy számítógépes látási modell alkalmazásával az objektumok észleléséhez. Az élő video-hírcsatornában található keretek egy részhalmazát a rendszer egy következtetési szolgáltatásba továbbítja, és az eredményeket a IoT Edge hubhoz küldik. Egy Azure-beli virtuális gépet használ IoT Edge eszközként és szimulált élő videó streamként. Ez a cikk a C# nyelven írt mintakód alapján készült.

Ez a cikk [erre](detect-motion-emit-events-quickstart.md) a rövid útmutatóra épül. 

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) a gépen a következő kiterjesztésekkel:
    * [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* A [.net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) telepítve van a rendszeren
* Ha még nem végezte el [ezt](detect-motion-emit-events-quickstart.md) a rövid útmutatót, végezze el a következő lépéseket:
     * [Az Azure-erőforrások beállítása](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Az Azure IoT-eszközök telepítésekor előfordulhat, hogy a rendszer a Docker telepítésére kéri. Nyugodtan hagyja figyelmen kívül.

## <a name="review-the-sample-video"></a>A minta videó áttekintése
Az Azure-erőforrások létrehozásához szükséges lépések részeként a rendszer átmásolja az autópálya-forgalom (rövid) videóját az Azure-beli linuxos virtuális gépre, amelyet IoT Edge eszközként használ. Ez a videofájl az oktatóanyag élő folyamának szimulálására szolgál.

Használhat egy alkalmazást, például a [VLC Playert](https://www.videolan.org/vlc/), elindíthatja, megnyomhatja a CTRL + N billentyűkombinációt, és a lejátszás megkezdéséhez illessze be [ezt](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) a hivatkozást a videóra. Látni fogja, hogy a felvételek forgalma egy autópályán történik, és sok jármű van rajta.

Az alábbi lépések elvégzése után élő videó-elemzéseket használt IoT Edgeon, hogy felderítse az objektumokat, például a gépjárműveket, a személyeket stb., és tegye közzé a kapcsolódó következtetési eseményeket az IoT Edge hubhoz.

## <a name="overview"></a>Áttekintés

![Áttekintés](./media/quickstarts/overview-qs5.png)

A fenti ábra azt mutatja be, hogyan áramlik be a gyors útmutatóban szereplő jelek. Az Edge-modul (részletesen [itt](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) SZIMULÁL egy RTSP-kiszolgálót futtató IP-kamerát. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a képkockákat a [frame Fate Filter Processor](media-graph-concept.md#frame-rate-filter-processor) csomópontba küldi. Ez a processzor a [http-bővítmény processzor](media-graph-concept.md#http-extension-processor) -csomópontjának elérésekor korlátozza a videó stream képkockasebességét. 

A HTTP-bővítmény csomópontja egy proxy szerepét játssza le, a képkockákat a megadott képtípusra konvertálva, a kép átadását a REST-on keresztül egy másik peremhálózati modulba, amely egy HTTP-végpont mögötti AI-modellt futtat. Ebben a példában az Edge-modul a [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) modell használatával lett felépítve, amely képes számos típusú objektum észlelésére. A HTTP-bővítmény processzor-csomópontja összegyűjti az észlelés eredményeit, és közzéteszi az eseményeket a [IoT hub](media-graph-concept.md#iot-hub-message-sink ) fogadó csomóponton, amely ezután elküldi az eseményt az [IoT Edge hubhoz](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Ebben a rövid útmutatóban a következőket fogja megtekinteni:

1. A Media Graph létrehozása és üzembe helyezése.
1. Az eredmények értelmezése.
1. Erőforrások karbantartása.



## <a name="create-and-deploy-the-media-graph"></a>A Media Graph létrehozása és üzembe helyezése
    
### <a name="examine-and-edit-the-sample-files"></a>A mintaadatok vizsgálata és szerkesztése

Az előfeltételek részeként a mintakód egy mappába lett letöltve. Indítsa el a Visual Studio Code-ot, és nyissa meg a mappát.

1. A Visual Studio Code-ban keresse meg az "src/Edge" parancsot. Ekkor megjelenik a létrehozott. env fájl, amely néhány központi telepítési sablon fájlját tartalmazza.

    * A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik néhány helyőrző értékkel. A. env fájl ezen változók értékeit tartalmazhatja.
1. Ezután tallózással keresse meg a "src/Cloud-to-Device-Console-app" mappát. Itt láthatja a létrehozott appSettings. JSON fájlt, valamint néhány további fájlt is:

    * C2D-Console-app. csproj – ez a Project fájl a Visual Studio Code-hoz.
    * Operations. JSON – ez a fájl felsorolja azokat a különböző műveleteket, amelyekre a programot futtatni szeretné.
    * Program.cs – ez a mintakód, amely a következő műveleteket végzi el:

        * Betölti az alkalmazás beállításait.
        *  Az élő videó Analytics IoT Edge modulban elérhető közvetlen metódusokat hívja meg. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md) meghívásával 
        * Szünetelteti a program kimenetének vizsgálatát a terminál ablakban, valamint a kimenet ablakban a modul által generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez   


1. Végezze el az alábbi módosításokat a Operations. JSON fájlban
    * Módosítsa a Graph-topológiára mutató hivatkozást:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * A GraphInstanceSet alatt szerkessze a gráf topológiájának nevét, hogy az megfeleljen a fenti hivatkozásban szereplő értéknek.`"topologyName" : "InferencingWithHttpExtension"`
    * A GraphTopologyDelete alatt szerkessze a nevet`"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése

1. Kattintson a jobb gombbal az "src/Edge/Deployment. yolov3. template. JSON" fájlra, és kattintson a IoT Edge üzembe helyezési jegyzék előállítása elemre.

    ![IoT Edge üzembe helyezési jegyzék előállítása](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. Ehhez létre kell hoznia egy jegyzékfájlt az src/Edge/config mappában, a "Deployment. yolov3. amd64. JSON" nevű fájlban.
1. Ha korábban már elvégezte [a gyors](detect-motion-emit-events-quickstart.md)üzembe helyezést, ugorja át ezt a lépést. Ellenkező esetben állítsa be a IoTHub-kapcsolódási karakterláncot úgy, hogy az AZURE IOT HUB panel melletti "További műveletek" ikonra kattint a bal alsó sarokban. A karakterláncot a appSettings. JSON fájlból másolhatja. (Íme egy másik ajánlott módszer, amely biztosítja, hogy a megfelelő IoT Hub konfigurálva legyen a Visual Studio Code-ban a [Select IoT hub parancs](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)használatával).
    
    ![IoTHub-kapcsolatok karakterlánca](./media/quickstarts/set-iotconnection-string.png)
1. Ezután kattintson a jobb gombbal az "src/Edge/config/Deployment. yolov3. amd64. JSON" elemre, majd kattintson a "központi telepítés létrehozása egyetlen eszközhöz" elemre. 

    ![Központi telepítés létrehozása egyetlen eszközhöz](./media/quickstarts/create-deployment-single-device.png)
1. Ezután meg kell adnia egy IoT Hub eszköz kiválasztását. Válassza ki a LVA-Sample-Device elemet a legördülő menüből.
1. Körülbelül 30 másodperc alatt frissítse az Azure IOT hub-t a bal alsó szakaszban, és a következő modulokat kell telepítenie a peremhálózati eszközön:

    1. A "lvaEdge" névvel ellátott Live Video Analytics-modul.
    1. Egy "rtspsim" nevű modul, amely szimulál egy RTSP-kiszolgálót, amely egy élő videó-hírcsatorna forrásaként működik.
    1. Egy "yolov3" nevű modul, amely a neve is sugallja, a YOLOv3-objektum észlelési modellje, amely a számítógépeket a lemezképekre alkalmazza, és több objektumtípust ad vissza.
 
        ![YOLOv3-objektum észlelési modellje](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Felkészülés a figyelési eseményekre

Kattintson a jobb gombbal az élő videó Analytics eszközre, és kattintson a "beépített esemény végpontjának figyelése" elemre. Ez a lépés szükséges a IoT Hub események figyeléséhez és a Visual Studio Code kimeneti ablakában való megjelenítéséhez. 

![Figyelés indítása](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>A minta program futtatása

1. Indítsa el a hibakeresési munkamenetet (nyomja meg az F5 billentyűt). Ekkor néhány üzenet jelenik meg a terminál ablakban.
1. A Operations. JSON elindul a közvetlen metódusok GraphTopologyList és GraphInstanceList való hívásával. Ha az előző gyors útmutatók után törölte az erőforrásokat, az üres listát ad vissza, majd szünetelteti az ENTER billentyű lenyomását.
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

1. Amikor lenyomja a "Enter" billentyűt a terminál ablakban, a rendszer a közvetlen metódusok következő készletét kéri
     * A fenti topologyUrl használó GraphTopologySet-hívás
     * A GraphInstanceSet hívása a következő törzs használatával
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
     * A Graph-példány elindítására irányuló GraphInstanceActivate-hívás, valamint a videó folyamatának elindítása
     * Egy második hívás a GraphInstanceList, amely azt mutatja, hogy a Graph-példány valóban fut állapotban van
1. A terminál ablakban lévő kimenet szünetelteti a folytatást a "nyomja meg az ENTER billentyűt" üzenettel. Ne nyomja meg az "Enter" értéket. A felkeresett közvetlen metódusokhoz tartozó JSON-válaszokkal kapcsolatos hasznos adatok megtekintéséhez lapozzunk felfelé
1. Ha most átvált a kimeneti ablakra a Visual Studio Code-ban, akkor a IoT Hub küldött üzeneteket a IoT Edge modul Live Video Analytics szolgáltatásával küldi el a rendszer.
     * Ezeket az üzeneteket az alábbi szakaszban tárgyaljuk
1. A Media Graph továbbra is futni fog, és kinyomtatja az eredményeket – az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához lépjen vissza a terminál ablakába, és nyomja le az ENTER billentyűt. A következő meghívások sorozata az erőforrások tisztítására készül:
     * GraphInstanceDeactivate hívása a Graph-példány inaktiválására
     * A példány törlésére irányuló GraphInstanceDelete-hívás
     * A topológia törlésére irányuló GraphTopologyDelete-hívás
     * A GraphTopologyList utolsó hívása, amely azt mutatja, hogy a lista már üres

## <a name="interpret-results"></a>Eredmények értelmezése

A Media Graph futtatásakor a http-bővítmény processzor csomópontjának eredményeit a rendszer a IoT Hub fogadó csomóponton keresztül továbbítja a IoT Hub. A Visual Studio Code kimenet ablakában látható üzenetek "Body" szakaszt és egy "applicationProperties" szakaszt tartalmaznak. Ennek a résznek a megismeréséhez olvassa el [ezt a](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) cikket.

Az alábbi üzenetekben az alkalmazás tulajdonságait és a törzs tartalmát az élő videó elemzési modulja határozza meg. 



### <a name="mediasession-established-event"></a>MediaSession-létrehozási esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-live55 tárolón futó RTSP-kiszolgálóhoz. Ha ez sikeres, a rendszer kinyomtatja ezt az eseményt. Az esemény típusa Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

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

Vegye figyelembe a következőket a fenti üzenetben:
* Az üzenet egy diagnosztikai esemény, a MediaSessionEstablished azt jelzi, hogy az RTSP-forrás csomópont (a tárgy) képes volt kapcsolatot létesíteni az RTSP-szimulátorral, és elkezdi fogadni a (szimulált) élő hírcsatornát.
* a "tárgy" a applicationProperties azt jelzi, hogy az üzenet a Media Graph RTSP-forrás csomópontjában lett létrehozva.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy diagnosztikai esemény.
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* a "Body" a diagnosztikai eseménnyel kapcsolatos adatokat tartalmaz, amelyek ebben az esetben a [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatai.

### <a name="inference-event"></a>Következtetési esemény

A HTTP-bővítmény processzor-csomópontja a yolov3 modulból származtatott eredményeket fogad, és a IoT Hub fogadó csomóponton keresztül bocsátja ki őket. Ezekben az eseményekben a típus "Entity" (entitás) értékre van állítva, amely azt jelzi, hogy egy entitás, például egy autó vagy egy teherautó, a eventTime pedig megadja, hogy a rendszer mikor (UTC) észlelte az objektumot. Az alábbi példa azt mutatja be, hogy két autót észlelt különböző megbízhatósági szintekkel ugyanazon a videó keretén belül.

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

Vegye figyelembe a következőket a fenti üzenetekben:

* a "tárgy" a applicationProperties hivatkozik a gráf topológiájában lévő csomópontra, amelyből az üzenet létrejött. 
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy elemzési esemény.
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* a "Body" az elemzési eseményre vonatkozó információkat tartalmaz. Ebben az esetben az esemény egy következtetési esemény, ezért a törzs "következtetéseket" tartalmaz.
* a "következtetések" szakasz azt jelzi, hogy a "type" az "Entity", és további információkat tartalmaz az "Entity" tulajdonságról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi rövid útmutatót, tartsa be a következőt: a létrehozott erőforrásokhoz. Ellenkező esetben lépjen a Azure Portalra, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben futtatta ezt a rövid útmutatót, és törölje az összes erőforrást.

## <a name="next-steps"></a>További lépések

Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az [ONVIF](https://www.onvif.org/conformant-products/) -kompatibilis termékek lapon megkeresheti az RTSP-támogatással rendelkező IP-kamerákat a G, S vagy T profiloknak megfelelő eszközök keresésével.
* AMD64 vagy x64 Linux rendszerű eszköz használata (Azure Linux rendszerű virtuális gép használata). Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. Kövesse az [Azure IoT Edge Runtime telepítése Linux rendszeren](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) című témakör utasításait, majd kövesse az [első IoT Edge modul üzembe helyezése a virtuális Linux-eszközökhöz című](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rövid útmutatót az eszköz Azure-IoT hub való regisztrálásához.

