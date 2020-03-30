---
title: Szűrők definiálása az Azure Media Servicesszolgáltatásban
description: Ez a témakör azt ismerteti, hogyan hozhat létre szűrőket, hogy az ügyfél használhassa őket az adatfolyam adott szakaszainak streameléséhez. A Media Services dinamikus jegyzékeket hoz létre a szelektív streamelés eléréséhez.
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
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251465"
---
# <a name="filters"></a>Szűrők

Amikor a tartalom az ügyfelek (Élő közvetítés események vagy video igény szerint), az ügyfél szükség lehet nagyobb rugalmasságra, mint amit az alapértelmezett eszköz jegyzékfájljában leírt. Az Azure Media Services előre definiált szűrőkalapján kínál [dinamikus jegyzékfájlokat.](filters-dynamic-manifest-overview.md) 

A szűrők olyan kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek számára, hogy például a következőket tegyék: 

- A videónak csak egy részét játssza le (a teljes videó lejátszása helyett). Példa:
  - A jegyzékfájl kicsinyítése élő esemény ("alklipszűrés") részklipjének megjelenítéséhez, vagy
  - A videó elejének levágása ("videó vágása").
- Csak a megadott interpretációkat és/vagy meghatározott nyelvi sávokat jelenítse meg, amelyeket a tartalom lejátszására használt eszköz támogat ("interpretációs szűrés"). 
- Állítsa be a bemutatóablakot (DVR) annak érdekében, hogy a lejátszóban a DVR ablak ának korlátozott hossza biztosított ("bemutatóablak beállítása").

A Media Services lehetővé teszi **fiókszűrők** és **eszközszűrők** létrehozását a tartalomhoz. Ezenkívül az előre létrehozott szűrőket társítani is használhatja egy **streamelési lokátorral.**

## <a name="defining-filters"></a>Szűrők definiálása

Kétféle szűrő létezik: 

* [Fiókszűrők](https://docs.microsoft.com/rest/api/media/accountfilters) (globális) – az Azure Media Services-fiók bármely eszközére alkalmazható, élettartama a fiók.
* [Eszközszűrők](https://docs.microsoft.com/rest/api/media/assetfilters) (helyi) – csak olyan eszközre alkalmazható, amelyhez a szűrő a létrehozáskor társítva volt, élettartama van az eszközből. 

**A fiókszűrők** és **az eszközszűrők** típusai pontosan azonos tulajdonságokkal rendelkeznek a szűrő definiálásához/leírásához. Az **eszközszűrő**létrehozásakor kívül meg kell adnia azt az eszköznevet, amelyhez a szűrőt társítani szeretné.

A forgatókönyvtől függően ön dönti el, hogy milyen típusú szűrő megfelelőbb (eszközszűrő vagy fiókszűrő). A fiókszűrők olyan eszközprofilokhoz (interpretációszűréshez) alkalmasak, ahol eszközszűrők használhatók egy adott eszköz vágására.

A szűrők leírásához a következő tulajdonságokat használhatja. 

|Név|Leírás|
|---|---|
|firstQuality (elsőMinőség)|A szűrő első minőségi bitrátája.|
|bemutatóTimeRange|A bemutató időtartománya. Ez a tulajdonság a jegyzékfájl kezdő/végpontjainak, a bemutatóablak hosszának és az élő kezdőpozíciónak a szűrésére szolgál. <br/>További információ: [PresentationTimeRange](#presentationtimerange).|
|Számok|A számok kiválasztási feltételeket. További információ: [tracks](#tracks)|

### <a name="presentationtimerange"></a>bemutatóTimeRange

Használja ezt a tulajdonságot **eszközszűrőkkel.** A tulajdonságot nem ajánlott **fiókszűrőkkel**beállítani.

|Név|Leírás|
|---|---|
|**endTimestamp**|Igény szerinti videóra (VoD) vonatkozik.<br/>Az élő közvetítésű bemutató esetében a program csendben figyelmen kívül hagyja, és alkalmazza, amikor a bemutató befejeződik, és az adatfolyam VoD-vá válik.<br/>Ez egy hosszú érték, amely a bemutató abszolút végpontját jelenti, a legközelebbi következő GOP-kezdetre kerekítve. Az egység az időskála, így egy 1800000000 endTimestamp 3 percig lenne.<br/>A startTimestamp és a endTimestamp használatával vágja le a listában (jegyzékfájlban) lévő töredékeket.<br/>Például a startTimestamp=40000000 és az endTimestamp=10000000az alapértelmezett időskálával létrehoz egy lejátszási listát, amely a VoD-bemutató 4 másodperce és 10 másodperce közötti töredékeket tartalmaz. Ha egy töredék közrefogja a határt, az egész töredék szerepelni fog a jegyzékben.|
|**forceEndTimestamp**|Csak az élő közvetítésre vonatkozik.<br/>Azt jelzi, hogy a endTimestamp tulajdonságnak jelen kell-e lennie. Ha igaz, a endTimestamp paramétert meg kell adni, vagy hibás kérelemkódot kell visszaadni.<br/>Engedélyezett értékek: hamis, igaz.|
|**liveBackoffDuration**|Csak az élő közvetítésre vonatkozik.<br/> Ez az érték határozza meg a legújabb élő pozíciót, amelyet az ügyfél kereshet.<br/>Ezzel a tulajdonsággal késleltetheti az élő lejátszási pozíciót, és kiszolgálóoldali puffert hozhat létre a játékosok számára.<br/>A tulajdonság egysége időskála (lásd alább).<br/>A maximális visszautazás időtartama 300 másodperc (3000000000).<br/>A 200000000000 érték például azt jelenti, hogy a legfrissebb elérhető tartalom 20 másodperccel késik a valós élő éltől.|
|**bemutatóAblakIdőtartam**|Csak az élő közvetítésre vonatkozik.<br/>A presentationWindowDuration segítségével a lejátszási listába foglalandó töredékek tolóablakát alkalmazhatja.<br/>A tulajdonság egysége időskála (lásd alább).<br/>Állítsa be például a presentationWindowDuration=1200000000set egy kétperces csúszóablak alkalmazásához. Az élő éltől számított 2 percen belül az adathordozó szerepelni fog a lejátszási listában. Ha egy töredék közrefogja a határt, a teljes töredék szerepelni fog a lejátszási listában. A bemutatóablak minimális időtartama 60 másodperc.|
|**startTimestamp**|Igény szerinti videóra (VoD) vagy élő közvetítésre vonatkozik.<br/>Ez egy hosszú érték, amely az adatfolyam abszolút kezdőpontját jelöli. Az érték a legközelebbi következő GOP-kezdetre lesz kerekítve. Az egység az időskála, így a startTimestamp 150000000 lenne 15 másodpercig.<br/>A startTimestamp és az endTimestampp használatával vágja le a listában (jegyzékfájlban) lévő töredékeket.<br/>Például a startTimestamp=40000000 és az endTimestamp=10000000az alapértelmezett időskálával létrehoz egy lejátszási listát, amely a VoD-bemutató 4 másodperce és 10 másodperce közötti töredékeket tartalmaz. Ha egy töredék közrefogja a határt, az egész töredék szerepelni fog a jegyzékben.|
|**Időskála**|A bemutató időtartományában lévő összes időbélyegre és időtartamra vonatkozik, egy másodperc alatt megadott növekmények számaként.<br/>Az alapértelmezett érték 10000000 - tízmillió növekmény egy másodperc alatt, ahol minden növekmény 100 nanoszekundum hosszú lenne.<br/>Ha például 30 másodpercre szeretne beállítani egy startTimestamp értéket, az alapértelmezett időskáláhasználatakor 300000000 értéket kell használnia.|

### <a name="tracks"></a>Számok

Megadhatja a szűrőpálya tulajdonságfeltételeinek listáját (FilterTrackPropertyConditions), amely alapján a stream láncsávjait (élő közvetítés vagy igény szerinti videó) dinamikusan létrehozott jegyzékfájlba kell foglalni. A szűrők kombinálása logikai **ÉS** **VAGY** művelettel történik.

A pálya szűrési tulajdonságfeltételei a pályatípusokat, az értékeket (az alábbi táblázatban ismertetjük) és a műveleteket (Equal, NotEqual) írják le. 

|Név|Leírás|
|---|---|
|**Bitráta**|A sáv bitrátáját használhatja szűrésre.<br/><br/>Az ajánlott érték bitper szekundumban lévő bittartomány. Például: "0-2427000".<br/><br/>Megjegyzés: bár használhat egy adott bitráta értéket, például 250000(bit/másodperc), ez a megközelítés nem ajánlott, mivel a pontos bitsebességek az egyik eszközről a másikra ingadozhatnak.|
|**Fourcc**|A sáv FourCC értékét használja szűrésre.<br/><br/>Az érték a kodekformátum első eleme a [6381.](https://tools.ietf.org/html/rfc6381) Jelenleg a következő kodekek támogatottak: <br/>Videóhoz: "avc1", "hev1", "hvc1"<br/>Audio: "mp4a", "ec-3"<br/><br/>Egy eszköz sávjának FourCC-értékeinek meghatározásához vegye le és vizsgálja meg a jegyzékfájl.|
|**Nyelv**|Használja a zeneszám nyelvét a szűréshez.<br/><br/>Az érték az 5646-os számú RFC-ben meghatározott nyelv címkéje. Például "en".|
|**Név**|A szűréshez használja a zeneszám nevét.|
|**Típus**|Használja a sáv típusát a szűréshez.<br/><br/>A következő értékek megengedettek: "video", "audio", vagy "text".|

### <a name="example"></a>Példa

A következő példa egy élő közvetítésszűrőt határoz meg: 

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

## <a name="associating-filters-with-streaming-locator"></a>Szűrők társítása a streamelési lokátorral

Megadhatja az [eszköz- vagy számlaszűrők](filters-concept.md) listáját a [streamelési lokátoron.](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body) A [dinamikus csomagzó](dynamic-packaging-overview.md) alkalmazza ezt a szűrőlistát az ügyfél által az URL-ben megadott szűrőkkel együtt. Ez a kombináció [dinamikus jegyzéket](filters-dynamic-manifest-overview.md)hoz létre, amely a streamelési lokátoron megadott URL+ szűrők szűrőin alapul. 

Lásd az alábbi példákat:

* [Szűrők társítása a streamelési lokátorral – .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Szűrők társítása a streamelési lokátorral - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Szűrők frissítése
 
**A streamelési lokátorok** nem frissíthetők, amíg a szűrők frissíthetők. 

Nem ajánlott frissíteni az aktívan közzétett **streamelési lokátorhoz**társított szűrők definícióját, különösen akkor, ha a CDN engedélyezve van. A streamelési kiszolgálók és a CDN-ek belső gyorsítótárai elavult gyorsítótárazott adatok visszaadását eredményezhetik. 

Ha a szűrő definícióját módosítani kell, fontolja meg egy új szűrő létrehozását, és adja hozzá a **streamelési lokátor** URL-címéhez, vagy közzétesz egy új **streamelési lokátort,** amely közvetlenül hivatkozik a szűrőre.

## <a name="next-steps"></a>További lépések

Az alábbi cikkek bemutatják, hogyan hozhat létre szűrőket programozott módon.  

- [Szűrők létrehozása REST API-kkal](filters-dynamic-manifest-rest-howto.md)
- [Szűrők létrehozása .NET használatával](filters-dynamic-manifest-dotnet-howto.md)
- [Szűrők létrehozása CLI-vel](filters-dynamic-manifest-cli-howto.md)

