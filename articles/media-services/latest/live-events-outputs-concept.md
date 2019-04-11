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
ms.date: 03/30/2019
ms.author: juliako
ms.openlocfilehash: 00dab8381c26a6331dd325eacd4a550892bd3411
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469811"
---
# <a name="live-events-and-live-outputs"></a>Élő események és élő kimenetek

Az Azure Media Services lehetővé teszi, hogy az ügyfeleknek az Azure-felhőben lévő események élő közvetítésére. Az élő események streamelése konfigurálása a Media Services v3-as, szüksége ebben a cikkben tárgyalt fogalmakat megértéséhez. <br/>A lap jobb oldalán a szakaszok listája megjelenik.

## <a name="live-events"></a>Élő események

Az [élő események](https://docs.microsoft.com/rest/api/media/liveevents) az élő videóadatok betöltését és feldolgozását végzik. Amikor élő eseményt hoz létre, olyan bemeneti végpont jön létre, amellyel élő jelet küldhet egy távoli kódolóról. A távoli élő kódoló a bemeneti adatokat erre a bemeneti végpontra küldi az [RTMP](https://www.adobe.com/devnet/rtmp.html) vagy a [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (darabolt MP4) protokollal. A Smooth Streaming betöltési protokollt, a támogatott URL-sémák a `http://` vagy `https://`. Az az RTMP betöltési protokollt, a támogatott URL-sémák a `rtmp://` vagy `rtmps://`. 

## <a name="live-event-types"></a>Élő események típusai

A [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: csatlakoztatott mind az élő kódolás. 

### <a name="pass-through"></a>Továbbítás

![az átmenő](./media/live-streaming/pass-through.svg)

A továbbított **élő esemény** használatakor a helyszíni élő kódoló használatával létrehoz egy többféle sávszélességű videóstreamet, amelyet elküld az élő eseménynek bemeneti adatként (RTMP vagy darabolt MP4 protokollal). Az élő esemény ezután további feldolgozás nélkül továbbítja a bejövő videóstreameket. Az ilyen továbbított élő események hosszú ideig futó élő eseményekhez vagy folyamatos (napi 24 órás, 365 napos) lineáris élő streameléshez vannak optimalizálva. Az ilyen típusú élő események létrehozásakor adja meg a None (LiveEventEncodingType.None) kódolási típust.

A bemeneti adatokat legfeljebb 4K felbontással és 60 képkocka/másodperc képkockasebességgel továbbíthatja, H.264/AVC vagy H.265/HEVC videokodekkel és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hangkodekkel.  További részleteket [az élő események típusainak összehasonlításában](live-event-types-comparison.md) talál.

> [!NOTE]
> Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
> 

Tekintse meg a .NET-példakód az [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![Live encoding](./media/live-streaming/live-encoding.svg)

Amikor élő kódolást alkalmaz a Media Services használatával, úgy konfigurálja a helyszíni élő kódolót, hogy egyféle sávszélességű videót küldjön bemeneti adatként az élő eseménynek (RTMP vagy darabolt Mp4 protokollal). Az élő esemény egy [többféle sávszélességű videóstreammé](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) kódolja ezt a bejövő egyféle sávszélességű streamet, így az olyan protokollokkal továbbítható a lejátszóeszközökre, mint az MPEG-DASH, a HLS és a Smooth Streaming. Az ilyen típusú élő események létrehozásakor adja meg a **Standard** kódolási típust (LiveEventEncodingType.Standard).

A bemeneti adatokat legfeljebb 1080p felbontással és 30 képkocka/másodperc képkockasebességgel továbbíthatja H.264/AVC videokodekkel és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hangkodekkel. További részleteket [az élő események típusainak összehasonlításában](live-event-types-comparison.md) talál.

Élő kódolás használatakor (amikor az élő esemény beállítása **Standard**), a kódolási előbeállítás határozza meg, hogyan van többféle bitsebességbe vagy rétegbe kódolva a bejövő stream. További információkat a [rendszerbeállítás-készletekkel](live-event-types-comparison.md#system-presets) kapcsolatos szakaszban talál.

> [!NOTE]
> Jelenleg az élő események Standard típusához az egyetlen engedélyezett előbeállítási érték a *Default720p*. Ha egyéni élő kódolási előbeállítást kell használnia, írjon az amshelp@microsoft.com címre. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Győződjön meg arról, hogy csak egy réteg 720p sebességű, és hogy legfeljebb 6 réteg van.

## <a name="live-event-creation-options"></a>Élő esemény-létrehozási beállítások

Amikor egy élő eseményt hoz létre, megadhatja a következő beállításokat:

* Az élő esemény az adatfolyam-továbbítási protokoll (jelenleg az RTMP és Smooth Streaming protokollokat támogatottak).<br/>Az élő esemény vagy kapcsolódó Live Kimenetétől futása közben, a protokollbeállítás nem módosítható. Ha eltérő protokollok használatára van szüksége, létre kell hoznia az egyes streamprotokollokhoz külön élő esemény.  
* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja, hogy az IP-címek, amelyek jogosultak videókat az élő esemény betölteni. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.<br/>Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címeket kell lennie a következő formátumok egyikében: IpV4-cím 4 számjegyből, CIDR-címtartományt.
* Az esemény létrehozásakor megadhatja, hogy az automatikusan induljon el. <br/>Ha autostart értéke igaz, az élő esemény létrehozása után fog elindulni. A számlázás elindul, amint az élő esemény elindult. Leállítás explicit módon kell meghívnia, további számlázási megállítani az élő esemény erőforráson. Másik lehetőségként megkezdése az eseményt, amikor készen áll a streamelés elindításához. 

    További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>Élő esemény betöltési URL-címek

Az élő esemény létrehozása után lekérheti a betöltési URL-címeket. Ezeket kell megadnia az élő helyszíni kódolónak. Az élő kódoló ezekre az URL-címekre küldi a bemeneti élő streamet. További információkért lásd: [ajánlott a helyszíni élő kódolók](recommended-on-premises-live-encoders.md). 

Kreatív vagy nem kreatív URL-címeket is használhat. 

* Non-vanity URL

    A nem kreatív URL-cím az alapértelmezett mód az AMS 3-as verziójában. Az élő eseményt gyorsan megkaphatja, de a bemeneti URL-cím csak az élő esemény kezdetekor válik ismertté. Az URL-cím megváltozik, ha leállítja/elindítja az élő eseményt. <br/>A nem kreatív URL-címek olyan esetekben hasznosak, amikor egy végfelhasználó olyan alkalmazással szeretne streamelni, ahol az alkalmazás a lehető leghamarabb szeretné megkapni az élő eseményt, és nem jelent problémát a dinamikus bemeneti URL-cím.
* Személyes URL-címe

    A kreatív módot a nagy műsorszolgáltatók használják, akik hardveres közvetítéskódolókat használnak, és nem szeretnék újrakonfigurálni a kódolókat az élő esemény kezdetekor. Prediktív bemeneti URL-címet szeretnének, amely nem változik idővel.

> [!NOTE] 
> A bemeneti URL-címet kell prediktív használja a "személyes" módot, majd továbbítja a saját hozzáférési jogkivonat (egy véletlenszerű lexikális elem szerepel az URL-cím elkerülésére) szüksége.

### <a name="live-ingest-url-naming-rules"></a>Élő betöltési URL-elnevezési szabályok

Az alábbi *véletlenszerű* sztring egy 128 bites hexadecimális szám (amely 32 karakterből áll 0-9-ig és a-f-ig).<br/>
Az alábbi *hozzáférési jogkivonatot* kell megadnia rögzített URL-címhez. Ez is 128 bites hexadecimális szám.

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

## <a name="live-event-long-running-operations"></a>Élő esemény hosszú ideig futó műveletek

További információkért lásd: [hosszú ideig futó műveletek](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Élő kimenetek

Miután a stream az élő esemény beérkeznek, megkezdése a streamelési eseményt hoz létre egy [eszköz](https://docs.microsoft.com/rest/api/media/assets), [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs), és [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators). Élő kimeneti fog archiválja a streamet, és tegye elérhetővé a nézők keresztül a [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Létrehozás kimenetek start Live, és állítsa le a törlésekor. Az élő kimeneti törlésekor nem törlünk az alapul szolgáló eszköz és az eszköz tartalmát. 

Közötti kapcsolat egy **élő esemény** és a hozzá tartozó **élő kimenetek** van hagyományos televíziós adás, amellyel egy csatornát (**élő esemény**) állandó jelöli Stream-videó és a egy felvételt (**élő kimeneti**) egy adott időpont szegmens (például esténként hírek a 18:30:00, 7:00 és 18) hatókörét. A televíziós adások egy digitális videórögzítővel (DVR) rögzíthetők – az élő eseményekben az ezzel egyenértékű funkciót az **ArchiveWindowLength** tulajdonság szabályozza. Egy ISO-8601 timespan időtartam (például PTHH:MM:SS), amely meghatározza a DVR kapacitását, és legfeljebb 25 óra legalább 3 percig állítható be.

A **élő kimeneti** objektum olyan, mintha egy felvevő, amely a tényleges és jegyezze fel az élő stream a Media Services-fiók egy adategységbe. A fiók csatlakozik a tárolóba, az eszköz erőforrás által meghatározott Azure Storage-fiókba felvett tartalmát maradnak. A **élő kimeneti** is lehetővé teszi, hogy szabályozza a kimenő élő streamet, például az archív felvétel (például a felhőalapú DVR-Funkciókkal kapacitásának) mennyi az adatfolyam maradjanak, és e megkezdheti a megtekintők néhány tulajdonságát az élő stream megtekintését. Az archívum a lemezen egy kör alakú archive "időszak", amely csak a megadott tartalom mennyisége tárolja a **archiveWindowLength** tulajdonságát a **élő kimeneti**. Ebben az ablakban kívülre eső tartalom automatikusan törlődik a storage-tárolóból, és nem állítható helyre. Létrehozhat több **élő kimenetek** (legfeljebb három maximális) a egy **élő esemény** másik archiválási hosszúságok és beállításokkal.  

Miután közzétette a **élő kimeneti**a **eszköz** használatával egy **Streamelési lokátor**, a **élő esemény** lesz (akár a DVR időszak hossza) továbbra is megtekinthető a Streamelési lokátor lejárati vagy törlését, amelyiket hamarabb.

További információkért lásd: [használatával egy felhőalapú DVR-Funkciókkal](live-event-cloud-dvr.md).

## <a name="next-steps"></a>További lépések

[Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)
