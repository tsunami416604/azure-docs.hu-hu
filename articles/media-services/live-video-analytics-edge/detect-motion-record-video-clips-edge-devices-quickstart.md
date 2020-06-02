---
title: Mozgás észlelése, videó rögzítése az Edge-eszközökön – Azure
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket az élő videó-hírcsatornák (szimulált) IP-kameráról való elemzéséhez, hogy észlelje, hogy van-e mozgás, és ha igen, jegyezzen fel egy MP4-videoklipet a peremhálózati eszköz helyi fájljába.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d824870ea95922bbbdbf01cf2c95692522936f85
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262079"
---
# <a name="quickstart-detect-motion-record-video-on-edge-devices"></a>Gyors útmutató: mozgás észlelése, videó rögzítése a peremhálózati eszközökön
 
Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket az élő videó-hírcsatornák (szimulált) IP-kameráról való elemzéséhez, hogy észlelje, hogy van-e mozgás, és ha igen, jegyezzen fel egy MP4-videoklipet a peremhálózati eszköz helyi fájljába. Egy Azure-beli virtuális gépet használ IoT Edge eszközként és szimulált élő videó streamként. Ez a cikk a C# nyelven írt mintakód alapján készült.

Ez a cikk [erre](detect-motion-emit-events-quickstart.md) a rövid útmutatóra épül. 

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) a gépen a következő kiterjesztésekkel:
    * [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* A [.net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) telepítve van a rendszeren
* Ha még nem végezte el [ezt](detect-motion-emit-events-quickstart.md) a rövid útmutatót, végezze el a következő lépéseket:
     * [Az Azure-erőforrások beállítása](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     * [A fejlesztési környezet beállítása](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     * [Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest)
     * [Felkészülés a figyelési eseményekre](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

> [!TIP]
> Az Azure IoT-eszközök telepítésekor előfordulhat, hogy a rendszer a Docker telepítésére kéri. Nyugodtan hagyja figyelmen kívül.

## <a name="review-the-sample-video"></a>A minta videó áttekintése
Az Azure-erőforrások létrehozásához szükséges lépések részeként a rendszer egy (rövid) videót másol az Azure-beli linuxos virtuális gépre, amelyet a IoT Edge eszközként használ. Ez a videofájl az oktatóanyag élő folyamának szimulálására szolgál.

Használhat egy alkalmazást, például a [VLC Playert](https://www.videolan.org/vlc/), elindíthatja, lenyomhatja a CTRL + N billentyűkombinációt, majd beillesztheti [ezt](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) a lehetőséget a parkoló videóra a lejátszás megkezdéséhez. Az 5 másodperces jel körülbelül egy fehér autó halad át a parkolón.

Az alábbi lépések elvégzése után élő videó-elemzéseket használt IoT Edgeon az autó mozgásának észleléséhez és az 5 másodperces megjelölés körüli videoklip rögzítéséhez.

## <a name="overview"></a>Áttekintés

![Áttekintés](./media/quickstarts/overview-qs4.png)

A fenti ábra azt mutatja be, hogyan áramlik be a gyors útmutatóban szereplő jelek. Az Edge-modul (részletesen [itt](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) SZIMULÁL egy RTSP-kiszolgálót futtató IP-kamerát. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a video-képkockákat a [mozgásérzékelő processzor](media-graph-concept.md#motion-detection-processor) -csomópontjára küldi. Az RTSP-forrás ugyanazokat a képkockákat küldi el egy [Signal Gate processzor](media-graph-concept.md#signal-gate-processor) -csomópontba, amely mindaddig be van zárva, amíg egy esemény nem indítja el.

Ha a mozgásérzékelő processzora meghatározza, hogy a mozgás megtalálható a videóban, akkor egy eseményt küld a Signal Gate processzor-csomópontnak, és elindítja azt. A kapu a beállított időtartamra nyílik meg, és képkockákat küld a [file mosogató](media-graph-concept.md#file-sink) csomópontba. Ez a fogadó csomópont MP4-fájlként rögzíti a videót a peremhálózati eszköz helyi fájlrendszerében a konfigurált helyen.

Ebben a rövid útmutatóban a következőket fogja megtekinteni:

1. A Media Graph létrehozása és üzembe helyezése
1. Az eredmények értelmezése
1. Az erőforrások eltávolítása

## <a name="examine-and-edit-the-sample-files"></a>A mintaadatok vizsgálata és szerkesztése
Az előfeltételek részeként a mintakód egy mappába lett letöltve. Indítsa el a Visual Studio Code-ot, és nyissa meg a mappát.

1. A Visual Studio Code-ban keresse meg az "src/Edge" parancsot. A létrehozott. env fájlt a telepítési sablonok néhány fájlja is tartalmazza
    * A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik néhány helyőrző értékkel. A. env fájl ezen változók értékeit tartalmazhatja.
1. Ezután tallózással keresse meg a "src/Cloud-to-Device-Console-app" mappát. Itt láthatja a létrehozott appSettings. JSON fájlt, valamint néhány további fájlt is:
    * C2D-Console-app. csproj – ez a Project fájl a Visual Studio Code-hoz
    * Operations. JSON – ez a fájl felsorolja azokat a különböző műveleteket, amelyekre a programot futtatni szeretné
    * Program.cs – ez a mintakód, amely a következő műveleteket végzi el:

        * Az alkalmazás beállításainak betöltése
        * Az élő videó Analytics IoT Edge modulban elérhető közvetlen metódusokat hívja meg. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md) meghívásával 
        * Szünetelteti a program kimenetének vizsgálatát a terminál ablakban, valamint a kimenet ablakban a modul által generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez   

1. Végezze el az alábbi módosításokat a Operations. JSON fájlban
    * Módosítsa a Graph-topológiára mutató hivatkozást:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * A GraphInstanceSet alatt szerkessze a gráf topológiájának nevét, hogy az megfeleljen a fenti hivatkozásban szereplő értéknek.`"topologyName" : "EVRToFilesOnMotionDetection"`
    * Szerkessze az RTSP URL-címét is, hogy a kívánt videofájl felé mutasson`"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * A GraphTopologyDelete alatt szerkessze a nevet`"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-status-of-the-modules"></a>A modulok ellenőrzési állapotának ellenőrzése
Ha a [IoT Edge üzembe helyezési jegyzékfájljának létrehozása és telepítése](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest) lépésben a Visual Studio Code-ban kibontja a "LVA-Sample-Device" csomópontot az Azure IoT hub alatt (a bal alsó szakaszban), akkor a következő modulokat kell látnia:

    1. A "lvaEdge" névvel ellátott Live Video Analytics-modul
    1. Egy "rtspsim" nevű modul, amely szimulál egy RTSP-kiszolgálót, amely egy élő videó-hírcsatorna forrásaként működik

        ![Modulok](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Felülvizsgálat – felkészülés a figyelési eseményekre
Győződjön meg arról, hogy végrehajtotta a [figyelési események előkészítésének](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events) lépéseit.

![A beépített esemény-végpont figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>A minta program futtatása

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
A Media Graph futtatásakor a mozgásérzékelő processzor csomópontjának eredményeit a rendszer a IoT Hub fogadó csomóponton keresztül továbbítja a IoT Hub. A Visual Studio Code kimenet ablakában látható üzenetek "Body" szakaszt és egy "applicationProperties" szakaszt tartalmaznak. Ennek a résznek a megismeréséhez olvassa el [ezt a](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) cikket.

Az alábbi üzenetekben az alkalmazás tulajdonságait és a törzs tartalmát az élő videó elemzési modulja határozza meg.

## <a name="mediasession-established-event"></a>MediaSession-létrehozási esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a művelet sikeres, az a következő eseményt fogja kinyomtatni:

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

* Az üzenet egy diagnosztikai esemény, a MediaSessionEstablished azt jelzi, hogy az RTSP-forrás csomópont (a tárgy) képes volt kapcsolatot létesíteni az RTSP-szimulátorral, és elkezdi fogadni a (szimulált) élő hírcsatornát.
* A "tárgy" a applicationProperties hivatkozik a Graph-topológia azon csomópontjára, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy diagnosztikai esemény.
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* a "Body" a diagnosztikai eseménnyel kapcsolatos adatokat tartalmaz, amelyek ebben az esetben a [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatai.


## <a name="recording-started-event"></a>Felvétel elindítva esemény

Ahogy az [itt](#overview)látható, a mozgás észlelésekor a Signal Gate processzor csomópontja aktiválva lesz, és a Media Graph fájl fogadó csomópontja az MP4-fájl írására indul. A file mosogató csomópont egy operatív eseményt küld. A típus "Motion" (mozgás) értékre van állítva, amely jelzi, hogy a mozgásészlelési processzor eredménye, a eventTime pedig jelzi, hogy milyen időpontban (UTC) történt a mozgás. Az alábbiakban egy példa látható:

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

* a "tárgy" a applicationProperties azon a csomóponton hivatkozik, amelyből az üzenet létrejött. Ebben az esetben az üzenet a file mosogató csomópontból származik.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy működési esemény.
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett. Vegye figyelembe, hogy ez a 5-6 másodperc a MediaSessionEstablished után, a videó pedig elkezd áramlani. Ez megfelel a 5-6 második jelzésnek, ha az [autó elindult](#review-the-sample-video) a parkolóba.
* a "Body" az operatív eseménnyel kapcsolatos információkat tartalmaz, amelyek ebben az esetben a "outputType" és a "outputLocation" típusúak.
* a "outputType" érték azt jelzi, hogy ezek az adatok a fájl elérési útjára mutatnak
* a "outputLocation" az MP4-fájl helyét adja meg az Edge-modulon belül

## <a name="recording-stopped-and-available-events"></a>Leállított és elérhető események rögzítése

Ha megvizsgálja a Signal Gate processzor csomópontjának tulajdonságait a [Graph-topológiában](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), látni fogja, hogy az aktiválási időpontok értéke 5 másodperc. Tehát körülbelül 5 másodperccel a RecordingStarted esemény fogadása után a következőt kapja
* RecordingStopped esemény, amely azt jelzi, hogy a rögzítés leállt
* RecordingAvailable-esemény, amely azt jelzi, hogy az MP4-fájl már használható a megtekintéshez

A két eseményt általában másodpercek alatt bocsátjuk ki.

### <a name="playing-back-the-mp4-clip"></a>Az MP4-klip lejátszása

1. Az MP4-fájlokat a peremhálózati eszköz egy olyan könyvtárába írja a rendszer, amelyet a. env fájlban konfigurált a kulcs-OUTPUT_VIDEO_FOLDER_ON_DEVICE. Ha az alapértelmezett értékre hagyta, akkor az eredmények a/Home/lvaadmin/Samples/output/
1. Nyissa meg az erőforráscsoportot, keresse meg a virtuális gépet, és kapcsolódjon a Bastion használatával

    ![Erőforráscsoport](./media/quickstarts/resource-group.png)
 
    ![VM](./media/quickstarts/virtual-machine.png)
1. Miután bejelentkezett (az [ebben a](detect-motion-emit-events-quickstart.md#set-up-azure-resources) lépésben létrehozott hitelesítő adatok használatával), a parancssorban nyissa meg a megfelelő könyvtárat (alapértelmezett:/Home/lvaadmin/Samples/output), és ott kell megjelennie az MP4-fájloknak. [A fájlokat](https://docs.microsoft.com/azure/virtual-machines/linux/copy-files-to-linux-vm-using-scp) a helyi gépre is felhasználhatja, és a [VLC Playerben](https://www.videolan.org/vlc/) vagy bármely más MP4-lejátszón keresztül játssza le.

    ![Kimenet](./media/quickstarts/samples-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi rövid útmutatót, tartsa be a következőt: a létrehozott erőforrásokhoz. Ellenkező esetben lépjen a Azure Portalra, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben futtatta ezt a rövid útmutatót, és törölje az összes erőforrást.

## <a name="next-steps"></a>További lépések

* Futtassa a [Live Video Analytics futtatása a saját modellhez](use-your-model-quickstart.md) című útmutatót, amely bemutatja, hogyan alkalmazhatja a mesterséges intelligenciát élő videós hírcsatornára.
* Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

    * Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az ONVIF-kompatibilis [termékek](https://en.wikipedia.org/wiki/IP_camera) lapon megkeresheti az RTSP-támogatással rendelkező IP-kamerákat a G, S vagy T profiloknak megfelelő eszközök keresésével.
    * AMD64 vagy x64 Linux rendszerű eszköz használata (Azure Linux rendszerű virtuális gép használata). Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. Kövesse az [Azure IoT Edge Runtime Linux rendszeren való telepítésének](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) utasításait, majd kövesse az [első IoT Edge modul üzembe helyezése a virtuális Linux-eszközökhöz című](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rövid útmutatót az eszköz Azure-IoT hub való regisztrálásához.
