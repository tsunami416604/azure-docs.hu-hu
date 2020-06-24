---
title: A Motion és a kibocsátás eseményeinek észlelése – Azure
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket a mozgások észlelésére és az események kibocsátására a közvetlen metódusok programozott módon történő meghívásával.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 69486515125c624b3ef5d44aba6e6d8f7694a3cc
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84816704"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Rövid útmutató: a mozgás észlelése és az események kibocsátása

Ez a rövid útmutató végigvezeti azon lépéseken, amelyekkel megkezdheti az élő videók elemzését IoT Edgeon. Azure-beli virtuális gépet használ IoT Edge-eszközként és szimulált élő videóstreamként. A telepítés lépéseinek elvégzése után egy szimulált élő videó streamet futtathat egy olyan adathordozó-gráfon keresztül, amely az adott stream bármely mozgását észleli és jelenti. Az alábbi ábrán az adott adathordozó-gráf grafikus ábrázolása látható.

![Élő videó-elemzés a mozgásészlelés alapján](./media/analyze-live-video/motion-detection.png) 

Ez a cikk a C# nyelven írt [mintakód](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) alapján készült.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , ha még nem rendelkezik ilyennel.
* [Visual Studio Code](https://code.visualstudio.com/) a következő kiterjesztésekkel:
    * [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!TIP]
> Előfordulhat, hogy a rendszer a Docker telepítésére kéri az Azure IoT-eszközök bővítményének telepítésekor. A promptot figyelmen kívül hagyhatja.

## <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása

Az oktatóanyaghoz a következő Azure-erőforrások szükségesek:

* IoT Hub
* Tárfiók
* Azure Media Services fiók
* Linux virtuális gép az Azure-ban, [IoT Edge futtatókörnyezettel](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) telepítve

Ebben a rövid útmutatóban azt javasoljuk, hogy az Azure-előfizetésében lévő szükséges erőforrások üzembe helyezéséhez használja az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg [Azure Cloud Shell](https://shell.azure.com).
1. Ha első alkalommal használja a Cloud Shell, a rendszer kérni fogja, hogy válasszon egy előfizetést egy Storage-fiók és egy Microsoft Azure-fájlmegosztás létrehozásához. Válassza a **tároló létrehozása** lehetőséget a Cloud Shell munkamenet-információhoz tartozó Storage-fiók létrehozásához. Ez a Storage-fiók elkülönül a parancsfájl által a Azure Media Services-fiókkal való használatra létrehozott fióktól.
1. A Cloud Shell ablak bal oldalán lévő legördülő menüben válassza a **bash** lehetőséget a környezetében.

    ![Környezeti választó](./media/quickstarts/env-selector.png)

1. Futtassa a következő parancsot.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Ha a parancsfájl sikeresen befejeződik, az előfizetésben szereplő összes szükséges erőforrást látnia kell.

1. A szkript befejeződése után válassza ki a kapcsos zárójeleket, hogy elérhetővé tegye a mappastruktúrát. A *~/clouddrive/LVA-Sample* könyvtárban néhány fájl jelenik meg. Ebben a rövid útmutatóban a következők szerepelnek:

     * ***~/clouddrive/LVA-Sample/Edge-Deployment/.env*** – ez a fájl olyan tulajdonságokat tartalmaz, amelyeket a Visual Studio Code használ a modulok peremhálózati eszközre való telepítéséhez.
     * ***~/clouddrive/lva-sample/appsetting.json*** -Visual Studio Code ezt a fájlt használja a mintakód futtatásához.
     
Ezekre a fájlokra szüksége lesz, amikor a következő szakaszban a Visual Studio Code-ban beállítja a fejlesztési környezetet. Előfordulhat, hogy most egy helyi fájlba szeretné másolni őket.

 ![Alkalmazásbeállítások](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

1. A tárház klónozása erről a helyről: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. A Visual Studio Code-ban nyissa meg azt a mappát, ahol a tárház le lett töltve.
1. A Visual Studio Code-ban nyissa meg a *src/Cloud-to-Device-Console-app* mappát. Ott hozzon létre egy fájlt, és nevezze el *appsettings.js*. Ez a fájl a program futtatásához szükséges beállításokat fogja tartalmazni.
1. Másolja a tartalmat a *~/clouddrive/lva-sample/appsettings.jsról a* rövid útmutatóban korábban létrehozott fájlra.

    A szövegnek a következő kimenethez hasonlóan kell kinéznie.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Nyissa meg az *src/Edge* mappát, és hozzon létre egy *. env*nevű fájlt.
1. Másolja a */clouddrive/LVA-Sample/Edge-Deployment/.env* fájl tartalmát. A szövegnek a következő kódhoz hasonlóan kell kinéznie.

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
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>A mintaadatok vizsgálata

1. A Visual Studio Code-ban lépjen az *src/Edge*elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik, ahol egyes tulajdonságok esetében változókat kell használni. A *. env* fájl tartalmazza a változók értékeit.
1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a fájl *appsettings.jsét* és néhány további fájlt:

    * ***C2D-Console-app. csproj*** – a Project fájl a Visual Studio Code-hoz.
    * ***operations.json*** – a program futtatásához használni kívánt műveletek listája.
    * ***Program.cs*** – a minta programkódja. Ez a kód:
    
      * Betölti az alkalmazás beállításait.
      * Az élő videó Analytics IoT Edge modulban elérhető közvetlen metódusokat hívja meg. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md)meghívásával.
      * Felfüggeszti a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
      * Közvetlen metódusokat hív meg az erőforrások törléséhez.   

## <a name="generate-and-deploy-the-deployment-manifest"></a>Az üzembe helyezési jegyzék előállítása és üzembe helyezése

Az üzembe helyezési jegyzék meghatározza, hogy a rendszer mely modulokat telepíti egy peremhálózati eszközre. Emellett meghatározza a modulok konfigurációs beállításait is. 

Kövesse az alábbi lépéseket a jegyzékfájlnak a sablonból való létrehozásához, majd a peremhálózati eszközre történő telepítéséhez.

1. Nyissa meg a Visual Studio Code-ot.
1. Az **Azure IOT hub** panel mellett válassza a **További műveletek** ikont a IoT hub kapcsolódási karakterlánc beállításához. A karakterláncot a *src/Cloud-to-Device-Console-app/appsettings.js* fájlból másolhatja. 

    ![IOT-kapcsolatok karakterláncának beállítása](./media/quickstarts/set-iotconnection-string.png)

1. Kattintson a jobb gombbal **az src/Edge/deployment.template.js** elemre, és válassza a **IoT Edge üzembe helyezési jegyzék előállítása**lehetőséget.

    ![A IoT Edge üzembe helyezési jegyzékének előállítása](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Ehhez a művelethez létre kell hoznia egy *deployment.amd64.js* nevű jegyzékfájlt az *src/Edge/config* mappában.
1. Kattintson a jobb gombbal az **src/Edge/config/deployment.amd64.js**elemre, válassza a **központi telepítés létrehozása egyetlen eszközhöz**lehetőséget, majd válassza ki a peremhálózati eszköz nevét.

    ![Központi telepítés létrehozása egyetlen eszközhöz](./media/quickstarts/create-deployment-single-device.png)

1. Amikor a rendszer rákérdez a IoT Hub eszköz kiválasztására, válassza a **LVA-minta-eszköz** lehetőséget a legördülő menüből.
1. Körülbelül 30 másodperc elteltével frissítse az Azure IoT Hubt az ablak bal alsó sarkában. A peremhálózati eszköz mostantól a következő központilag telepített modulokat mutatja:

    * Élő videó-elemzés a IoT Edgeon (modul neve `lvaEdge` )
    * Valós idejű Streaming Protocol (RTSP) szimulátor (modul neve `rtspsim` )

Az RTSP-szimulátor modul egy élő videó streamet szimulál egy olyan videofájl használatával, amelyet a peremhálózati eszközre másoltak, amikor futtatta az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

Ebben a szakaszban a modulok üzembe vannak helyezve, de nincsenek aktív adathordozó-diagramok.

## <a name="prepare-to-monitor-events"></a>Felkészülés az események figyelésére

A Live Video Analytics IoT Edge modulján a beérkező élő videó streamben való mozgás észlelésére és az események IoT Hubba való küldésére használható. Az események megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az Azure IoT Hub a bal alsó sarokban.
1. Bontsa ki az **eszközök** csomópontot.
1. Kattintson a jobb gombbal a **LVA-Sample-Device** elemre, és válassza a **figyelés beépített esemény végpontja**lehetőséget.

    ![Beépített esemény-végpont figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>A minta program futtatása

A mintakód futtatásához kövesse az alábbi lépéseket:

1. A Visual Studio Code-ban lépjen a *src/Cloud-to-Device-Console-app/operations.js*elemre.
1. A **GraphTopologySet** csomóponton ellenőrizze, hogy a következő érték jelenik-e meg:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. A **GraphInstanceSet** és a **GraphTopologyDelete** csomóponton győződjön meg arról, hogy a `topologyName` `name` Graph-topológia tulajdonságának értéke megegyezik a következővel:

    `"topologyName" : "MotionDetection"`
    
1. Indítsa el a hibakeresési munkamenetet az F5 billentyű kiválasztásával. A **terminál** ablakban néhány üzenet jelenik meg.
1. A fájl *operations.js* a és a hívásával elindul `GraphTopologyList` `GraphInstanceList` . Ha az előző rövid útmutatók befejezése után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. A folytatáshoz válassza az ENTER billentyűt.

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
           "name": "Sample-Graph",
           "properties": {
             "topologyName": "MotionDetection",
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
1. A **terminál** ablakban lévő kimenet a következő időpontban szünetel: `Press Enter to continue` . Ne jelölje be az ENTER billentyűt. Görgessen felfelé, és tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a **kimeneti** ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulban az IoT hub-ra küld üzenetet. A rövid útmutató következő szakasza ezeket az üzeneteket tárgyalja.
1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a **terminál** ablakába, és válassza az ENTER billentyűt. 

    A hívások következő sorozata törli az erőforrásokat:
     * A Graph- `GraphInstanceDeactivate` példány inaktiválására irányuló hívás.
     * A `GraphInstanceDelete` példány törlésére irányuló hívás.
     * A `GraphTopologyDelete` topológia törlésére irányuló hívás.
     * Egy végső hívás, `GraphTopologyList` amely azt jelzi, hogy a lista üres.

## <a name="interpret-results"></a>Eredmények értelmezése

A Media Graph futtatásakor a mozgásérzékelő processzor csomópontjának eredményei áthaladnak a IoT Hub fogadó csomóponton az IoT hubhoz. A Visual Studio Code **kimenet** ablakában látható üzenetek egy `body` szakaszt és egy `applicationProperties` szakaszt tartalmaznak. További információ: [IoT hub üzenetek létrehozása és olvasása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

A következő üzenetekben az élő videó elemzési modulja az alkalmazás tulajdonságait és a törzs tartalmát határozza meg.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a kapcsolat sikeres, a következő esemény lesz kinyomtatva.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

Az előző kimenetben: 
* Az üzenet egy diagnosztikai esemény `MediaSessionEstablished` . Azt jelzi, hogy az RTSP-szimulátorhoz csatlakoztatott RTSP-forrás csomópont (a tárgy) megkezdte a (szimulált) élő adatcsatorna fogadását.
* A alkalmazásban a a `applicationProperties` `subject` Graph-topológiában található csomópontra hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* Az `eventTime` érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* A `body` szakasz a diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.


### <a name="motiondetection-event"></a>MotionDetection esemény

Ha a rendszer mozgást észlel, az élő videó Analytics IoT Edge modulban egy következtetési eseményt küld. A értéke úgy `type` van beállítva, hogy `motion` jelezze, hogy a mozgásészlelési processzor eredménye. Az `eventTime` érték azt jelzi, hogy mikor (UTC) történt a mozgás. 

Íme egy példa erre az üzenetre:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

Ebben a példában: 

* A alkalmazásban a a `applicationProperties` `subject` Media Graph azon csomópontjára hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet a mozgásészlelési processzor csomópontból származik.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy elemzési esemény.
* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett.
* Az `body` érték az elemzési eseményre vonatkozó információ. Ebben az esetben az esemény egy következtetési esemény, így a törzs tartalmaz és az adattartalmakat `timestamp` `inferences` .
* Az `inferences` érték azt jelzi, hogy a a `type` `motion` . További információkat tartalmaz az `motion` eseményről.
* A `box` szakasz a mozgó objektum körüli határoló mező koordinátáit tartalmazza. Az értékeket a videó szélessége és magassága (képpontban) normalizálja. Például a szélesség 1920, a magasság pedig 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha szeretné kipróbálni a többi rövid útmutatót, akkor meg kell őriznie a létrehozott erőforrásokat. Ellenkező esetben a Azure Portal nyissa meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben a rövid útmutatót futtatta, majd törölje az összes erőforrást.

## <a name="next-steps"></a>További lépések

Futtassa a többi rövid útmutatót, például egy objektum észlelése élő videó-hírcsatornában.        
