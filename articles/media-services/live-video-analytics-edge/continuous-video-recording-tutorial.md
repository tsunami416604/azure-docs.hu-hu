---
title: Folyamatos videofelvétel a felhőbe és a felhőben való lejátszásról – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az élő videók elemzését IoT Edgeon a videók felhőbe való folyamatos rögzítéséhez és a videó bármely részének továbbításához Azure Media Services használatával.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261857"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>Oktatóanyag: folyamatos videofelvétel a felhőbe és a felhőből való lejátszás  

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket a [folyamatos videofelvételek](continuous-video-recording-concept.md) (CVR) a felhőbe való elvégzéséhez, illetve a videó bármely részének a Media Services használatával történő továbbításához. Ez olyan helyzetekben hasznos, mint például a biztonság, a megfelelőség és mások, ahol a felvételek archiválását több napra (vagy hetekre) kell fenntartani a kamerából.

> [!div class="checklist"]
> * A kapcsolódó erőforrások beállítása
> * A CVR-t végrehajtó kód vizsgálata
> * A mintakód futtatása
> * Vizsgálja meg az eredményeket, és tekintse meg a videót

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás  

Javasoljuk, hogy olvassa el a következő dokumentációs oldalakat

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph alapfogalmai](media-graph-concept.md) 
* [Folyamatos videó-rögzítési forgatókönyvek](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag előfeltételei a következők:

* [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítménnyel és a [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) bővítménnyel.

    > [!TIP]
    > Előfordulhat, hogy a rendszer a Docker telepítését kéri. Ezt a kérdést figyelmen kívül hagyhatja.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) a fejlesztői gépen.
* Töltse ki az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)

A fenti lépések végén az Azure-előfizetésben üzembe helyezett Azure-erőforrásokkal fog rendelkezni, beleértve a következőket:

* IoT Hub
* Tárfiók
* Azure Media Services fiók
* Linux virtuális gép az Azure-ban, [IoT Edge futtatókörnyezettel](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) telepítve

## <a name="concepts"></a>Alapelvek

Az [itt](media-graph-concept.md)leírtaknak megfelelően a Media Graph segítségével meghatározhatja, hogy az adathordozót hogyan kell rögzíteni, hogyan kell feldolgozni, és hol kell elküldeni az eredményeket. A CVR elvégzéséhez egy RTSP-kompatibilis kameráról kell rögzítenie a videót, és folyamatosan rögzítenie kell egy [Azure Media Services eszközön](terminology.md#asset). Az alábbi ábra az adott adathordozó-gráf grafikus ábrázolását mutatja be.

![Adathordozó-gráf](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

Ebben az oktatóanyagban egy, a [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) használatával létrehozott Edge-modult fog használni az RTSP-kamera szimulálása érdekében. A Media Graph-ban egy [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontot fog használni az élő csatorna beszerzéséhez, majd elküldeni a videót az [eszköz fogadó csomópontjára](media-graph-concept.md#asset-sink) , amely rögzíti a videót egy eszközre.

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta a harmadik felsorolásjelet az [Előfeltételekben](#prerequisites). Miután az erőforrás-telepítési parancsfájl befejeződik, kattintson a kapcsos zárójelre, hogy elérhetővé tegye a mappastruktúrát. A ~/clouddrive/LVA-Sample könyvtár alatt létrehozott néhány fájl jelenik meg.

![Alkalmazásbeállítások](./media/quickstarts/clouddrive.png)

Ebben az oktatóanyagban a következők érdeklik:

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

Ezekre a fájlokra szüksége lesz az alábbi lépésekhez.

1. A tárház klónozása innen https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Indítsa el a Visual Studio Code-ot, és nyissa meg azt a mappát, ahová a tárházat letöltötte.
1. A Visual Studio Code-ban keresse meg az "src/Cloud-to-Device-Console-app" mappát, és hozzon létre egy "appSettings. JSON" nevű fájlt. Ez a fájl a program futtatásához szükséges beállításokat fogja tartalmazni.
1. Másolja a tartalmat a ~/clouddrive/LVA-Sample/appSettings.JSON fájlból. A szövegnek a következőhöz hasonlóan kell kinéznie:
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    A IoT Hub kapcsolódási karakterlánc lehetővé teszi, hogy a Visual Studio Code használatával parancsokat küldjön az Edge-moduloknak az Azure IoT Hub segítségével.
    
1. Ezután tallózással keresse meg az "src/Edge" mappát, és hozzon létre egy ". env" nevű fájlt.
1. Másolja a tartalmat a ~/clouddrive/LVA-sample/.env fájlból. A szövegnek a következőhöz hasonlóan kell kinéznie:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>A mintaadatok vizsgálata

A Visual Studio Code-ban nyissa meg a "src/Edge/Deployment. template. JSON" fájlt. Ez a sablon határozza meg, hogy mely peremhálózati modulok lesznek üzembe helyezhetők a peremhálózati eszközön (az Azure-beli linuxos virtuális gépen). Vegye figyelembe, hogy a "modulok" szakaszban két bejegyzés szerepel a következő nevekkel:

* lvaEdge – ez az élő videó Analytics IoT Edge modulon
* rtspsim – ez az RTSP-szimulátor

Ezután tallózással keresse meg a "src/Cloud-to-Device-Console-app" mappát. Itt láthatja a létrehozott appSettings. JSON fájlt, valamint néhány további fájlt is:

* C2D-Console-app. csproj – a Project fájl a Visual Studio Code-hoz.
* Operations. JSON – ez a fájl felsorolja a futtatott különböző műveleteket
* Program.cs – a minta programkódja, amely a következő műveleteket végzi el:
    * Az alkalmazás beállításainak betöltése
    * Az élő videó Analytics IoT Edge modulban elérhető közvetlen metódusokat hívja meg. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md) meghívásával
    * Szünetelteti a program kimenetének vizsgálatát a terminál ablakban, valamint a kimenet ablakban a modul által generált eseményeket.
    * Közvetlen metódusokat hív meg az erőforrások törléséhez

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése 

Az üzembe helyezési jegyzék meghatározza, hogy mely modulok legyenek üzembe helyezve egy peremhálózati eszközön, valamint a modulok konfigurációs beállításai. Kövesse az alábbi lépéseket egy ilyen jegyzékfájl létrehozásához a sablon fájlból, majd telepítse azt a peremhálózati eszközre.

1. Indítsa el a Visual Studio Code-ot
1. Állítsa be a IoTHub-kapcsolódási karakterláncot úgy, hogy az AZURE IOT HUB panel melletti "További műveletek" ikonra kattint a bal alsó sarokban. A karakterláncot a src/Cloud-to-Device-Console-app/appSettings. JSON fájlból másolhatja. 

    ![IOT-kapcsolatok karakterláncának beállítása](./media/quickstarts/set-iotconnection-string.png)
1. Ezután kattintson a jobb gombbal az "src/Edge/Deployment. template. JSON" fájlra, és kattintson a "IoT Edge üzembe helyezési jegyzék előállítása" elemre. A Visual Studio Code a. env fájl értékeit használja a központi telepítési sablonban található változók cseréjéhez. Ehhez létre kell hoznia egy jegyzékfájlt az src/Edge/config mappában, a "Deployment. amd64. JSON" nevű fájlban.

   ![IoT Edge üzembe helyezési jegyzék előállítása](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Kattintson a jobb gombbal az "src/Edge/config/Deployment. amd64. JSON" elemre, és kattintson a "központi telepítés létrehozása egyetlen eszközhöz" elemre.

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/quickstarts/create-deployment-single-device.png)
1. Ekkor a rendszer megkéri, hogy "válasszon ki egy IoT Hub eszközt". Válassza ki a LVA-Sample-Device elemet a legördülő listából.
1. Körülbelül 30 másodperc alatt frissítse az Azure IoT Hub a bal alsó szakaszban, és látnia kell, hogy a peremhálózati eszközön telepítve vannak a következő modulok:
    * Élő videó-elemzés a IoT Edgeon (modul neve: "lvaEdge")
    * RTSP-szimulátor (modul neve: "rtspsim")
 
    ![IoT hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Felkészülés a modulok figyelésére 

Ha élő videó-elemzést használ IoT Edge modulban az élő videó stream rögzítéséhez, akkor az eseményeket a IoT Hub küldi. Az események megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az Azure IoT Hub a bal alsó sarokban.
1. Bontsa ki az eszközök csomópontot.
1. Kattintson a jobb gombbal a LVA-Sample-Device elemre, és válassza a "beépített figyelési események figyelése" lehetőséget.

    ![A beépített esemény-végpont figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>A program futtatása 

1. Visual Studio Code, navigáljon a "src/Cloud-to-Device-Console-app/Operations. JSON" elemre.
1. A csomópont GraphTopologySet alatt szerkessze a következőket:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Ezután a csomópontok GraphInstanceSet és GraphTopologyDelete területen győződjön meg arról, hogy a topologyName értéke megegyezik a fenti Graph-topológia "Name" tulajdonságának értékével:

    `"topologyName" : "CVRToAMSAsset"`  
1. Nyissa meg a [topológiát](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) egy böngészőben, és tekintse meg a következőt: assetNamePattern. Annak érdekében, hogy rendelkezzen egy egyedi névvel rendelkező eszközzel, érdemes lehet módosítani a Graph-példány nevét a Operations. JSON fájlban (az alapértelmezett "Sample-Graph-1" értékről).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Indítsa el a hibakeresési munkamenetet (nyomja meg az F5 billentyűt). Ekkor néhány üzenet jelenik meg a terminál ablakban.
1. A Operations. JSON elindul a GraphTopologyList és a GraphInstanceList hívásával. Ha az előző rövid útmutatók vagy oktatóanyagok után törölte az erőforrásokat, az üres listát ad vissza, majd szünetelteti az ENTER billentyű lenyomását, például az alábbiakat:

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
     * A fenti topologyUrl használó GraphTopologySet hívása.
     * A GraphInstanceSet hívása a következő törzs használatával.
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
1. A Graph-példány továbbra is futni fog, és rögzíti a videót – az RTSP-szimulátor megtartja a forrás videóját. A rögzítés leállításához lépjen vissza a TERMINÁLABLAK ablakába, és nyomja le az ENTER billentyűt. A következő meghívások sorozata az erőforrások tisztítására készül:

     * GraphInstanceDeactivate hívása a Graph-példány inaktiválására
     * A példány törlésére irányuló GraphInstanceDelete-hívás
     * A topológia törlésére irányuló GraphTopologyDelete-hívás
     * A GraphTopologyList utolsó hívása, amely azt mutatja, hogy a lista már üres

## <a name="interpret-the-results"></a>Az eredmények értelmezése 

A Media Graph futtatásakor az élő videó Analytics IoT Edge modulban bizonyos diagnosztikai és műveleti eseményeket küld az IoT Edge hubhoz. Ezek az események a Visual Studio Code kimeneti ablakában látható üzenetek, amelyek "Body" szakaszt és egy "applicationProperties" szakaszt tartalmaznak. Ennek a résznek a megismeréséhez olvassa el [ezt a](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) cikket.

Az alábbi üzenetekben az alkalmazás tulajdonságait és a törzs tartalmát az élő videó elemzési modulja határozza meg.

## <a name="diagnostic-events"></a>Diagnosztikai események 

### <a name="mediasession-established-event"></a>MediaSession-létrehozási esemény

Ha a Graph-példány aktiválva van, az RTSP-forrás csomópont megpróbál csatlakozni a rtspsim modulban futó RTSP-kiszolgálóhoz. Ha a művelet sikeres, az a következő eseményt fogja kinyomtatni:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Az üzenet egy diagnosztikai esemény, a MediaSessionEstablished azt jelzi, hogy az RTSP-forrás csomópont (a tárgy) képes volt kapcsolatot létesíteni az RTSP-szimulátorral, és elkezdi fogadni a (szimulált) élő hírcsatornát.
* A "tárgy" a applicationProperties hivatkozik a Graph-topológia azon csomópontjára, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy diagnosztikai esemény.
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* a "Body" a diagnosztikai eseménnyel kapcsolatos adatokat tartalmaz, amelyek ebben az esetben a [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatai.

## <a name="operational-events"></a>Működési események 

### <a name="recordingstarted-event"></a>RecordingStarted esemény

Amikor az eszköz elfogadó csomópontja elkezdi a videó rögzítését, a Microsoft. Media. Graph. Operational. RecordingStarted típusú eseményt bocsát ki.

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

A "tárgy" a applicationProperties hivatkozik a diagramban található, az üzenetet létrehozó objektum fogadó csomópontjára.

A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van. Jegyezze fel ezt az értéket.

### <a name="recordingavailable-event"></a>RecordingAvailable esemény

Ahogy a neve is sugallja, a rendszer a RecordingStarted eseményt küldi el a rögzítés megkezdése után, de előfordulhat, hogy a videós adatrekordokat még nem töltötték fel az eszközre. Amikor az eszköz fogadó csomópontja feltöltötte a videós adatmennyiséget az eszközre, a Microsoft. Media. Graph. Operational. RecordingAvailable típusú eseményt bocsát ki.

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Ez az esemény azt jelzi, hogy az eszköznek elegendő adattal kell megírnia, hogy a játékosok/ügyfelek a videó lejátszását kezdeményezzenek.

A "tárgy" a applicationProperties hivatkozik a gráf AssetSink csomópontjára, amely létrehozta ezt az üzenetet.

A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van.

### <a name="recordingstopped-event"></a>RecordingStopped esemény

Ha inaktiválja a Graph-példányt, az eszköz fogadó csomópontja leállítja a videó rögzítését az eszközre, a Microsoft. Media. Graph. Operational. RecordingStopped típusú eseményt bocsát ki.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Ez az esemény azt jelzi, hogy a rögzítés leállt.

A "tárgy" a applicationProperties hivatkozik a gráf AssetSink csomópontjára, amely létrehozta ezt az üzenetet.

A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van.

## <a name="media-services-asset"></a>Media Services eszköz  

Megvizsgálhatja a Media Graph által létrehozott Media Services adategységet, ha bejelentkezik a Azure Portalba, és megtekinti a videót.

1. Nyissa meg a webböngészőt, és lépjen a [Azure Portal](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.
1. Keresse meg Media Services-fiókját az előfizetésében található erőforrások között, és nyissa meg a fiók panelt
1. Kattintson az eszközök elemre a Media Services listájában

    ![Eszközök](./media/continuous-video-recording-tutorial/assets.png)
1. Itt található egy sampleAsset-CVRToAMSAsset-Sample-Graph-1 nevű objektum, amely a Graph-topológiai fájlban kiválasztott elnevezési minta.
1. Kattintson az eszközre.
1. Az eszköz adatai lapon kattintson az **új létrehozása** a streaming URL-címe szövegmezőbe.

    ![Új eszköz](./media/continuous-video-recording-tutorial/new-asset.png)

1. A megnyíló varázslóban fogadja el az alapértelmezett beállításokat, és nyomja le az "add" (Hozzáadás) lehetőséget. További információ: [Videolejátszás](video-playback-concept.md).

    > [!TIP]
    > Ellenőrizze, hogy [fut-e a folyamatos átviteli végpont](../latest/streaming-endpoint-concept.md).
1. A lejátszónak be kell töltenie a videót, és el kell tudnia érni a **Play**> * * lehetőséget a megtekintéshez.

> [!NOTE]
> Mivel a videó forrása egy kamera-hírcsatornát szimuláló tároló volt, a videó időbélyegei a Graph-példány aktiválásakor és az inaktiválásakor kapcsolódnak. [Ebből](playback-multi-day-recordings-tutorial.md) az oktatóanyagból megtudhatja, hogyan böngészhet a többnapos felvételen, és hogyan tekintheti meg az Archívum egyes részeit. Ebben az oktatóanyagban a képernyőn megjelenő videó időbélyegeit is látni fogja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi oktatóanyagot, tartsa be a következőt: a létrehozott erőforrásokhoz. Ellenkező esetben lépjen a Azure Portal, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben az oktatóanyagot futtatta, és törölje az erőforráscsoportot.

## <a name="next-steps"></a>További lépések

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az ONVIF-kompatibilis [termékek lapon](https://www.onvif.org/conformant-products/) megkeresheti az RTSP-támogatással rendelkező IP-kamerákat a G, S vagy T profiloknak megfelelő eszközök keresésével.
* AMD64 vagy x64 Linux rendszerű eszköz használata (Azure Linux rendszerű virtuális gép használata). Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. Kövesse az [Azure IoT Edge Runtime Linux rendszeren való telepítésének](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) utasításait, majd kövesse az [első IoT Edge modul üzembe helyezése a virtuális Linux-eszközökhöz című](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rövid útmutatót az eszköz Azure-IoT hub való regisztrálásához.
