---
title: Élő videó-elemzés IoT Edge GYIK-ban – Azure
description: Ez a témakör válaszokat ad az élő videók elemzésére IoT Edge GYIK-ben.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011515"
---
# <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYIK)

Ez a témakör válaszokat ad az élő videók elemzésére IoT Edge GYIK-ben.

## <a name="general"></a>Általános kérdések

Mik azok a rendszerváltozók, amelyek használhatók a Graph topológia definíciójában?

|Változó   |Leírás|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Egy azonnali időpontot jelöl, amely általában dátum és napszak szerint van megadva.|
|System. GraphTopologyName   |Egy Graph-topológiát jelöl, amely egy gráf tervrajzát tartalmazza.|
|System. GraphInstanceName|  Egy Media Graph-példányt jelöl, a paraméter értékeit tartalmazza, és a topológiára hivatkozik.|

## <a name="configuration-and-deployment"></a>Konfigurálás és üzembe helyezés

Telepíthetem a Media Edge-modult egy Windows 10-es eszközre?
    * Igen. Tekintse meg a [Linux-tárolók a Windows 10 rendszerben](/virtualization/windowscontainers/deploy-containers/linux-containers)című cikket.

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Rögzítés az IP-kamera és az RTSP-beállítások alapján

* Speciális SDK-t kell használnom az eszközömön videóstream küldéséhez?
    * Nem. A IoT Edge Live Video Analytics támogatja az adathordozók rögzítését az RTSP Video Streaming Protocol használatával (amely a legtöbb IP-kamera esetében támogatott).
* Leküldhetem a médiát a IoT Edge az RTMP vagy a Smooth (például egy Media Services élő esemény) használatával élő video Analyticsre?
    * Nem. A LVA csak az RTSP-t támogatja a videók IP-kamerákból való rögzítéséhez.
    * Minden olyan kamera, amely támogatja a TCP/HTTP protokollon keresztüli RTSP-átvitelt, működnie kell. 
* Alaphelyzetbe állíthatom vagy frissíthetem az RTSP-forrás URL-címét egy grafikonpéldányon?
    * Igen, ha a Graph-példány inaktív állapotban van.  
* Van elérhető RTSP-szimulátor a tesztelés és a fejlesztés során?
    * Igen. A gyors üzembe helyezési útmutatóban egy [RTSP szimulátor](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge-modul használható, amely támogatja a tanulási folyamatot. Ezt a modult tájékoztató jelleggel biztosítjuk, és előfordulhat, hogy nem mindig érhető el. Javasoljuk, hogy ezt ne használja több órán át. Az éles környezetben történő üzembe helyezés előtt érdemes befektetni a tényleges RTSP-forrással végzett tesztelésbe.
* Támogatott az IP-kamerák ONVIF-felderítése a peremhálózaton?
    * Nem, az eszközök ONVIF-felderítése nem támogatott a peremhálózaton.

## <a name="streaming-and-playback"></a>Folyamatos átvitel és lejátszás

* A szélétől az AMS-re rögzített eszközök a Media Services streaming Technologies, például a HLS vagy a DASH használatával lesznek lejátszva?
    * Igen. A rögzített adategységek adatfolyamként használhatók, mint bármely más eszköz Azure Media Services. A tartalom továbbításához létre kell hoznia egy folyamatos átviteli végpontot és a futó állapotot. A standard szintű folyamatos átviteli lokátor létrehozási folyamata hozzáférést biztosít egy HLS vagy DASH-jegyzékfájlhoz, amely bármely alkalmas Player-keretrendszerhez elérhető. A közzétételi HLS vagy kötőjel-jegyzékfájlok létrehozásával kapcsolatos részletekért lásd: [dinamikus csomagolás](../latest/dynamic-packaging-overview.md).
* Használhatom a Media Services szabványos tartalomvédelem és DRM funkcióit archivált eszközön?
    * Igen. Az összes szabványos dinamikus titkosítási tartalomvédelem és DRM-funkció a Media Graph-ból rögzített eszközökön használható.
* Milyen játékosokat használhatok a rögzített eszközök tartalmának megtekintésére?
   * Minden olyan szabványos lejátszó támogatott, amely támogatja a megfelelő Apple HTTP Live Streaming (HLS) 3-as vagy 4-es verziójú verziót. Emellett minden olyan lejátszó is támogatott, amely kompatibilis MPEG-DASH-lejátszásra képes.
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
* Milyen korlátozások vonatkoznak a Media Graph-eszközök továbbítására?
    * A Media Graph élő vagy rögzített eszközének adatfolyam-továbbítása ugyanazt a nagy léptékű infrastruktúrát és folyamatos átviteli végpontot használja, amely Media Services támogatja az igény szerinti és élő közvetítést a Media & Entertainment, OTT és a Broadcast ügyfelek számára. Ez azt jelenti, hogy gyorsan és egyszerűen engedélyezheti a Azure CDN, a Verizon vagy a Akamai számára, hogy a tartalmat a közönség számára kis méretekben, vagy akár akár több millióra is kiterjesztheti a forgatókönyvtől függően.

    A tartalom az Apple HTTP Live Streaming (HLS) vagy az MPEG-DASH használatával is továbbítható.

## <a name="monitoring-and-metrics"></a>Monitorozás és mérőszámok

* Nyomon követhető a peremhálózati adathordozó-gráf a Event Grid használatával?
    * Nem. Jelenleg Event Grid nem támogatott.
* Használhatom Azure Monitor a saját adathordozó-diagramok állapotának, metrikáinak és teljesítményének megtekintéséhez a felhőben vagy a peremen?
    * Nem.
* Vannak olyan eszközök, amelyek megkönnyítik a Media Services IoT Edge modul figyelését?
    * A Visual Studio Code támogatja az Azure IoT Tools bővítményt, amely lehetővé teszi az LVAEdge-modul végpontok egyszerű figyelését. Ezzel az eszközzel gyorsan megkezdheti az "események" IoT Hub beépített végpontjának figyelését, és megtekintheti a peremhálózati eszközről a felhőbe irányított következtetéseket. 

    Ezen felül a bővítmény használatával szerkesztheti a LVAEdge modulhoz tartozó Twin modult a Media Graph beállításainak módosításához.

További információ: [figyelés és naplózás](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Számlázás és rendelkezésre állás

* Hogyan történik a LiveVideo Analytics IoT Edge számlázása?
    * A részletekért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>További lépések

[Gyors útmutató: első lépések – élő videó-elemzés IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
