---
title: Az Azure Media Services v3-as kiadási megjegyzések | Microsoft dokumentumok
description: A legújabb fejlemények naprakészen maradásához ez a cikk az Azure Media Services 3-as v3-as frissítéseit tartalmazza.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 1db7009096635fc1279ce8a8358e0d8131209722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372590"
---
# <a name="azure-media-services-v3-release-notes"></a>Az Azure Media Services v3-as kiadási megjegyzések

>Kap értesítést, hogy mikor kell újra ezt az oldalt a `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` frissítések másolásával és beillesztése ezt az URL-t: az RSS feed olvasó.

Ahhoz, hogy naprakész legyen a legújabb fejleményekkel, ez a cikk az ön számára tájékoztatást nyújt:

* A legújabb kiadások
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="known-issues"></a>Ismert problémák

> [!NOTE]
> Az Azure [Portal](https://portal.azure.com/) segítségével kezelheti a v3-as [élő eseményeket,](live-events-outputs-concept.md)tekintse meg a v3 [Assets nézetet,](assets-concept.md)és információkat kaphat az API-k eléréséről. Az összes többi felügyeleti feladathoz (például átalakítások és feladatok) használja a [REST API-t,](https://aka.ms/ams-v3-rest-ref)a [CLI-t](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK-k egyikét.](media-services-apis-overview.md#sdks)

További információ: [Áttelepítési útmutató a Media Services 2-es v2-ről a 3-as v3-ra való áttéréshez.](migrate-from-v2-to-v3.md#known-issues)
 
## <a name="january-2020"></a>2020. január

### <a name="improvements-in-media-processors"></a>A médiaprocesszorok fejlesztései

- Továbbfejlesztett támogatása váltottsoros források Video Analysis – az ilyen tartalom most de-váltott soros helyesen, mielőtt elküldi a következtetéseket motorok.
- A "Legjobb" módban történő bélyegképek létrehozásakor a kódoló 30 másodpercen túl keres, hogy olyan keretet válasszon, amely nem monokróm.

### <a name="azure-government-cloud-updates"></a>Az Azure Government felhőfrissítései

Media Services GA'ed a következő Azure Government régiókban: *USGov Arizona* és *USGov Texas*.

## <a name="december-2019"></a>2019. december

CdN-támogatás hozzáadva az *Origin-Assist prefetch* fejlécekhez mind az élő, mind az igény szerinti videóstreameléshez; az Akamai CDN-nel közvetlenül szerződéssel rendelkező ügyfelek számára. Az Origin-Assist CDN-Prefetch szolgáltatás a következő HTTP-fejléccseréket foglalja magában az Akamai CDN és az Azure Media Services eredete között:

|HTTP FEJLÉC|Értékek|Küldő|Fogadó|Cél|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 1 (alapértelmezett) vagy 0 |Tartalomkézbesítési hálózat (CDN)|Forrás|Annak jelzése, hogy a CDN előhívás engedélyezve van|
|CDN-Origin-Assist-Prefetch-Path| Példa: <br/>Töredékek(video=1400000000,formátum=mpd-idő-cmaf)|Forrás|Tartalomkézbesítési hálózat (CDN)|A CDN előzetes betöltési útvonalának biztosítása|
|CDN-Origin-Assist-Prefetch-Request|1 (előzetes betöltési kérelem) vagy 0 (normál kérés)|Tartalomkézbesítési hálózat (CDN)|Forrás|A CDN-től érkező kérés jelzése előzetes|

Ha működés közben szeretné látni a fejléccsere egy részét, próbálkozzon az alábbi lépésekkel:

1. A Postman vagy a Curl használatával kérelmet nyújthat be a Media Services eredetéhez egy hang- vagy videoszegmenshez vagy töredékhez. Győződjön meg arról, hogy adja hozzá a fejléc CDN-Origin-Assist-Prefetch-Enabled: 1 a kérelemben.
2. A válaszban a CDN-Origin-Assist-Prefetch-Path fejlécet kell látnia, amelynek értéke egy relatív elérési út.

## <a name="november-2019"></a>2019. november

### <a name="live-transcription-preview"></a>Élő átírás előnézete

Az élő átírás most már nyilvános előzetes verzióban érhető el, és az USA nyugati régiójában használható.

Az élő transzkripció t az élő eseményekkel együtt kiegészítő képességként való együttműködésre tervezték.  Ez támogatott mind a pass-through és standard vagy prémium kódolás élő események.  Ha ez a funkció engedélyezve van, a szolgáltatás a Cognitive Services [Beszéd-szöveg](../../cognitive-services/speech-service/speech-to-text.md) szolgáltatását használja a bejövő hang kimondott szavainak szöveggé történő átírásához. Ez a szöveg ezután elérhetővé válik a videó és audió mpeg-DASH és HLS protokollokban történő kézbesítésre. A számlázás egy új bővítménymérőn alapul, amely az élő esemény többletköltsége, ha "Futás" állapotban van.  Az élő átírással és számlázással kapcsolatos részletekaz [Élő átírásban](live-transcription.md)

> [!NOTE]
> Jelenleg az élő transzkripció csak az USA nyugati régiójában érhető el előnézeti funkcióként. Ez támogat átírás -ból mondott szöveg angolul ( en-minket) egyetlen ebben az időben.

### <a name="content-protection"></a>Tartalomvédelem

A *token replay megelőzés* funkció megjelent a korlátozott régiókban vissza szeptemberben már elérhető minden régióban.
A Media Services-ügyfelek mostantól korlátozhatják, hogy hányszor lehet ugyanazt a jogkivonatot használni egy kulcs vagy licenc igényléséhez. További információ: [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Új ajánlott élő kódoló partnerek

Az RTMP élő közvetítéséhez a következő új ajánlott partnerkódolók támogatása:

- [Cambria Élő 4,3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 és Max akciókamerák](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Fájlkódolási fejlesztések

- Mostmár elérhető egy új tartalomérzékeny kódolási készlet. A tartalomérzékeny kódolás sal gop-hoz igazított MP4-eket hoz létre. Bármilyen bemeneti tartalom esetén a szolgáltatás elvégzi a bemeneti tartalom kezdeti könnyű elemzését. Ezeket az eredményeket használja a rétegek optimális számának, a megfelelő átviteli sebességnek és az adaptív streamelésáltali kézbesítéshez szükséges felbontási beállítások meghatározásához. Ez az előre beállított beállítás különösen hatékony az alacsony komplexitású és közepes komplexitású videók esetében, ahol a kimeneti fájlok alacsonyabb átviteli sebességgel, de olyan minőségben vannak, amely még mindig jó élményt nyújt a nézőknek. A kimenet mp4 fájlokat tartalmaz, amelyek video- és hanganyagokat tartalmaznak. További információt a [nyílt API-specifikációk című témakörben talál.](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)
- Jobb teljesítmény és többszálas működés a standard kódoló újrasizer. Bizonyos körülmények között az ügyfélnek 5-40% VOD kódolás közötti teljesítménynövekedést kell látnia. A több átviteli sebességbe kódolt alacsony összetettségű tartalom a legnagyobb teljesítménynövekedést fogja látni. 
- A szabványos kódolás mostantól rendszeres gop-ütemet tart fenn a változó képkockasebesség (VFR) tartalmához a VOD kódolás során az időalapú GOP beállítás használatakor.  Ez azt jelenti, hogy az ügyfél benyújtja a vegyes képkockasebesség tartalom, amely változik 15-30 fps például most látnia kell a rendszeres GOP távolságok számított kimenet adaptív sávszélesség ű streamelésMP4 fájlokat. Ez javítja a képességét, hogy zökkenőmentesen válthat a pályák között, amikor szállít több mint HLS vagy DASH. 
-  Továbbfejlesztett AV-szinkronizálás a változó képkockasebességű (VFR) forrástartalomhoz

### <a name="video-indexer-video-analytics"></a>Video indexelő, Videó elemzés

- A VideoAnalyzer készlettel kinyert kulcsképek mostantól a videó eredeti felbontásában vannak, ahelyett, hogy átméretezik őket. A nagy felbontású kulcskép-kihúzás eredeti minőségű képeket eredményez, és lehetővé teszi a Microsoft Computer Vision és Custom Vision szolgáltatások által biztosított képalapú mesterséges intelligencia modellek használatát, hogy még több betekintést nyerjen a videóból.

## <a name="september-2019"></a>2019. szeptember

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Élő események élő lineáris kódolása

A Media Services v3 bejelentette az élő események 24 óra x 365 napos élő lineáris kódolásának előnézetét.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Médiaprocesszorok eprecációja

Bejelentjük az *Azure Media Indexer* és az Azure Media *Indexer 2 Preview*evesítését. A nyugdíjazási dátumokat lásd az [örökölt összetevők](../previous/legacy-components.md) témakörben. [Az Azure Media Services videoindexelő](https://docs.microsoft.com/azure/media-services/video-indexer/) jeleváltja ezeket az örökölt médiaprocesszorokat.

További információ: Áttelepítés az [Azure Media Indexerről és az Azure Media Indexer 2-ről az Azure Media Services videoindexelőre](../previous/migrate-indexer-v1-v2.md)című témakörben.

## <a name="august-2019"></a>2019. augusztus

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Dél-Afrika regionális pár nyitott Media Services 

Media Services már elérhető Dél-Afrika Észak-és Dél-Afrika nyugati régiókban.

További információ: [Felhők és régiók, ahol a Media Services v3 létezik.](azure-clouds-regions.md)

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Médiaprocesszorok eprecációja

Bejelentjük a megszüntetés alatt álló *Windows Azure Media Encoder* (WAME) és *Az Azure Media Encoder* (AME) médiaprocesszorok evesztését. A nyugdíjazási dátumokat lásd az [örökölt összetevők](../previous/legacy-components.md) témakörben.

További információt a [WAME áttelepítése a Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) alkalmazásba és [a a a me-k átáttelepítése a Media Encoder Standard alkalmazásba (AME áttelepítése media encoder sztenderdbe) témakörben talál.](https://go.microsoft.com/fwlink/?LinkId=2101335)
 
## <a name="july-2019"></a>2019. július

### <a name="content-protection"></a>Tartalomvédelem

A jogkivonat-korlátozással védett tartalom streamelése esetén a végfelhasználóknak be kell szerezniük egy jogkivonatot, amely a kulcskézbesítési kérelem részeként kerül elküldésre. A *Token Replay Prevention* funkció lehetővé teszi a Media Services-ügyfelek számára, hogy korlátozzák, hogy hányszor ugyanazt a jogkivonatot lehet használni egy kulcs vagy licenc igényléséhez. További információ: [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

Júliustól az előzetes funkció csak az USA közép- és amerikai nyugati középső részén volt elérhető.

## <a name="june-2019"></a>2019. június

### <a name="video-subclipping"></a>Video részrecsúszás

Mostantól levághatja vagy részcsípheti a videót, ha [a feladatot használva](https://docs.microsoft.com/rest/api/media/jobs)kódolja. 

Ez a funkció minden [olyan átalakítással](https://docs.microsoft.com/rest/api/media/transforms) működik, amely a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) készletekkel vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) készletekkel épül fel. 

Lásd a példákat:

* [Videoklip a .NET segítségével](subclip-video-dotnet-howto.md)
* [Videoklip rest-el](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019. május

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Az Azure Monitor támogatja a Media Services diagnosztikai naplóit és metrikákat

Most már használhatja az Azure Monitor telemetriai adatok a Media Services által kibocsátott adatok megtekintéséhez.

* Az Azure Monitor diagnosztikai naplók segítségével figyelheti a Media Services-kulcskézbesítési végpont által küldött kérelmeket. 
* A Media Services streaming [végpontjai](streaming-endpoint-concept.md)által kibocsátott mérőszámok figyelése .   

További információt a [Media Services-mutatók és diagnosztikai naplók figyelése című témakörben talál.](media-services-metrics-diagnostic-logs.md)

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>A többhangsáv támogatja a dinamikus csomagolást 

Ha több kodeket és nyelvet tartalmazó, több hangsávot tartalmazó eszközök streamelése esetén a [Dynamic Packaging](dynamic-packaging-overview.md) mostantól támogatja a HLS kimenet (4-es vagy újabb verzió) több hangsávját.

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea regionális pár nyitva media services 

Media Services már elérhető Korea Közép-és Korea Dél-régiókban. 

További információ: [Felhők és régiók, ahol a Media Services v3 létezik.](azure-clouds-regions.md)

### <a name="performance-improvements"></a>Teljesítménnyel kapcsolatos fejlesztések

Hozzáadott frissítések, amelyek tartalmazzák a Media Services teljesítményének javításait.

* Frissült a feldolgozásra támogatott maximális fájlméret. Lásd: [Kvóták és korlátozások](limits-quotas-constraints.md).
* [A kódolás felgyorsítja a fejlesztéseket.](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types)

## <a name="april-2019"></a>2019. április

### <a name="new-presets"></a>Új készletek

* [A FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) bekerült a beépített analizátor készletekbe.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) lett hozzáadva a beépített kódoló készletekhez. További információt a [Tartalomérzékeny kódolás című](content-aware-encoding.md)témakörben talál. 

## <a name="march-2019"></a>2019. március

A Dynamic Packaging mostantól támogatja a Dolby Atmos-t. További információ: [Dinamikus csomagolás által támogatott hangkodekek.](dynamic-packaging-overview.md#audio-codecs)

Most már megadhatja az eszköz- vagy fiókszűrők listáját, amely a streamelési lokátorra vonatkozna. További információ: [Szűrők társítása a streamelési lokátorral](filters-concept.md#associating-filters-with-streaming-locator)című témakörben talál.

## <a name="february-2019"></a>2019. február

A Media Services v3 mostantól támogatott az Azure nemzeti felhőiben. Még nem minden funkció érhető el minden felhőben. További információt a [Felhők és régiók, amelyekben az Azure Media Services v3 létezik.](azure-clouds-regions.md)

[Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) esemény hozzáadva az Azure Event Grid-sémák a Media Services.

## <a name="january-2019"></a>2019. január

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard és MPI fájlok 

Mp4-fájl(ok) létrehozására a Media Encoder Standard kódolásakor egy új .mpi fájl jön létre, és hozzáadódik a kimeneti eszközhöz. Ez az MPI fájl célja, hogy javítsa a teljesítményt a [dinamikus csomagolási](dynamic-packaging-overview.md) és streaming forgatókönyvek.

Ne módosítsa vagy távolítsa el az MPI-fájlt, és ne függjön a szolgáltatásban az ilyen fájl létezésétől (vagy sem-től).

## <a name="december-2018"></a>2018. december

A V3 API GA-kiadásának frissítései a következők:
       
* A **PresentationTimeRange** tulajdonságai már nem "szükségesek" az **eszközszűrőkhöz** és **a fiókszűrőkhöz.** 
* A feladatok és átalakítások $top és $skip **lekérdezési** beállításait **eltávolították,** és $orderby adták hozzá. Az új rendelési funkció hozzáadásának részeként kiderült, hogy a $top és $skip a lehetőségek korábban véletlenül ki voltak téve annak ellenére, hogy nincsenek megvalósítva.
* A felsorolás bővíthetősége újra engedélyezve lett. Ez a funkció engedélyezve volt az SDK előzetes verzióiban, és véletlenül le lett tiltva a GA verzióban.
* Két előre definiált streamelési házirend lett átnevezve. **SecureStreaming** most **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** most **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>2018. november

A CLI 2.0 modul már elérhető az [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – 2.0.50-es verzióhoz.

### <a name="new-commands"></a>Új parancsok

- [az ams-fiók](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams fiókszűrő](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams eszköz](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams eszközszűrő](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams munka](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams élő esemény](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams élő kimenet](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-végpont](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-lokátor](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - lehetővé teszi a Media Reserved Units kezelését. További információt a [Médiaszámára fenntartott egységek méretezése című témakörben talál.](media-reserved-units-cli-how-to.md)

### <a name="new-features-and-breaking-changes"></a>Új funkciók és törésmódosítások

#### <a name="asset-commands"></a>Eszközparancsok

- ```--storage-account```és ```--container``` érveket hozzá.
- A lejárati idő alapértelmezett értékei (Most+23h) és a parancs engedélyei ```az ams asset get-sas-url``` (Olvasás) hozzáadva.

#### <a name="job-commands"></a>Feladatparancsok

- ```--correlation-data```és ```--label``` érvek hozzá
- ```--output-asset-names```átnevezték ```--output-assets```a névre. Most elfogadja az eszközök szóközre bontott listáját "assetName=label" formátumban. Egy címke nélküli eszköz a következőkkel küldhető el: 'assetName='.

#### <a name="streaming-locator-commands"></a>Streamelési lokátor parancsok

- ```az ams streaming locator```base parancsot ```az ams streaming-locator```a gombra cseréljük.
- ```--streaming-locator-id```és ```--alternative-media-id support``` érveket hozzá.
- ```--content-keys argument```argumentum frissítve.
- ```--content-policy-name```átnevezték ```--content-key-policy-name```a névre.

#### <a name="streaming-policy-commands"></a>Streamelési házirend parancsai

- ```az ams streaming policy```base parancsot ```az ams streaming-policy```a gombra cseréljük.
- Titkosítási paraméterek ```az ams streaming-policy create``` támogatása hozzáadva.

#### <a name="transform-commands"></a>Átalakítás parancsok

- ```--preset-names```az argumentum ```--preset```helyébe az lép. Most már csak akkor lehet beállítani 1 kimenet / előre egy ```az ams transform output add```időben (hozzá több meg kell futtatni). Egyéni StandardEncoderPreset-et is beállíthat az egyéni JSON elérési útján.
- ```az ams transform output remove```lehet végezni halad a kimeneti index et eltávolítani.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract```argumentumokat ```az ams transform create``` és ```az ams transform output add``` parancsokat.

## <a name="october-2018---ga"></a>2018. október - GA

Ez a szakasz az Azure Media Services (AMS) októberi frissítéseit ismerteti.

### <a name="rest-v3-ga-release"></a>REST v3 GA kiadás

A [REST v3 ga-kiadás](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) a további API-k at tartalmaz az élő, fiók/eszköz szintű jegyzékszűrők és a DRM-támogatás.

#### <a name="azure-resource-management"></a>Azure erőforrás-kezelés 

Az Azure Resource Management támogatása lehetővé teszi az egységes felügyeleti és üzemeltetési API-t (most mindent egy helyen).

Ebből a kiadásból kiindulva az Erőforrás-kezelő sablonjai segítségével élő eseményeket hozhat létre.

#### <a name="improvement-of-asset-operations"></a>Az eszközműveletek javítása 

A következő fejlesztések et vezették be:

- Http-URL-ek vagy Az Azure Blob Storage SAS URL-címek betöltése.
- Adja meg az eszközök saját tárolóneveit. 
- Egyszerűbb kimeneti támogatás egyéni munkafolyamatok létrehozásához az Azure Functions segítségével.

#### <a name="new-transform-object"></a>Új átalakítás objektum

Az új **Transzformátor** objektum leegyszerűsíti a kódolási modellt. Az új objektum megkönnyíti a kódolási Erőforrás-kezelő sablonok és készletek létrehozását és megosztását. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory-hitelesítés és RBAC

Az Azure AD-hitelesítés és szerepköralapú hozzáférés-vezérlés (RBAC) biztonságos átalakításokat, LiveEvents eseményeket, tartalomkulcs-házirendeket vagy szerepkör vagy felhasználók szerint az Azure AD-ben biztonságos átalakításokat, LiveEvents-szabályzatokat vagy eszközök szerepkör vagy felhasználók szerint engedélyezi.

#### <a name="client-sdks"></a>Ügyfél-SDK-k  

A Media Services 3-as verzióban támogatott nyelvek: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Élő kódolási frissítések

A következő élő kódolási frissítések kerülnek bevezetésre:

- Új alacsony késleltetésű mód élő (10 másodperc végponttól végpontig).
- Továbbfejlesztett RTMP támogatás (nagyobb stabilitás és több forráskódoló támogatás).
- RTMPS biztonságos betöltés.

    Élő esemény létrehozásakor most antól 4 betöltési URL-t kap. A 4 betöltési URL-címek szinte azonosak, ugyanazt a streamelési jogkivonatot (AppId), csak a port szám része más. Két URL elsődleges és az RTMPS biztonsági mentése. 
- 24 órás átkódolási támogatás. 
- Továbbfejlesztett hirdetésjelző támogatás az RTMP-ben az SCTE35-ön keresztül.

#### <a name="improved-event-grid-support"></a>Továbbfejlesztett eseményrács-támogatás

Az Event Grid következő támogatási fejlesztései láthatók:

- Az Azure Event Grid integrációja a Logic Apps és az Azure Functions egyszerűbb fejlesztéséhez. 
- Iratkozzon fel a kódolással, az élő csatornákkal és egyebek eseményeire.

### <a name="cmaf-support"></a>CMAF-támogatás

CMAF és "cbcs" titkosítási támogatás a CMAF-ot támogató Apple HLS (iOS 11+) és MPEG-DASH lejátszókhoz.

### <a name="video-indexer"></a>Videóindexelő

Video Indexer GA kiadás jelentették be augusztusban. A jelenleg támogatott szolgáltatásokról a [Mi a VideoIndexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)című témakörben talál új információt. 

### <a name="plans-for-changes"></a>Változások tervei

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Az Azure CLI 2.0 modul, amely tartalmazza a műveleteket az összes funkció (beleértve az élő, tartalomkulcs-házirendek, fiók/eszköz szűrők, streamelési szabályzatok) hamarosan érkezik. 

### <a name="known-issues"></a>Ismert problémák

Csak az ügyfelek, akik az előzetes API-t az eszköz vagy AccountFilters érinti a következő probléma.

Ha 09/28 és 10/12 között hozott létre eszközök vagy számlaszűrőket a Media Services v3 CLI-vel vagy API-kkal, el kell távolítania az összes eszköz- és AccountFilters-szűrőt, és újra létre kell hoznia őket egy verzióütközés miatt. 

## <a name="may-2018---preview"></a>2018. május - Előzetes verzió

### <a name="net-sdk"></a>.NET SDK

A .NET SDK a következő szolgáltatásokat tartalmazza:

* **Átalakítja** **és a feladatok** kódolni vagy elemezni médiatartalom. Példák: [Stream fájlok](stream-files-tutorial-with-api.md) és [Elemzés](analyze-videos-tutorial-with-api.md).
* **A tartalom végfelhasználói** eszközökre történő közzétételéhez és streameléséhez szolgáló keresők
* **A streamelési szabályzatok** és **a tartalomkulcs-házirendek** a kulcskézbesítés és a tartalomvédelem (DRM) konfigurálásához a tartalom kézbesítésekor.
* **Élő események** és **élő kimenetek** az élő streamelési tartalom betöltésének és archiválásának konfigurálásához.
* Az Azure Storage-ban a médiatartalmak tárolására és közzétételére szolgáló **eszközök.** 
* **Végpontok streamelése** dinamikus csomagolás, titkosítás és streamelés konfigurálásához és méretezéséhez élő és igény szerinti médiatartalmakhoz.

### <a name="known-issues"></a>Ismert problémák

* Egy feladat elküldésekor megadhatja, hogy a forrásvideó https-URL-címek, SAS-URL-címek vagy az Azure Blob storage-ban található fájlok elérési útjai használatával betöltése. Az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

- [Áttekintés](media-services-overview.md)
- [A Media Services v2 kiadási megjegyzések](../previous/media-services-release-notes.md)
