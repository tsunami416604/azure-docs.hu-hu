---
title: A Batch átírása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Batch-átírás ideális megoldás, ha nagy mennyiségű hanganyagot szeretne átírni a Storage-ban, például az Azure-blobokat. A dedikált REST API használatával a hangfájlok közös hozzáférésű aláírási (SAS) URI-val és aszinkron módon fogadhatók.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 46bfabfb2ccf091fd5dc0fcf0e9b447bad7c34d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208618"
---
# <a name="what-is-batch-transcription"></a>Mi a Batch-átírás?

A Batch átírása REST API művelet, amely lehetővé teszi nagy mennyiségű hang tárolását. A hangfájlok közös hozzáférésű aláírási (SAS) URI-val, az átírási eredmények aszinkron fogadásával is megadhatók.

Az aszinkron beszéd – szöveg átírása csak az egyik funkció. A Batch átírása REST API-kkal a következő módszereket hívhatja:



|    Kötegelt átírási művelet                                             |    Módszer    |    REST API hívás                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Új átírást hoz létre.                                              |    POST      |    API/speechtotext/v 2.0/átiratok            |
|    Lekéri a hitelesített előfizetéshez tartozó átírások listáját.    |    GET       |    API/speechtotext/v 2.0/átiratok            |
|    Lekéri a támogatott területi beállítások listáját az offline átírásokhoz.              |    GET       |    API/speechtotext/v 2.0/átiratok/területi beállítások    |
|    Frissíti az azonosító alapján azonosított átirat megváltoztathatatlan részleteit.    |    JAVÍTÁS     |    API/speechtotext/v 2.0/átiratok/{azonosító}       |
|    Törli a megadott átírási feladatot.                                 |    DELETE    |    API/speechtotext/v 2.0/átiratok/{azonosító}       |
|    Lekéri az adott azonosító által azonosított átírást.                        |    GET       |    API/speechtotext/v 2.0/átiratok/{azonosító}       |




Áttekintheti és tesztelheti a részletes API-t, amely egy [hencegő dokumentumként](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)érhető el `Custom Speech transcriptions`a fejléc alatt.

A Batch-átírási feladatok ütemezése a lehető legjobb megoldás szerint történik. Jelenleg nincs becslés arra az időpontra, amikor a feladatok a futó állapotba változnak. A normál rendszerterhelés alatt perceken belül meg kell történnie. A futó állapotban a tényleges átírást a rendszer gyorsabban dolgozza fel, mint a valós időben.

A könnyen használható API mellett nem kell egyéni végpontokat telepítenie, és nem rendelkezik egyidejűségi követelményekkel a megfigyeléshez.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscription-key"></a>Előfizetői azonosító

Ahogy a Speech Service összes funkciója esetében, létrehozhat egy előfizetési kulcsot a [Azure Portal](https://portal.azure.com) az első [lépéseket ismertető útmutatóban](get-started.md).

>[!NOTE]
> A Batch-átírás használatához standard előfizetés (S0) szükséges a Speech Service-hez. Az ingyenes előfizetési kulcsok (F0) nem működnek. További információ: [díjszabás és korlátok](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Egyéni modellek

Ha az akusztikai vagy nyelvi modellek testreszabását tervezi, kövesse az [akusztikus modellek testreszabása](how-to-customize-acoustic-models.md) és a [testreszabási nyelvi modellek](how-to-customize-language-model.md)testreszabása című témakör lépéseit. Ha a létrehozott modelleket a Batch-átírásban szeretné használni, szüksége lesz a modell-azonosítóra. A modell AZONOSÍTÓját a modell részleteinek vizsgálatakor kérheti le. A Batch átíró szolgáltatáshoz nem szükséges egy telepített egyéni végpont.

## <a name="the-batch-transcription-api"></a>A Batch-átírási API

### <a name="supported-formats"></a>Támogatott formátumok

A Batch transzkripciós API a következő formátumokat támogatja:

| Formátum | Codec | Sávszélességű | Mintavételezési arány                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bites  | 8 kHz vagy 16 kHz, monó vagy sztereó |
| MP3    | PCM   | 16 bites  | 8 kHz vagy 16 kHz, monó vagy sztereó |
| VORBIS    | OPUS  | 16 bites  | 8 kHz vagy 16 kHz, monó vagy sztereó |

A sztereó hangadatfolyamok esetében a bal és a jobb oldali csatorna az átírás során oszlik meg. Minden csatornához létre kell hozni egy JSON-eredményhalmaz fájlját. A Kimondás után generált időbélyegek lehetővé teszik a fejlesztő számára a rendezett végső átirat létrehozását.

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
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Konfigurációs tulajdonságok

Ezeket a választható tulajdonságokat az átírás konfigurálásához használhatja:

:::row:::
   :::column span="1":::
      **Paraméter**
   :::column-end:::
   :::column span="2":::
      **Leírás**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Meghatározza, hogyan kezelhető a káromkodás az eredmények felismerésében. Az elfogadott értékek `None` a káromkodás szűrésének letiltására, `Masked` a káromkodás és a csillagokkal `Removed` való kiváltására, az eredményből való `Tags` káromkodás eltávolítására, vagy a "káromkodás" címkék hozzáadására szolgálnak. Az alapértelmezett beállítás: `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Meghatározza, hogyan kezelhető a központozás a felismerési eredményekben. Az elfogadott értékek `None` az `Dictated` írásjelek letiltására szolgálnak, a explicit (szóbeli) `Automatic` írásjelek megjelenítéséhez, hogy a dekóder a központozással legyen kezelve, vagy `DictatedAndAutomatic` pedig diktált és automatikus írásjeleket használjon. Az alapértelmezett beállítás: `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Megadja, hogy a rendszer hozzáadja-e a Word szintű időbélyegeket a kimenethez. Az elfogadott értékek `true` lehetővé teszik a Word szintű időbélyegek és `false` a (az alapértelmezett érték) letiltását.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Itt adható meg, hogy a rendszer a kiértékelésre alkalmazza-e az érzelmi elemzést. Az elfogadott értékek `true` lehetővé teszik a `false` (az alapértelmezett érték) engedélyezését és letiltását. További részletekért tekintse meg [Hangulatelemzés](#sentiment-analysis) .
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Meghatározza, hogy a diarization-elemzést a bemeneten kell végrehajtani, amely a várt két hangokat tartalmazó monó-csatorna. Az elfogadott értékek `true` lehetővé teszik a `false` diarization és (az alapértelmezett érték) letiltását. Azt is megköveteli `AddWordLevelTimestamps` , hogy igaz értékre legyen állítva.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Opcionális URL-cím a [Service sas](../../storage/common/storage-sas-overview.md) -vel egy írható tárolóhoz az Azure-ban. Az eredmény ebben a tárolóban tárolódik.
:::row-end:::

### <a name="storage"></a>Storage

A Batch átirata támogatja az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) -t, hogy hang-és írási átírásokat olvasson a tárolóba.

## <a name="the-batch-transcription-result"></a>A Batch átírásának eredménye

A Mono bemeneti hang esetében egy átírási eredményű fájl jön létre. Sztereó bemeneti hang esetén két transzkripciós eredmény-fájl jön létre. Mindegyik a következő struktúrával rendelkezik:

```json
{
  "AudioFileResults":[
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

Az eredmény az alábbi űrlapokat tartalmazza:

:::row:::
   :::column span="1":::
      **Űrlap**
   :::column-end:::
   :::column span="2":::
      **Tartalom**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      A tényleges szavak felismerhetők.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Inverz szöveg – a felismert szöveg normalizált formája. A rövidítések ("doktor Smith" a "Dr Smith"), a telefonszámok és más átalakítások alkalmazása.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      A ITN űrlap a káromkodás maszkolásával lett alkalmazva.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      A felismert szöveg megjelenítési formája A rendszer a hozzáadott írásjeleket és a nagybetűket is tartalmazza.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Beszélő elkülönítése (Diarization)

A Diarization a hangszórók elválasztásának folyamata egy hanganyagban. A Batch-folyamat támogatja a diarization-t, és képes a Mono Channel-felvételek két hangszórójának felismerésére. A szolgáltatás nem érhető el a sztereó felvételeken.

Minden átírási kimenet `SpeakerId`tartalmaz egy. Ha a diarization nincs használatban, a `"SpeakerId": null` JSON-kimenetben jelenik meg. A diarization két hangokat támogatunk, így a hangszórók a vagy `"1"` `"2"`a néven azonosíthatók.

A diarization igényléséhez egyszerűen hozzá kell adnia a megfelelő paramétert a HTTP-kérelemben az alább látható módon.

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

A fenti kérelemben szereplő paraméterek esetében a Word-szintű időbélyegeket is be kell kapcsolni.

## <a name="sentiment-analysis"></a>Hangulatelemzés

A hangulati funkció a hangban kifejezett érzést becsüli. Az érzést 0 és 1 közötti érték fejezi ki a `Negative`, `Neutral`a és `Positive` a hangulat alapján. Az érzelmi elemzés például a Call Center-forgatókönyvekben használható:

- Az ügyfelek elégedettségének megismerése
- Az ügynökök teljesítményének megismerése (a hívások fogadása)
- Megkeresi a pontos időpontot, amikor a hívás negatív irányba vált
- Mi volt a negatív hívás pozitív irányba való bekapcsolásakor
- Azonosítsa az ügyfeleket, mint a terméket vagy szolgáltatást?

A hangulat egy hangszegmensen alapul, a lexikális elem alapján. Az adott hangszegmens teljes szövege az érzelmek kiszámítására szolgál. A teljes átíráshoz nincs kiszámítva összesített hangulat. A hangulat elemzése jelenleg csak az angol nyelven érhető el.

> [!NOTE]
> Javasoljuk Ehelyett a Microsoft Text Analytics API használatát. Fejlettebb funkciókat kínál, mint például a legfontosabb kifejezés kinyerése, az automatikus nyelvfelismerés és egyebek. A [text Analytics dokumentációjában](https://azure.microsoft.com/services/cognitive-services/text-analytics/)talál információkat és mintákat.
>

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

## <a name="best-practices"></a>Ajánlott eljárások

Az átírási szolgáltatás nagy számú beküldött átírást képes kezelni. Az átírások `GET` állapotát az [átírási módszer](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)használatával kérdezheti le. A `take` paraméter (néhány száz) megadásával megtarthatja az adatok megfelelő méretre való visszaküldését. Az eredmények lekérése után a szolgáltatásból rendszeresen [törölje az átírásokat](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) . Ez garantálja az átírásos felügyeleti hívások gyors válaszait.

## <a name="sample-code"></a>Mintakód

A teljes minták a [GitHub minta adattárában](https://aka.ms/csspeech/samples) érhetők el az `samples/batch` alkönyvtáron belül.

Testre kell szabnia a mintakód előfizetési adatait, a szolgáltatási régiót, az olyan SAS URI-t, amely a hangfájlra mutat, és a modell-azonosítókat arra az esetre, ha egyéni akusztikai vagy nyelvi modellt szeretne használni.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

A mintakód beállítja az ügyfelet, és elküldi az átírási kérelmet. Ezután lekérdezi az állapotadatok adatait, és kinyomtatja az átírási folyamat részleteit.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Az előző hívásokkal kapcsolatos részletes információkért tekintse meg a [hencegő dokumentumot](https://westus.cris.ai/swagger/ui/index). Az itt látható teljes minta esetében lépjen a [githubra](https://aka.ms/csspeech/samples) az `samples/batch` alkönyvtárban.

Jegyezze fel a hang-és átírási állapot küldésének aszinkron beállítását. A létrehozott ügyfél egy .NET HTTP-ügyfél. Létezik egy `PostTranscriptions` módszer a hangfájl részleteinek elküldésére, `GetTranscriptions` valamint az eredmények fogadására szolgáló metódusra. `PostTranscriptions`egy leírót ad vissza `GetTranscriptions` , és a használatával létrehoz egy leírót az átirat állapotának lekéréséhez.

Az aktuális mintakód nem ad meg egyéni modellt. A szolgáltatás az alapmodelleket használja a fájl vagy fájlok átírásához. A modellek megadásához ugyanazt a módszert adja át, mint az akusztikus és a nyelvi modell azonosítói.

> [!NOTE]
> Alapértékek átírásakor nem kell deklarálnia az alapmodellek AZONOSÍTÓját. Ha csak a nyelvi modell AZONOSÍTÓját (és az akusztikus modell AZONOSÍTÓját) adta meg, a rendszer automatikusan kiválasztja a megfelelő akusztikus modellt. Ha csak az akusztikus modell AZONOSÍTÓját adta meg, a rendszer automatikusan kiválasztja a megfelelő nyelvi modellt.

## <a name="download-the-sample"></a>A minta letöltése

A mintát a GitHub- `samples/batch` [minta adattárában](https://aka.ms/csspeech/samples)található könyvtárban találja.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
