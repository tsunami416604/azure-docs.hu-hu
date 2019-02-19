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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405727"
---
# <a name="define-account-filters-and-asset-filters"></a>Adja meg a fiók és eszköz szűrőket  

Az ügyfelek számára (élő eseményeket vagy igény szerinti Videószolgáltatás streaming) tartalomtovábbításkor az ügyfél igényelhet az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot. Az Azure Media Services lehetővé teszi, hogy meghatározza a fiók és a tartalom eszköz szűrőket. 

Szűrők kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek, például a következők: 

- Lejátszani egy videót (a teljes videó lejátszása) helyett csak egy részét. Példa:
  - Csökkentse a jegyzékfájlt ("aljelentés klip szűrés"), az élő esemény alárendelt klip megjelenítéséhez, vagy
  - Trim ("vágás egy videó") egy videó kezdete.
- Csak a megadott beállításkészletben és/vagy a megadott nyelv nyomon követi az eszköz, amellyel a tartalmat ("megjelenítésszűrés") által támogatott kézbesítéséhez. 
- Módosítsa a megjelenítési ablakot (DVR) annak érdekében, hogy a Windows Media player ("módosításának bemutató időszak") megadott DVR időszak korlátozott hosszát.

Media Services által [dinamikus jegyzékfájlok](filters-dynamic-manifest-overview.md) előre definiált szűrők alapján. Miután meghatározott szűrőket, az ügyfelek lehetett használni őket a streamelési URL-CÍMÉT. Az adaptív sávszélességű streamelési protokollok szűrőket is lehet alkalmazni: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming.

Az alábbi táblázatban néhány példa az URL-címek szűrőket jeleníti meg:

|Protokoll|Példa|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>Szűrők megadása

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

## <a name="example"></a>Példa

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

## <a name="next-steps"></a>További lépések

A következő cikkek bemutatják, hogyan hozhat létre programozott módon a szűrők.  

- [Szűrők létrehozása REST API-k](filters-dynamic-manifest-rest-howto.md)
- [Szűrők létrehozása .NET használatával](filters-dynamic-manifest-dotnet-howto.md)
- [Szűrők létrehozása a CLI-vel](filters-dynamic-manifest-cli-howto.md)

