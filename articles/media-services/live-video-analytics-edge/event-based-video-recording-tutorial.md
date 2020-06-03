---
title: Esemény-alapú videofelvétel a felhőbe és a felhőbe való lejátszásról a Cloud tutorialból – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket a felhőbe irányuló és a felhőből való lejátszást végző esemény-alapú videofelvételek elvégzéséhez.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 92367634a2f5785ecbb102db1e03f3d5f12d744e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300824"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Oktatóanyag: eseményvezérelt videofelvétel a felhőben és a felhőből való lejátszás

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a IoT Edge Live Video Analytics szolgáltatást, hogy az élő videó forrásainak szelektív rögzítését a felhőben Media Services. Ez a használati eset a jelen oktatóanyagban [Event-based video Recording](event-based-video-recording-concept.md) (EVR) néven is ismert. Ennek elvégzéséhez egy objektum-észlelési AI-modellt kell használnia az objektumok kereséséhez a videóban, és csak bizonyos típusú objektumok észlelésekor rögzíthet videoklipeket. Azt is megismerheti, hogyan lehet lejátszani a rögzített videoklipeket Media Services használatával. Ez számos esetben hasznos lehet, ha meg kell őrizni az érdekes videoklipek archívumát.

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
* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)
* [Oktatóanyag: IoT Edge modul fejlesztése](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Az üzembe helyezés szerkesztése. *. template. JSON](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Az [útvonalak deklarálása IoT Edge telepítési jegyzékben –](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) szakasz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag előfeltételei a következők:

* [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítménnyel és a [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) bővítménnyel.

    > [!TIP]
    > Előfordulhat, hogy a rendszer a Docker telepítését kéri. Ezt a kérdést figyelmen kívül hagyhatja.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) a fejlesztői gépen.
* Töltse ki az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) , és [állítsa be a környezetet](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

A fenti lépések végén az Azure-előfizetésben üzembe helyezett Azure-erőforrásokkal fog rendelkezni, beleértve a következőket:

* IoT Hub
* Tárfiók
* Azure Media Services fiók
* Linux virtuális gép az Azure-ban, [IoT Edge futtatókörnyezettel](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) telepítve

## <a name="concepts"></a>Alapelvek

Az Event-based video Recording (EVR) az esemény által aktivált videó rögzítési folyamatára utal. Ez az esemény a videós jel feldolgozásával hozható létre (például egy mozgó objektum észlelése a videóban) vagy egy független forrásból (például egy ajtó megnyitásakor). Azt is megteheti, hogy csak akkor aktiválja a rögzítést, ha egy következtetési szolgáltatás észleli, hogy egy adott esemény történt.  Ebben az oktatóanyagban egy autópályán mozgó járműveket használunk, és videoklipeket rögzíthetünk, amikor a rendszer teherautót észlel.

![Médiagrafikon](./media/event-based-video-recording-tutorial/overview.png)

A fenti ábra egy [Media Graph](media-graph-concept.md) képi ábrázolása, valamint a kívánt forgatókönyvet megvalósító további modulok. A rendszer négyféle IoT Edge modult foglal magában:

* Élő videó-elemzés IoT Edge modulon.
* Egy Edge-modul, amely egy HTTP-végpont mögötti AI-modellt futtat. Ez az AI-modul a [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) modellt használja, amely képes számos típusú objektum észlelésére.
* Az oktatóanyagban felépíteni és üzembe helyezni kívánt objektumok (a fenti ábrán az objektum számlálója) számlálására és szűrésére szolgáló egyéni modul.
* Egy [RTSP szimulátor-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , amely egy RTSP-kamerát szimulál.
    
    Ahogy az ábrán látható, egy RTSP- [forrás](media-graph-concept.md#rtsp-source) csomópontot fog használni a Media Graphban, hogy rögzítse a szimulált élő videót (a forgalom egy autópályán), majd küldje el a videót két elérési útra.

* Az első útvonal egy [frame rate szűrő processzor](media-graph-concept.md#frame-rate-filter-processor) -csomópont, amely a képkockákat a megadott (csökkentett) képkockán jeleníti meg. Ezeket a képkockákat a rendszer egy HTTP-bővítményi csomópontba küldi, amely ezután a képkockákat (képként) továbbítja az AI-modulhoz (YOLO v3 – ez egy objektum-detektor), és eredményeket fogad, amely a modell által észlelt objektumok (a forgalomban lévő járművek) lesznek. A HTTP-bővítmény csomópont ezt követően közzéteszi az eredményeket az IoT Hub Message fogadó csomóponton keresztül az IoT Edge hubhoz.
* Az objektum-számláló modul úgy van beállítva, hogy üzeneteket fogadjon az IoT Edge hubhoz, amely tartalmazza az objektum-észlelési eredményeket (a forgalomban lévő járműveket). Ellenőrzi, hogy vannak-e olyan objektumok, amelyek adott típusú objektumokat keresenek (egy beállítással konfigurálva). Ha ilyen objektum található, a modul üzenetet küld IoT Edge hubhoz. Az "objektum található" üzeneteket a rendszer átirányítja a Media Graph IoT Hub forrás csomópontjára. Egy ilyen üzenet fogadásakor a Media Graph IoT Hub forrás csomópontja elindítja a [Signal Gate processzor](media-graph-concept.md#signal-gate-processor) csomópontját, így az utóbbi egy beállított ideig nyitva van. A videó a kapun keresztül áramlik át az eszköz fogadó csomópontjára az adott időtartamra. Az élő adatfolyam ezen részét a rendszer az [eszköz](media-graph-concept.md#asset-sink) fogadó csomópontján keresztül rögzíti az Azure Media Service-fiókban lévő egyik [eszközre](terminology.md#asset) .

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta a harmadik felsorolásjelet az [Előfeltételekben](#prerequisites). Miután az erőforrás-telepítési parancsfájl befejeződik, kattintson a kapcsos zárójelre, hogy elérhetővé tegye a mappastruktúrát. A ~/clouddrive/LVA-Sample könyvtár alatt létrehozott néhány fájl jelenik meg.

![Alkalmazásbeállítások](./media/quickstarts/clouddrive.png)

Ebben az oktatóanyagban a következők érdeklik:

* ~/clouddrive/LVA-Sample/Edge-Deployment/.env – olyan tulajdonságokat tartalmaz, amelyeket a Visual Studio Code használ a modulok peremhálózati eszközre való telepítéséhez.
* ~/clouddrive/LVA-Sample/AppSetting.JSON – a minta kód futtatásához a Visual Studio Code-ot használja.

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

## <a name="examine-the-template-file"></a>A sablon fájljának vizsgálata 

Az előző lépésben elindította a Visual Studio Code-ot, és megnyitotta a mintakódt tartalmazó mappát.

A Visual Studio Code-ban keresse meg az "src/Edge" parancsot. Ekkor megjelenik a létrehozott. env fájl, valamint néhány központi telepítési sablon fájl. Ez a sablon határozza meg, hogy mely peremhálózati modulok lesznek üzembe helyezhetők a peremhálózati eszközön (az Azure-beli linuxos virtuális gépen). A. env fájl tartalmazza a sablonokban használt változók értékeit, például a Media szolgáltatás hitelesítő adatait.

Nyissa meg a "src/Edge/Deployment. objectCounter. template. JSON" fájlt. Vegye figyelembe, hogy a "modulok" szakaszban négy bejegyzés szerepel – a fent felsorolt elemeknek megfelelően (fogalmak szakaszban):

* lvaEdge – ez az élő videó Analytics IoT Edge modulon
* yolov3 – ez a YOLO v3-modellel létrehozott AI-modul
* rtspsim – ez az RTSP-szimulátor
* objectCounter – ez az a modul, amely a yolov3 eredményeiből származó konkrét objektumokat keres

A objectCounter modul esetében tekintse meg a "rendszerkép" értékhez használt karakterláncot ($ {modules. objectCounter}), amely a IoT Edge modul fejlesztésének [oktatóanyagán](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) alapul. A Visual Studio Code automatikusan felismeri, hogy az objektum-számláló modul kódja az "src/Edge/Module/objectCounter" alatt található. 

Olvassa el [ezt](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) a szakaszt az útvonalak deklarálása a IoT Edge telepítési jegyzékben, majd a sablon JSON-fájljában lévő útvonalak vizsgálatához. Vegye figyelembe a következőket:

* A LVAToObjectCounter adott események küldésére szolgál egy adott végpontra a objectCounter modulban.
* A ObjectCounterToLVA egy eseményindító-eseménynek egy adott végpontra való küldésére szolgál (amely az IoT Hub forrás csomópontja) a lvaEdge modulban.
* a objectCounterToIoTHub hibakeresési eszközként használható, amely segítséget nyújt a objectCounter kimenetének megtekintéséhez az oktatóanyag futtatásakor.

> [!NOTE]
> Ellenőrizze a objectCounter modul kívánt tulajdonságait, amelynek beállításával a "Truck" címkével jelölt objektumokat keres, legalább 50%-os megbízhatósági szinttel.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése 

Az üzembe helyezési jegyzék meghatározza, hogy mely modulok legyenek üzembe helyezve egy peremhálózati eszközön, valamint a modulok konfigurációs beállításai. Kövesse az alábbi lépéseket egy ilyen jegyzékfájl létrehozásához a sablon fájlból, majd telepítse azt a peremhálózati eszközre.

A Visual Studio Code [használatával a következő utasításokat](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) követve bejelentkezhet a Docker-be és a IoT Edge-megoldás létrehozásával és leküldésével, de az src/Edge/Deployment. objectCounter. template. JSON használatával ehhez a lépéshez.

![IoT Edge-megoldás létrehozása és leküldése](./media/event-based-video-recording-tutorial/build-push.png)

Ezzel létrehozza a objectCounter modult az objektumok számlálásához, és leküldi a rendszerképet a Azure Container Registryba (ACR).

* Győződjön meg arról, hogy rendelkezik a környezeti változókkal CONTAINER_REGISTRY_USERNAME_myacr és CONTAINER_REGISTRY_PASSWORD_myacr a. env fájlban.

A fenti lépés létrehozza a IoT Edge üzembe helyezési jegyzéket az src/Edge/config/Deployment. objectCounter. amd64. JSON fájlban. Kattintson a jobb gombbal a fájlra, majd kattintson a "központi telepítés létrehozása egyetlen eszközhöz" elemre.

![Üzemelő példány létrehozása egyetlen eszközhöz](./media/quickstarts/create-deployment-single-device.png)

Ha ez az első oktatóanyag a IoT Edge Live Video Analytics szolgáltatással, a Visual Studio Code megkéri, hogy adja meg a IoTHub-kapcsolódási karakterláncot. A appSettings. JSON fájlból másolhatja.

A Visual Studio Code ezt követően egy IoT hub-eszköz kiválasztását kéri. Válassza ki IoT Edge eszközét (legyen "LVA-Sample-Device").

Ebben a szakaszban az Edge-modulok üzembe helyezése a IoT Edge eszközön megkezdődött.
Körülbelül 30 másodperc alatt frissítse az Azure-IoT Hub a Visual Studio Code bal alsó részén, és látnia kell, hogy 4 modul van telepítve (jegyezze fel a következő neveket: lvaEdge, rtspsim, yolov3 és objectCounter).

![4 modul üzembe helyezése](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Felkészülés a figyelési eseményekre

Az alábbi lépéseket követve megtekintheti az objektum számláló moduljának és a Live Video Analytics IoT Edge moduljának eseményeit:

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az Azure IoT Hub a bal alsó sarokban.
1. Bontsa ki az eszközök csomópontot.
1. Jobb gombbal a LVA-minta eszközre, és a **beépített események figyelésének megkezdése**lehetőséget választotta.

![A beépített esemény-végpont figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>A program futtatása

1. Visual Studio Code, navigáljon a "src/Cloud-to-Device-Console-app/Operations. JSON" elemre.

1. A csomópont GraphTopologySet alatt szerkessze a következőket:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Ezután a csomópontok GraphInstanceSet és GraphTopologyDelete, szerkesztés,

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
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
    1. Amikor lenyomja a "Enter" billentyűt a terminál ablakban, a rendszer a következő közvetlen metódus-hívásokat állítja be.
     * A fenti topologyUrl használó GraphTopologySet hívása.
     * A GraphInstanceSet hívása a következő törzs használatával.
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
     
1. A terminál ablakban lévő kimenet szünetelteti a folytatást a "nyomja meg az ENTER billentyűt" üzenettel. Ne nyomja meg az "Enter" értéket. A felkeresett közvetlen metódusok esetében a JSON-válaszok hasznos adatai láthatók.

1. Ha most átvált a kimeneti ablakra a Visual Studio Code-ban, akkor a IoT Hub küldött üzeneteket a IoT Edge modul Live Video Analytics szolgáltatásával küldi el a rendszer.

     * Ezeket az üzeneteket a következő szakaszban tárgyaljuk.
     
1. A Graph-példány továbbra is futni fog, és rögzíti a videót – az RTSP-szimulátor megtartja a forrás videóját. Tekintse át az üzeneteket az alábbi szakaszban leírtak szerint, majd a példány leállításához lépjen vissza a terminál ablakába, és nyomja le az ENTER billentyűt. A következő meghívások sorozata az erőforrások tisztítására készül:

     * GraphInstanceDeactivate hívása a Graph-példány inaktiválására
     * A példány törlésére irányuló GraphInstanceDelete-hívás
     * A topológia törlésére irányuló GraphTopologyDelete-hívás
     * A GraphTopologyList utolsó hívása, amely azt mutatja, hogy a lista már üres

## <a name="interpret-the-results"></a>Az eredmények értelmezése 

A Media Graph futtatásakor az élő videó Analytics IoT Edge modulban bizonyos diagnosztikai és műveleti eseményeket küld az IoT Edge hubhoz. Ezek az események a Visual Studio Code kimeneti ablakában látható üzenetek, amelyek "Body" szakaszt és egy "applicationProperties" szakaszt tartalmaznak. A következő fejezeteinek megismeréséhez lásd: [IoT hub üzenetek létrehozása és olvasása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Az alábbi üzenetekben az alkalmazás tulajdonságait és a törzs tartalmát az élő videó elemzési modulja határozza meg.

## <a name="diagnostic-events"></a>Diagnosztikai események

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény 

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az RTSP szimulátor-tárolón futó RTSP-kiszolgálóhoz. Ha ez sikeres, a rendszer kinyomtatja ezt az eseményt. Vegye figyelembe, hogy az esemény típusa Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Az üzenet egy diagnosztikai esemény, a MediaSessionEstablished azt jelzi, hogy az RTSP-forrás csomópont (a tárgy) képes volt kapcsolatot létesíteni az RTSP-szimulátorral, és elkezdi fogadni a (szimulált) élő hírcsatornát.

* A "tárgy" a applicationProperties hivatkozik a Graph-topológia azon csomópontjára, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.

* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy diagnosztikai esemény.

* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett, azaz az az idő, amikor a forgalmi videó (MKV-fájl) elindult a modulba élő streamként.

* a "Body" a diagnosztikai eseménnyel kapcsolatos adatokat tartalmaz, amelyek ebben az esetben a [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatai.


## <a name="operational-events"></a>Működési események

Miután a Media Graph egy darabig fut, végül egy eseményt fog kapni az objektum-számláló modulból. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

A applicationProperties tartalmazza a eventTime, amely azt az időpontot mutatja, amikor az objektum számláló modulja megfigyelte, hogy az YOLO v3 moduljának eredményei tartalmazzák a kamatos objektumokat (teherautók).

Előfordulhat, hogy a videóban több ilyen esemény jelenik meg, ahogy más kamionok is észlelhetők.

### <a name="recordingstarted-event"></a>RecordingStarted esemény

Szinte azonnal, miután az objektum számlálója elküldi az eseményt, a Microsoft. Media. Graph. Operational. RecordingStarted típusú eseményt fogja látni.

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

A "tárgy" a applicationProperties hivatkozik a diagramban található, az üzenetet létrehozó objektum fogadó csomópontjára. A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van. Jegyezze fel ezt az értéket.

### <a name="recordingavailable-event"></a>RecordingAvailable esemény

Amikor az eszköz befogadó csomópontja videót töltött fel az objektumra, a Microsoft. Media. Graph. Operational. RecordingAvailable típusú eseményt bocsát ki.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Ez az esemény azt jelzi, hogy az eszköznek elegendő adattal kell megírnia, hogy a játékosok/ügyfelek a videó lejátszását kezdeményezzenek. A "tárgy" a applicationProperties hivatkozik a gráf AssetSink csomópontjára, amely létrehozta ezt az üzenetet. A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van.

### <a name="recordingstopped-event"></a>RecordingStopped esemény

Ha megvizsgálja az aktiválási beállításokat (maximumActivationTime) a [topológia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)Signal Gate processzor-csomópontja számára, látni fogja, hogy a kapu egy, a videó elküldése után, 30 másodperces elküldés után van beállítva. A RecordingStarted esemény után nagyjából 30 másodperccel a Microsoft. Media. Graph. Operational. RecordingStopped típusú eseménynek kell megjelennie, amely azt jelzi, hogy az eszköz fogadó csomópontja leállította a videó rögzítését az eszközre.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Ez az esemény azt jelzi, hogy a rögzítés leállt. A "tárgy" a applicationProperties hivatkozik a gráf AssetSink csomópontjára, amely létrehozta ezt az üzenetet. A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van.

## <a name="media-services-asset"></a>Media Services eszköz  

Megvizsgálhatja a gráf által létrehozott Media Services adategységet, ha bejelentkezik a Azure Portalba, és megtekinti a videót.

1. Nyissa meg a webböngészőt, és lépjen a [Azure Portal](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.
1. Keresse meg Media Services-fiókját az előfizetésében található erőforrások között, és nyissa meg a fiók panelt
1. Kattintson az eszközök elemre a Media Services listájában.

    ![Objektumok](./media/continuous-video-recording-tutorial/assets.png)
1. A sampleAssetFromEVR-LVAEdge-{DateTime} nevű adategységet megtalálja, ez a név a RecordingStarted esemény outputLocation tulajdonságában szerepel. A topológia assetNamePattern határozza meg a név létrejöttének módját.
1. Kattintson az eszközre.
1. Az eszköz adatai lapon kattintson az **új létrehozása** a streaming URL-címe szövegmezőbe.

    ![Új eszköz](./media/continuous-video-recording-tutorial/new-asset.png)

1. A megnyíló varázslóban fogadja el az alapértelmezett beállításokat, és nyomja le az "add" (Hozzáadás) lehetőséget. További információ: [Videolejátszás](video-playback-concept.md).

    > [!TIP]
    > Ellenőrizze, hogy [fut-e a folyamatos átviteli végpont](../latest/streaming-endpoint-concept.md).
1. A lejátszónak be kell töltenie a videót, és meg kell tudnia **játszani a lejátszást** .

> [!NOTE]
> Mivel a videó forrása egy kamera-hírcsatornát szimuláló tároló volt, a videó időbélyegei a Graph-példány aktiválásakor és az inaktiválásakor kapcsolódnak. Ha a [Többnapos lejátszási](playback-multi-day-recordings-tutorial.md) útmutatóban beépített lejátszási vezérlőket használja, a képernyőn megjelenő videóban láthatja az időbélyegeket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi oktatóanyagot, tartsa be a következőt: a létrehozott erőforrásokhoz. Ellenkező esetben lépjen a Azure Portal, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben az oktatóanyagot futtatta, és törölje az erőforráscsoportot.

## <a name="next-steps"></a>További lépések

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az ONVIF-kompatibilis [termékek lapon](https://www.onvif.org/conformant-products/) megkeresheti az RTSP-támogatással rendelkező IP-kamerákat a G, S vagy T profiloknak megfelelő eszközök keresésével.
* AMD64 vagy x64 Linux rendszerű eszköz használata (Azure Linux rendszerű virtuális gép használata). Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. Kövesse az [Azure IoT Edge Runtime Linux rendszeren való telepítésének](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) utasításait, majd kövesse az [első IoT Edge modul üzembe helyezése a virtuális Linux-eszközökhöz című](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) rövid útmutatót az eszköz Azure-IoT hub való regisztrálásához.
