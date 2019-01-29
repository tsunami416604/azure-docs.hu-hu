---
title: Élő adások online közvetítése az Azure Media Services – élő események és élő kimenetek fogalmak |} A Microsoft Docs
description: Ez a cikk az Azure Media Services v3 élő streamelési fogalmak áttekintése biztosít.
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
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: b5bd08219c6e48f07e93d7704216e813d75dcd72
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099917"
---
# <a name="live-events-and-live-outputs"></a>Élő események és élő kimenetek

Az Azure Media Services lehetővé teszi, hogy az ügyfeleknek az Azure-felhőben lévő események élő közvetítésére. Konfigurálása az élő események streamelése a Media Services v3-as, meg kell ismernie a ebben a cikkben tárgyalt fogalmakat:

* [Élő események](#live-events)
* [Élő események típusai](#live-vent-types)
* [Élő esemény típusok összehasonlítása](#live-event-types-comparison)
* [Élő esemény-létrehozási beállítások](#live-event-creation-options)
* [Élő esemény betöltési URL-címek](#live-event-ingest-urls)
* [Élő esemény előnézeti URL-címe](#live-event-preview-url)
* [Élő kimenetek](#live-outputs).

## <a name="live-events"></a>Élő események

[Élő események](https://docs.microsoft.com/rest/api/media/liveevents) feldolgozására és feldolgozása az élő videóközvetítési felelősek. Amikor egy élő eseményt hoz létre, bemeneti végpont jön létre, használhatja élő jelet küld a távoli kódoló. A távoli az élő kódoló küld a hozzájárulás hírcsatorna, amelyek bemeneti végpont használatával vagy a [RTMP](https://www.adobe.com/devnet/rtmp.html) vagy [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (töredékes-MP4) protokollt. A Smooth Streaming betöltési protokollt, a támogatott URL-sémák a `http://` vagy `https://`. Az az RTMP betöltési protokollt, a támogatott URL-sémák a `rtmp://` vagy `rtmps://`. 

## <a name="live-event-types"></a>Élő események típusai

A [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: csatlakoztatott mind az élő kódolás. 

### <a name="pass-through"></a>Továbbítás

![az átmenő](./media/live-streaming/pass-through.png)

Az átmenő használatakor **élő esemény**, amelyeket összeköthet a helyszíni élő kódoló készítése több sávszélességű video-adatfolyamot, és elküldeni, mint a hozzájárulás hírcsatorna az élő esemény (protokollal RTMP vagy töredékes MP4). Az élő esemény majd sorozatéhoz keresztül a bejövő video-adatfolyamok további feldolgozás nélkül. Ilyen egy csatlakoztatott videókhoz optimalizált hosszú ideig futó élő eseményeket vagy 24 x 365 lineáris élő adatfolyam. Az ilyen típusú élő esemény létrehozásakor adja meg a None (LiveEventEncodingType.None).

Elküldheti a hozzájárulás hírcsatorna felbontás akár 4 K, illetve a keret 60 aránya keretek/másodperc H.264/AVC vagy H.265/HEVC videokodekek és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hang kodek.  Tekintse meg a [élő esemény-típusok összehasonlítása](live-event-types-comparison.md) további részleteivel.

> [!NOTE]
> Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
> 

Tekintse meg a .NET-példakód az [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![Live encoding](./media/live-streaming/live-encoding.png)

Valós idejű kódolás a Media Services használatakor állíthatók be a helyszíni élő kódoló egy egyféle sávszélességű videó elküldeni a hozzájárulás hírcsatorna az élő esemény (RTMP vagy Fragmented-Mp4 protokoll használatával). Az élő esemény kódol, hogy a bejövő egyféle sávszélességű adatfolyamot a egy [több sávszélességű video-adatfolyamot](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), elérhetővé tesz a kézbesítési lejátszására protokollok, mint például az MPEG-DASH, HLS és Smooth Streaming-eszközöket. Az ilyen típusú élő esemény létrehozásakor adja meg a kódolási típusként **Standard** (LiveEventEncodingType.Standard).

A hírcsatorna legfeljebb 30 képkocka/másodperc, a videó kodek H.264/AVC és AAC keret válthatnak 1080 képpont felbontású hozzájárulás küldése (AAC-LC, HE-AACv1 vagy HE-AACv2) hang kodek. Tekintse meg a [élő esemény-típusok összehasonlítása](live-event-types-comparison.md) további részleteivel.

## <a name="live-event-creation-options"></a>Élő esemény-létrehozási beállítások

Amikor egy élő eseményt hoz létre, megadhatja a következő beállításokat:

* Az élő esemény az adatfolyam-továbbítási protokoll (jelenleg az RTMP és Smooth Streaming protokollokat támogatottak).<br/>Az élő esemény vagy kapcsolódó Live Kimenetétől futása közben, a protokollbeállítás nem módosítható. Ha eltérő protokollok használatára van szüksége, létre kell hoznia az egyes streamprotokollokhoz külön élő esemény.  
* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja, hogy az IP-címek, amelyek jogosultak videókat az élő esemény betölteni. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.<br/>Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címeket kell lennie a következő formátumok egyikében: IpV4-cím 4 számjegyből, CIDR-címtartományt.
* Az esemény létrehozásakor megadhatja, hogy az automatikusan induljon el. <br/>Ha autostart értéke igaz, az élő esemény létrehozása után fog elindulni. Ez azt jelenti, a számlázási elindul, amint az élő esemény elindult. Leállítás explicit módon kell meghívnia, további számlázási megállítani az élő esemény erőforráson. Másik lehetőségként megkezdése az eseményt, amikor készen áll a streamelés elindításához. 

    További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>Élő esemény betöltési URL-címek

Az élő esemény létrehozása után megjelenik a betöltési URL-címek, amelyek ezeket a helyszíni élő kódoló kell megadnia. Az élő kódoló bemeneti élő streamet ezen URL-címeket használ. További információkért lásd: [ajánlott a helyszíni élő kódolók](recommended-on-premises-live-encoders.md). 

Használhatja a nem a kreatív URL-címeket vagy a kreatív URL-címeket. 

* Non-vanity URL

    Nem – személyes URL-je az AMS v3-as az alapértelmezett mód. Vélhetően az élő esemény gyorsan azonban betöltési URL-címe is ismert, csak ha az élő esemény elindult. Az URL-cím változik, ha leállításához/elindításához az élő esemény. <br/>Nem – személyes olyan esetekben hasznos, amikor egy végfelhasználó szeretne streamelni, ahol az alkalmazás szeretne egy élő esemény ASAP beolvasása és kellene egy dinamikus betöltési URL-címe nem probléma egy adott alkalmazás használatát.
* Személyes URL-címe

    Személyes módban van az előnyben részesített nagy adathordozó hardvert használó műsorszolgáltatók kódolók szórási, és nem szeretné újra azok kódolók konfigurálása, ha az élő esemény indítása. Szeretnék egy prediktív betöltési URL-címe, amely nem változik az idő függvényében.

> [!NOTE] 
> A bemeneti URL-címet kell prediktív használja a "személyes" módot, majd továbbítja a saját hozzáférési jogkivonat (egy véletlenszerű lexikális elem szerepel az URL-cím elkerülésére) szüksége.

### <a name="live-ingest-url-naming-rules"></a>Élő betöltési URL-elnevezési szabályok

A *véletlenszerű* az alábbi karakterláncot az a 128 bites hexadecimális szám (amely áll, amelyek 32 karakter hosszú lehet a 0-9-f).<br/>
A *hozzáférési jogkivonat* az alábbi, a rögzített URL-cím megadása szükséges. Emellett akkor is 128 bites hexadecimális szám.

#### <a name="non-vanity-url"></a>Non-vanity URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/<access token>`
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/<access token>`
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`
`https://<random 128bit hex string>.channel.media.azure.net/<access token>/ingest.isml`

#### <a name="vanity-url"></a>Személyes URL-címe

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/<access token>`
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/<access token>`
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/<access token>`

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<access token>/ingest.isml`

## <a name="live-event-preview-url"></a>Élő esemény előnézeti URL-címe

Miután a **élő esemény** fogadása a csatorna közreműködői elindul, használhatja az előzetes verziójú végpont és ellenőrzéséhez, hogy azért küldtük Önnek, az élő stream további közzététele előtt. Miután ellenőrizte, hogy az előzetes verzió adatfolyam jó, használhatja a videókhoz elérhetővé tenni az élő stream a kézbesítési egy vagy több (előre létrehozott) keresztül **adatfolyam-továbbítási végpontok**. Ennek érdekében a, hozzon létre egy új [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs) a a **élő esemény**. 

> [!IMPORTANT]
> Győződjön meg arról, hogy a videó áramlik az előnézeti URL-címet a folytatás előtt!

## <a name="live-outputs"></a>Élő kimenetek

Miután a stream az élő esemény beérkeznek, megkezdése a streamelési eseményt hoz létre egy [eszköz](https://docs.microsoft.com/rest/api/media/assets), [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs), és [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators). Élő kimeneti fog archiválja a streamet, és tegye elérhetővé a nézők keresztül a [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Létrehozás kimenetek start Live, és állítsa le a törlésekor. Az élő kimeneti törlésekor nem törlünk az alapul szolgáló eszköz és az eszköz tartalmát. 

Közötti kapcsolat egy **élő esemény** és a hozzá tartozó **élő kimenetek** van hagyományos televíziós adás, amellyel egy csatornát (**élő esemény**) állandó jelöli Stream-videó és a egy felvételt (**élő kimeneti**) egy adott időpont szegmens (például esténként hírek a 18:30:00, 7:00 és 18) hatókörét. Rögzítheti a televízió digitális videót rögzítő (DVR) használatával – az ennek megfelelő szolgáltatást, az élő események keresztül kezeli a **ArchiveWindowLength** tulajdonság. Egy ISO-8601 timespan időtartam (például PTHH:MM:SS), amely meghatározza a DVR kapacitását, és legfeljebb 25 óra legalább 3 percig állítható be.

A **élő kimeneti** objektum olyan, mintha egy felvevő, amely a tényleges és jegyezze fel az élő stream a Media Services-fiók egy adategységbe. A fiók csatlakozik a tárolóba, az eszköz erőforrás által meghatározott Azure Storage-fiókba felvett tartalmát maradnak. A **élő kimeneti** is lehetővé teszi, hogy szabályozza a kimenő élő streamet, például az archív felvétel (például a felhőalapú DVR-Funkciókkal kapacitásának) mennyi az adatfolyam maradjanak, és e megkezdheti a megtekintők néhány tulajdonságát az élő stream megtekintését. Az archívum a lemezen egy kör alakú archive "időszak", amely csak a megadott tartalom mennyisége tárolja a **archiveWindowLength** tulajdonságát a **élő kimeneti**. Ebben az ablakban kívülre eső tartalom automatikusan törlődik a storage-tárolóból, és nem állítható helyre. Létrehozhat több **élő kimenetek** (legfeljebb három maximális) a egy **élő esemény** másik archiválási hosszúságok és beállításokkal.  

Miután közzétette a **élő kimeneti**a **eszköz** használatával egy **Streamelési lokátor**, a **élő esemény** lesz (akár a DVR időszak hossza) továbbra is megtekinthető a Streamelési lokátor lejárati vagy törlését, amelyiket hamarabb.

További információkért lásd: [használatával egy felhőalapú DVR-Funkciókkal](live-event-cloud-dvr.md).

## <a name="next-steps"></a>További lépések

- [Élő események streamelése](live-streaming-overview.md)
- [Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)
