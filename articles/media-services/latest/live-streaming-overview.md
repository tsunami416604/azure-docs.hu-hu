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
ms.date: 01/22/2019
ms.author: juliako
ms.openlocfilehash: e7e5ddf06fc98f13b9848bf5ede208eef47efd69
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886662"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Élő Stream a az Azure Media Services v3

Az Azure Media Services lehetővé teszi, hogy az ügyfeleknek az Azure-felhőben lévő események élő közvetítésére. A Media Services élő események streamelése, a következőkre lesz szüksége:  

- Egy kamera, amely az élő esemény rögzítéséhez.
- Egy élő videókódoló, amely a kamera (vagy egy másik eszközt, például egy laptop) jelek alakítja át, amelyet elküld a Media Services-hírcsatorna hozzájárulás. A közreműködői hírcsatorna hirdetési, például a SCTE – 35 jelölők kapcsolatos jelek is tartalmazhatnak.
- A Media Services szolgáltatásban, amelyek lehetővé teszik, hogy, összetevők előzetes verzió, csomag, jegyezze fel, titkosítása és az élő esemény szórási, az ügyfelek számára, vagy egy CDN későbbi terjesztés.

Ez a cikk részletes nyújt útmutatást, és diagramokat, a fő összetevőkről az élő adások online közvetítése a Media Services részt tartalmaz.

## <a name="live-streaming-workflow"></a>Élő adatfolyam-továbbítási munkafolyamat

Az egy élő adatfolyam-továbbítási munkafolyamat lépései a következők:

1. Győződjön meg arról, hogy a **folyamatos átviteli végponton** fut-e. 
2. Hozzon létre egy **élő esemény**. <br/>Az esemény létrehozásakor megadhat autostart azt. Másik lehetőségként megkezdése az eseményt, amikor készen áll a streamelés elindításához.<br/> Autostart Ha értéke igaz, az élő esemény fogja elindítani a megfelelő létrehozása után. Ez azt jelenti, a számlázási elindul, amint az élő esemény elindult. Leállítás explicit módon kell meghívnia, további számlázási megállítani az élő esemény erőforráson. További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).
3. A betöltés URL-címe és a konfigurálása a helyszíni kódolót, a hírcsatorna-hozzájárulás küldése az URL-cím használatával.<br/>Lásd: [élő kódolók ajánlott](recommended-on-premises-live-encoders.md).
4. Előnézeti URL-címére, és a segítségével ellenőrizheti, hogy a kódoló a bemeneti ténylegesen fogadja.
5. Hozzon létre egy új **eszköz** objektum.
6. Hozzon létre egy **élő kimeneti** , és használja az Ön által létrehozott objektum nevét.<br/>A **élő kimeneti** archiválja a streamet, a **eszköz**.
7. Hozzon létre egy **Streamelési lokátor** a beépített **Streamelési házirend** típusokat.<br/>Ha azt tervezi, a tartalmak, tekintse át a [Content protection áttekintése](content-protection-overview.md).
8. Az útvonalak listájában a **Streamelési lokátor** visszatéréshez használandó URL-címek (ezek a determinisztikus).
9. A gazdanevének beszerzése a **folyamatos átviteli végponton** érkező adatfolyam szeretné.
10. Az URL-címet a 8. lépés kombinálva a teljes URL-Címének lekéréséhez 9. lépésben az állomásnevet.
11. Ha szeretné állítani, így a **élő esemény** megtekinthető, le kell állítania az esemény-és törlése a **Streamelési lokátor**.<br/>További információkért lásd: a [élő streamelési oktatóanyag](stream-live-tutorial-with-api.md).

## <a name="overview-of-main-components"></a>Fő összetevőinek áttekintése

A Media Services igény szerinti vagy élő adatfolyamot továbbítani kell rendelkeznie kell legalább egy [Streamvégpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints). A Media Services-fiók létrehozásakor egy **alapértelmezett** Streamvégpontok bekerül a fiókhoz a **leállítva** állapota. Indítsa el a Streamvégpontok, ahonnan a felhasználóknak a tartalmak streamelésére kell. Használhatja az alapértelmezett **folyamatos átviteli végponton**, vagy hozzon létre egy másik testre szabott **folyamatos átviteli végponton** a szükséges konfiguráció és a CDN-beállításokkal. Dönthet úgy, hogy engedélyezze a több **adatfolyam-továbbítási végpontok**, mindegyiknél különböző CDN célzás, és a egy egyedi eszköznév tartalom továbbításának biztosítása. 

A Media Services szolgáltatásban [élő események](https://docs.microsoft.com/rest/api/media/liveevents) feldolgozására és feldolgozása az élő videóközvetítési felelősek. Amikor egy élő eseményt hoz létre, bemeneti végpont jön létre, használhatja élő jelet küld a távoli kódoló. A távoli az élő kódoló küld a hozzájárulás hírcsatorna, amelyek bemeneti végpont használatával vagy a [RTMP](https://www.adobe.com/devnet/rtmp.html) vagy [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (töredékes-MP4) protokollt. A Smooth Streaming betöltési protokollt, a támogatott URL-sémák a `http://` vagy `https://`. Az az RTMP betöltési protokollt, a támogatott URL-sémák a `rtmp://` vagy `rtmps://`. További információkért lásd: [élő kódolók streamelési ajánlott](recommended-on-premises-live-encoders.md).<br/>
Amikor létrehozza a **élő esemény**, a következő formátumok egyikében megadhatja az engedélyezett IP-címek: IpV4-cím 4 számjegyből, CIDR-címtartományt.

Miután a **élő esemény** fogadása a csatorna közreműködői elindul, használhatja az előzetes verziójú végpont (előzetes verzió URL-cím és ellenőrzéséhez, hogy azért küldtük Önnek, az élő stream további közzététel előtt. Miután ellenőrizte, hogy az előzetes verzió adatfolyam jó, használhatja a videókhoz elérhetővé tenni az élő stream a kézbesítési egy vagy több (előre létrehozott) keresztül **adatfolyam-továbbítási végpontok**. Ennek érdekében a, hozzon létre egy új [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs) a a **élő esemény**. 

A **élő kimeneti** objektum olyan, mintha egy felvevő, amely a tényleges és jegyezze fel az élő stream a Media Services-fiók egy adategységbe. A fiók csatlakozik a tárolóba, az eszköz erőforrás által meghatározott Azure Storage-fiókba felvett tartalmát maradnak.  A **élő kimeneti** is lehetővé teszi, hogy szabályozza a kimenő élő streamet, például az archív felvétel (például a felhőalapú DVR-Funkciókkal kapacitásának) mennyi az adatfolyam maradjanak néhány tulajdonságát. Az archívum a lemezen egy kör alakú archive "időszak", amely csak a megadott tartalom mennyisége tárolja a **archiveWindowLength** tulajdonságát a **élő kimeneti**. Ebben az ablakban kívülre eső tartalom automatikusan törlődik a storage-tárolóból, és nem állítható helyre. Létrehozhat több **élő kimenetek** (legfeljebb három maximális) a egy **élő esemény** másik archiválási hosszúságok és beállításokkal.  

A Media Services használatával igénybe veheti **dinamikus csomagolási**, amely lehetővé teszi az élő adatstreameket és előzetes verzió [MPEG DASH, HLS és Smooth Streaming formátumban](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) hírcsatorna a hozzájárulását a hogy küldjön a szolgáltatásnak. A nézők vissza az élő stream továbbítása bármely HLS, DASH vagy Smooth Streaming kompatibilis lejátszók játszhatja le. Használhat [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) a web- vagy mobilalkalmazásokat, hogy a stream bármelyik ezeket a protokollokat.

A Media Services lehetővé teszi, hogy a tartalom, dinamikusan titkosítja (**a dinamikus titkosítás**) Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. A Media Services az AES-kulcsok és a DRM-licencek kézbesítéséhez az arra jogosult ügyfelek szolgáltatást is nyújt. A Media Services a tartalom titkosítása a további információkért lásd: [védelme tartalom áttekintése](content-protection-overview.md)

Igény szerint is alkalmazhatók a dinamikus szűrés, amely nyomon követi, formátum, bitsebességre való átkódolása és bemutató idő windows a játékosok megismerése által küldött száma használható. További információkért lásd: [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).

Az élő streameléshez v3 új képességekre vonatkozó további információkért lásd: [Migrálási útmutató segítséget nyújt a Media Services v2 áthelyezését v3](migrate-from-v2-to-v3.md).

## <a name="live-event-types"></a>Élő események típusai

A [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: csatlakoztatott mind az élő kódolás. 

### <a name="pass-through"></a>Továbbítás

![az átmenő](./media/live-streaming/pass-through.png)

Az átmenő használatakor **élő esemény**, amelyeket összeköthet a helyszíni élő kódoló készítése több sávszélességű video-adatfolyamot, és elküldeni, mint a hozzájárulás hírcsatorna az élő esemény (protokollal RTMP vagy töredékes MP4). Az élő esemény majd sorozatéhoz keresztül a bejövő video-adatfolyamok további feldolgozás nélkül. Ilyen egy csatlakoztatott videókhoz optimalizált hosszú ideig futó élő eseményeket vagy 24 x 365 lineáris élő adatfolyam. Az ilyen típusú élő esemény létrehozásakor adja meg a None (LiveEventEncodingType.None).

Elküldheti a hozzájárulás hírcsatorna felbontás akár 4 K, illetve a keret 60 aránya keretek/másodperc H.264/AVC vagy H.265/HEVC videokodekek és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hang kodek.  Tekintse meg a [élő esemény-típusok összehasonlítása és korlátozások](live-event-types-comparison.md) további részleteivel.

> [!NOTE]
> Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
> 

Egy élő példa a [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![Live encoding](./media/live-streaming/live-encoding.png)

Valós idejű kódolás a Media Services használatakor állíthatók be a helyszíni élő kódoló egy egyféle sávszélességű videó elküldeni a hozzájárulás hírcsatorna az élő esemény (RTMP vagy Fragmented-Mp4 protokoll használatával). Az élő esemény kódol, hogy a bejövő egyféle sávszélességű adatfolyamot a egy [több sávszélességű video-adatfolyamot](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), elérhetővé tesz a kézbesítési lejátszására protokollok, mint például az MPEG-DASH, HLS és Smooth Streaming-eszközöket. Az ilyen típusú élő esemény létrehozásakor adja meg a kódolási típusként **Standard** (LiveEventEncodingType.Standard).

A hírcsatorna legfeljebb 30 képkocka/másodperc, a videó kodek H.264/AVC és AAC keret válthatnak 1080 képpont felbontású hozzájárulás küldése (AAC-LC, HE-AACv1 vagy HE-AACv2) hang kodek. Tekintse meg a [élő esemény-típusok összehasonlítása és korlátozások](live-event-types-comparison.md) további részleteivel.

## <a name="live-event-types-comparison"></a>Élő esemény típusok összehasonlítása

A következő cikkben egy táblát tartalmaz, amely összehasonlítja a két élő esemény típusú szolgáltatások: [Összehasonlítás](live-event-types-comparison.md).

## <a name="live-output"></a>Élő kimenet

A [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs) lehetővé teszi, hogy szabályozza a kimenő élő streamet, mekkora az adatfolyam rögzíti (például a kapacitás a felhőbeli DVR), és -e a nézők megkezdheti a élő streamet például tulajdonságait. Közötti kapcsolat egy **élő esemény** és a hozzá tartozó **élő kimeneti**s kapcsolat hasonlít a hagyományos televíziós adás, amelynek során egy csatornát (**élő esemény**) egy videó és a egy felvételt állandó adatfolyam jelöli (**élő kimeneti**) egy adott időpont szegmens (például esténként hírek a 18:30:00, 7:00 és 18) hatókörét. Rögzítheti a televízió digitális videót rögzítő (DVR) használatával – az egyenértékű LiveEvents szolgáltatással kezelt ArchiveWindowLength tulajdonságon keresztül. Egy ISO-8601 timespan időtartam (például PTHH:MM:SS), amely meghatározza a DVR kapacitását, és legfeljebb 25 óra legalább 3 percig állítható be.

> [!NOTE]
> **Kimeneti élő**s létrehozás indítása és leállítása, ha törli. Ha töröl a **élő kimeneti**, nem törli az alapul szolgáló **eszköz** és az eszköz a tartalom. 
>
> Miután közzétette a **élő kimeneti** eszköz használatával egy **Streamelési lokátor**, a **élő esemény** (akár a DVR időszak hossza) továbbra is csak megtekinthető a **Streamelési lokátor**a lejárati vagy törlését, amelyik először bekövetkezik.

További információkért lásd: [a felhőalapú DVR-Funkciókkal](live-event-cloud-dvr.md).

## <a name="streaming-endpoint"></a>Streamvégpont

Miután a tárfiókba kerülnek stream a **élő esemény**, megkezdheti a streamelési eseményt hoz létre egy **eszköz**, **élő kimeneti**, és **Streamelési lokátor** . Ezzel archiválja a streamet, és tegye elérhetővé a nézők keresztül a [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints).

A Media Services-fiók létrehozásakor egy alapértelmezett streamvégpontot a fiókhoz leállítva állapotban van hozzáadva. A tartalom streamelésének megkezdéséhez, és a dinamikus csomagolás és a dinamikus titkosítás kihasználásához, a tartalomstreameléshez használt streamvégpont, ahonnan tartalomstreameléshez rendelkezik, a futó állapotot.

## <a name="a-idbilling-live-event-states-and-billing"></a><a id="billing" />Élő esemény állapotok és számlázás

A **élő esemény** kezdődik a számlázás, amint állapotában átkerül **futó**. Az élő esemény, a számlázás megszüntetéséhez akkor állítsa le az élő esemény.

Részletes információkért lásd: [állapotok és számlázási](live-event-states-billing.md).

## <a name="latency"></a>Késés

LiveEvents késéssel kapcsolatos részletes információkért lásd: [késés](live-event-latency.md).

## <a name="next-steps"></a>További lépések

- [Élő esemény típusok összehasonlítása](live-event-types-comparison.md)
- [Állapotok és számlázás](live-event-states-billing.md)
- [Késés](live-event-latency.md)
