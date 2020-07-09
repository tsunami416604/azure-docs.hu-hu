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
ms.openlocfilehash: 9804992aee318fdc34815bdbe4187144704cd667
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85099763"
---
# <a name="what-is-batch-transcription"></a>Mi a Batch-átírás?

A Batch átírása REST API művelet, amely lehetővé teszi nagy mennyiségű hang tárolását. A hangfájlok közös hozzáférésű aláírási (SAS) URI-val, az átírási eredmények aszinkron fogadásával is megadhatók. Az új v 3.0 API-val lehetősége van egy vagy több hangfájl átírására, vagy egy teljes tároló feldolgozására.

Az aszinkron beszéd – szöveg átírása csak az egyik funkció. A Batch átírása REST API-kkal a következő módszereket hívhatja:



|    Kötegelt átírási művelet                                             |    Metódus    |    REST API hívás                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Új átírást hoz létre.                                              |    POST      |    speechtotext/v 3.0/átiratok            |
|    Lekéri a hitelesített előfizetéshez tartozó átírások listáját.    |    GET       |    speechtotext/v 3.0/átiratok            |
|    Lekéri a támogatott területi beállítások listáját az offline átírásokhoz.              |    GET       |    speechtotext/v 3.0/átiratok/területi beállítások    |
|    Frissíti az azonosító alapján azonosított átirat megváltoztathatatlan részleteit.    |    JAVÍTÁS     |    speechtotext/v 3.0/átiratok/{azonosító}       |
|    Törli a megadott átírási feladatot.                                 |    DELETE    |    speechtotext/v 3.0/átiratok/{azonosító}       |
|    Lekéri az adott azonosító által azonosított átírást.                        |    GET       |    speechtotext/v 3.0/átiratok/{azonosító}       |
|    Lekéri a megadott azonosító által azonosított átirat eredményének fájljait.    |    GET       |    speechtotext/v 3.0/átiratok/{azonosító}/fájlok |




Áttekintheti és tesztelheti a részletes API-t, amely [hencegő dokumentumként](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)érhető el.

A Batch-átírási feladatok ütemezése a lehető legjobb megoldás szerint történik. Jelenleg nincs becslés arra az időpontra, amikor a feladatok a futó állapotba változnak. A normál rendszerterhelés alatt perceken belül meg kell történnie. A futó állapotban a tényleges átírást a rendszer gyorsabban dolgozza fel, mint a valós időben.

A könnyen használható API mellett nem kell egyéni végpontokat telepítenie, és nem rendelkezik egyidejűségi követelményekkel a megfigyeléshez.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscription-key"></a>Előfizetői azonosító

Ahogy a Speech Service összes funkciója esetében, létrehozhat egy előfizetési kulcsot a [Azure Portal](https://portal.azure.com) az első [lépéseket ismertető útmutatóban](get-started.md).

>[!NOTE]
> A Batch-átírás használatához standard előfizetés (S0) szükséges a Speech Service-hez. Az ingyenes előfizetési kulcsok (F0) nem működnek. További információ: [díjszabás és korlátok](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Egyéni modellek

Ha azt tervezi, hogy testreszabja a modelleket, kövesse az [akusztikai Testreszabás](how-to-customize-acoustic-models.md) és a [nyelvi Testreszabás](how-to-customize-language-model.md)lépéseit. Ha a létrehozott modelleket a Batch-átírásban szeretné használni, szüksége lesz a modell helyére. A modell helyét a modell (tulajdonság) részleteinek vizsgálatával kérheti le `self` . A Batch átíró szolgáltatáshoz *nem szükséges* egy telepített egyéni végpont.

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

A konfigurációs paraméterek JSON-ként vannak megadva (egy vagy több különálló fájl):

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

A konfigurációs paraméterek JSON-ként vannak megadva (teljes tároló feldolgozása):

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

Ha egyéni betanított modelleket szeretne használni a Batch-Átírásokban, az alábbihoz hasonló módon hivatkozhat rájuk:

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
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
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Meghatározza, hogyan kezelhető a káromkodás az eredmények felismerésében. Az elfogadott értékek a `None` káromkodás szűrésének letiltására, a `Masked` káromkodás és a csillagokkal való kiváltására, `Removed` az eredményből való káromkodás eltávolítására, vagy a `Tags` "káromkodás" címkék hozzáadására szolgálnak. Az alapértelmezett beállítás: `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Meghatározza, hogyan kezelhető a központozás a felismerési eredményekben. Az elfogadott értékek az `None` írásjelek letiltására szolgálnak, a `Dictated` explicit (szóbeli) írásjelek megjelenítéséhez, `Automatic` hogy a dekóder a központozással legyen kezelve, vagy `DictatedAndAutomatic` pedig diktált és automatikus írásjeleket használjon. Az alapértelmezett beállítás: `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Megadja, hogy a rendszer hozzáadja-e a Word szintű időbélyegeket a kimenethez. Az elfogadott értékek `true` lehetővé teszik a Word szintű időbélyegek és `false` a (az alapértelmezett érték) letiltását.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Meghatározza, hogy a diarization-elemzést a bemeneten kell végrehajtani, amely a várt két hangokat tartalmazó monó-csatorna. Az elfogadott értékek `true` lehetővé teszik a diarization és `false` (az alapértelmezett érték) letiltását. Azt is megköveteli, hogy `wordLevelTimestampsEnabled` igaz értékre legyen állítva.
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      A feldolgozandó csatornaszám választható tömbje. Itt megadható a hangfájlban található elérhető csatornák egy részhalmaza (például `0` csak). Ha nincs megadva, a rendszer a csatornákat `0` és `1` az alapértelmezettként írja le őket.
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Az átiratok befejezését követően nem választható időtartam az átírások automatikus törléséhez. A a `timeToLive` tömeges feldolgozás során hasznos, hogy a rendszer végül törölni lehessen őket (például: `PT12H` ). Ha nincs megadva, vagy a értékre van állítva `PT0H` , az átírás nem lesz automatikusan törölve.
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Opcionális URL-cím a [Service sas](../../storage/common/storage-sas-overview.md) -vel egy írható tárolóhoz az Azure-ban. Az eredmény ebben a tárolóban tárolódik. Ha nincs megadva, a Microsoft az eredményeket a Microsoft által kezelt tároló tárolóban tárolja. Ha az átírást törli az [átírás](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)meghívásával, a rendszer törli az eredményül kapott adatértéket is.
:::row-end:::

### <a name="storage"></a>Storage

A Batch átirata támogatja az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) -t, hogy hang-és írási átírásokat olvasson a tárolóba.

## <a name="the-batch-transcription-result"></a>A Batch átírásának eredménye

Minden bemeneti hang esetében egy átírási eredményű fájl jön létre. Az eredményül kapott fájlok listáját az [átírási fájlok](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles)meghívásával kérheti le. Ez a metódus az átíráshoz tartozó találati fájlok listáját adja vissza. Egy adott bemeneti fájl átírási fájljának megkereséséhez szűrje az összes visszaadott fájlt a és a értékkel `kind`  ==  `Transcription` `name`  ==  `{originalInputName.suffix}.json` .

Minden átírási eredmény fájlja a következő formátumú:

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
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
      `lexical`
   :::column-end:::
   :::column span="2":::
      A tényleges szavak felismerhetők.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Inverz szöveg – a felismert szöveg normalizált formája. A rövidítések ("doktor Smith" a "Dr Smith"), a telefonszámok és más átalakítások alkalmazása.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      A ITN űrlap a káromkodás maszkolásával lett alkalmazva.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      A felismert szöveg megjelenítési formája A rendszer a hozzáadott írásjeleket és a nagybetűket is tartalmazza.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Beszélő elkülönítése (Diarization)

A Diarization a hangszórók elválasztásának folyamata egy hanganyagban. A Batch-folyamat támogatja a diarization-t, és képes a Mono Channel-felvételek két hangszórójának felismerésére. A szolgáltatás nem érhető el a sztereó felvételeken.

Az diarization-mel rendelkező átiratok kimenete `Speaker` minden egyes átmásolt kifejezéshez tartalmaz egy bejegyzést. Ha a diarization nincs használatban, a tulajdonság `Speaker` nem szerepel a JSON-kimenetben. A diarization két hangokat támogatunk, így a hangszórók a vagy a néven azonosíthatók `1` `2` .

A diarization igényléséhez egyszerűen hozzá kell adnia a megfelelő paramétert a HTTP-kérelemben az alább látható módon.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

A Word-szintű időbélyegeket engedélyezni kell, ha a fenti kérelemben szereplő paraméterek jelzik.

## <a name="best-practices"></a>Ajánlott eljárások

Az átírási szolgáltatás nagy számú beküldött átírást képes kezelni. Az átiratok állapotát lekérdezheti a `GET` [Get átiratok](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)használatával. Az eredmények lekérése után rendszeresen hívja meg a [delete átiratot](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) a szolgáltatásból. Másik lehetőségként állítsa be a `timeToLive` tulajdonságot egy ésszerű értékre az eredmények végleges törlésének biztosítása érdekében.

## <a name="sample-code"></a>Mintakód

A teljes minták a [GitHub minta adattárában](https://aka.ms/csspeech/samples) érhetők el az `samples/batch` alkönyvtáron belül.

Ha egyéni modellt szeretne használni, frissítse a mintát az előfizetési adatokkal, a szolgáltatási régióval, az adott hangfájlra mutató SAS URI-val, és adja meg a modell helyét.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

A mintakód beállítja az ügyfelet, és elküldi az átírási kérelmet. Ezután lekérdezi az állapotadatok adatait, és kinyomtatja az átírási folyamat részleteit.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

Az előző hívásokkal kapcsolatos részletes információkért tekintse meg a [hencegő dokumentumot](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Az itt látható teljes minta esetében lépjen a [githubra](https://aka.ms/csspeech/samples) az `samples/batch` alkönyvtárban.

Jegyezze fel a hang-és átírási állapot küldésének aszinkron beállítását. A létrehozott ügyfél egy .NET HTTP-ügyfél. Létezik egy `PostTranscriptions` módszer a hangfájl részleteinek elküldésére, valamint az `GetTranscriptions` állapotok fogadására szolgáló metódusra. `PostTranscriptions`egy leírót ad vissza, és a `GetTranscriptions` használatával létrehoz egy leírót az átirat állapotának lekéréséhez.

Az aktuális mintakód nem ad meg egyéni modellt. A szolgáltatás az alapmodellt használja a fájl vagy fájlok átírásához. A modell megadásához ugyanezt a módszert kell átadni az egyéni modellhez tartozó modell-referenciára.

> [!NOTE]
> Az alapértékek átírásakor nem kell deklarálnia az alapmodell AZONOSÍTÓját.

## <a name="download-the-sample"></a>A minta letöltése

A mintát a `samples/batch` [GitHub-minta adattárában](https://aka.ms/csspeech/samples)található könyvtárban találja.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
