---
title: Esemény-alapú videofelvétel a felhőbe és a felhőbe való lejátszásról a Cloud tutorialból – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket a felhőbe irányuló és a felhőből való lejátszást végző esemény-alapú videofelvételek elvégzéséhez.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: daab1f06d8950aa7710c7e808ea6362ee3bfd626
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261929"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Oktatóanyag: eseményvezérelt videofelvétel a felhőben és a felhőből való lejátszás

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a IoT Edge Live Video Analytics szolgáltatást, hogy az élő videó forrásainak szelektív rögzítését a felhőben Media Services. Ez a használati eset a jelen oktatóanyagban [Event-based video Recording](event-based-video-recording-concept.md) (EVR) néven is ismert. Ennek elvégzéséhez egy objektum-észlelési AI-modellt kell használnia az objektumok kereséséhez a videóban, és csak bizonyos típusú objektumok észlelésekor rögzíthet videoklipeket. Azt is megismerheti, hogyan lehet lejátszani a rögzített videoklipeket Media Services használatával. Ez számos esetben hasznos, ha meg kell őrizni az érdekes videoklipek archívumát.

> [!div class="checklist"]
> * A kapcsolódó erőforrások beállítása
> * A EVR-t végrehajtó kód vizsgálata
> * A mintakód futtatása
> * Ellenőrizze az eredményeket, és tekintse meg a videót

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás  

Javasoljuk, hogy olvassa el a következő dokumentációs oldalakat

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph alapfogalmai](media-graph-concept.md) 
* [Event-alapú videó rögzítése](event-based-video-recording-concept.md)
<!--* [Quickstart: Event-based recording based on motion events]()-->
* [Oktatóanyag: IoT Edge modul fejlesztése](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Az üzembe helyezés szerkesztése. *. template. JSON](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Az [útvonalak deklarálása IoT Edge telepítési jegyzékben –](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) szakasz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag előfeltételei a következők:

* A [Docker](https://docs.docker.com/desktop/) telepítése a fejlesztői gépen
* [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítménnyel és a [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) bővítménnyel.

    > [!TIP]
    > Előfordulhat, hogy a rendszer a Docker telepítését kéri. Ezt a kérdést figyelmen kívül hagyhatja.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) a fejlesztői gépen.
* Töltse ki az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) , és [állítsa be a környezetet](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

A fenti lépések végén az Azure-előfizetésben üzembe helyezett Azure-erőforrásokkal fog rendelkezni, beleértve a következőket:

* IoT Hub
* Tárfiók
* Media Services-fiók
* Linux rendszerű virtuális gép

## <a name="concepts"></a>Alapelvek

![Adathordozó-gráf](./media/event-based-video-recording-tutorial/overview.png)

Az Event-based video Recording (EVR) az esemény által aktivált videó rögzítési folyamatára utal. A szóban forgó esemény a videós jel feldolgozása miatt is előfordulhat (például ha egy mozgó objektumot észlel a videóban), vagy egy független forrásból származhat (például egy ajtó megnyitásakor). Azt is megteheti, hogy csak akkor aktiválja a rögzítést, ha egy külső viszonyítási szolgáltatás észleli, hogy egy adott esemény történt.  Ebben az oktatóanyagban egy autópályán mozgó járműveket használunk, és videoklipeket rögzíthetünk, amikor a rendszer teherautót észlel.

A fenti ábra egy [Media Graph](media-graph-concept.md) képi ábrázolása, valamint a kívánt forgatókönyvet megvalósító további modulok. A rendszer négyféle IoT Edge modult foglal magában:

* Élő videó-elemzés IoT Edge modulon
* Az [YOLO v3-modellel](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) létrehozott AI-modul
* Az oktatóanyagban felépíteni és üzembe helyezni kívánt objektumok (a fenti ábrán az objektum számlálója) számlálására és szűrésére szolgáló egyéni modul
* Egy olyan [RTSP szimulátor-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , amely egy RTSP-kamerát szimulál
    
Ahogy az ábrán is látható, egy [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontot fog használni a Media Graphban az élő videó rögzítéséhez és a videó két elérési útra való elküldéséhez.

* Az első útvonal egy [frame rate szűrő processzor](media-graph-concept.md#frame-rate-filter-processor) -csomópont, amely a képkockákat a megadott képkockán jeleníti meg. Ezeket a képkockákat a HTTP kiterjesztésű csomópont bemenetként adja meg. A HTTP-bővítmény csomópontja képkockákat (képként) küld a AI-modulnak (YOLO v3 – amely egy objektum-detektor), és eredményként fogadja az eredményeket, amelyek a modell által észlelt objektumok lesznek. A HTTP-bővítmény csomópont ezt követően közzéteszi az eredményeket a IoT Hub Message Mosogatón keresztül az IoT Edge hubhoz
* Az objektum-számláló modul úgy van beállítva, hogy üzeneteket fogadjon az IoT Edge hubhoz, amely tartalmazza az objektum-észlelési eredményeket (a forgalomban lévő járműveket). Ellenőrzi, hogy vannak-e olyan üzenetek, amelyek adott típusú objektumokat keresenek (egy Twin tulajdonságon keresztül konfigurálva), és egy üzenetet küld IoT Edge hubhoz. A rendszer ezután visszairányítja ezeket az üzeneteket a Media Graph IoT Hub forrás csomópontjára. Üzenet fogadásakor a Media Graph IoT Hub forrás csomópontja elindítja a [Signal Gate processzor](media-graph-concept.md#signal-gate-processor) -csomópontot, hogy a kaput a beállított időtartamra nyissa meg. A videó a kapun keresztül áramlik át az eszköz fogadó csomópontjára az adott időtartamra. Az élő adatfolyam ezen részét a rendszer az [eszköz](media-graph-concept.md#asset-sink) fogadó csomópontján keresztül rögzíti az Azure Media Service-fiókban lévő egyik [eszközre](terminology.md#asset) .

## <a name="set-up-the-environment"></a>A környezet beállítása

1. A tárház klónozása innen https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
2. Indítsa el a Visual Studio Code (VSCode) alkalmazást, és nyissa meg azt a mappát, ahová a tárház le van töltve.
3. A VSCode nyissa meg a "src/Cloud-to-Device-Console-app" mappát, és hozzon létre egy "appSettings. JSON" nevű fájlt. Ez a fájl a program futtatásához szükséges beállításokat fogja tartalmazni.
3. Másolja az clouddrive/LVA-Sample/appSettings. JSON fájl tartalmát az erőforrás-telepítési parancsfájl végrehajtása után. Lásd: [előfeltételek #4](event-based-video-recording-tutorial.md#prerequisites). Miután az erőforrás-telepítési parancsfájl befejeződik, kattintson a kapcsos zárójelre, hogy elérhetővé tegye a mappastruktúrát. A clouddrive/LVA-Sample alatt létrehozott három fájl jelenik meg. Az érdeklődés jelenleg a. env fájlok és a Alkalmazásbeállítás. JSON. Ezekre szüksége lesz a Visual Studio Code-ban lévő fájlok frissítéséhez a gyors útmutatóban. Előfordulhat, hogy most egy helyi fájlba szeretné másolni őket.

    ![Alkalmazásbeállítások](./media/quickstarts/clouddrive.png)

    A clouddrive/LVA-Sample/appSettings. JSON fájlból származó szövegnek a következőképpen kell kinéznie:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Ezután tallózással keresse meg az "src/Edge" mappát, és hozzon létre egy ". env" nevű fájlt.
1. Másolja a tartalmat a clouddrive/LVA-sample/. env fájlból. A szövegnek a következőhöz hasonlóan kell kinéznie:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```
## <a name="examine-the-template-file"></a>A sablon fájljának vizsgálata 

A környezet beállítása során elindította a Visual Studio Code-ot, és megnyitotta a mintakód-t tartalmazó mappát.

A Visual Studio Code-ban keresse meg az "src/Edge" parancsot. Ekkor megjelenik a létrehozott. env fájl, valamint néhány központi telepítési sablon fájl. Ezek a sablonok határozzák meg, hogy mely peremhálózati modulok telepíthetők a Linux rendszerű virtuális gépre. A. env fájlban szerepelnek a sablonokban használt változók értékei, például a IoT Hub kapcsolódási karakterlánc, amely lehetővé teszi parancsok küldését az Edge-modulokra az Azure IoT Hub használatával.

Nyissa meg a "src/Edge/Deployment. objectCounter. template. JSON" fájlt. Vegye figyelembe, hogy a "modulok" szakaszban négy bejegyzés szerepel – a fent felsorolt elemeknek megfelelően (fogalmak szakaszban):

* lvaEdge – ez az élő videó Analytics IoT Edge modulon
* yolov3 – ez a YOLO v3-modell használatával létrehozott következtetési szolgáltatás
* rtspsim – ez az RTSP-szimulátor
* objectCounter – ez az a modul, amely a yolov3 által visszaadott üzenetekben keresi a konkrét objektumokat.

A objectCounter modul esetében tekintse meg a "rendszerkép" értékhez használt karakterláncot, amely a IoT Edge modul fejlesztésének oktatóanyagán alapul. A Visual Studio Code automatikusan felismeri, hogy az objektum-számláló modul kódja az "src/Edge/Module/objectCounter" alatt található. Olvassa el az útvonalak deklarálása IoT Edge telepítési jegyzékben című szakaszt, majd vizsgálja meg az útvonalakat a sablon JSON-fájljában. Vegye figyelembe a következőket:

* A LVAToObjectCounter adott események küldésére szolgál egy adott végpontra a objectCounter modulban
* A ObjectCounterToLVA egy eseményindító-esemény küldésére szolgál egy adott végpontra (amely a IoT Hub forrásoldali csomópontnak kell lennie) a lvaEdge modulban
* a objectCounterToIoTHub hibakeresési eszközként használható – az oktatóanyag futtatásakor a objectCounter kimenetének megtekintéséhez

> [!NOTE]
> A objectCounter modul kívánt tulajdonságai – a rendszer úgy állítja be, hogy a "Truck" címkével rendelkező objektumokat keresse meg, legalább 50%-os megbízhatósági szinttel.

## <a name="deploy-the-edge-modules"></a>A peremhálózati modulok üzembe helyezése

A Visual Studio Code használatával kövesse az utasításokat a Docker-be való bejelentkezéshez, valamint a "Létrehozás és leküldése IoT Edge megoldáshoz", de használja az src/Edge/Deployment. objectCounter. template. JSON fájlt ehhez a lépéshez.

![IoT Edge-megoldás létrehozása és leküldése](./media/event-based-video-recording-tutorial/build-push.png)

Ez létrehozza a objectCounter modult az objektumok számlálásához, és leküldi a rendszerképet a Azure Container Registryba (ACR)

* Győződjön meg arról, hogy rendelkezik a környezeti változók CONTAINER_REGISTRY_USERNAME_myacr és az CONTAINER_REGISTRY_PASSWORD_myacr. env fájlban.

A fenti lépés létrehozza a IoT Edge üzembe helyezési jegyzéket az src/Edge/config/Deployment. objectCounter. amd64. JSON fájlban.

A Visual Studio Code-ban keresse meg az src/Edge/config/Deployment. objectCounter. amd64. JSON fájlt, kattintson a jobb gombbal a fájlra, és válassza a "központi telepítés létrehozása egyetlen eszközhöz" lehetőséget. 

Ha ez az első oktatóanyag a IoT Edge Live Video Analytics szolgáltatással, a Visual Studio Code megkéri, hogy adja meg a IoTHub-kapcsolódási karakterláncot. A appSettings. JSON fájlból másolhatja.

A Visual Studio Code ezt követően egy IoT hub-eszköz kiválasztását kéri. Válassza ki IoT Edge eszközét (legyen "LVA-Sample-Device").

Ebben a szakaszban az Edge-modulok üzembe helyezése a IoT Edge eszközön megkezdődött.
Körülbelül 30 másodperc alatt frissítse az Azure IOT hub-t a Visual Studio Code bal alsó részén, és látnia kell, hogy 4 modul van telepítve (jegyezze fel újra a következő neveket: lvaEdge, rtspsim, yolov3 és objectCounter).

![4 modul üzembe helyezése](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Felkészülés a figyelési eseményekre

Kattintson a jobb gombbal a peremhálózati eszközre ("LVA-Sample-Device"), majd kattintson a "beépített figyelési esemény végpontja" elemre. Az élő videó elemzése IoT Edge modulban [operatív](#operational-events) és [diagnosztikai](#diagnostic-events) eseményeket bocsát ki az IoT Edge hubhoz, és a Visual Studio Code-ban a "kimenet" ablakban láthatja ezeket az eseményeket.

## <a name="run-the-program"></a>A program futtatása

1. Visual Studio Code, navigáljon a "src/Cloud-to-Device-Console-app/Operations. JSON" elemre.
1. A csomópont GraphTopologySet állítsa be a következőt: "topologyUrl": " https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json " 
1. Ezután a csomópont GraphInstanceSet alatt szerkessze a "topologyName": "EVRtoAssetsOnObjDetect"
1. Nyomja le az "F5" billentyűt. Ekkor elindul a hibakeresési munkamenet.
1. A terminál ablakban láthatja, hogy a program által kezdeményezett [közvetlen metódusokra](direct-methods.md) adott válaszok a IoT Edge modul Live Video Analytics szolgáltatására mutatnak, amely a következő:

    1. GraphTopologyList – lekéri a modulhoz hozzáadott gráf-topológiák listáját, ha van ilyen

        A folytatáshoz nyomja meg az ENTER billentyűt
    1. GraphInstanceList – lekéri a létrehozott Graph-példányok listáját, ha van ilyen

        A folytatáshoz nyomja meg az ENTER billentyűt
    1. GraphTopologySet – hozzáadja a fenti, "EVRtoAssetsOnObjDetect" nevű topológiát a modulhoz
    1. GraphInstanceSet – létrehozza a fenti topológia egy példányát, és behelyettesíti a paramétereket.
    1. A kamat a rtspUrl paraméter. A Linux rendszerű virtuális gépre letöltött MKV-fájlra mutat arra a helyre, ahonnan az RTSP-szimulátor beolvassa
    1. GraphInstanceActivate – elindítja a Media Graphot, ami a videó áthaladását okozza
    1. GraphInstanceList – annak megjelenítéséhez, hogy most már van egy példánya a modulban, amelyen fut

        Ekkor szüneteltetni kell, és *nem* nyomja meg az ENTER billentyűt.
1. A kimeneti ablakban láthatja, hogy milyen működési és diagnosztikai üzeneteket küld a rendszer a IoT Hubnak, IoT Edge modul Live Video Analytics szolgáltatásával
1. A Media Graph továbbra is futni fog, és kinyomtatja az eseményeket – az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához nyomja meg ismét az ENTER billentyűt a terminál ablakban. A program a következőket küldi el:

    1. GraphInstanceDeactivate – a Graph-példány leállítása és a videó rögzítésének leállítása
    1. GraphInstanceDelete – a példány törlése a modulból
    1. GraphInstanceList – annak bemutatására, hogy a modulban már nincsenek példányok

> [!NOTE]
> A Graph-topológia nem lett törölve. Ha ezt szeretné tenni, futtassa ezt a lépést a következő JSON-törzstel:

```
{
    "@apiVersion" : "1.0",
    "name" : "EVRtoAssetsOnObjDetect"
}
```

## <a name="examine-the-output"></a>A kimenet vizsgálata
 
Az élő videó elemzése IoT Edge modulban [operatív](#operational-events) és [diagnosztikai](#diagnostic-events) eseményeket bocsát ki az IoT Edge hub számára, amely a Visual Studio Code kimenet ablakában látható szöveg, az eszközről a felhőbe irányuló kommunikációhoz létrehozott streaming Messaging formátum IoT hub:

* Az alkalmazás tulajdonságainak halmaza. Az alkalmazás által definiálható és elérhető karakterlánc-tulajdonságok szótára anélkül, hogy az üzenet törzsét deszerializálni kellene. IoT Hub soha nem módosítja ezeket a tulajdonságokat
* Egy átlátszatlan bináris törzs

Az alábbi üzenetekben az alkalmazás tulajdonságai és a törzs tartalmának meghatározása az élő videó Analytics IoT Edge modulban című részében található. További információ: [figyelés és naplózás](monitoring-logging.md). 

## <a name="diagnostic-events"></a>Diagnosztikai események

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény 

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az RTSP szimulátor-tárolón futó RTSP-kiszolgálóhoz. Ha ez sikeres, a rendszer kinyomtatja ezt az eseményt. Vegye figyelembe, hogy az esemény típusa Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [2:02:54 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589749373980489 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T21:02:53.981Z",
    "dataVersion": "1.0"
  }
}
```

Vegye figyelembe a következőket:

* A applicationProperties "tárgy" a MediaGraph azon csomópontjára hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy diagnosztikai esemény
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* a "Body" a diagnosztikai eseményre vonatkozó információkat tartalmaz – ez a SDP-üzenet

Jegyezze fel a eventTime – ez az az idő, amikor a forgalmi videó (MKV-fájl) megkezdte a modulnak élő streamként való megérkezését.

## <a name="operational-events"></a>Működési események

Miután a Media Graph egy darabig fut, végül egy eseményt fog kapni az objektum-számláló modulból. 

```
[IoTHubMonitor] [2:03:21 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T21:03:21.062Z"
  }
}
```

A applicationProperties csak a eventTime tartalmazza, amely az az idő, amikor a modul megfigyelte, hogy az YOLO v3 modulból származó objektumok tartalmaznak-e érdekes objektumokat (Trucks).

Előfordulhat, hogy a videóban több ilyen esemény jelenik meg, ahogy más kamionok is észlelhetők.

### <a name="recordingstarted-event"></a>RecordingStarted esemény

Szinte azonnal, miután az objektum számlálója elküldte az eseményt, a Microsoft. Media. Graph. Operational. RecordingStarted típusú eseményt fogja látni.

```
[IoTHubMonitor] [2:03:22 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T21:03:22.532Z",
    "dataVersion": "1.0"
  }
}
```

A "tárgy" a applicationProperties hivatkozik a diagramban található, az üzenetet létrehozó objektum fogadó csomópontjára.

A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van. Jegyezze fel ezt az értéket.

### <a name="recordingavailable-event"></a>RecordingAvailable esemény

Amikor az eszköz befogadó csomópontja videót töltött fel az objektumra, a Microsoft. Media. Graph. Operational. RecordingAvailable típusú eseményt bocsát ki.

```
[IoTHubMonitor] [2:03:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T21:03:31.808Z",
    "dataVersion": "1.0"
  }
}
```

Ez az esemény azt jelzi, hogy az eszköznek elegendő adattal kell megírnia, hogy a játékosok/ügyfelek a videó lejátszását kezdeményezzenek.

A "tárgy" a applicationProperties hivatkozik a gráf AssetSink csomópontjára, amely létrehozta ezt az üzenetet.

A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van.

### <a name="recordingstopped-event"></a>RecordingStopped esemény

Ha megvizsgálja az aktiválási beállításokat (maximumActivationTime) a topológia Signal Gate processzor-csomópontja számára, látni fogja, hogy a kapu a videó 30 másodpercét követő bezárásra van beállítva. A RecordingStarted esemény után nagyjából 30 másodperccel a Microsoft. Media. Graph. Operational. RecordingStopped típusú eseménynek kell megjelennie, amely azt jelzi, hogy az eszköz fogadó csomópontja leállította a videó rögzítését az eszközre.

```
[IoTHubMonitor] [2:03:52 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T21:03:52.040Z",
    "dataVersion": "1.0"
  }
}
```

Ez az esemény azt jelzi, hogy a rögzítés leállt.

A "tárgy" a applicationProperties hivatkozik a gráf AssetSink csomópontjára, amely létrehozta ezt az üzenetet.

A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van.

## <a name="media-services-asset"></a>Media Services eszköz  

Megvizsgálhatja a gráf által létrehozott Media Services adategységet, ha bejelentkezik a Azure Portalba, és megtekinti a videót.

1. Nyissa meg a webböngészőt, és lépjen a [Azure Portal](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.
1. Keresse meg Media Services-fiókját az előfizetésében található erőforrások között, és nyissa meg a fiók panelt
1. Kattintson az eszközök elemre a Media Services listájában

    ![Eszközök](./media/continuous-video-recording-tutorial/assets.png)
1. A sampleAssetFromCVR-LVAEdge-{DateTime} nevű adategységet a rendszer a Media Graph-topológia fájljában kiválasztott elnevezési mintát fogja megtalálni.
1. Kattintson az eszközre.
1. Az eszköz adatai lapon kattintson az **új létrehozása** a streaming URL-címe szövegmezőbe.

    ![Új eszköz](./media/continuous-video-recording-tutorial/new-asset.png)

1. A megnyíló varázslóban fogadja el az alapértelmezett beállításokat, és nyomja le az "add" (Hozzáadás) lehetőséget. További információ: [Videolejátszás](video-playback-concept.md).

    > [!TIP]
    > Ellenőrizze, hogy [fut-e a folyamatos átviteli végpont](../latest/streaming-endpoint-concept.md).
1. A lejátszónak be kell töltenie a videót, és el kell tudnia érni a **Play**> * * lehetőséget a megtekintéshez.

> [!NOTE]
> Mivel a videó forrása egy kamera-hírcsatornát szimuláló tároló volt, a videó időbélyegei a Graph-példány aktiválásakor és az inaktiválásakor kapcsolódnak. További információ: [Többnapos felvételek lejátszása](playback-multi-day-recordings-tutorial.md) a többnapos felvételek tallózásával és az Archívum egyes részeinek megtekintése. Ebben az oktatóanyagban láthatja a képernyőn megjelenő videó időbélyegeit is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi oktatóanyagot, tartsa be a következőt: a létrehozott erőforrásokhoz. Ellenkező esetben lépjen a Azure Portal, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben az oktatóanyagot futtatta, és törölje az erőforráscsoportot.

## <a name="next-steps"></a>További lépések

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az ONVIF-kompatibilis [termékek lapon](https://www.onvif.org/conformant-products/) megkeresheti az RTSP-támogatással rendelkező IP-kamerákat a G, S vagy T profiloknak megfelelő eszközök keresésével.
* AMD64 vagy x64 Linux rendszerű eszköz használata (Azure Linux rendszerű virtuális gép használata). Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. Kövesse az [Azure IoT Edge Runtime Linux rendszeren való telepítésének](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) utasításait, majd kövesse az [első IoT Edge modul üzembe helyezése a virtuális Linux-eszközökhöz című](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rövid útmutatót az eszköz Azure-IoT hub való regisztrálásához.
