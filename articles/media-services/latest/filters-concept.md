---
title: Az Azure Media Services szűrők megadása
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, így az ügyfél használhatja őket stream konkrét szakaszokra datového proudu. A Media Services dinamikus jegyzékek érdekében, ami a szelektív hoz létre.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66225413"
---
# <a name="filters"></a>Szűrők

Az ügyfelek számára (események élő vagy igény szerinti Videószolgáltatás) tartalomtovábbításkor az ügyfél az alapértelmezett eszköz jegyzékfájl leírtnál rugalmasabb előfordulhat, hogy kell. Az Azure Media Services által [dinamikus jegyzékfájlok](filters-dynamic-manifest-overview.md) előre definiált szűrők alapján. 

Szűrők kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek, például a következők: 

- Lejátszani egy videót (a teljes videó lejátszása) helyett csak egy részét. Példa:
  - Csökkentse a jegyzékfájlt ("aljelentés klip szűrés"), az élő esemény alárendelt klip megjelenítéséhez, vagy
  - Trim ("vágás egy videó") egy videó kezdete.
- Csak a megadott beállításkészletben és/vagy a megadott nyelv nyomon követi az eszköz, amellyel a tartalmat ("megjelenítésszűrés") által támogatott kézbesítéséhez. 
- Módosítsa a megjelenítési ablakot (DVR) annak érdekében, hogy a Windows Media player ("módosításának bemutató időszak") megadott DVR időszak korlátozott hosszát.

Media Services lehetővé teszi, hogy hozzon létre **szűrők fiók** és **eszköz szűrők** a tartalomhoz. Emellett az előre létrehozott szűrőket a társíthatja egy **Streamelési lokátor**.

## <a name="defining-filters"></a>Szűrők megadása

A szűrők két típusa van: 

* [Szűrők fiók](https://docs.microsoft.com/rest/api/media/accountfilters) (globális) – az Azure Media Services-fiókban lévő bármely eszközre is alkalmazható, a fiók élettartama.
* [Az eszközintelligencia szűrők](https://docs.microsoft.com/rest/api/media/assetfilters) (helyi) – csak egy eszköz, amellyel a szűrő lett társítva létrehozásakor kell alkalmazni, az eszköz élettartama. 

**Szűrők fiók** és **eszköz szűrők** típusok definiálása/leíró a szűrő pontosan azonos tulajdonságokkal rendelkezik. Kivéve, amikor létrehozza a **eszköz szűrő**, meg kell adnia az objektum nevét, amelyhez hozzá szeretne rendelni a szűrő.

A forgatókönyvtől függően úgy dönt, hogy milyen típusú szűrő megfelelőbbek, (eszköz szűrő vagy szűrő). Fiókszűrők alkalmasak (megjelenítésszűrés) eszközprofilok ahol eszköz szűrők és a záró szóközöket egy adott eszköz használható.

A következő tulajdonságok használatával írja le a szűrőket. 

|Name (Név)|Leírás|
|---|---|
|firstQuality|Az első minőségi sávszélességű a szűrő.|
|presentationTimeRange|A bemutató időtartományban. Ez a tulajdonság jegyzékfájl kezdő és záró pontot bemutató időszak hossza és az élő kezdőpozíciója szűréshez használható. <br/>További információkért lásd: [PresentationTimeRange](#presentationtimerange).|
|nyomon követi|A számok kiválasztási feltételeket. További információkért lásd: [nyomon követi](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Ezzel a tulajdonsággal rendelkező **eszköz szűrők**. Nem javasoljuk, hogy a tulajdonság a **Fiókszűrők**.

|Name (Név)|Leírás|
|---|---|
|**endTimestamp**|A videó igény szerinti (VoD) vonatkozik.<br/>A Live Streaming bemutató csendes figyelmen kívül hagyja és alkalmazza a bemutató véget ér, a stream válik VoD.<br/>Ez az egy olyan hosszú értéket, amely egy abszolút végpont a bemutató kerekítve a legközelebbi tovább Képcsoporttal Start jelöli. Az egység a időskálára, így egy endTimestamp 1800000000-3 percig lesz.<br/>StartTimestamp és endTimestamp használja az azokat a töredékeket, amely a lista (manifest) lesz.<br/>Ha például startTimestamp = 40000000 és endTimestamp = 100000000 az alapértelmezett időskálán használatával hoz létre egy listát, amely tartalmazza a töredékek munkafüzet 4 másodperc és a VoD-bemutató 10 másodperc. Ha egy töredék feladata a határt, a teljes töredék fog szerepelni a jegyzékfájlban.|
|**forceEndTimestamp**|Csak az élő Streamelés vonatkozik.<br/>Azt jelzi, hogy a endTimestamp tulajdonság jelen kell lennie. Ha az értéke igaz, endTimestamp meg kell adni, vagy egy hibás kérés kódot ad vissza.<br/>Megengedett értékek: False (hamis), az igaz.|
|**liveBackoffDuration**|Csak az élő Streamelés vonatkozik.<br/> Ez az érték határozza meg a legújabb élő pozíciótól, amely egy ügyfelet is szolgálnak.<br/>Ezt a tulajdonságot használja, azt élő lejátszási pozícióját, hozzon létre egy kiszolgálóoldali puffer lejátszók.<br/>Ez a tulajdonság a mértékegysége időskálára (lásd alább).<br/>Az élő visszatartási időtartama maximális mérete 300 másodperc (3000000000).<br/>Például a valós élő Edge késleltetett 2000000000 azt jelenti, hogy a legújabb elérhető tartalmak érték: 20 másodperc érték.|
|**presentationWindowDuration**|Csak az élő Streamelés vonatkozik.<br/>PresentationWindowDuration használatával a alkalmazni egy csúszóablakban töredékek fel a listára.<br/>Ez a tulajdonság a mértékegysége időskálára (lásd alább).<br/>Például állítsa be a presentationWindowDuration = a alkalmazni egy kétperces csúszóablakban 1200000000. Média élő széle 2 percen belül fog szerepelni a listában. Ha egy töredék feladata a határt, a teljes töredék a lista fog szerepelni. A minimális bemutató ablak időtartamának érték 60 másodperc.|
|**startTimestamp**|A videó igény szerinti (VoD-) vagy élő Streamelés vonatkozik.<br/>Ez az egy olyan hosszú értéket, amely a Stream-látható abszolút kezdő pont jelöli. Az érték beolvasása kerekítve a legközelebbi tovább Képcsoporttal Start. Az egység a időskálára, így egy startTimestamp 150000000, a 15 másodperc lesz.<br/>StartTimestamp és endTimestampp használja az azokat a töredékeket, amely a lista (manifest) lesz.<br/>Ha például startTimestamp = 40000000 és endTimestamp = 100000000 az alapértelmezett időskálán használatával hoz létre egy listát, amely tartalmazza a töredékek munkafüzet 4 másodperc és a VoD-bemutató 10 másodperc. Ha egy töredék feladata a határt, a teljes töredék fog szerepelni a jegyzékfájlban.|
|**időskálára**|Bemutató időtartomány, a lépésközök számát egy második megadott összes időbélyegeket és időtartamok vonatkozik.<br/>Alapértelmezett érték 10000000 - tízmillió lépésekben egy második, ahol minden egyes fokozathoz állítsa 100 nanoszekundumban hosszú lehet.<br/>Például ha szeretné beállítani egy startTimestamp 30 másodperc, használna 300000000 érték az alapértelmezett időskálán használatakor.|

### <a name="tracks"></a>nyomon követi

Azt adja meg, szűrő nyomon követése Tulajdonságfeltételek (FilterTrackPropertyConditions) listáját, amelyen a nyomon követi a Stream (élő adatfolyamos és igény szerinti Videószolgáltatás) kell szerepelnie, dinamikusan létrehozott jegyzékfájl. A szűrők használatával egy logikai kombinált **és** és **vagy** műveletet.

Szűrő nyomon követése Tulajdonságfeltételek követési típusok, értékeket (az alábbi táblázatban ismertetett) és (egyenlő, NotEqual) műveleteket ismertetik. 

|Name (Név)|Leírás|
|---|---|
|**Átviteli sebesség**|Az átviteli sebesség, a nyomon követése a szűréshez használni.<br/><br/>A javasolt érték, amely egy bitsebességre való átkódolása bit / másodperc. Ha például "0-2427000".<br/><br/>Megjegyzés: használhatja adott sávszélességű értékének, például a 250000 (bit / másodperc), amíg ez a megközelítés van nem ajánlott, mivel a pontos bitsebességre való átkódolása ingadozhaz, egy eszköz a másikba.|
|**FourCC**|A szám FourCC értéket használja a szűréshez.<br/><br/>Az első elemét kodekek formátum, a megadott érték [RFC 6381](https://tools.ietf.org/html/rfc6381). Jelenleg a következő kodekek támogatottak: <br/>Videó: "Avc1", "hev1", "hvc1"<br/>Hang: "Mp4a", "3-EK"<br/><br/>Annak megállapításához, nyomon követi az adategység FourCC értékeit, beolvasása, és tekintse át a jegyzékfájlt.|
|**Nyelv**|A nyelv, a nyomon követése a szűréshez használni.<br/><br/>Adja meg, mint a megadott RFC 5646 szeretné egy nyelv a címke értéke. Például "hu".|
|**Name (Név)**|A track nevét használja a szűréshez.|
|**Típus**|A szám típust használják a szűréshez.<br/><br/>A következő értékek használata engedélyezett: "videó", "hang" vagy "szöveg".|

### <a name="example"></a>Példa

Az alábbi példa egy élő adatfolyam-szűrő határozza meg: 

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

## <a name="associating-filters-with-streaming-locator"></a>Streamelési lokátor szűrők társítása

Egy listában megadhatja [eszköz vagy a fiók szűrők](filters-concept.md) a a [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). A [dinamikus Packager](dynamic-packaging-overview.md) vonatkozik ez a lista azokat az URL-címet adja meg az ügyfél és-szűrők. Állít elő, ez a kombináció egy [dinamikus Manifest](filters-dynamic-manifest-overview.md), amely alapján az URL-címben szűrők + szűrők a Streamelési lokátor meg kell adnia. 

Lásd az alábbi példákat:

* [Szűrők társítása Streamelési lokátor – .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Szűrők társítása Streamelési lokátor – CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Szűrők frissítése
 
**A streamelési Lokátorok** amelyek nem frissíthető, miközben szűrők frissítheti. 

Nem javasoljuk, hogy a szűrők aktívan közzétett társított a definíció frissítésének az **Streamelési lokátor**, különösen akkor, ha a CDN engedélyezve van. Adatfolyam-kiszolgálók és a CDN rendelkezhet a belső gyorsítótárakat, amelyek elavult gyorsítótárazott adatokat vissza kell eredményezhet. 

Ha módosítani kell a szűrő definícióját, fontolja meg egy új szűrő létrehozása és hozzáadása, hogy a **Streamelési lokátor** URL-cím vagy egy új közzétételi **Streamelési lokátor** közvetlenül hivatkozik, amely a szűrőt.

## <a name="next-steps"></a>További lépések

A következő cikkek bemutatják, hogyan hozhat létre programozott módon a szűrők.  

- [Szűrők létrehozása REST API-k](filters-dynamic-manifest-rest-howto.md)
- [Szűrők létrehozása .NET használatával](filters-dynamic-manifest-dotnet-howto.md)
- [Szűrők létrehozása a CLI-vel](filters-dynamic-manifest-cli-howto.md)

