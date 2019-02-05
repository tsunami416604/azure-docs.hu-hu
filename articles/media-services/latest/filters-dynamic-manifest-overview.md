---
title: Szűrők és az Azure Media Services dinamikus jegyzékek |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, így az ügyfél használhatja őket stream konkrét szakaszokra datového proudu. A Media Services dinamikus jegyzékek archiválása a szelektív streamelési hoz létre.
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
ms.date: 01/24/2019
ms.author: juliako
ms.openlocfilehash: 139f6283c2b59aee53afa3f0dd52e06e2b0eff4c
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695219"
---
# <a name="filters-and-dynamic-manifests"></a>Szűrők és dinamikus jegyzékek

Az ügyfelek számára (élő eseményeket vagy igény szerinti Videószolgáltatás streaming) tartalomtovábbításkor az ügyfél igényelhet az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot. Az Azure Media Services lehetővé teszi, hogy meghatározza a fiók és a tartalom eszköz szűrőket. 

Szűrők kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek, például a következők: 

- Lejátszani egy videót (a teljes videó lejátszása) helyett csak egy részét. Példa:

    - Csökkentse a jegyzékfájlt ("aljelentés klip szűrés"), az élő esemény egy részklip megjelenítéséhez, vagy
    - Trim ("vágás egy videó") egy videó kezdete.

- Csak a megadott beállításkészletben és/vagy a megadott nyelv nyomon követi az eszköz, amellyel a tartalmat ("megjelenítésszűrés") által támogatott kézbesítéséhez. 
- Módosítsa a megjelenítési ablakot (DVR) annak érdekében, hogy a Windows Media player ("módosításának bemutató időszak") megadott DVR időszak korlátozott hosszát.

Ez a témakör ismerteti [fogalmak](#concepts) és [látható szűrők definíciók](#definitions). Ezután megadja gyakori helyzetek részleteit. A cikk végén található hivatkozásokat, amelyek bemutatják, hogyan hozhat létre programozott módon a szűrőket.  

## <a name="concepts"></a>Alapelvek

### <a name="dynamic-manifests"></a>A dinamikus jegyzékek

Media Services által **dinamikus jegyzékfájlok** alapú az előre meghatározott [szűrők](#filters). Miután meghatározott szűrők, a az ügyfelek használhatja őket egy adott megjelenítés a klipek alárendelt a videó továbbításához. A streamelési URL-CÍMBEN szereplő azokat megadni szűrő(k) alapján. Az adaptív sávszélességű streamelési protokollok szűrőket is lehet alkalmazni: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming. 

Az alábbi táblázatban néhány példa az URL-címek szűrőket jeleníti meg:

|Protokoll|Példa|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

> [!NOTE]
> A dinamikus jegyzékek nem változtatja az eszköz és az eszköz alapértelmezett jegyzék. Az ügyfél kérése egy streamet, vagy a szűrők nélkül választhatja. 
> 
> 

### <a name="manifest-files"></a>Jegyzékfájlok

Ha Ön kódolása az adaptív bitsebességű folyamatos átvitel, egy **manifest** (lista) fájl jön létre (a fájl a szöveg- vagy XML-alapú). A **manifest** fájl tartalmaz metaadatokat például streamelési: nyomon követheti a típusa (hang, videó vagy szöveg), nyomon követheti a neve, kezdési és befejezési idő, sávszélességű (Tulajdonságok), nyomon követése nyelvek, bemutató ablak (csúszóablakban rögzített időtartama), videó kodek () FourCC). A arra utasítja a Windows Media player beolvasni a következő részlet azáltal, hogy a következő lejátszható videó szilánkok érhető el, és azok helyétől kapcsolatos információkat is. Szilánk (vagy szegmensek) olyan a tényleges "" a videó tartalmát.

Íme egy példa egy HLS-jegyzékfájl: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>GET, és vizsgálja meg a jegyzékfájlok

Azt adja meg, szűrő nyomon követése Tulajdonságfeltételek listáját, amelyen a nyomon követi a Stream (élő vagy igény szerinti Videószolgáltatás) kell szerepelnie, dinamikusan létrehozott jegyzékfájl. És vizsgálja meg a tulajdonságait, a nyomon követi, akkor először betölteni a Smooth Streaming jegyzékfájlt.

A [feltöltése, kódolása és streamelése a .NET-tel fájlok](stream-files-tutorial-with-api.md) oktatóanyag bemutatja, hogyan hozhat létre a streamelési URL-címet a .NET (jelenik meg, a [URL-címek létrehozását](stream-files-tutorial-with-api.md#get-streaming-urls) szakaszt). Ha futtatja az alkalmazást, az URL-címek közül mutat a Smooth Streaming jegyzékfájl: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Másolja és illessze be az URL-címet a böngésző címsorába. A rendszer letölti a fájlt. Megnyithatja egy tetszőleges szövegszerkesztőben.

A REST-példa: [feltöltése, kódolása és streamelése a fájlokat a REST segítségével](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Az átviteli sebesség, video-adatfolyamot figyelése

Használhatja a [Azure Media Player demó lap](http://aka.ms/amp) figyelése az átviteli sebesség, video-adatfolyamot. A bemutató lap megjeleníti a diagnosztikai információ a **diagnosztikai** lapon:

![Az Azure Media Player diagnostics][amp_diagnostics]

## <a name="defining-filters"></a>Szűrők megadása

Az eszközintelligencia szűrők két típusa van: 

* [Szűrők fiók](https://docs.microsoft.com/rest/api/media/accountfilters) (globális) – az Azure Media Services-fiókban lévő bármely eszközre is alkalmazható, a fiók élettartama.
* [Az eszközintelligencia szűrők](https://docs.microsoft.com/rest/api/media/assetfilters) (helyi) – csak egy eszköz, amellyel a szűrő lett társítva létrehozásakor kell alkalmazni, az eszköz élettartama. 

[Szűrő fiók](https://docs.microsoft.com/rest/api/media/accountfilters) és [eszköz szűrő](https://docs.microsoft.com/rest/api/media/assetfilters) típusok definiálása/leíró a szűrő pontosan azonos tulajdonságokkal rendelkezik. Kivéve, amikor létrehozza a **eszköz szűrő**, meg kell adnia az objektum nevét, amelyhez hozzá szeretne rendelni a szűrő.

A forgatókönyvtől függően úgy dönt, hogy milyen típusú szűrő megfelelőbbek, (eszköz szűrő vagy szűrő). Fiókszűrők alkalmasak (megjelenítésszűrés) eszközprofilok ahol eszköz szűrők és a záró szóközöket egy adott eszköz használható.

A következő tulajdonságok használatával írja le a szűrőket. 

|Name (Név)|Leírás|
|---|---|
|firstQuality|Az első minőségi sávszélességű a szűrő.|
|presentationTimeRange|A bemutató időtartományban. Ez a tulajdonság jegyzékfájl kezdő és záró pontot bemutató időszak hossza és az élő kezdőpozíciója szűréshez használható. <br/>További információkért lásd: [PresentationTimeRange](#PresentationTimeRange).|
|nyomon követi|A számok kiválasztási feltételeket. További információkért lásd: [nyomon követi](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Ezzel a tulajdonsággal rendelkező **eszköz szűrők**. Nem javasoljuk, hogy a tulajdonság a **Fiókszűrők**.

|Name (Név)|Leírás|
|---|---|
|**endTimestamp**|Az abszolút befejezési időkorlátját. A videó igény szerinti (VoD) vonatkozik. Az élő bemutató azt csendes figyelmen kívül hagyja, és alkalmazza a bemutató véget ér, a stream válik VoD.<br/><br/>Az érték egy abszolút végpont az adatfolyam jelöli. Azt lekérdezi kerekítve a legközelebbi tovább Képcsoporttal Start.<br/><br/>Használja a StartTimestamp és EndTimestamp szűkítheti a listát (manifest). Ha például StartTimestamp = 40000000 és EndTimestamp = 100000000 létrehoz egy listát, amely tartalmazza az adathordozó StartTimestamp és EndTimestamp között. Ha egy töredék feladata a határt, a teljes töredék fog szerepelni a jegyzékfájlban.<br/><br/>További információ a **forceEndTimestamp** definíciót, amely követi.|
|**forceEndTimestamp**|Az élő szűrők vonatkozik.<br/><br/>**forceEndTimestamp** logikai érték beolvasása, amely azt jelzi, hogy van-e **endTimestamp** érvényes értékre lett beállítva. <br/><br/>Ha az érték **igaz**, a **endTimestamp** értéket kell megadni. Ha nincs megadva, majd egy hibás kérés adja vissza.<br/><br/>Ha például szeretne meghatározni egy szűrőt, amely 5 perccel indul el, a bemeneti videóhoz, és a stream végéig aktiválást, így állíthatja be **forceEndTimestamp** hamis értékre, és hagyja ki a beállítás **endTimestamp**.|
|**liveBackoffDuration**|Csak az élő vonatkozik. A tulajdonság élő lejátszási pozíció meghatározására szolgál. Ezzel a szabállyal késleltetés élő lejátszási pozícióját, és hozzon létre egy kiszolgálóoldali puffer lejátszók. LiveBackoffDuration van élő helyétől függően. Az élő leállítási maximális időtartam érték 300 másodperc.|
|**presentationWindowDuration**|Az élő vonatkozik. Használat **presentationWindowDuration** csúszóablakszerűen történik alkalmazni a listára. Például állítsa be a presentationWindowDuration = a alkalmazni egy kétperces csúszóablakban 1200000000. Média élő széle 2 percen belül fog szerepelni a listában. Ha egy töredék feladata a határt, a teljes töredék a lista fog szerepelni. A minimális bemutató ablak időtartamának érték 60 másodperc.|
|**startTimestamp**|VoD- vagy élő streamet vonatkozik. Az érték egy abszolút kezdőpontja az adatfolyam jelöli. Az érték beolvasása kerekítve a legközelebbi tovább Képcsoporttal Start.<br/><br/>Használat **startTimestamp** és **endTimestamp** szűkítheti a listát (manifest). Ha például startTimestamp = 40000000 és endTimestamp = 100000000 létrehoz egy listát, amely tartalmazza az adathordozó StartTimestamp és EndTimestamp között. Ha egy töredék feladata a határt, a teljes töredék fog szerepelni a jegyzékfájlban.|
|**időskálára**|VoD- vagy élő streamet vonatkozik. A időskálára időbélyegei által használt és a fent megadott időtartam. Az alapértelmezett időskálán 10000000. Egy alternatív időskálán is használható. Alapértelmezés szerint száma 10 000 000 HNS (száz nanoszekundumos).|

### <a name="tracks"></a>nyomon követi

Azt adja meg, szűrő nyomon követése Tulajdonságfeltételek (FilterTrackPropertyConditions) listáját, amelyen a nyomon követi a Stream (élő vagy igény szerinti Videószolgáltatás) kell szerepelnie, dinamikusan létrehozott jegyzékfájl. A szűrők használatával egy logikai kombinált **és** és **vagy** műveletet.

Szűrő nyomon követése Tulajdonságfeltételek követési típusok, értékeket (az alábbi táblázatban ismertetett) és (egyenlő, NotEqual) műveleteket ismertetik. 

|Name (Név)|Leírás|
|---|---|
|**Átviteli sebesség**|Az átviteli sebesség, a nyomon követése a szűréshez használni.<br/><br/>A javasolt érték, amely egy bitsebességre való átkódolása bit / másodperc. Ha például "0-2427000".<br/><br/>Megjegyzés: használhatja adott sávszélességű értékének, például a 250000 (bit / másodperc), amíg ez a megközelítés van nem ajánlott, mivel a pontos bitsebességre való átkódolása ingadozhaz, egy eszköz a másikba.|
|**FourCC**|A szám FourCC értéket használja a szűréshez.<br/><br/>Az első elemét kodekek formátum, a megadott érték [RFC 6381](https://tools.ietf.org/html/rfc6381). Jelenleg a következő kodekek támogatottak: <br/>Videó: "Avc1", "hev1", "hvc1"<br/>Hang: "Mp4a", "3-EK"<br/><br/>Nyomon követi egy adategység FourCC értékeinek meghatározásához [lekérése, és vizsgálja meg a jegyzékfájl](#get-and-examine-manifest-files).|
|**Nyelv**|A nyelv, a nyomon követése a szűréshez használni.<br/><br/>Adja meg, mint a megadott RFC 5646 szeretné egy nyelv a címke értéke. Például "hu".|
|**Name (Név)**|A track nevét használja a szűréshez.|
|**Típus**|A szám típust használják a szűréshez.<br/><br/>A következő értékek használata engedélyezett: "videó", "hang" vagy "szöveg".|

### <a name="example"></a>Példa

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="rendition-filtering"></a>Megjelenítés szűrése

Előfordulhat, hogy az eszköz több kódolási profilt (H.264 alapterv, H.264 magas, AACL, AACH, Dolby digitális Plus) és több minőségi bitsebességre való átkódolása kódolása választja. Azonban nem minden ügyfél eszköz lesz a támogatja, az objektumhoz tartozó összes profilok és bitsebességre való átkódolása. Régebbi Android-eszközök például csak H.264 alapkonfiguráció + AACL támogatják. Magasabb bitsebességre való átkódolása küld egy eszközt, amely nem olvasható be az előnyöket, hulladékok sávszélesség- és eszköz számításokra. Az ilyen eszközt kell dekódolja az összes megadott információt, csak a vertikális leskálázás való megjelenítéshez.

A dinamikus Manifest eszközprofilok hozhat létre például mobil-, HD/SD, stb-konzol és a nyomon követi és minőségű, amit érdemes figyelembe venni az egyes profilok.

![Megjelenítés szűrési példát][renditions2]

A következő példában egy kódoló egy mezzanine eszköz kódolandó hét ISO MP4-fájlnak videó beállításkészletben (a 180p a 1080p) lett megadva. A kódolt objektumhoz dinamikusan csomagolható be a következő adatfolyam-továbbítási protokollok bármelyikét: HLS, MPEG DASH és Smooth.  A diagram tetején jelenik meg a HLS-jegyzékfájl a szűrők az eszköz (tartalmaz minden hét beállításkészletben).  A bal alsó "ott" nevű szűrőt alkalmaztak, amelyhez a HLS-jegyzékfájl jelenik meg. A "ott" szűrő meghatározza, hogy távolítsa el az összes bitsebességre való átkódolása alább 1 MB/s, amely eredményezett az alsó két minőségi szint alatt levágja, a válaszban. Az alul a jobb oldalon a HLS-jegyzékfájlt, amelyhez "mobileszköz" nevű szűrőt alkalmaztak jelenik meg. A "mobileszköz" szűrő Megadja, hogy távolítsa el a beállításkészletben, ahol a megoldás nem haladja meg a két eredményezett 720p 1080p beállításkészletben levágja, folyamatban van.

![Megjelenítés szűrése][renditions1]

## <a name="removing-language-tracks"></a>Nyelv eltávolítása sávok
Az eszközök lehetnek több hang nyelvet, például az angol, spanyol, francia, stb. Általában a lejátszó SDK kezelők alapértelmezett hangsávra kiválasztása, és elérhető hang követi nyomon egy felhasználó által választott. Az ilyen lejátszó SDK-k fejlesztéséhez kihívást jelenthet, implementálható különböző eszközspecifikus player-keretrendszerek van szükség. Is az egyes platformokon Player API-k korlátozva, és nem tartalmaznak arról, hogy a felhasználók hol nem válassza ki és módosítsa az alapértelmezett hangsávra hang kijelölés funkció. Eszköz szűrőket szabályozhatja a viselkedés hang kívánt nyelveket tartalmazó szűrők létrehozásával.

![Nyelvi nyomon követi a szűréshez][language_filter]

## <a name="trimming-start-of-an-asset"></a>Egy eszköz levágási kezdete
A legtöbb élő események streamelése operátorok néhány tesztet, a tényleges esemény előtt futtassa. Ezek lehetnek például egy befutó hasonlóan ez az esemény kezdete előtt: "Program megkezdődik rövid ideig". Ha a program az archiválás, a tesztelés és állóképek adatokat is archivált és benne a bemutatót. Azonban ezt az információt nem kell feltüntetni az ügyfelek számára. A dinamikus jegyzékfájl létrehozásához egy kezdési idejének szűrője, és a nemkívánatos adatok eltávolítása a jegyzékfájlban.

![Tisztítás kezdő][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Az élő archívumot subclips (nézetek) létrehozása
Sokáig futó számos élő események és élő archívumot tartalmazhat több esemény. Az élő esemény befejezése után műsorszolgáltatók érdemes részekre az élő archívumot logikai programot, és állítsa le a feladatütemezések. Ezután a virtuális programok külön-külön nem közzé bejegyzés az élő archívumot feldolgozását, és nem hoz létre a különböző eszközöket (amely nem kap a CDN az az előnye, hogy a meglévő gyorsítótárazott szilánkok). Ilyen virtuális programok a következők: a negyedévek egy labdarúgó vagy Kosárlabda játékot, a baseball innings vagy egyéni események minden sport-program.

A dinamikus Manifest hozhat létre szűrők használatával kezdési/befejezési idején és virtuális nézeteket hozhat létre az élő archívumot tetején keresztül. 

![Részklip szűrő][subclip_filter]

Szűrt eszköz:

![Síelés][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Bemutató ablak (DVR) beállítása
Jelenleg az Azure Media Services. kör alakú archív, ahol konfigurálható az időtartam, 5 perc között – 25 óra kínál. Jegyzékfájl szűrés működés közbeni DVR ablakának létrehozása az archívum felső keresztül media törlése nélkül is használható. Ha sikeresen szeretne biztosítani egy korlátozott DVR-ablak áthelyezése élő oldala, és a egy időben megtartandó egy nagyobb méretű archiválási ablak számos forgatókönyv közül választhat. A szórást küldő számítógép lehet, hogy szeretné használni az adatokat, a DVR ablakban jelölje ki a legjobb pillanatokból összeválogatott kívül esik, vagy adjon meg másik DVR windows a különböző eszközökön szeretnék. Például a mobileszközök a legtöbb kezelik a nagy DVR windows (is rendelkezik egy 2 perces DVR ablak mobileszközökhöz és a egy órás asztali ügyfelek esetén).

![DVR ablak][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff (élő pozíciótól) beállítása
Jegyzékfájl szűrés használható néhány másodpercig eltávolítása egy élő programot élő szélétől. Szűrés lehetővé teszi, hogy sikeresen, tekintse meg a bemutatót az előzetes verzió kiadvány ponton és a hirdetmény beszúrási pontok létrehozása előtt a megtekintők az adatfolyamhoz (biztonsági-ki által 30 másodperc). Ezután leküldése a hirdetmények az ügyfél-keretrendszereket időt a fogadott és feldolgozni az információkat a hirdetés lehetőség előtt sikeresen.

A hirdetés támogatás mellett a LiveBackoff beállítás segítségével a megtekintők pozíció beállítása, hogy amikor az ügyfelek konfigurációsodródás, és nyomja le az élő edge továbbra is hozzáférhetnek a töredékek helyett HTTP 404-es vagy 412 hiba-kiszolgálóról.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Egyetlen szűrő szabályokban több kombinálása
Egyetlen szűrő több szűrési szabály kombinálhatók. Például megadhat egy "tartomány szabály" befutók eltávolítása egy élő archívumot, és elérhető bitsebességre való átkódolása ki is szűrheti. Több szűrési szabályok alkalmazásakor a végeredmény az összes szabály metszet.

![több-szabályok][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinált szűrők (szűrő összeállítás)

Több szűrő, egy egyetlen URL-címben is kombinálhatók. 

Az alábbi forgatókönyvet mutat be, hogy miért érdemes a szűrők kombinálásával:

1. A videó tulajdonságait, például Android- vagy iPAD eszközökhöz szűrése (ahhoz, hogy korlátozza a videó minőségű) kell. Szeretné eltávolítani a nem kívánt tulajdonságok, akkor kell létrehoznia egy szűrő az eszközprofilok alkalmas. Fiókszűrők társítás minden további nélkül azonos media services fiók alatt az összes eszköz használható. 
2. Szeretné is, és a egy eszköz a kezdő és záró idő záró szóközöket. Ennek érdekében szeretné az Eszközintelligencia-szűrő létrehozásához, és a kezdő és záró idő beállítása. 
3. Mindkét ezeket a szűrőket összevonására (nélkül kombináció, hozzá kell a tisztítás szűréséhez, amelyek nehezebbé szűrő használat szűrésének minőségi).

A szűrők kombinálásával, állítsa be a szűrő nevét a jegyzékfájl listára kell pontosvesszővel elválasztott URL-CÍMÉT. Tegyük fel, hogy egy szűrő nevű *MyMobileDevice* , amely szűri tulajdonságait, és van egy másik nevű *MyStartTime* beállítása egy adott kezdési idő. Ehhez hasonló kombinálhatja őket:

Legfeljebb három szűrők kombinálásával. 

További információkért lásd: [ez](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

- A tartozó értékeket **forceEndTimestamp**, **presentationWindowDuration**, és **liveBackoffDuration** VoD szűrő nem kell beállítani. Csak élő szűrő célokra szolgálnak. 
- Dinamikus jegyzékfájl működik, Képcsoporttal határok (kulcs keretek), ezért vágást rendelkezik Képcsoporttal pontosságát. 
- Használhatja ugyanazt a fiókot és az Eszközintelligencia szűrők szűrő nevet. Eszközintelligencia szűrők rendelkezik nagyobb prioritással, és felülírja a fiók szűrők.
- Frissít egy szűrőt, ha a streamvégpont frissítéséhez a szabályok akár 2 percet vesz igénybe. Ha a tartalom kiszolgálása egyes szűrők használatával (és a proxyk és a CDN gyorsítótárazza a gyorsítótárak), player hibák frissítése ezeket a szűrőket eredményezhet. Javasoljuk, hogy a gyorsítótár ürítése a szűrő frissítése után. Ha ezt a beállítást nem lehetséges, fontolja meg egy másik szűrővel.
- Az ügyfeleknek kell manuálisan a jegyzékfájl letöltése és elemzése, a pontos startTimestamp és időskálára.
    
    - A számok az adategység tulajdonságainak meghatározásához [lekérése, és vizsgálja meg a jegyzékfájl](#get-and-examine-manifest-files).
    - Az eszközintelligencia-szűrő időbélyeg tulajdonságainak beállítása a képletet: <br/>startTimestamp = &lt;kezdő időpont a jegyzékfájlban&gt; +  &lt;szűrő kezdési idő másodpercben várt&gt;* időskálára


## <a name="next-steps"></a>További lépések

A következő cikkek bemutatják, hogyan hozhat létre programozott módon a szűrők.  

- [Szűrők létrehozása REST API-k](filters-dynamic-manifest-rest-howto.md)
- [Szűrők létrehozása .NET használatával](filters-dynamic-manifest-dotnet-howto.md)
- [Szűrők létrehozása a CLI-vel](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
