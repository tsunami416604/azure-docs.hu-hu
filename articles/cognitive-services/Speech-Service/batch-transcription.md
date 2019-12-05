---
title: A Batch transzkripció használata – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Batch-átírás ideális megoldás, ha nagy mennyiségű hanganyagot szeretne átírni a Storage-ban, például az Azure-blobokat. A dedikált REST API használatával a hangfájlok közös hozzáférésű aláírási (SAS) URI-val és aszinkron módon fogadhatók.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 2cccd17ce04b3954a7d0720d9ba25bbe792da3b6
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806337"
---
# <a name="why-use-batch-transcription"></a>Miért érdemes a Batch-átírást használni?

A Batch-átírás ideális megoldás, ha nagy mennyiségű hanganyagot szeretne átírni a Storage-ban, például az Azure-blobokat. A dedikált REST API használatával a hangfájlok közös hozzáférésű aláírási (SAS) URI-val és aszinkron módon fogadhatók.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscription-key"></a>Előfizetői azonosító

Ahogy a Speech Service összes funkciója esetében, létrehozhat egy előfizetési kulcsot a [Azure Portal](https://portal.azure.com) az első [lépéseket ismertető útmutatóban](get-started.md). Ha tervbe kell vennie az alapmodellből való átírásokat, a kulcs létrehozásához mindössze annyit kell tennie.

>[!NOTE]
> A Batch-átírás használatához standard előfizetés (S0) szükséges a Speech Service-hez. Az ingyenes előfizetés kulcsa (F0) nem fog működni. További információ: [díjszabás és korlátok](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Egyéni modellek

Ha az akusztikus vagy nyelvi modellek testreszabását tervezi, kövesse az [akusztikus modellek testreszabása](how-to-customize-acoustic-models.md) és a [nyelvi modellek testreszabása](how-to-customize-language-model.md)című témakör lépéseit. Ha a létrehozott modelleket a Batch-átírásban szeretné használni, szüksége lesz a modell-azonosítóra. Ez az azonosító nem a végpontok részletes nézetében megtalált végpont-azonosító, ez a modell azonosítója, amelyet a modellek részleteinek kiválasztásakor kérhet le.

## <a name="the-batch-transcription-api"></a>A Batch-átírási API

A Batch-átírási API aszinkron beszéd-szöveg átírást biztosít, valamint további funkciókat is tartalmaz. Ez egy REST API, amely a következő metódusokat teszi elérhetővé:

1. Batch-feldolgozási kérelmek létrehozása
1. Lekérdezés állapota
1. Átiratok letöltése

> [!NOTE]
> A Batch-átírási API ideális a Call Centers szolgáltatásokhoz, ami általában több ezer órányi hangot is felhalmoz. Megkönnyíti a nagy mennyiségű hangfelvételek átírását.

### <a name="supported-formats"></a>Támogatott formátumok

A Batch transzkripciós API a következő formátumokat támogatja:

| Formátum | Codec | Sávszélességű | Mintavételezési arány |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bites | 8 vagy 16 kHz, monó, sztereó |
| MP3 | PCM | 16 bites | 8 vagy 16 kHz, monó, sztereó |
| VORBIS | OPUS | 16 bites | 8 vagy 16 kHz, monó, sztereó |

A sztereó hangadatfolyamok esetében a Batch transzkripciós API a bal és a jobb oldali csatornát feldarabolja az átírás során. Az eredményrel rendelkező két JSON-fájl egyetlen csatornából lett létrehozva. A kizáró időbélyegek lehetővé teszik a fejlesztő számára a rendezett végső átirat létrehozását. Ez a példa a káromkodás szűrésére, írásjelekre és a Word szintű időbélyegekre vonatkozó tulajdonságokat tartalmazza.

### <a name="configuration"></a>Konfiguráció

A konfigurációs paraméterek JSON-ként vannak megadva:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> A Batch-átírási API REST-szolgáltatást használ az átírások, az állapotuk és a hozzájuk tartozó eredmények kérelmezéséhez. Az API bármilyen nyelven használható. A következő szakasz az API használatának módját ismerteti.

### <a name="configuration-properties"></a>Konfigurációs tulajdonságok

Ezeket a választható tulajdonságokat az átírás konfigurálásához használhatja:

| Paraméter | Leírás |
|-----------|-------------|
| `ProfanityFilterMode` | Meghatározza, hogyan kezelhető a káromkodás az eredmények felismerésében. Az elfogadott értékek olyan `None`, amelyek letiltják a káromkodások szűrését, `masked` amely a csillagokkal való káromkodást váltja fel, `removed`, amely eltávolítja az eredményből az összes káromkodást, vagy `tags`, amely a "káromkodás" címkét adja meg. Az alapértelmezett beállítás: `masked`. |
| `PunctuationMode` | Meghatározza, hogyan kezelhető a központozás a felismerési eredményekben. Az elfogadott értékek olyan `None`, amelyek letiltják a központozást, `dictated` amely explicit írásjeleket feltételez, `automatic`, amely lehetővé teszi, hogy a dekóder a központozás vagy a `dictatedandautomatic`, amely a diktált írásjeleket vagy az automatikus értéket jelenti. |
 | `AddWordLevelTimestamps` | Megadja, hogy a rendszer hozzáadja-e a Word szintű időbélyegeket a kimenethez. Az elfogadott értékek olyan `true`, amelyek lehetővé teszik a Word szintű időbélyegek és a `false` (az alapértelmezett érték) letiltását. |
 | `AddSentiment` | Azt adja meg, hogy a rendszer milyen érzést kell hozzáadnia a teljes értékhez. Az elfogadott értékek `true`, amelyek lehetővé teszik a vélemények kiértékelését és a `false` (az alapértelmezett érték) letiltását. |
 | `AddDiarization` | Meghatározza, hogy a diarization-elemzést a bemeneten kell végrehajtani, amely két hangból álló mono-csatornának kellene lennie. Az elfogadott értékek olyan `true`, amelyek lehetővé teszik a diarization és a `false` (az alapértelmezett érték) letiltását. Azt is megköveteli, hogy a `AddWordLevelTimestamps` True értékre legyen állítva.|

### <a name="storage"></a>Adattárolás

A Batch átirata támogatja az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) -t, hogy hang-és írási átírásokat olvasson a tárolóba.

## <a name="speaker-separation-diarization"></a>Beszélő elkülönítése (Diarization)

A Diarization a hangszórók elválasztásának folyamata egy hanganyagban. A Batch-folyamat támogatja a Diarization-t, és képes a Mono Channel-felvételek két hangszórójának felismerésére.

Ahhoz, hogy a rendszer feldolgozza a hang-átírási kérést a diarization, egyszerűen hozzá kell adnia a megfelelő paramétert a HTTP-kérelemben az alább látható módon.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

A Word szintű időbélyegeket is be kell kapcsolni, mivel a fenti kérelemben szereplő paraméterek jelzik. 

A megfelelő hang tartalmazza a számokkal azonosított hangsugárzókat (jelenleg csak két hang támogatott), így a hangszórók "Speaker 1" és "Speaker 2" néven lesznek azonosítva, majd az átírás kimenete.

Azt is vegye figyelembe, hogy a Diarization nem érhető el a sztereó felvételekben. Emellett minden JSON-kimenet tartalmazni fogja a beszélő címkét. Ha a diarization nincs használatban, a "beszélő: NULL" jelenik meg a JSON-kimenetben.

> [!NOTE]
> A Diarization minden régióban és minden területi beállításban elérhető.

## <a name="sentiment"></a>Vélemény

A hangulat a Batch átírási API új funkciója, amely a Call Center tartomány egyik fontos funkciója. Az ügyfelek a `AddSentiment` paramétereket használhatják a kéréseiket

1.  Az ügyfelek elégedettségének megismerése
2.  Az ügynökök teljesítményének megismerése (a hívások fogadása)
3.  Pontosan olyan időpontot kell kijelölni, amikor egy hívás negatív irányba vált
4.  Pinpoint – a negatív hívások pozitívra váltása
5.  Azonosítsa az ügyfeleket, mint a terméket vagy szolgáltatást?

A hangulat egy olyan hangszegmensre mutat, amelyben egy hangszegmens van meghatározva, mivel az idő a Kimondás (eltolás) kezdete és a bájtos adatfolyam végének észlelése között esik. Az adott szegmensen belüli teljes szöveg az érzelmek kiszámítására szolgál. A teljes hívás vagy az egyes csatornák teljes beszéde esetében nem számítunk fel összesített hangulati értékeket. Ezek az összesítések továbbra is érvényben maradnak a tartomány tulajdonosának.

A hangulat a lexikális űrlapon van alkalmazva.

A JSON-kimenet mintája az alábbihoz hasonlóan néz ki:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```
A szolgáltatás egy olyan hangulati modellt használ, amely jelenleg a bétaverzióban van.

## <a name="sample-code"></a>Mintakód

A teljes minták a GitHub- [minta adattárában](https://aka.ms/csspeech/samples) érhetők el a `samples/batch` alkönyvtáron belül.

Testre kell szabnia a mintakód előfizetési adatait, a szolgáltatási régiót, az olyan SAS URI-t, amely a hangfájlra mutat, és a modell-azonosítókat arra az esetre, ha egyéni akusztikai vagy nyelvi modellt szeretne használni.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

A mintakód beállítja az ügyfelet, és elküldi az átírási kérelmet. Ezután lekérdezi az állapotadatok állapotát, és kinyomtatja az átírási folyamat részleteit.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Az előző hívásokkal kapcsolatos részletes információkért tekintse meg a [hencegő dokumentumot](https://westus.cris.ai/swagger/ui/index). Az itt látható teljes minta esetében lépjen a [githubra](https://aka.ms/csspeech/samples) a `samples/batch` alkönyvtárban.

Jegyezze fel a hang-és átírási állapot küldésének aszinkron beállítását. A létrehozott ügyfél egy .NET HTTP-ügyfél. Létezik egy `PostTranscriptions` módszer a hangfájl részleteinek elküldésére és egy `GetTranscriptions` metódusra az eredmények fogadásához. a `PostTranscriptions` egy leírót ad vissza, és `GetTranscriptions` használja egy leíró létrehozásához az átirat állapotának lekéréséhez.

Az aktuális mintakód nem ad meg egyéni modellt. A szolgáltatás az alapmodelleket használja a fájl vagy fájlok átírásához. A modellek megadásához ugyanazt a módszert adja át, mint az akusztikus és a nyelvi modell azonosítói.

> [!NOTE]
> Alapértékek átírásakor nem kell deklarálnia az alapmodellek AZONOSÍTÓját. Ha csak a nyelvi modell AZONOSÍTÓját (és az akusztikus modell AZONOSÍTÓját) adta meg, a rendszer automatikusan kiválasztja a megfelelő akusztikus modellt. Ha csak az akusztikus modell AZONOSÍTÓját adta meg, a rendszer automatikusan kiválasztja a megfelelő nyelvi modellt.

## <a name="download-the-sample"></a>A minta letöltése

A minta a `samples/batch` könyvtárban található a [GitHub-minta adattárában](https://aka.ms/csspeech/samples).

> [!NOTE]
> A Batch-átírási feladatok ütemezése a legjobb megoldás, ha a feladat futási állapotra változik. A futási állapotot követően a tényleges átírás feldolgozása gyorsabb, mint a hang valós ideje.

## <a name="next-steps"></a>Következő lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
