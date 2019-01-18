---
title: Az Azure Media Services segítségével élő Streamelés áttekintése |} A Microsoft Docs
description: Ez a cikk nyújt áttekintést élő adatfolyam továbbítása az Azure Media Services v3 használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: 6f7c6c2265fe13eb50aa900e9a51e11edfd90201
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382079"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Élő Stream a az Azure Media Services v3

Az Azure Media Services lehetővé teszi, hogy az ügyfeleknek az Azure-felhőben lévő események élő közvetítésére. A Media Services élő események streamelése, a következőkre lesz szüksége:  

- Egy kamera, amely az élő esemény rögzítéséhez.
- Egy élő videókódoló, amely a kamera (vagy egy másik eszközt, például egy laptop) jelek alakítja át, amelyet elküld a Media Services-hírcsatorna hozzájárulás. A közreműködői hírcsatorna hirdetési, például a SCTE – 35 jelölők kapcsolatos jelek is tartalmazhatnak.
- A Media Services szolgáltatásban, amelyek lehetővé teszik, hogy, összetevők előzetes verzió, csomag, jegyezze fel, titkosítása és az élő esemény szórási, az ügyfelek számára, vagy egy CDN későbbi terjesztés.

Ez a cikk részletes nyújt útmutatást, és diagramokat, a fő összetevőkről az élő adások online közvetítése a Media Services részt tartalmaz.

## <a name="live-streaming-workflow"></a>Élő adatfolyam-továbbítási munkafolyamat

Az egy élő adatfolyam-továbbítási munkafolyamat lépései a következők:

1. Hozzon létre egy **élő esemény**.
2. Hozzon létre egy új **eszköz** objektum.
3. Hozzon létre egy **élő kimeneti** , és használja az Ön által létrehozott objektum nevét.
4. Hozzon létre egy **Streamelési házirend** és **tartalom kulcs** Ha DRM a tartalmak szeretne.
5. Ha nem használ DRM, hozzon létre egy **Streamelési lokátor** a beépített **Streamelési házirend** típusokat.
6. Az útvonalak listájában a **Streamelési házirend** visszatéréshez használandó URL-címek (ezek a determinisztikus).
7. A gazdanevének beszerzése a **folyamatos átviteli végponton** érkező adatfolyam szeretné (Győződjön meg arról, hogy a folyamatos átviteli végponton fut). 
8. Az URL-címet, a 6. lépés kombinálva a a teljes URL-Címének lekéréséhez 7. lépésben az állomásnevet.
9. Ha szeretné állítani, így a **élő esemény** megtekinthető, le kell állítania az esemény streamelési törlésével a **Streamelési lokátor**.

További információkért lásd: egy [élő streamelési oktatóanyag](stream-live-tutorial-with-api.md) , amely alapján a [élő .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live) minta.

## <a name="overview-of-main-components"></a>Fő összetevőinek áttekintése

A Media Services igény szerinti vagy élő adatfolyamot továbbítani kell rendelkeznie kell legalább egy [Streamvégpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints). A Media Services-fiók létrehozásakor egy **alapértelmezett** Streamvégpontok bekerül a fiókhoz a **leállítva** állapota. Indítsa el a Streamvégpontok, ahonnan a felhasználóknak a tartalmak streamelésére kell. Használhatja az alapértelmezett **Streamvégpontok**, vagy hozzon létre egy másik testre szabott **Streamvégpontok** a szükséges konfiguráció és a CDN-beállításokkal. Ahhoz, hogy több Streamvégpontok, mindegyiknél célzó különböző CDN és a egy egyedi eszköznév megadása a tartalom továbbításának dönthet. 

A Media Services szolgáltatásban [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) feldolgozására és feldolgozása az élő videóközvetítési felelősek. Amikor létrehoz egy videókhoz, egy bemeneti végpont jön létre, használhatja élő jelet küld a távoli kódoló. A távoli az élő kódoló küld a hozzájárulás hírcsatorna, amelyek bemeneti végpont használatával vagy a [RTMP](https://www.adobe.com/devnet/rtmp.html) vagy [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (töredékes-MP4) protokollt. A Smooth Streaming betöltési protokollt, a támogatott URL-sémák a `http://` vagy `https://`. Az az RTMP betöltési protokollt, a támogatott URL-sémák a `rtmp://` vagy `rtmps://`. További információkért lásd: [élő kódolók streamelési ajánlott](recommended-on-premises-live-encoders.md).<br/>
Amikor létrehozza a **videókhoz**, a következő formátumok egyikében megadhatja az engedélyezett IP-címek: IpV4-cím 4 számjegyből, CIDR-címtartományt.

Miután a **videókhoz** fogadása a csatorna közreműködői elindul, használhatja az előzetes verziójú végpont (előzetes verzió URL-cím és ellenőrzéséhez, hogy azért küldtük Önnek, az élő stream további közzététel előtt. Miután ellenőrizte, hogy az előzetes verzió adatfolyam jó, használhatja a videókhoz elérhetővé tenni az élő stream a kézbesítési egy vagy több (előre létrehozott) keresztül **Streamvégpontok**. Ennek érdekében a, hozzon létre egy új [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) a a **videókhoz**. 

A **LiveOutput** objektum olyan, mintha egy felvevő, amely a tényleges és jegyezze fel az élő stream a Media Services-fiók egy adategységbe. A fiók csatlakozik a tárolóba, az eszköz erőforrás által meghatározott Azure Storage-fiókba felvett tartalmát maradnak.  A **LiveOuput** is lehetővé teszi, hogy szabályozza a kimenő élő streamet, például az archív felvétel (például a felhőalapú DVR-Funkciókkal kapacitásának) mennyi az adatfolyam maradjanak néhány tulajdonságát. Az archívum a lemezen egy kör alakú archive "időszak", amely csak a megadott tartalom mennyisége tárolja a **archiveWindowLength** tulajdonságát a **LiveOutput**. Ebben az ablakban kívülre eső tartalom automatikusan törlődik a storage-tárolóból, és nem állítható helyre. Létrehozhat több LiveOutputs (legfeljebb három maximális) egy másik archiválási hosszúságok és beállításokkal videókhoz.  

A Media Services használatával igénybe veheti **dinamikus csomagolási**, amely lehetővé teszi az élő adatstreameket és előzetes verzió [MPEG DASH, HLS és Smooth Streaming formátumban](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) hírcsatorna a hozzájárulását a hogy küldjön a szolgáltatásnak. A nézők vissza az élő stream továbbítása bármely HLS, DASH vagy Smooth Streaming kompatibilis lejátszók játszhatja le. Használhat [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) a web- vagy mobilalkalmazásokat, hogy a stream bármelyik ezeket a protokollokat.

A Media Services lehetővé teszi, hogy a tartalom, dinamikusan titkosítja (**a dinamikus titkosítás**) Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. A Media Services az AES-kulcsok és a DRM-licencek kézbesítéséhez az arra jogosult ügyfelek szolgáltatást is nyújt. A Media Services a tartalom titkosítása a további információkért lásd: [védelme tartalom áttekintése](content-protection-overview.md)

Igény szerint is alkalmazhatók a dinamikus szűrés, amely nyomon követi, formátum, bitsebességre való átkódolása és bemutató idő windows a játékosok megismerése által küldött száma használható. További információkért lásd: [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).

### <a name="new-capabilities-for-live-streaming-in-v3"></a>Az élő streameléshez v3 az új képességek

A a v3 API-k a Media Services előnyökkel jár a következő új funkciók:

- Új közel valós idejű módban. További információkért lásd: [késés](live-event-latency.md).
- Továbbfejlesztett RMTP-támogatása (fokozott stabilitás és további forráskód kódoló).
- Biztonságos RTMPS betöltését.<br/>Amikor létrehoz egy videókhoz, 4 kap betöltési URL-címeket. A 4 betöltési URL-címek olyan majdnem teljesen megegyezik, rendelkezik a azonos streamelési token (alkalmazásazonosító), csak a port száma rész nem egyezik. Az URL-címek kettő elsődleges és tartalék RTMPS számára.   
- Akár 24 órát hosszú egy kimeneti adatfolyamba, amely rendelkezik több bitsebességre való átkódolása hírcsatornaelem átkódolása egyféle sávszélességű hozzájárulás a Media Services segítségével élő eseményeket streamelheti. 

## <a name="liveevent-types"></a>Videókhoz típusok

A [videókhoz](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: csatlakoztatott mind az élő kódolás. 

### <a name="pass-through"></a>Továbbítás

![az átmenő](./media/live-streaming/pass-through.png)

A csatlakoztatott videókhoz használatakor támaszkodik a helyszíni élő kódoló készítése több sávszélességű video-adatfolyamot, és küldhet, hogy a hozzájárulását, hírcsatorna a videókhoz (protokollal RTMP vagy töredékes MP4). A videókhoz majd sorozatéhoz keresztül a bejövő video-adatfolyamok további feldolgozás nélkül. Ilyen egy csatlakoztatott videókhoz optimalizált hosszú ideig futó élő eseményeket vagy 24 x 365 lineáris élő adatfolyam. Az ilyen típusú videókhoz létrehozásakor adja meg a None (LiveEventEncodingType.None).

Elküldheti a hozzájárulás hírcsatorna felbontás akár 4 K, illetve a keret 60 aránya keretek/másodperc H.264/AVC vagy H.265/HEVC videokodekek és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hang kodek.  Tekintse meg a [videókhoz-típusok összehasonlítása és korlátozások](live-event-types-comparison.md) további részleteivel.

> [!NOTE]
> Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
> 

Egy élő példa a [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![Live encoding](./media/live-streaming/live-encoding.png)

Valós idejű kódolás a Media Services használatakor állíthatók be a helyszíni élő kódoló egy egyféle sávszélességű videó elküldeni a hozzájárulás hírcsatorna a videókhoz (RTMP vagy Fragmented-Mp4 protokoll használatával). A videókhoz kódol, hogy a bejövő egyféle sávszélességű adatfolyamot a egy [több sávszélességű video-adatfolyamot](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), elérhetővé tesz a kézbesítési lejátszására protokollok, mint például az MPEG-DASH, HLS és Smooth Streaming-eszközöket. Az ilyen típusú videókhoz létrehozásakor adja meg a kódolási típusként **Standard** (LiveEventEncodingType.Standard).

A hírcsatorna legfeljebb 30 képkocka/másodperc, a videó kodek H.264/AVC és AAC keret válthatnak 1080 képpont felbontású hozzájárulás küldése (AAC-LC, HE-AACv1 vagy HE-AACv2) hang kodek. Tekintse meg a [videókhoz-típusok összehasonlítása és korlátozások](live-event-types-comparison.md) további részleteivel.

## <a name="liveevent-types-comparison"></a>Videókhoz típusok összehasonlítása

A következő cikkben egy táblát, amely összehasonlítja a két típusú videókhoz funkciókat tartalmazza: [Összehasonlítás](live-event-types-comparison.md).

## <a name="liveoutput"></a>LiveOutput

A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) lehetővé teszi, hogy szabályozza a kimenő élő streamet, mekkora az adatfolyam rögzíti (például a kapacitás a felhőbeli DVR), és -e a nézők megkezdheti a élő streamet például tulajdonságait. Közötti kapcsolat egy **videókhoz** és a hozzá tartozó **LiveOutput**s kapcsolat hasonlít a hagyományos televíziós adás, amelynek során egy csatornát (**videókhoz**) egy videó és a egy felvételt állandó adatfolyam jelöli (**LiveOutput**) egy adott időpont szegmens (például esténként hírek a 18:30:00, 7:00 és 18) hatókörét. Rögzítheti a televízió digitális videót rögzítő (DVR) használatával – az egyenértékű LiveEvents szolgáltatással kezelt ArchiveWindowLength tulajdonságon keresztül. Egy ISO-8601 timespan időtartam (például PTHH:MM:SS), amely meghatározza a DVR kapacitását, és legfeljebb 25 óra legalább 3 percig állítható be.

> [!NOTE]
> **LiveOutput**s létrehozás indítása és leállítása, ha törli. Ha töröl a **LiveOutput**, nem törli az alapul szolgáló **eszköz** és a tartalom az eszközben. 
>
> Miután közzétette **Streamelési lokátor**s tartozó objektum a **LiveOutput**, az esemény (akár a DVR időszak hossza) továbbra is meg szeretné jeleníteni a befejezési idő, amíg a **Streamelési lokátor**  vagy a mai napig tartó, amikor törli a lokátor, amelyik előbb bekövetkezik.   

További információkért lásd: [a felhőalapú DVR-Funkciókkal](live-event-cloud-dvr.md).

## <a name="streamingendpoint"></a>StreamingEndpoint

Miután a tárfiókba kerülnek adatfolyam a **videókhoz**, megkezdheti a streamelési eseményt hoz létre egy **eszköz**, **LiveOutput**, és **StreamingLocator** . Ezzel archiválja a streamet, és tegye elérhetővé a nézők keresztül a [Streamvégpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints).

A Media Services-fiók létrehozásakor egy alapértelmezett streamvégpontot a fiókhoz leállítva állapotban van hozzáadva. A tartalom streamelésének megkezdéséhez, és a dinamikus csomagolás és a dinamikus titkosítás kihasználásához, a tartalomstreameléshez használt streamvégpont, ahonnan tartalomstreameléshez rendelkezik, a futó állapotot.

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />Videókhoz állapotok és számlázás

A videókhoz kezdődik a számlázás, amint állapotában átkerül **futó**. Le szeretné állítani a számlázás a videókhoz, akkor állítsa le a videókhoz.

Részletes információkért lásd: [állapotok és számlázási](live-event-states-billing.md).

## <a name="latency"></a>Késés

LiveEvents késéssel kapcsolatos részletes információkért lásd: [késés](live-event-latency.md).

## <a name="next-steps"></a>További lépések

- [Videókhoz típusok összehasonlítása](live-event-types-comparison.md)
- [Állapotok és számlázás](live-event-states-billing.md)
- [Késés](live-event-latency.md)
