---
title: Élő videó elemzése a IoT Edge és az Azure élő videó-elemzésével Custom Vision
description: Megtudhatja, hogyan használhatja a Custom Visiont olyan tárolós modell kiépítéséhez, amely képes észlelni a játékok teherautóját, és az élő videó-elemzések AI-bővíthetőségi funkcióját használja a IoT Edge (LVA) szolgáltatásban a modellnek az élő videó streamből való észlelésére.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 97aa514e2347b3398aab6293d8543afcdc239bdb
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776595"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Oktatóanyag: élő videó elemzése élő videó-elemzéssel IoT Edge és az Azure-on Custom Vision

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a [Custom Visiont](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) egy olyan tárolós modell kiépítéséhez, amely képes észlelni egy játék-teherautót, és az élő videó-IoT Edge elemzés [AI-bővíthetőségi funkciójának](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) használatával üzembe helyezi a modelleket az élő videó streamből való észleléshez.

Bemutatjuk, hogyan egyesítheti a Custom Vision hatékonyságát, amely lehetővé teszi, hogy bárki felépítse és betanítsa a számítógép-jövőkép modelljét azáltal, hogy egyszerűen feltölti és címkézi néhány képet anélkül, hogy az adatelemzéssel, a ML-vel vagy a mesterséges intelligenciával együtt könnyedén üzembe helyezhet egy egyéni modellt, és elemezni tudja a szimulált élő videó-hírcsatornát. Ez az oktatóanyag egy Azure-beli virtuális gépet használ IoT Edge eszközként, amely a C# nyelven írt mintakód alapján épül fel, és az [események észlelésére és a kibocsátások](detect-motion-emit-events-quickstart.md) kiküldésére szolgál.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Állítsa be a megfelelő erőforrásokat.
> * Hozzon létre egy Custom Vision modellt a felhőben a játékszer-teherautók észleléséhez és a peremhálózati üzembe helyezéséhez
> * Media Graph létrehozása és üzembe helyezése http-bővítménnyel egyéni látási modellhez
> * Futtassa a kódot.
> * Vizsgálja meg és értelmezze az eredményeket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás  

Javasoljuk, hogy a Kezdés előtt olvassa el a következő cikkeket: 

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Az Azure Custom Vision áttekintése](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph alapfogalmai](media-graph-concept.md)
* [Live Video Analytics videófelvétel nélkül](analyze-live-video-concept.md)
* [Élő videó-elemzés futtatása saját modellel](use-your-model-quickstart.md)
* [Oktatóanyag: IoT Edge modul fejlesztése](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Az üzembe helyezés szerkesztése. * .template.jsbekapcsolva](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag előfeltételei a következők:

* [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) és [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) -bővítménnyel.

    > [!TIP]
    > Előfordulhat, hogy a rendszer a Docker telepítését kéri. Hagyja figyelmen kívül ezt a kérést.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) a fejlesztői gépen.
* Győződjön meg arról, hogy rendelkezik az alábbiakkal:
    
    * [Azure-erőforrások beállítása](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [A fejlesztési környezet beállítása](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>A minta videó áttekintése

Ez az oktatóanyag egy, az élő stream szimulálása érdekében egy [Toy Car-következtetést tartalmazó videót](https://lvamedia.blob.core.windows.net/public/t2.mkv/) használ. Megvizsgálhatja a videót egy alkalmazással, például a [VLC Media Player](https://www.videolan.org/vlc/)használatával. Válassza a CTRL + N billentyűkombinációt, majd illesszen be egy hivatkozást a [Toy Car következtetési videóra](https://lvamedia.blob.core.windows.net/public/t2.mkv) a lejátszás megkezdéséhez. A videó megtekintéséhez vegye figyelembe, hogy a videóban megjelenik a 36-Second marker an Toy Truck. Az egyéni modell ki lett tanítva, hogy észlelje az adott Toy Truck-t. Ebben az oktatóanyagban élő videó-elemzéseket fog használni a IoT Edgeon az ilyen játékszer-teherautók észleléséhez és a kapcsolódó következtetési események közzétételéhez IoT Edge hubhoz.

## <a name="overview"></a>Áttekintés

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Custom Vision áttekintése":::

Ez az ábra az oktatóanyagban szereplő jelek folyamatát mutatja be. Az [Edge-modulok](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) egy valós idejű Streaming Protocol-(RTSP-) kiszolgálót üzemeltető IP-kamerát szimulálnak. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a képkockákat a [frame rate szűrő processzor](media-graph-concept.md#frame-rate-filter-processor) -csomópontjára küldi. Ez a processzor korlátozza a [http-bővítmény processzor](media-graph-concept.md#http-extension-processor) -csomópontját elérő video stream képkockasebességét.
A HTTP-bővítmény csomópont egy proxy szerepét játssza le. A képkockákat a megadott képtípusra konvertálja. Ezt követően továbbítja a képet a REST-ben egy másik Edge-modulba, amely egy HTTP-végpont mögötti AI-modellt futtat. Ebben a példában az Edge-modul a Custom Vision használatával létrehozott Toy Truck detektor modell. A HTTP-bővítmény processzor-csomópontja összegyűjti az észlelés eredményeit, és közzéteszi az eseményeket a [IoT hub](media-graph-concept.md#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket [IoT Edge hubhoz](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Custom Vision Toy Detection-modell létrehozása és üzembe helyezése 

Ahogy a neve Custom Vision sugallja, használhatja azt a saját egyéni objektum-Kiderítő vagy a felhőben való besorolás létrehozásához. Egy egyszerű, könnyen használható és intuitív kezelőfelületet biztosít a felhőben vagy az Edge-tárolón keresztül üzembe helyezhető egyéni látási modellek létrehozásához. 

A Toy Truck-detektor létrehozásához azt javasoljuk, hogy kövesse ezt az egyéni jövőképet a web Portal [gyors üzembe helyezési cikke](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector) segítségével.

További megjegyzések:
 
* Ebben az oktatóanyagban ne használja a gyors üzembe helyezési cikk [előfeltételeinek részeként](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#prerequisites)megadott mintaképeket. Ehelyett kihasználunk egy bizonyos képérzékelőt a Toy-detektor egyéni vizualizációs modelljének létrehozásához, javasoljuk, hogy [ezeket a képeket](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) akkor használja, ha a gyors útmutatóban megkéri a [betanítási lemezképek kiválasztását](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#choose-training-images) .
* A gyors üzembe helyezési kép szakaszban győződjön meg arról, hogy a képen látható Truck a "Delivery Truck" címkével van megjelölve.

Ha elkészült, a modell készen áll az Ön megelégedésére, a teljesítmény lapon lévő Exportálás gombra kattintva exportálhatja azt egy Docker-tárolóba. Győződjön meg arról, hogy a Linux lehetőséget választja a tároló platform típusaként. Ez az a platform, amelyen a tároló futni fog. A tárolót letöltő gép lehet Windows vagy Linux. Az alábbi utasítások a Windows rendszerű gépre letöltött tároló fájlon alapulnak.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Custom Vision áttekintése"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Ez a parancs teszteli a tárolót a helyi gépen, és ha a rendszerkép ugyanazzal a szállítóval rendelkezik, mint a modell betanítása, akkor a kimenetnek a következőhöz hasonlónak kell lennie, ami azt sugallja, hogy a szállító teherautó 90,12%-os valószínűséggel lett észlelve.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>A mintaadatok vizsgálata

1. A VSCode-ban keresse meg az "src/Edge" parancsot. Ekkor megjelenik a létrehozott. env fájl, amely néhány központi telepítési sablon fájlját tartalmazza.

    A központi telepítési sablon a peremhálózati eszköz központi telepítési jegyzékére hivatkozik néhány helyőrző értékkel. A. env fájl ezen változók értékeit tartalmazhatja.
1. Ezután tallózással keresse meg a "src/Cloud-to-Device-Console-app" mappát. Itt láthatja a appsettings.jsa létrehozott fájlon, néhány további fájllal együtt:

    * C2D-Console-app. csproj – ez a VSCode tartozó projektfájl.
    * operations.json – ez a fájl felsorolja a program által futtatandó különböző műveleteket.
    * Program.cs – ez a mintakód, amely a következő műveleteket végzi el:

        * Betölti az alkalmazás beállításait.
        * Hívja meg az élő videó-elemzést IoT Edge modul közvetlen metódusait a topológia létrehozásához, a gráf létrehozásához és a gráf aktiválásához.
        * Szünetelteti a diagram kimenetének vizsgálatát a terminál ablakban, valamint az IoT hub számára a kimeneti ablakban elküldett eseményeket.
        * Inaktiválja a Graph-példányt, törölje a Graph-példányt, és törölje a Graph-topológiát.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>Az üzembe helyezési jegyzék előállítása és üzembe helyezése

1. A VSCode-ben navigáljon a "src/Cloud-to-Device-Console-app/operations.json" elemre.

1. A GraphTopologySet területen győződjön meg arról, hogy a következők teljesülnek:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. A GraphInstanceSet területen győződjön meg a következőket: 
    1. "topologyName" : "InferencingWithHttpExtension"
    1. Adja hozzá a következőt a paraméterek tetejéhez: Array- `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Módosítsa a rtspUrl paraméter értékét a következőre: – "rtsp://rtspsim:554/media/t2.mkv"    
1. A GraphTopologyDelete területen győződjön meg arról, hogy a "Name": "InferencingWithHttpExtension"
1. Kattintson a jobb gombbal az "src/Edge/deployment.customvision.template.json" fájlra, és kattintson a **IoT Edge üzembe helyezési jegyzék előállítása**elemre.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Custom Vision áttekintése" ikonra kattint. A karakterláncot a appsettings.jsfájlból másolhatja. (Itt van egy másik ajánlott módszer, amellyel biztosíthatja, hogy a megfelelő IoT Hub a VSCode-n belül legyen konfigurálva a [Select IoT hub parancs](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)használatával).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Custom Vision áttekintése" elemre, majd kattintson a **központi telepítés létrehozása egyetlen eszközhöz**lehetőségre. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Custom Vision áttekintése" néven.
    * Egy nevű modul `rtspsim` , amely szimulál egy RTSP-kiszolgálót, amely egy élő videó-hírcsatorna forrásaként működik.
    * Egy nevű modul `cv` , amelynek a neve azt sugallja, az a Custom Vision Toy Truck észlelési modell, amely a lemezképek egyéni nézetét alkalmazza, és több címkét ad vissza. (A modell csak egy címkére lett betanítva – "Delivery Truck").

## <a name="prepare-for-monitoring-events"></a>Felkészülés a figyelési eseményekre

Kattintson a jobb gombbal a Live Video Analytics-eszközre, és válassza a **figyelés beépített esemény végpontjának elindítása**lehetőséget. Erre a lépésre szüksége lesz a Visual Studio Code kimeneti ablakának IoT Hub eseményeinek figyeléséhez.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Custom Vision áttekintése":::

## <a name="run-the-sample-program"></a>A minta program futtatása

Ha megnyitja az oktatóanyaghoz tartozó Graph-topológiát egy böngészőben, látni fogja, hogy a inferencingUrl értéke be van állítva http://cv:80/image , ami azt jelenti, hogy a következtetési kiszolgáló visszaadja az eredményeket, miután az élő videóban észleli a Toy Trucks-t, ha van ilyen.

1. A hibakeresési munkamenet indításához válassza az F5 billentyűt. A terminál ablakban láthatók a kinyomtatott üzenetek.
1. A kód operations.jsa GraphTopologyList és a GraphInstanceList közvetlen metódusok hívásával indul el. Ha az előző rövid útmutatók befejezése után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. A folytatáshoz válassza az ENTER billentyűt.
    
   A terminál ablak a közvetlen metódusok következő készletét jeleníti meg:
    
   * Az előző topologyUrl használó GraphTopologySet hívása.
   * A következő törzset használó GraphInstanceSet hívása:
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
   * Egy GraphInstanceActivate-hívás, amely elindítja a Graph-példányt és a videó áramlását.
   * Egy második hívás a GraphInstanceList, amely azt mutatja, hogy a gráf példánya futó állapotban van.
    
1. A terminál ablakban lévő kimenet szünetelteti az ENTER billentyűt a folytatáshoz. Ne jelölje be az ENTER billentyűt. Görgessen felfelé, és tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a kimeneti ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulban az IoT hub-ra küld üzenetet. Az oktatóanyag következő szakasza ezeket az üzeneteket tárgyalja.
1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a terminál ablakába, és válassza az ENTER billentyűt.
A hívások következő sorozata törli az erőforrásokat:
    
   * A GraphInstanceDeactivate meghívása inaktiválja a Graph-példányt.
   * A GraphInstanceDelete hívása törli a példányt.
   * A GraphTopologyDelete hívása törli a topológiát.
   * A GraphTopologyList végső hívása azt mutatja, hogy a lista üres.
    
## <a name="interpret-the-results"></a>Az eredmények értelmezése

A Media Graph futtatásakor a HTTP-bővítmény processzor-csomópontjának eredményei áthaladnak a IoT Hub fogadó csomóponton az IoT hubhoz. A kimeneti ablakban látható üzenetek törzs szakaszt és applicationProperties szakaszt tartalmaznak. További információ: [IoT hub üzenetek létrehozása és olvasása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

A következő üzenetekben az élő videó elemzési modulja az alkalmazás tulajdonságait és a törzs tartalmát határozza meg.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a kapcsolat sikeres, a következő esemény lesz kinyomtatva. Az esemény típusa Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

Ebben az üzenetben a következő információkat láthatja:

* Az üzenet egy diagnosztikai esemény. A MediaSessionEstablished azt jelzi, hogy az RTSP-szimulátorhoz csatlakoztatott RTSP-forrás csomópont (a tárgy) megkezdte a (szimulált) élő adatcsatorna fogadását.
* A applicationProperties-ben a tárgy azt jelzi, hogy az üzenet a Media Graph RTSP-forrás csomópontjában lett létrehozva.
* A applicationProperties-ben a eventType azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* A eventTime jelzi az esemény előfordulásának időpontját.
* A törzs a diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.

### <a name="inference-event"></a>Következtetési esemény

A HTTP-bővítmény processzor-csomópontja a Custom Vision tárolóból származtatott eredményeket fogad, és az eredményeket a IoT Hub fogadó csomóponton keresztül bocsátja ki következtetési eseményként.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Vegye figyelembe a következőket a fenti üzenetekben:

* A applicationProperties tárgya a MediaGraph azon csomópontjára hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet a http-bővítmény processzorból származik.
* A applicationProperties eventType azt jelzi, hogy ez egy elemzési következtetési esemény.
* A eventTime jelzi az esemény előfordulásának időpontját.
* a "Body" az elemzési eseményre vonatkozó információkat tartalmaz. Ebben az esetben az esemény egy következtetési esemény, ezért a törzs az "előrejelzések" elnevezésű következtetések tömbjét tartalmazza.
* a "jóslatok" szakasz azon előrejelzések listáját tartalmazza, ahol a Toy Delivery Truck (címke = szállítási teherautó) található a keretben. Ahogy azt szeretné, a szállítási kamion az egyéni címkét adja meg, amelyet a Toy Truck számára a saját betanított modelljéhez adott meg, és a modell a játék teherautóját a különböző valószínűségi megbízhatósági pontszámokkal rendelkező bemeneti videóban azonosítja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a más oktatóanyagokat vagy gyors útmutatókat, tartsa be a következőt: a létrehozott erőforrásokhoz. Ellenkező esetben lépjen a Azure Portalra, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben az oktatóanyagot futtatta, és törölje az összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Megkeresheti az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőséggel](https://www.onvif.org/conformant-products/) rendelkező termékek oldalon. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.
* Az Azure Linux rendszerű virtuális gépek helyett AMD64-vagy x64-es Linux-eszközt használjon. Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. A [Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)című témakör útmutatását követheti. 

Ezután regisztrálja az eszközt az Azure IoT Hub az [első IoT Edge modul üzembe helyezése virtuális Linux-eszközre](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)című témakör útmutatását követve.

