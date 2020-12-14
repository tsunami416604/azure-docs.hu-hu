---
title: Élő videó-elemzés IoT Edge GYIK-ban – Azure
description: Ez a témakör válaszokat ad az élő videók elemzésére IoT Edge GYIK-ben.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401576"
---
# <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYIK)

Ez a témakör válaszokat ad az élő videók elemzésére IoT Edge GYIK-ben.

## <a name="general"></a>Általános kérdések

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>Mik azok a rendszerváltozók, amelyek használhatók a Graph topológia definíciójában?

|Változó   |Leírás|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|UTC-idő szerint egy pillanatot jelöl, amely általában a nap dátumának és időpontjának (alapszintű képviseleti yyyyMMddTHHmmssZ) kifejezése.|
|System. PreciseDateTime|A ISO8601-fájlnak megfelelő formátumú, ezredmásodpercben (alapszintű yyyyMMddTHHmmss. fffZ).|
|System. GraphTopologyName   |Egy Graph-topológiát jelöl, amely egy gráf tervrajzát tartalmazza.|
|System. GraphInstanceName|  Egy Media Graph-példányt jelöl, a paraméter értékeit tartalmazza, és a topológiára hivatkozik.|

## <a name="configuration-and-deployment"></a>Konfigurálás és üzembe helyezés

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>Telepíthetem a Media Edge-modult egy Windows 10-es eszközre?

Igen. Tekintse meg a [Linux-tárolók a Windows 10 rendszerben](/virtualization/windowscontainers/deploy-containers/linux-containers)című cikket.

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Rögzítés az IP-kamera és az RTSP-beállítások alapján

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>Speciális SDK-t kell használnom az eszközömön videóstream küldéséhez?

Nem. A IoT Edge Live Video Analytics támogatja az adathordozók rögzítését az RTSP Video Streaming Protocol használatával (amely a legtöbb IP-kamera esetében támogatott).

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>Leküldhetem a médiát a IoT Edge az RTMP vagy a Smooth (például egy Media Services élő esemény) használatával élő video Analyticsre?

* Nem. Az élő videó elemzése csak az RTSP-t támogatja a videók IP-kamerákból való rögzítéséhez.
* Minden olyan kamera, amely támogatja a TCP/HTTP protokollon keresztüli RTSP-átvitelt, működnie kell. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>Alaphelyzetbe állíthatom vagy frissíthetem az RTSP-forrás URL-címét egy grafikonpéldányon?

Igen, ha a Graph-példány inaktív állapotban van.  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>Van elérhető RTSP-szimulátor a tesztelés és a fejlesztés során?

Igen. A gyors üzembe helyezési útmutatóban egy [RTSP szimulátor](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge-modul használható, amely támogatja a tanulási folyamatot. Ezt a modult tájékoztató jelleggel biztosítjuk, és előfordulhat, hogy nem mindig érhető el. Javasoljuk, hogy ezt ne használja több órán át. Az éles környezetben történő üzembe helyezés előtt érdemes befektetni a tényleges RTSP-forrással végzett tesztelésbe.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>Támogatott az IP-kamerák ONVIF-felderítése a peremhálózaton?

Nem, az eszközök ONVIF-felderítése nem támogatott a peremhálózaton.

## <a name="streaming-and-playback"></a>Folyamatos átvitel és lejátszás

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>A szélétől az AMS-re rögzített eszközök a Media Services streaming Technologies, például a HLS vagy a DASH használatával lesznek lejátszva?

Igen. A rögzített adategységek adatfolyamként használhatók, mint bármely más eszköz Azure Media Services. A tartalom továbbításához létre kell hoznia egy folyamatos átviteli végpontot és a futó állapotot. A standard szintű folyamatos átviteli lokátor létrehozási folyamata hozzáférést biztosít egy HLS vagy DASH-jegyzékfájlhoz, amely bármely alkalmas Player-keretrendszerhez elérhető. A közzétételi HLS vagy kötőjel-jegyzékfájlok létrehozásával kapcsolatos részletekért lásd: [dinamikus csomagolás](../latest/dynamic-packaging-overview.md).

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>Használhatom a Media Services szabványos tartalomvédelem és DRM funkcióit archivált eszközön?

Igen. Az összes szabványos dinamikus titkosítási tartalomvédelem és DRM-funkció a Media Graph-ból rögzített eszközökön használható.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>Milyen játékosokat használhatok a rögzített eszközök tartalmának megtekintésére?

Minden olyan szabványos lejátszó támogatott, amely támogatja a megfelelő Apple HTTP Live Streaming (HLS) 3-as vagy 4-es verziójú verziót. Emellett minden olyan lejátszó is támogatott, amely kompatibilis MPEG-DASH-lejátszásra képes.

A teszteléshez ajánlott játékosok a következők:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [A-lejátszó](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple Native HTTP Live Streaming](https://developer.apple.com/streaming/)
* Az Edge, a Chrome vagy a Safari beépített HTML5-videolejátszó
* HLS vagy DASH lejátszást támogató kereskedelmi játékosok

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>Milyen korlátozások vonatkoznak a Media Graph-eszközök továbbítására?

A Media Graph élő vagy rögzített eszközének adatfolyam-továbbítása ugyanazt a nagy léptékű infrastruktúrát és folyamatos átviteli végpontot használja, amely Media Services támogatja az igény szerinti és élő közvetítést a Media & Entertainment, OTT és a Broadcast ügyfelek számára. Ez azt jelenti, hogy gyorsan és egyszerűen engedélyezheti a Azure CDN, a Verizon vagy a Akamai számára, hogy a tartalmat a közönség számára kis méretekben, vagy akár akár több millióra is kiterjesztheti a forgatókönyvtől függően.

A tartalom az Apple HTTP Live Streaming (HLS) vagy az MPEG-DASH használatával is továbbítható.

## <a name="design-your-ai-model"></a>AI-modell megtervezése 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>Több AI-modell van becsomagolva egy Docker-tárolóba. Hogyan használhatom őket az élő videó Analytics szolgáltatással? 

A megoldások különbözőek attól függően, hogy a hivatkozó kiszolgáló milyen kommunikációs protokollt használ az élő videó-elemzésekkel való kommunikációhoz. Ezt a következő módokon teheti meg.

#### <a name="http-protocol"></a>HTTP protokoll:

* Egyetlen tároló (egyetlen lvaExtension):  

   A következtetésben álló kiszolgálón egyetlen portot, de különböző AI-modellekhez tartozó végpontokat is használhat. Egy Python-minta esetében például a következő módon használhat különböző `route` s-modelleket: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   Majd az élő videó elemzési üzembe helyezése során, amikor gráfokat hoz létre, állítsa be a következtetési kiszolgáló URL-címét az egyes példányokhoz a következő módon: 

   1. példány: következtetés a kiszolgáló URL-címe =`http://lvaExtension:44000/score/face_detection`<br/>
   második példány: következtetés a kiszolgáló URL-címe =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > Azt is megteheti, hogy az AI-modelleket különböző portokon teszi elérhetővé, és a gráfok létrehozásakor meghívja őket.  

* Több tároló: 

   Minden tároló egy másik névvel van telepítve. Jelenleg a Live Video Analytics dokumentációs készletében bemutatjuk, hogyan helyezhet üzembe egy bővítményt a következő névvel: **lvaExtension**. Most már két különböző tárolót is fejleszthet. Minden tároló ugyanazzal a HTTP-felülettel rendelkezik (azaz ugyanazt a `/score` végpontot). Telepítse ezt a két tárolót különböző nevekkel, és ügyeljen arra, hogy mindkettő **különböző portokat** figyeljen. 

   Például egy, a nevű tároló figyeli a `lvaExtension1` portot `44000` , a nevet tartalmazó másik tároló `lvaExtension2` figyeli a portot `44001` . 

   Az élő videó-elemzési topológiában két gráfot hoz létre különböző következtetési URL-címekkel, például: 

   Első példány: következtetés a kiszolgáló URL-címe = `http://lvaExtension1:44001/score`    
   Második példány: következtetési kiszolgáló URL-címe = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>GRPC protokoll: 

Az 1,0-as Live Video Analytics-modul gRPC protokoll használata esetén az egyetlen módszer, ha a gRPC-kiszolgáló különböző AI-modelleket adott portokon keresztül tesz elérhetővé. [Ebben a példában](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)egyetlen port van, 44000, amely az összes Yolo-modellt kiteszi. Elméletileg a Yolo gRPC-kiszolgáló újra írható, hogy elérhetővé tegyen néhány modellt a 44000-es, másoknál a 45000-es, a... 

Az 2,0-es Live Video Analytics-modul használatával új tulajdonságot ad hozzá a gRPC-bővítmény csomóponthoz. Ez a tulajdonság neve **extensionConfiguration** , amely egy opcionális karakterlánc, amelyet a gRPC-szerződés részeként használhat. Ha egyetlen következtetési kiszolgálón több AI-modell van csomagolva, az összes AI-modellhez nem szükséges csomópontot kitenni. Ehelyett egy Graph-példány esetében a bővítmény szolgáltatója (Ön) megadhatja, hogyan válassza ki a különböző AI-modelleket a **extensionConfiguration** tulajdonsággal, és a végrehajtás során a Live Video Analytics átadja ezt a karakterláncot a következtetést felhasználó kiszolgálónak, amely a kívánt AI-modell meghívására használható. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>GRPC-kiszolgálót dolgozok fel egy AI-modell körül, és támogatni szeretném a több kamera/gráf példány használatát. Hogyan kell létrehozni a kiszolgálót? 

 Először is győződjön meg arról, hogy a kiszolgáló egyszerre több kérést tud kezelni. Vagy győződjön meg arról, hogy a kiszolgáló párhuzamos szálon működik. 

Például az egyik [élő videó Analytics GRPC-minta](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)esetében a párhuzamos csatornák alapértelmezett száma is meg van határozva. Lásd: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

A fenti gRPC-kiszolgáló példányában a kiszolgáló egyszerre csak három csatornát nyithat meg (tehát gráf-topológiai példány esetén). Ne próbáljon háromnál több példányt csatlakozni a kiszolgálóhoz. Ha több mint három csatornát próbál megnyitni, a kérelmek mindaddig függőben lesznek, amíg egy meglévőt el nem veszít.  

A gRPC-kiszolgáló implementációja a Python-mintákban is használatos. A fejlesztők saját kiszolgálókat hozhatnak létre, vagy a fenti alapértelmezett implementációban növelhetik a feldolgozói számot a videó-hírcsatornák beolvasásához használt fényképezőgépek számával. 

Több kamera beállításához és használatához a fejlesztők több gráf-topológiát is létrehozhatnak, ahol az egyes példányok azonos vagy eltérő következtetésre (például a fenti bekezdésben említett kiszolgálóra) mutatnak. 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>Több képkockát szeretnék fogadni a felsőbb rétegből, mielőtt következtetéseket hozna. Hogyan engedélyezhető a szolgáltatás? 

Az aktuális [alapértelmezett minták](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) "állapot nélküli" módban működnek. Ezek a minták nem tartanak fenn az előző hívások állapotát, és még a hívottak is (azaz több topológiai példány is hívhatja ugyanazt a következtetési kiszolgálót, és a kiszolgáló nem fogja tudni megkülönböztetni, hogy ki hívja meg és adja meg a hívót) 

#### <a name="http-protocol"></a>HTTP protokoll

HTTP protokoll használata esetén: 

Az állapot megtartása érdekében minden hívó (gráf-topológiai példány) meghívni fogja a hivatkozó kiszolgálót a hívónak egyedi HTTP lekérdezési paraméterrel. Példa: következtetés a kiszolgáló URL-címére a következőhöz:  

1. topológiai példány = `http://lvaExtension:44000/score?id=1`<br/>
második topológiai példány = `http://lvaExtension:44000/score?id=2`

… 

A kiszolgáló oldalon a pontszám útvonala fogja tudni, hogy ki hívja meg. Ha az ID = 1, akkor az az állapotot külön is megtarthatja a hívó számára (gráf-topológia példánya). Ezután megtarthatja a kapott képkockákat egy pufferben (például tömb vagy szótár a DateTime kulccsal, az érték pedig a keret), majd megadhatja a kiszolgálót, amely az x keretek beérkezése után feldolgozható (következtető). 

#### <a name="grpc-protocol"></a>GRPC protokoll 

GRPC protokoll használata esetén: 

A gRPC-bővítmények mindegyike egyetlen kamerás hírcsatornára vonatkozik, így nincs szükség az azonosító megadására. Így most már a extensionConfiguration tulajdonsággal tárolhatja a képkockákat egy pufferben, és meghatározhatja azt a kiszolgálót, amely feldolgozza (következtet) x keretek fogadása után. 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>Egy adott tároló összes ProcessMediaStreams ugyanazt a AI-modellt futtatja? 

Nem.  

A végfelhasználók által a Graph-példányon kezdeményezett hívások indítása/leállítása egy munkamenetet alkot, vagy lehet, hogy a kamera leválasztása/újracsatlakozása folyamatban van. A cél egy munkamenet fenntartása, ha a kamera streaming videó. 

* A videó feldolgozásra való továbbítása két kamerával, két munkamenetet hoz létre. 
* Egy kamera egy olyan gráfra mutat, amely két gRPCExtension-csomóponttal két munkamenetet hoz létre. 

Minden munkamenet egy teljes kétirányú kapcsolat az élő videó-elemzés és a gRPC-kiszolgáló között, és az egyes munkamenetek egy másik modellhez/folyamathoz is tartozhatnak. 

> [!NOTE]
> Ha egy kamera kapcsolatának leválasztása/újracsatlakozása (a kamerán kívüli, tűréshatáron túli időszakban), az élő videó Analytics új munkamenetet nyit meg a gRPC-kiszolgálóval. A kiszolgáló nem követeli meg az állapot nyomon követését ezen munkamenetek között. 

Az élő videó Analytics több gRPC-bővítmény támogatását is támogatja egyetlen kamerához egy gráf-példányban. Ezekkel a gRPC-bővítményekkel szekvenciálisan vagy párhuzamosan végezheti el az AI-feldolgozást, vagy akár mindkettő kombinációját is használhatja. 

> [!NOTE]
> Ha több bővítmény párhuzamosan fut, az hatással lesz a hardver erőforrásaira, és a számítási igényeknek megfelelő hardver kiválasztásakor meg kell tartania ezt az elmét. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>Mi az egyidejű ProcessMediaStreams maximális száma? 

Az élő videó-elemzések nem érvényesek.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>Hogyan dönthető el, hogy a hivatkozó kiszolgálónak CPU-t vagy GPU-t vagy bármely más hardveres gyorssegédet kell használnia? 

Ez teljesen függ attól, hogy milyen összetett az AI-modell, és hogy a fejlesztő hogyan kívánja használni a CPU-t és a hardveres gyorssegédeket. Az AI-modell fejlesztése során a fejlesztők meghatározhatják, hogy a modell milyen erőforrásokat használjon a műveletek végrehajtásához. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>A Hogyan a képeket a behatároló dobozok feldolgozás után? 

Jelenleg a határolókeret koordinátáit biztosítjuk, csak a következtetési üzenetekben. A fejlesztők létrehozhatnak egy egyéni MJPEG-es streamet, amely használhatja ezeket az üzeneteket, és átfedi a határoló mezőket a videó kereteken.  

## <a name="grpc-compatibility"></a>gRPC kompatibilitása 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>Honnan tudhatom meg, hogy a Media stream-leíró kötelező mezői milyenek? 

A nem megadott mezőérték a [gRPC által megadott](https://developers.google.com/protocol-buffers/docs/proto3#default)alapértelmezett értéket kapja meg.  

Az élő videó Analytics a **proto3** verzióját használja. Az élő videó-elemzési szerződések által használt összes protokoll-puffer-érték elérhető az [itt definiált](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)protokoll-pufferben. 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>Hogyan biztosítható, hogy a rendszer a legfrissebb protokoll-puffer-fájlokat használja? 

[Itt lehet megszerezni](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)a protokollok legújabb puffereit. Amikor frissítjük a szerződések fájljait, azok megjelennek az ezen a helyen. Habár nincs azonnali terv a protokoll fájljainak frissítésére, a fájlok tetején keresse meg a csomag nevét, és Ismerje meg a verziót. A következőket kell elolvasni: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

A fájlok frissítései a név végén növelik a "v-Value" értéket. 

> [!NOTE]
> Mivel az élő video Analytics a nyelv proto3 verzióját használja, a mezők nem kötelezőek, és így visszamenőleg és visszafelé is kompatibilisek lesznek. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Milyen gRPC funkciók érhetők el az élő videó Analytics használatával? Mely funkciók kötelezőek, és melyek választhatók? 

Bármely kiszolgálóoldali gRPC funkció használható, ha a protopuf-szerződés teljesül. 

## <a name="monitoring-and-metrics"></a>Monitorozás és mérőszámok

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Nyomon követhető a peremhálózati adathordozó-gráf a Event Grid használatával?

Igen. Felhasználhatja a Prometheus-metrikákat, és közzéteheti őket az Event Gridben. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Használhatom Azure Monitor a saját adathordozó-diagramok állapotának, metrikáinak és teljesítményének megtekintéséhez a felhőben vagy a peremen?

Igen. Ez támogatott. További információ a [Azure monitor mérőszámok használatáról](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Vannak olyan eszközök, amelyek megkönnyítik a Media Services IoT Edge modul figyelését?

A Visual Studio Code támogatja az Azure IoT Tools bővítményt, amely lehetővé teszi az LVAEdge-modul végpontok egyszerű figyelését. Ezzel az eszközzel gyorsan megkezdheti az "események" IoT Hub beépített végpontjának figyelését, és megtekintheti a peremhálózati eszközről a felhőbe irányított következtetéseket. 

Ezen felül a bővítmény használatával szerkesztheti a LVAEdge modulhoz tartozó Twin modult a Media Graph beállításainak módosításához.

További információ: [figyelés és naplózás](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Számlázás és rendelkezésre állás

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Hogyan történik a Live Video Analytics IoT Edge számlázása?

A részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>Következő lépések

[Gyors útmutató: első lépések – élő videó-elemzés IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
