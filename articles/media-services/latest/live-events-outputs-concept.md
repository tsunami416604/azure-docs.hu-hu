---
title: Élő események és élő kimenetek – fogalmak a Azure Media Services v3-ban
titleSuffix: Azure Media Services
description: Ez a témakör áttekintést nyújt az élő eseményekről és a Azure Media Services v3 élő kimenetéről.
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
ms.date: 04/08/2020
ms.author: juliako
ms.openlocfilehash: 8eca95f9fca47fca4d54bacbab35f3a0ffc3ba31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010579"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Élő események és élő kimenetek Media Services

Azure Media Services lehetővé teszi, hogy élő eseményeket nyújtson az ügyfeleknek az Azure-felhőben. Az élő adatfolyam-továbbítási események Media Services v3-ban való beállításához ismernie kell a cikkben tárgyalt fogalmakat.

> [!TIP]
> Media Services v2 API-kból áttelepítést igénylő ügyfelek esetében az **élő esemény** entitás a v2-es **csatornát** váltja fel, és az **élő kimenet** lecseréli a **programot**.

## <a name="live-events"></a>Élő események

Az [élő események](https://docs.microsoft.com/rest/api/media/liveevents) az élő videóadatok betöltését és feldolgozását végzik. Élő esemény létrehozásakor létrejön egy elsődleges és egy másodlagos bemeneti végpont, amelynek használatával élő jeleket küldhet egy távoli kódolóból. A távoli élő kódoló az [RTMP](https://www.adobe.com/devnet/rtmp.html) vagy a [Smooth streaming](https://msdn.microsoft.com/library/ff469518.aspx) (darabolt MP4) bemeneti protokoll használatával küldi el a hozzájárulási csatornát az adott bemeneti végpontnak. Az RTMP betöltési protokoll esetében a tartalom a (z) Clear (`rtmp://`) vagy a Wire (`rtmps://`) biztonságos titkosításával is elvégezhető. A Smooth Streaming betöltési protokoll esetében a támogatott URL- `http://` sémák a vagy `https://`a.  

## <a name="live-event-types"></a>Élő események típusai

Egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) lehet egy *átmenő* (egy helyszíni élő kódoló több bitrátás streamet küld) vagy *élő kódolást* (a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld). A típusok beállítása a [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)használatával történik a létrehozás során:

* **LiveEventEncodingType. None**: a helyszíni élő kódoló több bitrátás streamet küld. A betöltött adatfolyam további feldolgozás nélkül halad át az élő eseményen. Más néven áteresztő üzemmód.
* **LiveEventEncodingType. Standard**: a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld az élő eseménynek, és Media Services több bitrátás adatfolyamot hoz létre. Ha a hozzájárulási hírcsatorna 720p vagy magasabb felbontású, a **Default720p** -készlet 6 feloldási/bitrátás párokat fog kódolni.
* **LiveEventEncodingType. Premium1080p**: a helyszíni élő kódoló egyetlen sávszélességű adatfolyamot küld az élő eseménynek, és Media Services több bitrátás adatfolyamot hoz létre. A Default1080p-készlet meghatározza a feloldási/bitráta párok kimeneti készletét.

### <a name="pass-through"></a>Továbbítás

![áteresztő élő esemény Media Services példa diagrammal](./media/live-streaming/pass-through.svg)

A továbbított **élő esemény** használatakor a helyszíni élő kódoló használatával létrehoz egy többféle sávszélességű videóstreamet, amelyet elküld az élő eseménynek bemeneti adatként (RTMP vagy darabolt MP4 protokollal). Az élő esemény ezután további feldolgozás nélkül továbbítja a bejövő videóstreameket. Egy ilyen átmenő élő esemény a hosszan futó élő eseményekre vagy 24x365 lineáris élő közvetítésre van optimalizálva. Az ilyen típusú élő események létrehozásakor adja meg a None (LiveEventEncodingType.None) kódolási típust.

A bemeneti adatokat legfeljebb 4K felbontással és 60 képkocka/másodperc képkockasebességgel továbbíthatja, H.264/AVC vagy H.265/HEVC videokodekkel és AAC (AAC-LC, HE-AACv1 vagy HE-AACv2) hangkodekkel. További információ: [élő események típusai összehasonlítás](live-event-types-comparison.md).

> [!NOTE]
> A továbbítási módszer használata a leggazdaságosabb módja az élő közvetítésnek, ha hosszú időn keresztül végez több eseményt, és már befektetett a helyszíni kódolóba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
>

Tekintse meg a .NET-kód példáját a [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)-ben.

### <a name="live-encoding"></a>Live Encoding  

![élő kódolás Media Services példa diagrammal](./media/live-streaming/live-encoding.svg)

Ha a Media Services élő kódolást használ, a helyszíni élő kódolót úgy konfigurálja, hogy egyetlen bitráta-videót küldjön az élő eseménynek (RTMP vagy darabolt MP4 protokollt használó) hozzájárulási csatornának. Ezután beállíthat egy élő eseményt úgy, hogy az a bejövő egyszeri sávszélességű adatfolyamot [több bitrátás videó streambe](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)kódolja, és elérhetővé teszi a kimenetet a továbbításhoz az eszközökön, például az MPEG-Dash, a HLS és a Smooth streaming protokollok használatával.

Ha élő kódolást használ, a hozzájárulási csatornát csak a 30 képkocka/másodperces képkocka sebességű felbontásban, a H. 264/AVC Video Codec és az AAC (AAC-LC, it-AACv1 vagy AACv2) hangkodek használatával küldheti el. Vegye figyelembe, hogy az áteresztő élő események a 60-es képkockán/másodpercen belüli 4K-os felbontást támogatják. További információ: [élő események típusai összehasonlítás](live-event-types-comparison.md).

Az élő kódoló kimenetében található felbontásokat és bitrátákat az előre beállított érték határozza meg. Ha **standard** Live Encodert (LiveEventEncodingType. Standard) használ, akkor a *Default720p* előre definiált hat felbontási/átviteli sebesség párokat határoz meg, amely a 720P-on 3,5 Mbps-ig, a 192p-on 200 kbps. Ellenkező esetben, ha **Premium1080p** élő kódolót (LiveEventEncodingType. Premium1080p) használ, akkor a *Default1080p* -készlet hat felbontási/bitsűrűségi párokat határoz meg, amely az 1080p 3,5 Mbps-ról 200 kbps-ra van állítva. További információkat a [rendszerbeállítás-készletekkel](live-event-types-comparison.md#system-presets) kapcsolatos szakaszban talál.

> [!NOTE]
> Ha testre kell szabnia az élő kódolási beállításkészletet, nyisson meg egy támogatási jegyet Azure Portalon keresztül. Határozza meg a kívánt felbontást és bitrátát. Győződjön meg arról, hogy a 720p-ban csak egy réteg található (ha a standard Live Encoder esetében egy beállításkészletet igényel) vagy 1080p (ha egy Premium1080p élő kódolóhoz van beállítva), és legfeljebb 6 réteget.

## <a name="creating-live-events"></a>Élő események létrehozása

### <a name="options"></a>Beállítások

Élő események létrehozásakor a következő beállításokat adhatja meg:

* Az élő esemény folyamatos átviteli protokollja (jelenleg az RTMP és a Smooth Streaming protokollok támogatottak).<br/>A protokoll beállítás nem módosítható, amíg az élő esemény vagy a hozzá tartozó élő kimenet fut. Ha eltérő protokollokra van szüksége, hozzon létre egy külön élő eseményt az egyes streaming protokollokhoz.  
* Az esemény létrehozásakor megadhatja az automatikus indítást. <br/>Ha az autostart értéke TRUE (igaz), a rendszer az élő eseményt a létrehozás után indítja el. A számlázás azonnal elindul, amint az élő esemény elindul. A további számlázás leállításához explicit módon hívnia kell az élő esemény erőforrásának leállítását. Azt is megteheti, hogy elindíthatja az eseményt, amikor készen áll a folyamatos átvitelre.

    További információ: [élő események állapota és számlázása](live-event-states-billing.md).

* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja azokat az IP-címeket, amelyek számára engedélyezett az élő esemény Videójának beolvasása. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.<br/>Ha nincs megadva IP-cím, és nincs szabály definíciója, akkor a rendszer nem engedélyezi az IP-címet. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címnek a következő formátumok egyikében kell lennie: IpV4-cím, amely négy számot vagy CIDR-címtartományt tartalmaz.

    Ha bizonyos IP-címeket szeretne engedélyezni a saját tűzfalakon, vagy szeretné korlátozni az élő eseményekre vonatkozó adatokat az Azure-beli IP-címekre, töltsön le egy JSON-fájlt az [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653)közül. A fájllal kapcsolatos részletekért válassza a lap **részletek** szakaszát.
    
* Az esemény létrehozásakor dönthet úgy, hogy bekapcsolja az élő átírásokat. <br/> Alapértelmezés szerint az élő átírás le van tiltva. Ez a tulajdonság nem módosítható, amíg az élő esemény vagy a hozzá tartozó élő kimenetek futnak. 

### <a name="naming-rules"></a>Elnevezési szabályok

* Az élő esemény maximális neve 32 karakter.
* A névnek ezt a [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) -mintát kell `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`követnie:.

Lásd még: [adatfolyam-végpontok elnevezési konvenciói](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Az élő esemény nevének egyediségének biztosításához létrehozhat egy GUID azonosítót, és eltávolíthatja az összes kötőjelet és kapcsos zárójelet (ha van ilyen). A karakterlánc az összes élő eseménynél egyedi lesz, és a hossza garantált, hogy 32.

## <a name="live-event-ingest-urls"></a>Élő események betöltési URL-címei

Az élő esemény létrejötte után betöltheti az élő helyszíni kódolóhoz megadható betöltési URL-címeket. Az élő kódoló ezekre az URL-címekre küldi a bemeneti élő streamet. További információ: [ajánlott helyszíni élő kódolók](recommended-on-premises-live-encoders.md).

Kreatív vagy nem kreatív URL-címeket is használhat.

> [!NOTE] 
> Ahhoz, hogy a betöltési URL-cím prediktív, állítsa a "Vanity" módot.

* Nem hiúság URL-címe

    A nem hiúságos URL-cím az alapértelmezett mód a Media Services v3-ban. Az élő eseményt gyorsan megkaphatja, de a bemeneti URL-cím csak az élő esemény kezdetekor válik ismertté. Az URL-cím megváltozik, ha leállítja/elindítja az élő eseményt. <br/>A nem hiúság olyan helyzetekben hasznos, amikor a végfelhasználó egy olyan alkalmazással szeretne adatfolyamot használni, ahol az alkalmazás egy élő eseményt szeretne kapni, és a dinamikus betöltési URL-cím nem jelent problémát.

    Ha egy ügyfélalkalmazás nem kell előkészítenie a betöltési URL-címet az élő esemény létrehozása előtt, Media Services automatikusan hozza létre az élő esemény hozzáférési jogkivonatát.

* A hiúság URL-címe

    A hiúság üzemmódot a hardveres szórásos kódolókat használó nagyméretű média-műsorszolgáltatók használják, és nem szeretnék újrakonfigurálni a kódolókat az élő esemény indításakor. Ezek a műsorszolgáltatók olyan prediktív betöltési URL-címet szeretnének használni, amely idővel nem változik.
    
    > [!NOTE]
    > A Azure Portal a hiúság URL-címét "*állandó bemeneti URL-cím*" névvel kell elnevezni.

    Ha ezt a módot az API-ban szeretné `vanityUrl` megadni `true` , állítsa a következőre: `false`létrehozás időpontja (alapértelmezett érték). A létrehozáskor is át kell adnia a saját hozzáférési`LiveEventInput.accessToken`tokenjét (). Megadhatja a jogkivonat értékét, hogy elkerülje a véletlenszerű tokent az URL-címben. A hozzáférési jogkivonatnak érvényes GUID-karakterláncnak kell lennie (kötőjelekkel vagy anélkül). A mód beállítása után nem frissíthető.

    A hozzáférési tokennek egyedinek kell lennie az adatközpontban. Ha az alkalmazásnak a Vanity URL-címet kell használnia, akkor azt javasoljuk, hogy mindig hozzon létre egy új GUID-példányt a hozzáférési jogkivonathoz (a meglévő GUID-azonosítók újbóli használata helyett).

    A következő API-k használatával engedélyezheti a hiúság URL-címét, és a hozzáférési tokent érvényes GUID azonosítóra (például: `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`) állíthatja be.  

    |Nyelv|A hiúság URL-címének engedélyezése|Hozzáférési jogkivonat beállítása|
    |---|---|---|
    |REST|[Properties. vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |parancssori felület|[--Vanity-URL](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--Access-Token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Élő bemeneti URL-címek elnevezési szabályai

* Az alábbi *véletlenszerű* sztring egy 128 bites hexadecimális szám (amely 32 karakterből áll 0-9-ig és a-f-ig).
* *hozzáférési jogkivonat*: a Vanity mód használatakor beállított érvényes GUID-karakterlánc. Például: `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *stream neve*: megadja az adott kapcsolatok adatfolyamának nevét. Az adatfolyam-név értékét általában a használt élő kódoló adja hozzá. Az élő kódoló úgy is beállítható, hogy bármilyen nevet használjon a kapcsolódás leírásához, például: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-vanity-url"></a>Nem hiúság URL-címe

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>A hiúság URL-címe

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Élő esemény előnézetének URL-címe

Miután az élő esemény megkezdi a hozzájárulási hírcsatorna fogadását, az előzetes verziójú végponton megtekintheti és ellenőrizheti, hogy a további közzététel előtt megkapja-e az élő streamet. Miután ellenőrizte, hogy az előnézeti adatfolyam megfelelő-e, a Live Event használatával elérhetővé teheti az élő streamet egy vagy több (előre létrehozott) streaming végponton keresztül. Ennek elvégzéséhez hozzon létre egy új [élő kimenetet](https://docs.microsoft.com/rest/api/media/liveoutputs) az élő eseményen.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a videó az előzetes verzió URL-címére áramlik.

## <a name="live-event-long-running-operations"></a>Élő esemény hosszan futó műveletei

Részletekért lásd: [hosszan futó műveletek](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Élő kimenetek

Ha a stream az élő eseménybe áramlik, megkezdheti a folyamatos átviteli eseményt egy [eszköz](https://docs.microsoft.com/rest/api/media/assets), egy [élő kimenet](https://docs.microsoft.com/rest/api/media/liveoutputs)és a [folyamatos átviteli lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators)létrehozásával. Az élő kimenet archiválja a streamet, és elérhetővé teszi a nézők számára a [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints)keresztül.  

Az élő kimenetekkel kapcsolatos részletes információkért lásd: [FELHŐALAPÚ DVR használata](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Tekintse meg a [Gyakori kérdések](frequently-asked-questions.md#live-streaming) című cikket.

## <a name="ask-questions-and-get-updates"></a>Kérdések és frissítések kérése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

[Élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)
