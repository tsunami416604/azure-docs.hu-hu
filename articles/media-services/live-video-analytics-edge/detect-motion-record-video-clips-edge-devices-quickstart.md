---
title: A Motion & Record Videójának észlelése az Edge-eszközökön – Azure
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket az élő videó-hírcsatornák (szimulált) IP-kameráról való elemzéséhez, hogy észlelje, hogy van-e mozgás, és ha igen, jegyezzen fel egy MP4-videoklipet a peremhálózati eszköz helyi fájljába.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 796def7cad3632dd50184bea751dc9f348569216
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067692"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Rövid útmutató: mozgás észlelése és videó rögzítése a peremhálózati eszközökön
 
Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Live Video Analytics szolgáltatást a IoT Edgeon az élő videó hírcsatornájának (szimulált) IP-kamerából való elemzéséhez. Bemutatja, hogyan lehet észlelni, hogy van-e mozgás, és ha igen, jegyezzen fel egy MP4-videoklipet a peremhálózati eszköz helyi fájljába. A rövid útmutató egy Azure-beli virtuális gépet használ IoT Edge eszközként, és egy szimulált élő videó streamet is használ. 

Ez a cikk a C# nyelven írt mintakód alapján készült. Az [észlelési és a kibocsátási események](detect-motion-emit-events-quickstart.md) gyors üzembe helyezésére épül. 

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , ha még nem rendelkezik ilyennel.
* [Visual Studio Code](https://code.visualstudio.com/)a következő kiterjesztésekkel:
    * [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Ha még nem fejezte be a [mozgás észlelése és az események kibocsátása](detect-motion-emit-events-quickstart.md) című rövid útmutatót, kövesse az alábbi lépéseket:
     1. [Az Azure-erőforrások beállítása](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [A fejlesztési környezet beállítása](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [Felkészülés az események figyelésére](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> Az Azure IoT-eszközök telepítésekor előfordulhat, hogy a rendszer a Docker telepítésére kéri. Nyugodtan hagyja figyelmen kívül a kérdést.

## <a name="review-the-sample-video"></a>A minta videó áttekintése
Az ehhez a rövid útmutatóhoz tartozó Azure-erőforrások beállítása során a rendszer egy, a IoT Edge eszközként használt Azure-beli linuxos virtuális gépre másolja a parkolót. Ez a videofájl az oktatóanyag élő folyamának szimulálására szolgál.

Nyisson meg egy alkalmazást, például a [VLC Media Playert](https://www.videolan.org/vlc/), válassza a CTRL + N billentyűkombinációt, majd illessze be [ezt a hivatkozást](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) a parkoló videóra a lejátszás megkezdéséhez. Az 5 másodperces jel körülbelül egy fehér autó halad át a parkolón.

Az alábbi lépéseket követve élő videó-elemzéseket használhat IoT Edgeon az autó mozgásának észleléséhez és az 5 másodperces megjelölés körüli videoklip rögzítéséhez.

## <a name="overview"></a>Áttekintés

![Áttekintés](./media/quickstarts/overview-qs4.png)

Az előző ábrán látható, hogyan áramlik a jelek a rövid útmutatóban. [Az Edge-modulok](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) egy valós idejű Streaming Protocol-(RTSP-) kiszolgálót futtató IP-kamerát szimulálnak. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a video-képkockákat a [mozgásérzékelő processzor](media-graph-concept.md#motion-detection-processor) -csomópontjára küldi. Az RTSP-forrás ugyanazt a képkockákat küldi el egy [Signal Gate processzor](media-graph-concept.md#signal-gate-processor) -csomópontnak, amely mindaddig be van zárva, amíg egy esemény nem indít el.

Ha a mozgásérzékelő processzora észleli a videót, egy eseményt küld a Signal Gate processzor-csomópontba, és elindítja azt. A kapu a beállított időtartamra nyílik meg, és képkockákat küld a [file mosogató](media-graph-concept.md#file-sink) csomópontba. Ez a fogadó csomópont MP4-fájlként rögzíti a videót a peremhálózati eszköz helyi fájlrendszerén. A fájlt a rendszer a konfigurált helyre menti.

Ebben a rövid útmutatóban a következőket fogja megtekinteni:

1. A Media Graph létrehozása és üzembe helyezése.
1. Az eredmények értelmezése.
1. Az erőforrások eltávolítása.

## <a name="examine-and-edit-the-sample-files"></a>A mintaadatok vizsgálata és szerkesztése
A rövid útmutató előfeltételeinek részeként letöltötte a mintakód egy mappába. A mintakód vizsgálatához és szerkesztéséhez kövesse az alábbi lépéseket.

1. A Visual Studio Code-ban lépjen az *src/Edge*elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik, ahol egyes tulajdonságok esetében változókat kell használni. A *. env* fájl tartalmazza a változók értékeit.
1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a fájl *appsettings.jsét* és néhány további fájlt:
    * ***C2D-Console-app. csproj*** – a Project fájl a Visual Studio Code-hoz.
    * ***operations.json*** – a program futtatásához használni kívánt műveletek listája.
    * ***Program.cs*** – a minta programkódja. Ez a kód:

        * Betölti az alkalmazás beállításait.
        * Közvetlen metódusokat hív meg, amelyeket az élő videó Analytics IoT Edge modulban tesz elérhetővé. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md)meghívásával. 
        * Szünetelteti, így megvizsgálhatja a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez.

1. Szerkessze a *operations.js* fájlt:
    * Módosítsa a Graph-topológiára mutató hivatkozást:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * A (z) alatt `GraphInstanceSet` szerkessze a gráf topológiájának nevét, hogy az megfeleljen az előző hivatkozásban szereplő értéknek:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`

    * Szerkessze az RTSP URL-címét, hogy a videofájl mutasson:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

    * `GraphTopologyDelete`A alatt szerkessze a nevet:

        `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-the-modules-status"></a>Ellenőrzés – a modulok állapotának ellenőrzése

A [IoT Edge üzembe helyezési jegyzékfájljának előállítása és telepítése](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) lépésben, a Visual Studio Code-ban bontsa ki a **LVA-Sample-Device** CSOMÓPONTOT az **Azure IoT hub** alatt (a bal alsó szakaszban). A következő modulokat kell látnia:

* A (z) nevű Live Video Analytics-modul`lvaEdge`
* A `rtspsim` modul, amely egy élő videó-hírcsatorna forrásaként szolgáló RTSP-kiszolgálót szimulál

  ![Modulok](./media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Ha saját peremhálózati eszközt használ a telepítési parancsfájlból kiépített helyett, nyissa meg a peremhálózati eszközét, és futtassa az alábbi parancsokat **rendszergazdai jogosultságokkal**az ehhez a rövid útmutatóhoz használt minta videofájl lekéréséhez és tárolásához:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```

## <a name="review---prepare-for-monitoring-events"></a>Felülvizsgálat – felkészülés a figyelési eseményekre
Győződjön meg arról, hogy végrehajtotta az [események figyelésének előkészítéséhez](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)szükséges lépéseket.

![A beépített esemény-végpont figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>A minta program futtatása

1. Indítsa el a hibakeresési munkamenetet az F5 billentyű kiválasztásával. A **Terminálablak** kinyomtat néhány üzenetet.
1. A kód *operations.js* a közvetlen metódusokat hívja `GraphTopologyList` meg `GraphInstanceList` . Ha az előző rövid útmutatók után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. Nyomja meg az Enter billentyűt.

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
  * A következőt `GraphTopologySet` használó hívás`topologyUrl` 
  * A `GraphInstanceSet` következő törzset használó hívás:

```
{
  "@apiVersion": "1.0",
  "name": "Sample-Graph",
  "properties": {
    "topologyName": "EVRToFilesOnMotionDetection",
    "description": "Sample graph description",
    "parameters": [
      {
        "name": "rtspUrl",
        "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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

3. A **terminál** ablakban lévő kimenet a következő időpontban szünetel: `Press Enter to continue` . Ne jelölje be az ENTER billentyűt. Görgetéssel tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a **kimeneti** ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulon az IoT hub-ra küld üzenetet. A rövid útmutató következő szakasza ezeket az üzeneteket tárgyalja.

1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a **terminál** ablakába, és válassza az ENTER billentyűt. 

    A következő hívási sorozat megtisztítja az erőforrásokat:
     * A Graph- `GraphInstanceDeactivate` példány inaktiválására irányuló hívás.
     * A `GraphInstanceDelete` példány törlésére irányuló hívás.
     * A `GraphTopologyDelete` topológia törlésére irányuló hívás.
     * A végső hívás `GraphTopologyList` azt mutatja, hogy a lista már üres.

## <a name="interpret-results"></a>Eredmények értelmezése 
A Media Graph futtatásakor a mozgásérzékelő processzor csomópontjának eredményei áthaladnak a IoT Hub fogadó csomóponton az IoT hubhoz. A Visual Studio Code **kimenet** ablakában látható üzenetek egy `body` szakaszt és egy `applicationProperties` szakaszt tartalmaznak. További információ: [IoT hub üzenetek létrehozása és olvasása](../../iot-hub/iot-hub-devguide-messages-construct.md).

A következő üzenetekben az élő videó elemzési modulja az alkalmazás tulajdonságait és a törzs tartalmát határozza meg.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a kapcsolat sikeres, a következő esemény lesz kinyomtatva.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

Az előző kimenetben: 

* Az üzenet egy diagnosztikai esemény `MediaSessionEstablished` . Azt jelzi, hogy az RTSP-forrás csomópontja (a tárgy) létrehozta a kapcsolódást az RTSP-szimulátorral, és megkezdte a (szimulált) élő adatcsatorna fogadását.
* A alkalmazásban a `applicationProperties` `subject` diagram topológiájában lévő csomópontra hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett.
* A `body` szakasz a diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.

### <a name="recordingstarted-event"></a>RecordingStarted esemény

Ha a rendszer mozgást észlel, a rendszer aktiválta a Signal Gate processzor csomópontját, és a Media Graph fájl fogadó csomópontja az MP4-fájl írására indul. A file mosogató csomópont egy operatív eseményt küld. A értéke úgy `type` van beállítva, hogy `motion` jelezze, hogy a mozgásészlelési processzor eredménye. Az `eventTime` érték az az UTC-idő, amikor a mozgás bekövetkezett. A folyamattal kapcsolatos további információkért tekintse meg a rövid útmutató [Áttekintés](#overview) című szakaszát.

Íme egy példa erre az üzenetre:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

Az előző üzenetben: 

* A alkalmazásban a a `applicationProperties` `subject` Media Graph azon csomópontjára hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet a file mosogató csomópontból származik.
* A ben a `applicationProperties` `eventType` azt jelzi, hogy ez az esemény működőképes.
* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett. Ez az idő 5 – 6 másodperc, amely után a `MediaSessionEstablished` videó elkezd áramlani. Ez az idő az 5 – 6 másodperces jelzésnek felel meg, amikor az [autó elindult](#review-the-sample-video) a parkolóba.
* A `body` szakasz az operatív eseményre vonatkozó információkat tartalmaz. Ebben az esetben az adathalmaz és a `outputType` `outputLocation` .
* A `outputType` változó azt jelzi, hogy ez az információ a fájl elérési útjáról szól.
* Az `outputLocation` érték az MP4-fájl helye a peremhálózati modulban.

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped és RecordingAvailable események

Ha megvizsgálja a Signal Gate processzor csomópontjának tulajdonságait a [Graph-topológiában](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), láthatja, hogy az aktiválási idő 5 másodpercre van állítva. Az esemény fogadását követően 5 másodperccel a `RecordingStarted` következőket kapja:

* Egy `RecordingStopped` esemény, amely azt jelzi, hogy a rögzítés leállt.
* Egy `RecordingAvailable` esemény, amely azt jelzi, hogy az MP4-fájl már használható megtekintésre.

A két eseményt általában másodpercek alatt bocsátják ki.

## <a name="play-the-mp4-clip"></a>MP4-klip lejátszása

Az MP4-fájlokat a peremhálózati eszköz egy olyan könyvtárába írja a rendszer, amelyet a *. env* fájlban konfigurált a OUTPUT_VIDEO_FOLDER_ON_DEVICE kulcs használatával. Ha az alapértelmezett értéket használta, akkor az eredményeknek a */var/Media/* mappában kell lenniük.

Az MP4-klip lejátszása:

1. Nyissa meg az erőforráscsoportot, keresse meg a virtuális gépet, majd az Azure Bastion használatával kapcsolódjon.

    ![Erőforráscsoport](./media/quickstarts/resource-group.png)
    
    ![VM](./media/quickstarts/virtual-machine.png)

1. Jelentkezzen be az [Azure-erőforrások beállításakor](detect-motion-emit-events-quickstart.md#set-up-azure-resources)létrehozott hitelesítő adatok használatával. 
1. A parancssorban nyissa meg a megfelelő könyvtárat. Az alapértelmezett hely a */var/Media*. Ekkor megjelenik az MP4-fájlok a könyvtárban.

    ![Kimenet](./media/quickstarts/samples-output.png) 

1. A [biztonságos másolás (SCP)](../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) használatával másolja a fájlokat a helyi gépre. 
1. A fájlokat a [VLC Media Player](https://www.videolan.org/vlc/) vagy bármely más MP4-lejátszó használatával játssza le.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi rövid útmutatót, tartsa meg a létrehozott erőforrásokat. Ellenkező esetben a Azure Portal nyissa meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben a rövid útmutatót futtatta, majd törölje az összes erőforrást.

## <a name="next-steps"></a>További lépések

* Kövesse a [Live Video Analytics futtatása a saját modell](use-your-model-quickstart.md) rövid útmutatójában a mesterséges intelligenciát élő video-hírcsatornák alkalmazásához.
* Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

    * Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőségi termékek](https://www.onvif.org/conformant-products) lapon találja. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.
    * A Linux rendszerű virtuális gépek helyett AMD64 vagy x64 Linux-eszközt használjon az Azure-ban. Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. Kövesse a következő témakör utasításait: [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Ezután kövesse az [első IoT Edge modul üzembe helyezése egy virtuális Linux-eszközön](../../iot-edge/quickstart-linux.md) című témakör útmutatását az eszköz Azure IoT hub való regisztrálásához.
