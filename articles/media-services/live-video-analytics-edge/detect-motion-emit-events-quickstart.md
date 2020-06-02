---
title: A Motion és a kibocsátás eseményeinek észlelése – Azure
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket a mozgások észlelésére és az események kibocsátására a közvetlen metódusok programozott módon történő meghívásával.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262025"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Rövid útmutató: a mozgás észlelése és az események kibocsátása

Ez a rövid útmutató végigvezeti azon lépéseken, amelyekkel megkezdheti az élő videók elemzését IoT Edgeon. Egy Azure-beli virtuális gépet használ IoT Edge eszközként és szimulált élő videó streamként. A telepítés lépéseinek elvégzése után egy szimulált élő videó streamet futtathat egy olyan adathordozó-gráfon keresztül, amely az adott stream bármely mozgását észleli és jelenti. Az alábbi ábra az adott adathordozó-gráf grafikus ábrázolását mutatja be.

![Élő videó-elemzés a mozgásészlelés alapján](./media/analyze-live-video/motion-detection.png) 

Ez a cikk a C# nyelven írt [mintakód](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) alapján készült.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) a gépen a következő kiterjesztésekkel:
    1. [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* A [.net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) telepítve van a rendszeren

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

    Ha a parancsfájl sikeresen befejeződik, az előfizetésben említett összes erőforrást látnia kell.

1. A szkript befejeződése után kattintson a kapcsos zárójelre a mappa struktúrájának megjelenítéséhez. A ~/clouddrive/LVA-Sample könyvtár alatt létrehozott néhány fájl jelenik meg. Ebben a rövid útmutatóban a következők szerepelnek:

     * ~/clouddrive/LVA-Sample/Edge-Deployment/.env – olyan tulajdonságokat tartalmaz, amelyeket a Visual Studio Code használ a modulok peremhálózati eszközre való telepítéséhez
     * ~/clouddrive/LVA-Sample/AppSetting.JSON – Visual Studio Code által használt mintakód futtatásához
     
Ezekre szüksége lesz a Visual Studio Code-ban lévő fájlok frissítéséhez a gyors útmutatóban. Előfordulhat, hogy most egy helyi fájlba szeretné másolni őket.


 ![Alkalmazásbeállítások](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

1. A tárház klónozása innen https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Indítsa el a Visual Studio Code-ot, és nyissa meg azt a mappát, ahová a tárház le lett töltve.
1. A Visual Studio Code-ban keresse meg az "src/Cloud-to-Device-Console-app" mappát, és hozzon létre egy "appSettings. JSON" nevű fájlt. Ez a fájl a program futtatásához szükséges beállításokat fogja tartalmazni.
1. Másolja a tartalmat az előző szakaszban létrehozott ~/clouddrive/LVA-Sample/appSettings.JSON-fájlból (lásd: 5. lépés)

    A szövegnek a következőhöz hasonlóan kell kinéznie:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Ezután tallózással keresse meg az "src/Edge" mappát, és hozzon létre egy ". env" nevű fájlt.
1. Másolja a tartalmat a "/clouddrive/LVA-Sample/Edge-Deployment/.env fájlból. A szövegnek a következőhöz hasonlóan kell kinéznie:

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

1. A Visual Studio Code-ban keresse meg az "src/Edge" parancsot. Ekkor megjelenik a létrehozott. env fájl, amely néhány központi telepítési sablon fájlját tartalmazza.

    A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik néhány helyőrző értékkel. A. env fájl ezen változók értékeit tartalmazhatja.
1. Ezután tallózással keresse meg a "src/Cloud-to-Device-Console-app" mappát. Itt láthatja a létrehozott appSettings. JSON fájlt, valamint néhány további fájlt is:

    * C2D-Console-app. csproj – a Project fájl a Visual Studio Code-hoz.
    * Operations. JSON – ez a fájl felsorolja azokat a különböző műveleteket, amelyekre a programot futtatni szeretné.
    * Program.cs – a minta programkódja, amely a következő műveleteket végzi el:
    
        * Az alkalmazás beállításainak betöltése
        * Az élő videó Analytics IoT Edge modulban elérhető közvetlen metódusokat hívja meg. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md) meghívásával 
        * Szünetelteti a program kimenetének vizsgálatát a terminál ablakban, valamint a kimenet ablakban a modul által generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése

Az üzembe helyezési jegyzék meghatározza, hogy mely modulok legyenek üzembe helyezve egy peremhálózati eszközön, valamint a modulok konfigurációs beállításai. Kövesse az alábbi lépéseket egy ilyen jegyzékfájl létrehozásához a sablon fájlból, majd telepítse azt a peremhálózati eszközre.

1. A Visual Studio Code megnyitása
1. Állítsa be a IoTHub-kapcsolódási karakterláncot úgy, hogy az AZURE IOT HUB panel melletti "További műveletek" ikonra kattint a bal alsó sarokban. A karakterláncot a src/Cloud-to-Device-Console-app/appSettings. JSON fájlból másolhatja. 

    ![IOT-kapcsolatok karakterláncának beállítása](./media/quickstarts/set-iotconnection-string.png)
1. Ezután kattintson a jobb gombbal az "src/Edge/Deployment. template. JSON" fájlra, és kattintson a "IoT Edge üzembe helyezési jegyzék előállítása" elemre.
    ![IoT Edge üzembe helyezési jegyzék előállítása](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Ehhez létre kell hoznia egy jegyzékfájlt az src/Edge/config mappában, a "Deployment. amd64. JSON" nevű fájlban.
1. Kattintson a jobb gombbal az "src/Edge/config/Deployment. amd64. JSON" elemre, majd kattintson a "központi telepítés létrehozása egyetlen eszközhöz" lehetőségre, és válassza ki a peremhálózati eszköz nevét.

    ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/quickstarts/create-deployment-single-device.png)
1. Ekkor a rendszer megkéri, hogy "válasszon ki egy IoT Hub eszközt". Válassza ki a LVA-Sample-Device elemet a legördülő listából.
1. Körülbelül 30 másodperc alatt frissítse az Azure IOT hubot a bal alsó részen, és látnia kell, hogy a peremhálózati eszközön telepítve vannak a következő modulok:

    * Élő videó-elemzés a IoT Edgeon (modul neve: "lvaEdge")
    * RTSP-szimulátor (modul neve: "rtspsim")

Az RTSP szimulátor modul szimulál egy élő videó streamet egy olyan tárolt videofájl használatával, amelyet a rendszer a peremhálózati eszközre másolt, amikor futtatta az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). Ebben a szakaszban már telepítve vannak a modulok, de nincsenek aktív adathordozó-diagramok.

## <a name="prepare-for-monitoring-events"></a>Felkészülés a figyelési eseményekre

A Live Video Analytics IoT Edge modulján fogja használni a mozgást a beérkező élő videó streamben, és elküldheti az eseményeket a IoT Hubba. Az események megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az Azure IoT Hub a bal alsó sarokban.
1. Bontsa ki az eszközök csomópontot.
1. A jobb gombbal a LVA-minta-eszközre kattint, és a "beépített figyelési esemény figyelése" lehetőséget választotta.

    ![A beépített esemény-végpont figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>A minta program futtatása

A mintakód futtatásához kövesse az alábbi lépéseket.
1. A Visual Studio Code-ban navigáljon a "src/Cloud-to-Device-Console-app/Operations. JSON" elemre.
1. A csomópont GraphTopologySet alatt ügyeljen a következőkre:

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Ezután a csomópontok GraphInstanceSet és GraphTopologyDelete területen győződjön meg arról, hogy a topologyName értéke megegyezik a fenti Graph-topológia "Name" tulajdonságának értékével:

    `"topologyName" : "MotionDetection"`
    
1. Indítsa el a hibakeresési munkamenetet (nyomja meg az F5 billentyűt). Ekkor néhány üzenet jelenik meg a terminál ablakban.
1. A Operations. JSON elindul a GraphTopologyList és a GraphInstanceList hívásával. Ha az előző rövid útmutatók után törölte az erőforrásokat, az üres listát ad vissza, majd szünetelteti az ENTER billentyűt.

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
     
     * A Graph-példány elindítására irányuló GraphInstanceActivate-hívás, valamint a videó folyamatának elindítása.
     * Egy második hívás a GraphInstanceList, amely azt mutatja, hogy a Graph-példány valóban fut állapotban van.
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

* Az üzenet egy diagnosztikai esemény, a MediaSessionEstablished azt jelzi, hogy az RTSP-forrás csomópont (a tárgy) képes volt kapcsolatot létesíteni az RTSP-szimulátorral, és elkezdi fogadni a (szimulált) élő hírcsatornát.
* A "tárgy" a applicationProperties hivatkozik a Graph-topológia azon csomópontjára, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy diagnosztikai esemény.
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* a "Body" a diagnosztikai eseménnyel kapcsolatos adatokat tartalmaz, amelyek ebben az esetben a [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatai.


## <a name="motion-detection-event"></a>Mozgásészlelési esemény

Ha a rendszer mozgást észlel, az élő videó Analytics peremhálózati modulja egy következtetési eseményt küld. A típus "Motion" (mozgás) értékre van állítva, amely jelzi, hogy a mozgásészlelési processzor eredménye, a eventTime pedig jelzi, hogy milyen időpontban (UTC) történt a mozgás. Az alábbiakban egy példa látható:

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

* a "tárgy" a applicationProperties azon a csomóponton hivatkozik, amelyből az üzenet létrejött. Ebben az esetben az üzenet a mozgásészlelési processzor csomópontból származik.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy elemzési esemény.
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
a "Body" az elemzési eseményre vonatkozó információkat tartalmaz. Ebben az esetben az esemény egy következtetési esemény, ezért a törzs "Timestamp" és "következtetéseket" tartalmaz.
* a "következtetések" érték azt jelzi, hogy a "type" a "Motion", és további információkat tartalmaz a "Motion" eseményről.
* a "Box" szakasz a mozgó objektum körüli határoló mező koordinátáit tartalmazza. Az értékeket a videó szélessége és magassága képpontban (például a 1920 szélessége és a 1080-es magasság.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi rövid útmutatót, tartsa be a következőt: a létrehozott erőforrásokhoz. Ellenkező esetben lépjen a Azure Portalra, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben futtatta ezt a rövid útmutatót, és törölje az összes erőforrást.

## <a name="next-steps"></a>További lépések

Futtassa a többi rövid útmutatót, például egy objektum észlelése élő videó-hírcsatornában.        
