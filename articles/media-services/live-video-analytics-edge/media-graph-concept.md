---
title: Media Graph-koncepció – Azure
description: A Media Graph lehetővé teszi annak meghatározását, hogy az adathordozót hol kell rögzíteni, hogyan kell feldolgozni, és hol kell elküldeni az eredményeket. Ez a cikk részletes leírást nyújt a Media Graph-koncepcióról.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: ee055c34fd37d2d1cc5e7d0bb5147c945dcbff94
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260966"
---
# <a name="media-graph-concept"></a>A Media Graph koncepciója

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)

## <a name="overview"></a>Áttekintés

A Media Graph lehetővé teszi annak meghatározását, hogy az adathordozót hol kell rögzíteni, hogyan kell feldolgozni, és hol kell elküldeni az eredményeket. A Media Graph forrás-, processzor-és fogadó csomópontokból áll. Az alábbi ábra egy Media Graph grafikus ábrázolását mutatja be.   

![A Media Graph grafikus ábrázolása](./media/media-graph/overview.png)


* A **forrásoldali csomópontok** lehetővé teszik az adathordozók rögzítését a Media Graphba. Ebben a kontextusban az adathordozók, például a hangadatfolyamok, a video streamek, az adatfolyamok, valamint a hang-, videó-és/vagy adatstreamek együttese egyetlen streamben.
* A **processzor csomópontjai** lehetővé teszik az adathordozók feldolgozását az adathordozó-gráfon belül.
* A fogadó **csomópontok** lehetővé teszik a feldolgozási eredmények kézbesítését a Media graphon kívüli szolgáltatásokhoz és alkalmazásokhoz.

## <a name="media-graph-topologies-and-instances"></a>Media Graph-topológiák és-példányok 

A IoT Edge élő videó-elemzések lehetővé teszik az adathordozó-diagramok kezelését két fogalommal – "gráf-topológia" és "Graph-példány". A Graph-topológia lehetővé teszi egy gráf tervrajzának meghatározását, amely a paramétereket helyőrzőként adja meg az értékekhez. A topológia határozza meg, hogy milyen csomópontok használatosak a Media Graphban, és hogy azok hogyan kapcsolódnak a Media Graph-on belül. A paraméterek értékei megadhatók a topológiára hivatkozó gráf-példányok létrehozásakor. Ez lehetővé teszi, hogy több példányt is hozzon létre ugyanarra a topológiára, de a topológiában megadott paraméterek eltérő értékeivel. 

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

Az RTSP-források lehetővé teszik az adathordozók az [RTSP](https://tools.ietf.org/html/rfc2326) -kiszolgálóról való rögzítését. Az RTSP a-kiszolgáló és az ügyfél közötti adathordozó-munkamenetek létrehozásához és vezérléséhez használatos. A Media Graph RTSP-forrás csomópontja ügyfélként működik, és képes létrehozni egy munkamenetet a megadott RTSP-kiszolgálóval. Számos eszköz, például a legtöbb [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) beépített RTSP-kiszolgálóval rendelkezik. A [ONVIF](https://www.onvif.org/) megbízza az RTSP-t, hogy az a [G, S & T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) szabványnak megfelelő eszközök definíciójában legyen támogatva. A Media Graph RTSP-forrás csomópontjának meg kell adnia egy RTSP URL-címet, valamint a hitelesített kapcsolat engedélyezéséhez szükséges hitelesítő adatokat.

#### <a name="iot-hub-message-source"></a>IoT Hub üzenet forrása 

A többi [IoT Edge-modulhoz](../../iot-edge/iot-edge-glossary.md#iot-edge-module)hasonlóan az élő videó Analytics IoT Edge modulban az [IoT Edge hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)használatával fogadhat üzeneteket. Ezek az üzenetek elküldhetők más modulokból, vagy a peremhálózati eszközön futó alkalmazásokból, illetve a felhőből is. Ezek az üzenetek továbbítva (átirányítva) a modul egy [nevesített bemenetéhez](../../iot-edge/module-composition.md#sink) . Az IoT Hub üzenet forrása lehetővé teszi az ilyen üzenetek továbbítását egy Media Graph-példányba. Ezek az üzenetek és jelek a Media Graph-ban belsőleg használhatók, jellemzően a Signal Gates aktiválásához (lásd az alábbi [Signal Gate-processzort](#signal-gate-processor) ). 

Rendelkezhet például egy olyan IoT Edge modullal, amely egy ajtó megnyitásakor üzenetet hoz létre. A modulból érkező üzenet átirányítható IoT Edge központba, ahonnan továbbítható a Media Graph IoT hub-üzenet forrása. A Media Graphban az IoT hub-üzenet forrása továbbíthatja az eseményt egy Signal Gate-feldolgozónak, amely Ezután bekapcsolhatja a videó rögzítését egy RTSP-forrásból egy fájlba. 

### <a name="processors"></a>Processzorok  

#### <a name="motion-detection-processor"></a>Mozgásészlelési processzor 

A mozgásészlelési processzor lehetővé teszi a mozgás észlelését az élő videókon. Megvizsgálja a beérkező videót, és meghatározza, hogy van-e mozgás a videóban. Ha a rendszer mozgást észlel, a videó átkerül az alsóbb rétegbeli csomópontra, és egy eseményt bocsát ki. A mozgásészlelési processzor (más Media Graph-csomópontokkal együtt) a bejövő videó rögzítésének indítására is használható a mozgás észlelése esetén.

#### <a name="frame-rate-filter-processor"></a>Frame rate Filter processzor  

A frame rate szűrő processzora lehetővé teszi, hogy a beérkező video streamből egy megadott képkockán mintákat lehessen mintát venni. Ez lehetővé teszi, hogy csökkentse a lefelé stream-csomópontokra (például a HTTP-bővítmény processzorára) küldött keretek számát a további feldolgozás érdekében.

#### <a name="http-extension-processor"></a>HTTP-bővítmény processzora 

A HTTP-bővítmény processzor lehetővé teszi, hogy a saját AI-t egy Media Graph-ba csatlakoztassa. A HTTP-bővítmény processzora bemenetként dekódolt képkockákat használ, és továbbítja ezeket a képkockákat egy HTTP-végpontnak. Ha szükséges, a processzor képes hitelesíteni a HTTP-végpontot. Emellett a processzor beépített rendszerkép-formázó felülettel rendelkezik, amely lehetővé teszi a képkockák méretezését és kódolását a továbbítás előtt. A skálázás a kép méretarányának megtartására, kitöltésére vagy kiosztására szolgáló lehetőségeket biztosít, míg a kódolás a különböző képkódolási lehetőségeket, például JPEG, PNG vagy BMP beállításokat tartalmaz.

#### <a name="signal-gate-processor"></a>Signal Gate processzor  

A Signal Gate-feldolgozó lehetővé teszi, hogy az adathordozó feltételesen legyen továbbítva az egyik csomópontról a másikra. Pufferként is működik, amely lehetővé teszi a média és az események szinkronizálását. Példa a használati esetre, ha be kell szúrni egy Signal Gate-processzort egy RTSP-forrás és egy objektum-fogadó között, és a mozgásérzékelő processzor kimenetét kell használnia a kapu elindításához. A Media Graph használatával csak akkor aktiválhatja az adathordozók rögzítését, ha a bejövő videóban mozgás észlelhető. 

### <a name="sinks"></a>Fogadóként  

#### <a name="asset-sink"></a>Eszköz fogadója  

Az adategységek elfogadója lehetővé teszi, hogy a Media Graph adathordozó (videó és/vagy hang) típusú adatokba írja az adatAzure Media Servicesi eszközöket. Tekintse meg az [eszköz](terminology.md#asset) szakaszt az eszközökről, valamint az adathordozók rögzítésével és lejátszásával kapcsolatos szerepéről.  

#### <a name="file-sink"></a>File mosogató  

A file fogadó lehetővé teszi, hogy a Media Graph adathordozó-(video-és/vagy hang-) adatokba írja a IoT Edge eszköz helyi fájlrendszerének helyét. A fájl fogadójának a Signal Gate-processzornak kell lennie. Ez korlátozza a kimeneti fájlok időtartamát a Signal Gate-feldolgozó tulajdonságaiban megadott értékekre.

#### <a name="iot-hub-message-sink"></a>Üzenet-fogadó IoT Hub  

Az IoT Hub Message fogadó lehetővé teszi az események közzétételét IoT Edge hubhoz. Az Edge hub ezután a peremhálózati eszközön más modulokba vagy alkalmazásokba is átirányíthatja az alkalmazásokat, illetve a felhőben IoT Hub a felhőben (az üzembe helyezési jegyzékben megadott útvonalakon). A IoT Hub Message fogadó képes fogadni a felsőbb rétegbeli processzorok, például a mozgásészlelési processzor vagy a HTTP-bővítmény processzorán keresztüli külső következtetési szolgáltatás eseményeit.

## <a name="scenarios"></a>Forgatókönyvek

A fent megadott forrás-, processzor-és fogadó csomópontok kombinációjának használatával számos különböző forgatókönyvhöz készíthet adathordozó-diagramokat. Néhány példa a következő forgatókönyvekre

* [Folyamatos videofelvétel](continuous-video-recording-concept.md)
* [Event-alapú videó rögzítése](event-based-video-recording-concept.md)
* [Videó-rögzítés nélküli élő videó-elemzés](analyze-live-video-concept.md)

## <a name="next-steps"></a>További lépések

Kövesse a gyors útmutató [: élő videó elemzés futtatása saját modell](use-your-model-quickstart.md) használatával című cikket, amelyből megtudhatja, hogyan futtathatja a mozgásérzékelőt élő videó-hírcsatornán.
