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
ms.date: 06/19/2019
ms.author: juliako
ms.openlocfilehash: a951ebd46335ad4639b8499283ddd30f13edd64e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605654"
---
# <a name="live-events-and-live-outputs"></a>Élő események és élő kimenetek

Az Azure Media Services lehetővé teszi, hogy az ügyfeleknek az Azure-felhőben lévő események élő közvetítésére. Az élő események streamelése konfigurálása a Media Services v3-as, szüksége ebben a cikkben tárgyalt fogalmakat megértéséhez.

> [!TIP]
> A Media Services v2 API-kon keresztül, migráló ügyfelek a **élő esemény** entitás cserél **csatorna** a v2-ben és **élő kimeneti** váltja fel **Program**.

## <a name="live-events"></a>Élő események

Az [élő események](https://docs.microsoft.com/rest/api/media/liveevents) az élő videóadatok betöltését és feldolgozását végzik. Amikor egy élő eseményt hoz létre, egy elsődleges és másodlagos bemeneti végpont jön létre, használhatja élő jelet küld a távoli kódoló. A távoli az élő kódoló küld a hozzájárulás hírcsatorna, amelyek bemeneti végpont használatával vagy a [RTMP](https://www.adobe.com/devnet/rtmp.html) vagy [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (töredékes-MP4) adjon meg protokollt. Az RTMP betöltési protokollt, a tartalom a titkosítatlan elküldött (`rtmp://`) vagy a keresztülhaladnak a hálózaton biztonságosan titkosított (`rtmps://`). A Smooth Streaming betöltési protokollt, a támogatott URL-sémák a `http://` vagy `https://`.  

## <a name="live-event-types"></a>Élő események típusai

A [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: csatlakoztatott mind az élő kódolás. A típusok létrehozása használata során vannak beállítva [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** – egy helyszíni élő kódoló egy több sávszélességű adatfolyamot küld. A feldolgozott adatfolyamok további feldolgozás nélkül áthalad az élő esemény. 
* **LiveEventEncodingType.Standard** – egy helyszíni élő kódoló küld az élő esemény és a Media Services egyféle sávszélességű adatfolyamot hoz létre több átviteli sebességű streamet. Ha a közreműködői hírcsatorna 720p vagy nagyobb felbontású a **Default720p** előbeállítás kódolja 6 felbontást és bitsebességre való átkódolása párok készletét.
* **LiveEventEncodingType.Premium1080p** – egy helyszíni élő kódoló küld az élő esemény és a Media Services egyféle sávszélességű adatfolyamot hoz létre több átviteli sebességű streamet. A Default1080p előbeállítás megadja kimeneti feloldási és bitsebességre való átkódolása párok. 

### <a name="pass-through"></a>Továbbítás

![átmenő típusú](./media/live-streaming/pass-through.svg)

A továbbított **élő esemény** használatakor a helyszíni élő kódoló használatával létrehoz egy többféle sávszélességű videóstreamet, amelyet elküld az élő eseménynek bemeneti adatként (RTMP vagy darabolt MP4 protokollal). Az élő esemény ezután további feldolgozás nélkül továbbítja a bejövő videóstreameket. Az ilyen egy csatlakoztatott élő esemény hosszú ideig futó élő események van optimalizálva, vagy 24 x 365 lineáris élő adatfolyam. Az ilyen típusú élő események létrehozásakor adja meg a None (LiveEventEncodingType.None) kódolási típust.

A bemeneti adatokat legfeljebb 4K felbontással és 60 képkocka/másodperc képkockasebességgel továbbíthatja, H.264/AVC vagy H.265/HEVC videokodekkel és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hangkodekkel.  További részleteket [az élő események típusainak összehasonlításában](live-event-types-comparison.md) talál.

> [!NOTE]
> Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
> 

Tekintse meg a .NET-példakód az [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![live encoding](./media/live-streaming/live-encoding.svg)

Amikor élő kódolást alkalmaz a Media Services használatával, úgy konfigurálja a helyszíni élő kódolót, hogy egyféle sávszélességű videót küldjön bemeneti adatként az élő eseménynek (RTMP vagy darabolt Mp4 protokollal). Majd állíthat be egy élő eseményt, hogy azt kódol, hogy a bejövő egyféle sávszélességű adatfolyamot a egy [több sávszélességű video-adatfolyamot](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), és elérhetővé teszi a kimeneti lejátszás protokollok tartalomfolyamok MPEG-DASH, HLS, eszközöket és a Smooth esetében Streamelési.

Valós idejű kódolás használata esetén is elküldheti a hozzájárulás csak felbontásnál hírcsatorna 1080p felbontással keret válthatnak 30 képkocka/másodperc, a videó kodek H.264/AVC és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hang kodek. Vegye figyelembe, hogy átmenő élő eseményeket is támogatja a megoldását, akár 4 K, 60 képkocka/másodperc. További részleteket [az élő események típusainak összehasonlításában](live-event-types-comparison.md) talál.

A felbontásra és bitsebességre való átkódolása az élő kódoló kimenetében található a beállítás határozza meg. Ha használja egy **Standard** live encoder (LiveEventEncodingType.Standard), majd a *Default720p* előbeállítás 6 feloldási/átviteli sebesség párok, 720 p 3.5Mbps 200 KB/s, 192 p le, azzal egy halmazát határozza meg. Egyéb esetben, ha a használatával egy **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), akkor a *Default1080p* előbeállítás 6 feloldási/átviteli sebesség párok, 1080 p 3.5Mbps, azzal egy halmazát határozza meg. lefelé 180 p, 200 KB/s. További információkat a [rendszerbeállítás-készletekkel](live-event-types-comparison.md#system-presets) kapcsolatos szakaszban talál.

> [!NOTE]
> Ha testre szabhatja a élő kódolási előbeállítás van szüksége, nyisson egy támogatási jegyet az Azure Portalon keresztül. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Győződjön meg arról, hogy csak egy réteg 720 p (Ha a készlet kér egy Standard élőadás-kódoló) vagy 1080 p (ha kér egy készletet Premium1080p élőadás-kódoló), és legfeljebb 6 rétegek.

## <a name="live-event-creation-options"></a>Élő esemény-létrehozási beállítások

Amikor egy élő eseményt hoz létre, megadhatja a következő beállításokat:

* Az élő esemény az adatfolyam-továbbítási protokoll (jelenleg az RTMP és Smooth Streaming protokollokat támogatottak).<br/>Az élő esemény vagy kapcsolódó Live Kimenetétől futása közben, a protokollbeállítás nem módosítható. Ha eltérő protokollok használatára van szüksége, létre kell hoznia az egyes streamprotokollokhoz külön élő esemény.  
* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja, hogy az IP-címek, amelyek jogosultak videókat az élő esemény betölteni. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.<br/>Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címeket kell lennie a következő formátumok egyikében: IpV4-cím 4 számjegyből, CIDR-címtartományt.
* Az esemény létrehozásakor megadhatja, hogy az automatikusan induljon el. <br/>Ha autostart értéke igaz, az élő esemény létrehozása után fog elindulni. A számlázás elindul, amint az élő esemény elindult. Leállítás explicit módon kell meghívnia, további számlázási megállítani az élő esemény erőforráson. Másik lehetőségként megkezdése az eseményt, amikor készen áll a streamelés elindításához. 

    További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>Élő esemény betöltési URL-címek

Az élő esemény létrehozása után lekérheti a betöltési URL-címeket. Ezeket kell megadnia az élő helyszíni kódolónak. Az élő kódoló ezekre az URL-címekre küldi a bemeneti élő streamet. További információkért lásd: [ajánlott a helyszíni élő kódolók](recommended-on-premises-live-encoders.md). 

Kreatív vagy nem kreatív URL-címeket is használhat. 

> [!NOTE] 
> A bemeneti URL-címet kell prediktív állítsa a "személyes" módot.

* Non-vanity URL

    Nem – személyes URL-je a Media Services v3-as az alapértelmezett mód. Az élő eseményt gyorsan megkaphatja, de a bemeneti URL-cím csak az élő esemény kezdetekor válik ismertté. Az URL-cím megváltozik, ha leállítja/elindítja az élő eseményt. <br/>A nem kreatív URL-címek olyan esetekben hasznosak, amikor egy végfelhasználó olyan alkalmazással szeretne streamelni, ahol az alkalmazás a lehető leghamarabb szeretné megkapni az élő eseményt, és nem jelent problémát a dinamikus bemeneti URL-cím.
    
    Ha egy ügyfélalkalmazás nem szükséges előre létrehozni egy bemeneti URL-címet, mielőtt az élő esemény jön létre, csak lehetővé teszik a Media Services automatikus létrehozása a hozzáférési tokent az élő esemény.
* Személyes URL-címe

    A kreatív módot a nagy műsorszolgáltatók használják, akik hardveres közvetítéskódolókat használnak, és nem szeretnék újrakonfigurálni a kódolókat az élő esemény kezdetekor. Prediktív bemeneti URL-címet szeretnének, amely nem változik idővel.
    
    Ebben a módban megadásához állítsa `vanityUrl` való `true` létrehozáskor (alapértelmezett érték a `false`). Ön is a saját hozzáférési token át kell (`LiveEventInput.accessToken`) létrehozáskor. Adjon meg a token értéket az URL-cím egy véletlenszerű token elkerülése érdekében. A hozzáférési jogkivonat nem érvényes GUID-karakterlánc (a vagy a kötőjelek nélkül) lehet. A mód beállítása után nem lehet frissíteni.

    A hozzáférési jogkivonat egyedinek kell lennie az adatközpontban. Ha az alkalmazás egy személyes URL-cím használatára van szüksége, ajánlott mindig hozzon létre egy új GUID-példányt a hozzáférési jogkivonat (újbóli használata bármely létező GUID-Azonosítót) helyett. 

    A következő API-k használata a személyes URL-cím engedélyezése és a egy érvényes GUID Azonosítót a hozzáférési jogkivonat beállítása (például `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`).  
    
    |Nyelv|Személyes URL-cím engedélyezése|Hozzáférési jogkivonat beállítása|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |parancssori felület|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Élő betöltési URL-elnevezési szabályok

* Az alábbi *véletlenszerű* sztring egy 128 bites hexadecimális szám (amely 32 karakterből áll 0-9-ig és a-f-ig).
* *a hozzáférési jogkivonat* – beállíthatja a személyes mód használata esetén érvényes GUID-karakterlánc. Például: `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *az adatfolyam neve* -azt jelzi, hogy egy adott kapcsolathoz tartozó stream nevét. A stream név-érték általában hozzáadódik a használata élő kódoló. Konfigurálhatja az élő kódoló bármely név használatával írja le a kapcsolatot, például: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-vanity-url"></a>Non-vanity URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Személyes URL-címe

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Élő esemény előnézeti URL-címe

Után az élő esemény elkezdi fogadni a hírcsatorna a hozzájárulást, használhatja az előzetes verziójú végpont és ellenőrzéséhez, hogy azért küldtük Önnek, az élő stream további közzététele előtt. Miután ellenőrizte, hogy az előzetes verzió adatfolyam jó, az élő esemény segítségével elérhetővé tenni az élő stream a kézbesítési egy vagy több (előre létrehozott) Streaming-végpontokon keresztül. Ennek érdekében a, hozzon létre egy új [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs) az élő esemény. 

> [!IMPORTANT]
> Győződjön meg arról, hogy a videó áramlik az előnézeti URL-címet a folytatás előtt!

## <a name="live-event-long-running-operations"></a>Élő esemény hosszú ideig futó műveletek

További információkért lásd: [hosszú ideig futó műveletek](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Élő kimenetek

Miután a stream az élő esemény beérkeznek, megkezdése a streamelési eseményt hoz létre egy [eszköz](https://docs.microsoft.com/rest/api/media/assets), [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs), és [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators). Élő kimeneti fog archiválja a streamet, és tegye elérhetővé a nézők keresztül a [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Létrehozás kimenetek start Live, és állítsa le a törlésekor. Az élő kimeneti törlésekor nem törlünk az alapul szolgáló eszköz és az eszköz tartalmát. 

Közötti kapcsolat egy **élő esemény** és a hozzá tartozó **élő kimenetek** hasonlít a hagyományos televíziós szórás számára, amellyel egy csatornát (élő esemény) jelöli egy állandó videó és a egy felvételt (élő adatfolyam Kimeneti) egy adott időpont szegmens (például esténként hírek a 18:30:00, 7:00 és 18) hatókörét. Rögzítheti a televízió digitális videót rögzítő (DVR) használatával – az ennek megfelelő szolgáltatást, az élő események keresztül kezeli a **archiveWindowLength** tulajdonság. Egy ISO-8601 timespan időtartam (például PTHH:MM:SS), amely meghatározza a DVR kapacitását, és legfeljebb 25 óra legalább 3 percig állítható be.

Az élő kimeneti objektum van például, hogy a rendszer a tényleges felvevő és -rekord az élő stream a Media Services-fiók egy adategységbe. A fiók csatlakozik a tárolóba, az eszköz erőforrás által meghatározott Azure Storage-fiókba felvett tartalmát maradnak. Az élő kimeneti is lehetővé teszi, hogy szabályozza a kimenő élő streamet, például az archív felvétel (például a felhőalapú DVR-Funkciókkal kapacitásának) mennyi az adatfolyam maradjanak és -e a nézők megkezdheti a élő streamet néhány tulajdonságát. Az archívum a lemezen egy kör alakú archive "időszak", amely csak az élő kimeneti archiveWindowLength tulajdonságában megadott tartalom mennyisége tárolja. Ebben az ablakban kívülre eső tartalom automatikusan törlődik a storage-tárolóból, és nem állítható helyre. Több élő kimenetek (legfeljebb három maximális) egy élő eseményt másik archiválási hosszúságok és beállításokkal is létrehozhat.  

Miután közzétette az élő kimeneti **eszköz** használatával egy **Streamelési lokátor**, az élő esemény (akár a DVR időszak hossza) továbbra is csak megtekinthető a Streamelési lokátor lejárati vagy törlés hamarabb.

További információkért lásd: [használatával egy felhőalapú DVR-Funkciókkal](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Tegyen fel kérdéseket, küldje el visszajelzését, frissítések beszerzése

Tekintse meg a [Azure Media Services-Közösség](media-services-community.md) kérdések, küldje el visszajelzését, és tudnivalók a Media Services-frissítések különböző módon olvashatja.

## <a name="next-steps"></a>További lépések

[Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)
