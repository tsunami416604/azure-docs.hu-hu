---
title: Media Graph-koncepció – Azure
description: A Media Graph lehetővé teszi annak meghatározását, hogy az adathordozót hol kell rögzíteni, hogyan kell feldolgozni, és hol kell elküldeni az eredményeket. Ez a cikk részletes leírást nyújt a Media Graph-koncepcióról.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8c6775da6804b5079c89cae73d4621dd8067e90a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798839"
---
# <a name="media-graph"></a>Médiagrafikon

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)

## <a name="overview"></a>Áttekintés

A Media Graph lehetővé teszi annak meghatározását, hogy az adathordozót hol kell rögzíteni, hogyan kell feldolgozni, és hol kell elküldeni az eredményeket. Ezt úgy hajthatja végre, hogy a kívánt módon csatlakoztatja az összetevőket vagy a csomópontokat. Az alábbi ábra egy Media Graph grafikus ábrázolását mutatja be.  

![A Media Graph grafikus ábrázolása](./media/media-graph/overview.png)

A IoT Edge élő videó-elemzések támogatják a különböző típusú forrásokat, processzorokat és mosogatókat.

* A **forrásoldali csomópontok** lehetővé teszik az adathordozók rögzítését a Media Graphba. Ebben a kontextusban az adathordozók, például a hangadatfolyamok, a video streamek, az adatfolyamok, valamint a hang-, videó-és/vagy adatstreamek együttese egyetlen streamben.
* A **processzor csomópontjai** lehetővé teszik az adathordozók feldolgozását az adathordozó-gráfon belül.
* A fogadó **csomópontok** lehetővé teszik a feldolgozási eredmények kézbesítését a Media graphon kívüli szolgáltatásokhoz és alkalmazásokhoz.

## <a name="media-graph-topologies-and-instances"></a>Media Graph-topológiák és-példányok 

A IoT Edge élő videó-elemzések lehetővé teszik az adathordozó-diagramok kezelését két fogalommal – "gráf-topológia" és "Graph-példány". A Graph-topológia lehetővé teszi egy gráf tervének meghatározását, amely a paramétereket helyőrzőként adja meg az értékekhez. A topológia határozza meg, hogy milyen csomópontok használatosak a Media Graphban, és hogy azok hogyan kapcsolódnak a Media Graph-on belül. Ha például egy kamerából szeretné rögzíteni a hírcsatornát, szüksége lesz egy gráfra egy olyan forrás-csomóponttal, amely videót kap, valamint egy fogadó csomópontot, amely beírja a videót.

A topológia paramétereinek értékei a topológiára hivatkozó gráf-példányok létrehozásakor vannak megadva. Ez lehetővé teszi, hogy több példányt is hozzon létre ugyanarra a topológiára, de a topológiában megadott paraméterek eltérő értékeivel. A fenti példában használhat paramétereket a kamera IP-címének és a rögzített videó nevének a megjelenítéséhez. Több gráf-példányt is létrehozhat, ezzel a topológiával – egy példányban az egyes kamerákhoz egy adott épületben, lehet, hogy mindegyik az adott IP-címmel és adott névvel rendelkezik.

## <a name="media-graph-states"></a>Media Graph-állapotok  

A Media Graph a következő állapotok egyike lehet:

* Inaktív – azt az állapotot jelöli, amelyben egy adathordozó-gráf konfigurálva van, de nem aktív.
* Aktiválás – az az állapot, amikor egy adathordozó-gráfot hoznak létre (azaz az inaktív és az aktív állapot közötti átmenetet).
* Aktív – az az állapot, amikor egy adathordozó-gráf aktív. 

    > [!NOTE]
    >  A Media Graph aktív adatok nélkül is aktiválható (például a bemeneti videó forrása offline állapotba kerül).
* Inaktiválás – ez az az állapot, amikor egy adathordozó-gráf Aktívról inaktívra vált.

Az alábbi ábra szemlélteti a Media Graph állapotjelző gépet.

![A Media Graph állapotjelző gépe](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>Források, processzorok és mosogatók  

A IoT Edge Live Video Analytics a következő típusú csomópontokat támogatja a Media graphon belül:

### <a name="sources"></a>Források 

#### <a name="rtsp-source"></a>RTSP-forrás 

Az RTSP-forrás csomópontok lehetővé teszik az adathordozók betöltését egy [RTSP] ( https://tools.ietf.org/html/rfc2326 kiszolgálóról. A megfigyelési és IP-alapú kamerák az adatokat egy RTSP (valós idejű Streaming-Protocol) protokollon keresztül továbbítják, amely eltér a más típusú eszközöktől, például a telefonoktól és a videokameráktól. Ez a protokoll a-kiszolgáló (a kamera) és az ügyfél közötti adathordozó-munkamenetek létrehozásához és szabályozásához használatos. A Media Graph RTSP-forrás csomópontja ügyfélként működik, és egy RTSP-kiszolgálóval létesített munkamenetet tud létesíteni. Számos eszköz, például a legtöbb [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) beépített RTSP-kiszolgálóval rendelkezik. A [ONVIF](https://www.onvif.org/) megbízza az RTSP-t, hogy az a [G, S & T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) szabványnak megfelelő eszközök definíciójában legyen támogatva. Az RTSP-forrás csomóponthoz meg kell adnia egy RTSP URL-címet, valamint a hitelesített kapcsolat engedélyezéséhez szükséges hitelesítő adatokat.

#### <a name="iot-hub-message-source"></a>IoT Hub üzenet forrása 

A többi [IoT Edge-modulhoz](../../iot-edge/iot-edge-glossary.md#iot-edge-module)hasonlóan az élő videó Analytics IoT Edge modulban az [IoT Edge hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)használatával fogadhat üzeneteket. Ezek az üzenetek elküldhetők más modulokból, vagy a peremhálózati eszközön futó alkalmazásokból, illetve a felhőből is. Az ilyen üzenetek továbbítása (átirányítva) egy [elnevezett bemenetre](../../iot-edge/module-composition.md#sink) történik a modulban. Egy IoT Hub Message Source csomópont lehetővé teszi, hogy az ilyen üzenetek elérjék a Media Graphot. Ezek az üzenetek vagy jelek ezután a Media Graph-ban belső használatra használhatók, jellemzően a Signal Gates aktiválásához (lásd a [Signal Gates](#signal-gate-processor) szakaszt alább). 

Rendelkezhet például egy olyan IoT Edge modullal, amely egy ajtó megnyitásakor üzenetet hoz létre. A modulból érkező üzenet átirányítható IoT Edge központba, ahonnan továbbítható a Media Graph IoT hub-üzenet forrása. A Media Graphban az IoT hub-üzenet forrása továbbíthatja az eseményt egy Signal Gate-feldolgozónak, amely Ezután bekapcsolhatja a videó rögzítését egy RTSP-forrásból egy fájlba. 

### <a name="processors"></a>Processzorok  

#### <a name="motion-detection-processor"></a>Mozgásészlelési processzor 

A mozgásészlelési processzor csomópont lehetővé teszi az élő videókon való mozgás észlelését. Megvizsgálja a beérkező képkockákat, és meghatározza, hogy van-e mozgás a videóban. Ha a rendszer mozgást észlel, a videó kereteit átadja az alsóbb rétegbeli összetevőnek, és egy eseményt bocsát ki. A mozgásérzékelő processzor csomópontja (más csomópontokkal együtt) felhasználható a bejövő videó rögzítésének indítására, amikor a rendszer mozgást észlelt.

#### <a name="frame-rate-filter-processor"></a>Frame rate Filter processzor  

A frame rate szűrő processzor csomópontja lehetővé teszi, hogy a beérkező videó streamből egy megadott arányban mintákat lehessen mintát venni. Ez lehetővé teszi, hogy csökkentse a lefelé stream-összetevőkre (például a HTTP-bővítmény processzor-csomópontjára) küldött keretek számát a további feldolgozás érdekében.

#### <a name="http-extension-processor"></a>HTTP-bővítmény processzora

A HTTP-bővítmény processzor-csomópontja lehetővé teszi saját IoT Edge moduljának összekapcsolását egy adathordozó-gráfhoz. Ez a csomópont dekódolja a képkockákat bemenetként, és az ilyen képkockákat a modul által elérhető HTTP REST-végpontra továbbítja. Ez a csomópont szükség esetén képes a REST-végpont hitelesítésére. Emellett a csomópont beépített rendszerképekkel rendelkezik a képkockák méretezéséhez és kódolásához, mielőtt azok továbbítva lesznek a REST-végpontnak. A méretezési lehetőségekkel a képméretarány megtartható, párnázott vagy kifeszíthető. A képkódoló támogatja a JPEG, a PNG vagy a BMP formátumot.

#### <a name="grpc-extension-processor"></a>gRPC-bővítmény processzora

A gRPC-bővítmény processzor-csomópontja dekódolja a képkockákat bemenetként, és továbbítja ezeket a képkockákat a modul által elérhető [gRPC](terminology.md#grpc) -végpontnak. Emellett a csomópont beépített rendszerképekkel rendelkezik a képkockák méretezéséhez és kódolásához, mielőtt azok továbbítva lesznek a gRPC-végpontnak. A méretezési lehetőségekkel a képméretarány megtartható, párnázott vagy kifeszíthető. A képkódoló támogatja a JPEG, a PNG vagy a BMP formátumot.

#### <a name="signal-gate-processor"></a>Signal Gate processzor  

A Signal Gate processzor csomópontja lehetővé teszi az adathordozók feltételes továbbítását az egyik csomópontról a másikra. Pufferként is működik, amely lehetővé teszi a média és az események szinkronizálását. A tipikus használati eset az, ha egy Signal Gate processzor-csomópontot szúr be az RTSP-forrás csomópontja és az objektum fogadó csomópontja között, és egy mozgásérzékelő processzor-csomópont kimenetét használja a kapu elindításához. A Media Graph segítségével csak akkor rögzíthet videót, ha a rendszer mozgást észlel.

### <a name="sinks"></a>Fogadóként  

#### <a name="asset-sink"></a>Eszköz fogadója  

Az eszköz fogadó csomópontja lehetővé teszi adathordozó (videó és/vagy hang) adatok írását egy Azure Media Services eszközre. A Media Graph-ban csak egyetlen objektum-fogadó csomópont lehet. Tekintse meg az [eszköz](terminology.md#asset) szakaszt az eszközökről, valamint az adathordozók rögzítésével és lejátszásával kapcsolatos szerepéről. A [folyamatos videofelvételek](continuous-video-recording-concept.md) című cikkből megtudhatja, hogyan használja a rendszer a csomópontok tulajdonságait.

#### <a name="file-sink"></a>File mosogató  

A file fogadó csomópont lehetővé teszi adathordozó (videó és/vagy hang) típusú adatok írását a IoT Edge eszköz helyi fájlrendszerének egyik helyére. Egy Media Graph-ban csak egy file mosogató csomópont lehet, és a jel a Signal Gate processzor csomópontjának kell, hogy legyen. Ez korlátozza a kimeneti fájlok időtartamát a Signal Gate-feldolgozó csomópontjának tulajdonságaiban megadott értékekre.

#### <a name="iot-hub-message-sink"></a>Üzenet-fogadó IoT Hub  

Egy IoT Hub Message fogadó csomópont lehetővé teszi az események közzétételét IoT Edge hubhoz. Az IoT Edge hub ezután átirányíthatja az adatforrásokat a peremhálózati eszközön lévő más modulokba vagy alkalmazásokba, vagy a felhőben IoT Hub (az üzembe helyezési jegyzékben megadott útvonalakon). A IoT Hub Message fogadó csomópont képes fogadni a felsőbb rétegbeli processzorokból származó eseményeket, például a mozgásészlelési processzor csomópontját vagy egy külső következtetési szolgáltatást egy HTTP-bővítmény processzor-csomópontján keresztül.

## <a name="rules-on-the-use-of-nodes"></a>A csomópontok használatára vonatkozó szabályok

A különböző csomópontok adathordozó-diagramon belüli használatának módjával kapcsolatos további szabályok a [Graph-topológiák korlátozásai](quotas-limitations.md#limitations-on-graph-topologies-at-preview) című témakörben találhatók.

## <a name="scenarios"></a>Forgatókönyvek

A fent definiált források, processzorok és mosogatók kombinációjának használatával különböző forgatókönyveket hozhat létre, amelyek az élő videók elemzését foglalják magukban. Példák a következőkre:

* [Folyamatos videófelvétel](continuous-video-recording-concept.md)
* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)
* [Live Video Analytics videófelvétel nélkül](analyze-live-video-concept.md)

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan futtathatja a mozgásérzékelőt élő videós hírcsatornán, tekintse meg a rövid útmutató [: élő videó-elemzés futtatása a saját modelljével](use-your-model-quickstart.md)című témakört.
