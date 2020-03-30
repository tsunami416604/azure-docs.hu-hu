---
title: Élő események és élő kimenetek fogalmak az Azure Media Services v3-ban
titleSuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt az Azure Media Services v3 élő eseményeiről és élő kimeneteiről.
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
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e6f2ad2c5c30e3c75e8d3588e386ea14e8e3350b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065949"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Élő események és élő kimenetek a Médiaszolgáltatásokban

Az Azure Media Services lehetővé teszi, hogy élő eseményeket közvetítsen ügyfeleinek az Azure-felhőben. Az élő streamelési események beállítása a Media Services v3-ban, meg kell értenie a jelen cikkben tárgyalt fogalmakat.

> [!TIP]
> A Media Services v2 API-król átkelő ügyfelek esetében a **Live Event** entitás váltja fel a **csatornát** a v2-ben, az **Élő kimenet pedig** a **Programot.**

## <a name="live-events"></a>Élő események

Az [élő események](https://docs.microsoft.com/rest/api/media/liveevents) az élő videóadatok betöltését és feldolgozását végzik. Élő esemény létrehozásakor létrejön egy elsődleges és másodlagos bemeneti végpont, amely segítségével élő jelet küldhet egy távoli kódolótól. A távoli élő kódoló az [RTMP](https://www.adobe.com/devnet/rtmp.html) vagy a Smooth [Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmented-MP4) bemeneti protokoll használatával küldi el a bemeneti végpontra a hozzájárulási hírcsatornát. Az RTMP betöltési protokoll esetében a tartalom`rtmp://`elküldhető a tiszta (`rtmps://`) vagy biztonságosan titkosítható a vezetéken( ). A smooth streaming betöltési protokoll esetében `http://` `https://`a támogatott URL-sémák vagy .  

## <a name="live-event-types"></a>Élő eseménytípusok

Az [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) beállítható *áthaladásra* (egy helyszíni élő kódoló több bitráta-adatfolyamot küld) vagy élő *kódolásra* (a helyszíni élő kódoló egyetlen bitráta-adatfolyamot küld). A típusok a [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)használatával történő létrehozás során vannak beállítva:

* **LiveEventEncodingType.None**: A helyszíni élő kódoló több átviteli sebességű adatfolyamot küld. A bevitt adatfolyam további feldolgozás nélkül halad át az élő eseményen. Más néven áthaladási mód.
* **LiveEventEncodingType.Standard**: A helyszíni élő kódoló egyetlen bitráta-adatfolyamot küld az élő eseménynek, és a Media Services több bitráta-adatfolyamot hoz létre. Ha a hozzájárulási hírcsatorna 720p vagy nagyobb felbontású, a **Default720p** készlet 6 felbontás/bitrátapárkészletet fog kódolni.
* **LiveEventEncodingType.Premium1080p**: A helyszíni élő kódoló egyetlen bitráta-adatfolyamot küld az Élő eseménynek, és a Media Services több bitráta-adatfolyamot hoz létre. A Default1080p készlet a felbontás/bitráták párosítási párjainak kimeneti készletét adja meg.

### <a name="pass-through"></a>Továbbítás

![átadó élő esemény a Media Services példadiagramjával](./media/live-streaming/pass-through.svg)

A továbbított **élő esemény** használatakor a helyszíni élő kódoló használatával létrehoz egy többféle sávszélességű videóstreamet, amelyet elküld az élő eseménynek bemeneti adatként (RTMP vagy darabolt MP4 protokollal). Az élő esemény ezután további feldolgozás nélkül továbbítja a bejövő videóstreameket. Az ilyen átmenő élő esemény hosszú ideig futó élő eseményekre vagy 24x365 lineáris élő közvetítésre van optimalizálva. Az ilyen típusú élő események létrehozásakor adja meg a None (LiveEventEncodingType.None) kódolási típust.

A bemeneti adatokat legfeljebb 4K felbontással és 60 képkocka/másodperc képkockasebességgel továbbíthatja, H.264/AVC vagy H.265/HEVC videokodekkel és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hangkodekkel. További információ: [Élő eseménytípusok összehasonlítása](live-event-types-comparison.md).

> [!NOTE]
> Az áteresztő módszer használata a leggazdaságosabb módja az élő streamelésnek, ha hosszú időn keresztül több eseményt hajt végre, és már befektetett a helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
>

Lásd a .NET kód példa [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![élő kódolás a Media Services példadiagramjával](./media/live-streaming/live-encoding.svg)

Ha élő kódolást használ a Media Services szolgáltatással, úgy konfigurálja a helyszíni élő kódolót, hogy egyetlen bitráta-videót küldjön az élő eseményhez való hozzájárulási hírcsatornaként (RTMP vagy Fragmented-Mp4 protokoll használatával). Ezután bekell állítania egy élő eseményt, hogy az egybitrátfolyamot [több bitráta-videóadat-adatfolyamba](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)kódolja, és elérhetővé teszi a kimenetet az eszközök lejátszására olyan protokollokon keresztül, mint az MPEG-DASH, a HLS és a Smooth Streaming.

Élő kódolás használata esetén a hozzájárulási hírcsatornát csak 1080p felbontásban, 30 képkocka/másodperc képkocka sebességgel, H.264/AVC videokodek és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hangkodekmal küldheti el. Vegye figyelembe, hogy az átmenő élő események 60 képkocka/másodperc felbontással legfeljebb 4K felbontást támogatnak. További információ: [Élő eseménytípusok összehasonlítása](live-event-types-comparison.md).

Az élő kódoló kimenetében található felbontásokat és bitrátákat az előre beállított beállítás határozza meg. **Ha egy szabványos** élő kódolót (LiveEventEncodingType.Standard) használ, akkor a *Default720p* készlet hat felbontás/átviteli sebességpárkészletet ad meg, amely 720p-ről 3,5 Mbps-ről 192p-re, 200 kbps-re megy. Ellenkező esetben, ha egy **Premium1080p** élő kódoló (LiveEventEncodingType.Premium1080p), majd a *Default1080p* előre meghatározott készlet hat felbontás / átviteli sebesség pár, megy 1080p a 3,5 Mbps le 180p 200 kbps. További információkat a [rendszerbeállítás-készletekkel](live-event-types-comparison.md#system-presets) kapcsolatos szakaszban talál.

> [!NOTE]
> Ha testre kell szabnia az élő kódolási készletet, nyisson meg egy támogatási jegyet az Azure Portalon keresztül. Adja meg a kívánt felbontási és bitráták táblázatot. Ellenőrizze, hogy csak egy réteg van-e 720p-nél (ha egy készletet kér egy standard élő kódolóhoz) vagy 1080p-t (ha premium1080p élő kódolóhoz előre beállított), és legtöbb 6 réteget.

## <a name="creating-live-events"></a>Élő események létrehozása

### <a name="options"></a>Beállítások

Élő esemény létrehozásakor a következő beállításokat adhatja meg:

* Az élő esemény streamelési protokollja (jelenleg az RTMP és a Smooth Streaming protokollok támogatottak).<br/>A protokollbeállítás nem módosítható, amíg az élő esemény vagy a hozzá tartozó Élő kimenetek futnak. Ha különböző protokollokra van szüksége, hozzon létre egy külön élő eseményt minden egyes streamelési protokollhoz.  
* Az esemény létrehozásakor megadhatja az automatikus indítást. <br/>Ha az automatikus indítás értéke igaz, az élő esemény a létrehozás után indul el. A számlázás akkor kezdődik, amikor az élő esemény elindul. A további számlázás leállításához kifejezetten meg kell hívnia a Stop on the Live Event erőforrást. Azt is megteheti, hogy elindítja az eseményt, amikor készen áll a streamelés megkezdésére.

    További információt az [Élő esemény állapotaés a számlázás](live-event-states-billing.md)című témakörben talál.

* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja azokat az IP-címeket, amelyek lehetővé teszik egy videó betöltését ebbe az élő eseménybe. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.<br/>Ha nincs megadva IP-cím, és nincs szabálydefiníció, akkor nem lesz engedélyezett AZ IP-cím. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címeknek a következő formátumok egyikében kell lenniük: IpV4-cím négy számmal vagy CIDR-címtartományban.

    Ha engedélyezni szeretne bizonyos IP-címeket a saját tűzfalain, vagy az élő eseményekbemeneteket Azure-IP-címekre szeretné korlátozni, töltsön le egy JSON-fájlt az [Azure Datacenter IP-címtartományaiból.](https://www.microsoft.com/download/details.aspx?id=41653) A fájllal kapcsolatos részletekért válassza a **lap Részletek** szakaszát.
    
* Az esemény létrehozásakor bekapcsolhatod az élő átiratokat. <br/> Alapértelmezés szerint az élő átírás le van tiltva. Ez a tulajdonság nem módosítható, amíg az élő esemény vagy a hozzá tartozó Élő kimenetek futnak. 

### <a name="naming-rules"></a>Elnevezési szabályok

* Az élő esemény maximális neve 32 karakter.
* A névnek ezt a `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`regex mintát kell [követnie:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

Lásd még: [Végpontok elnevezési konvenciók streamelése.](streaming-endpoint-concept.md#naming-convention)

> [!TIP]
> Az élő esemény nevének egyediségének biztosítása érdekében létrehozhat egy GUID azonosítót, majd eltávolíthatja az összes kötőjelet és göndör zárójelet (ha van ilyen). A karakterlánc egyedi lesz az összes élő eseményben, és hossza garantáltan 32 lesz.

## <a name="live-event-ingest-urls"></a>Élő események betöltési URL-címei

Az élő esemény létrehozása után lekaphatja az élő helyszíni kódoló számára biztosított betöltési URL-címeket. Az élő kódoló ezekre az URL-címekre küldi a bemeneti élő streamet. További információ: [Ajánlott helyszíni élő kódolók](recommended-on-premises-live-encoders.md).

Kreatív vagy nem kreatív URL-címeket is használhat.

> [!NOTE] 
> Ahhoz, hogy egy betöltési URL-t prediktív, állítsa be a "hiúság" mód.

* Nem hiúságurl-je

    A Nem hiúsági URL az alapértelmezett mód a Media Services v3-ban. Az élő eseményt gyorsan megkaphatja, de a bemeneti URL-cím csak az élő esemény kezdetekor válik ismertté. Az URL-cím megváltozik, ha leállítja/elindítja az élő eseményt. <br/>A nem hiúság akkor hasznos, ha a végfelhasználó olyan alkalmazás használatával szeretne streamelni, amelyben az alkalmazás aSAP-ban élő eseményt szeretne kapni, és dinamikus betöltési URL-címmel rendelkezik, nem jelent problémát.

    Ha egy ügyfélalkalmazásnak nem kell előre létrehoznia egy betöltő URL-t az élő esemény létrehozása előtt, hagyja, hogy a Media Services automatikusan generálja az élő esemény hozzáférési jogkivonatát.

* Hiúság URL-címe

    A hiúsági módot a hardveres műsorszórási kódolókat használó nagy médiaműsorszolgáltatók előnyben részesítik, és nem akarják újrakonfigurálni kódolóikat az élő esemény indításakor. Prediktív betöltési URL-t szeretnének, amely nem változik az idő múlásával.

    A mód megadásához `vanityUrl` `true` a létrehozás időpontjában `false`kell megadni (az alapértelmezett érték ). Azt is meg kell adnia`LiveEventInput.accessToken`a saját hozzáférési jogkivonat ( ) létrehozása kor. Adja meg a token értékét, hogy elkerülje a véletlenszerű jogkivonatot az URL-ben. A hozzáférési jogkivonatnak érvényes GUID karakterláncnak kell lennie (kötőjellel vagy anélkül). A mód beállítása után nem frissíthető.

    A hozzáférési jogkivonatnak egyedinek kell lennie az adatközpontban. Ha az alkalmazásnak hiúsági URL-címet kell használnia, javasoljuk, hogy mindig hozzon létre egy új GUID-példányt a hozzáférési jogkivonathoz (a meglévő GUID újrafelhasználása helyett).

    A következő API-k segítségével engedélyezze a Hiúság URL-címét, `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`és állítsa a hozzáférési jogkivonatot érvényes GUID azonosítóra (például).  

    |Nyelv|Hiúság url-címének engedélyezése|Hozzáférési jogkivonat beállítása|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |parancssori felület|[--hiúság-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--hozzáférés-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Élő bemeneti URL-címek elnevezési szabályai

* Az alábbi *véletlenszerű* sztring egy 128 bites hexadecimális szám (amely 32 karakterből áll 0-9-ig és a-f-ig).
* *a hozzáférési jogkivonat:* A hiúsági mód használatakor beállított érvényes GUID karakterlánc. Például: `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *adatfolyam neve*: Egy adott kapcsolat adatfolyamának nevét jelzi. Az adatfolyam névértékét általában a használt élő kódoló adja hozzá. Az élő kódoló bármilyen nevet használhat a kapcsolat leírására, például: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-vanity-url"></a>Nem hiúságurl-je

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Hiúság URL-címe

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Élő esemény előnézetének URL-címe

Miután az élő esemény megkezdi a hozzájárulási hírcsatorna fogadását, az előnézeti végpont segítségével megtekintheti és ellenőrizheti, hogy az élő közvetítést megkapja-e a további közzététel előtt. Miután ellenőrizte, hogy az előnézeti adatfolyam jó-e, az élő esemény segítségével elérhetővé teheti az élő közvetítést egy vagy több (előre létrehozott) streamelési végponton keresztül. Ehhez hozzon létre egy új [élő kimenetet](https://docs.microsoft.com/rest/api/media/liveoutputs) az élő eseményen.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a videó az előnézeti URL-címhez áramlik!

## <a name="live-event-long-running-operations"></a>Élő esemény hosszú ideig futó műveletek

További információt a [hosszú ideig futó műveletekben](media-services-apis-overview.md#long-running-operations)talál.

## <a name="live-outputs"></a>Élő kimenetek

Miután az adatfolyam beáramlott az élő eseménybe, megkezdheti a streamelési eseményt egy [eszköz,](https://docs.microsoft.com/rest/api/media/assets) [az élő kimenet](https://docs.microsoft.com/rest/api/media/liveoutputs)és a [streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators)létrehozásával. Az Élő kimenet archiválja az adatfolyamot, és elérhetővé teszi a megtekintők számára a [Streaming Végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints)keresztül.  

Az élő kimenetekről a [Felhőalapú dvr használata című](live-event-cloud-dvr.md)témakörben talál részletes információt.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Tekintse meg a [gyakran ismételt kérdésekről szóló](frequently-asked-questions.md#live-streaming) cikket.

## <a name="ask-questions-and-get-updates"></a>Kérdések et tehet fel, és frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

[Élő közvetítésoktató anyaga](stream-live-tutorial-with-api.md)
