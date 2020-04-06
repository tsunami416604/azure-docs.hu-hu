---
title: Mi a kötegelt átírás - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A kötegelt átírás ideális, ha nagy mennyiségű hangot szeretne átírni a storage-ban, például az Azure Blobs. A dedikált REST API használatával rámutathat a közös hozzáférésű uri-azonosítóval (SAS) rendelkező hangfájlokra, és aszinkron módon fogadhat átiratokat.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: fb39f1ec83416ee8ab2a33b514971110db0c0b17
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668838"
---
# <a name="what-is-batch-transcription"></a>Mi az a kötegátírás?

A kötegelt átírás rest API-műveletek készlete, amely lehetővé teszi nagy mennyiségű, a tárolóban lévő hang átírását. Rámutathat a hangfájlokra egy megosztott hozzáférésű azonosítóval (SAS) és aszinkron módon fogadhat átirati eredményeket.

Az aszinkron beszéd-szöveg átírás csak az egyik funkció. A kötegelt átírásrest api-k segítségével hívhatja meg a következő módszereket:



|    Kötegátírási művelet                                             |    Módszer    |    REST API-hívás                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Új átiratot hoz létre.                                              |    POST      |    api/speechtotext/v2.0/transzkripciók            |
|    Lekéri a hitelesített előfizetés átiratainak listáját.    |    GET       |    api/speechtotext/v2.0/transzkripciók            |
|    Lekéri az offline átiratok támogatott területi listáit.              |    GET       |    api/speechtotext/v2.0/transcriptions/locales    |
|    Frissíti az azonosítója által azonosított átírás változékony részleteit.    |    Javítás     |    api/speechtotext/v2.0/transcriptions/{id}       |
|    Törli a megadott átírási feladatot.                                 |    DELETE    |    api/speechtotext/v2.0/transcriptions/{id}       |
|    Leveszi az átiratot az adott azonosítóval.                        |    GET       |    api/speechtotext/v2.0/transcriptions/{id}       |




A cím `Custom Speech transcriptions`alatt áttekintheti és tesztelheti a részletes API-t, amely [Swagger-dokumentumként](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)érhető el.

A kötegelt átírási feladatok ütemezése a legjobb erőfeszítés alapján történik. Jelenleg nincs becslés, ha egy feladat a futó állapotba változik. Normál rendszerterhelés esetén perceken belül meg kell történnie. Miután a futó állapotban, a tényleges átírás feldolgozása gyorsabb, mint a hang valós időben.

A könnyen használható API mellett nem kell egyéni végpontokat üzembe helyeznie, és nincsenek megfigyelni egyidejűségi követelmények.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscription-key"></a>Előfizetői azonosító

A beszédfelismerési szolgáltatás összes funkciójához ugyanúgy, mint a beszédfelismerési szolgáltatás összes szolgáltatása, az [Első lépések útmutatót](get-started.md)követve hozzon létre egy előfizetési kulcsot az [Azure Portalról.](https://portal.azure.com)

>[!NOTE]
> A kötegelt átírás használatához szabványos előfizetés (S0) szükséges. Az ingyenes előfizetési kulcsok (F0) nem működnek. További információt az árképzés és a korlátok című témakörben [talál.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

### <a name="custom-models"></a>Egyéni modellek

Ha az akusztikai vagy nyelvi modellek testreszabását tervezi, kövesse az [Akusztikai modellek testreszabása](how-to-customize-acoustic-models.md) és a Design testreszabási nyelvi modellek című témakör [lépéseit.](how-to-customize-language-model.md) A létrehozott modellek kötegelt átírásban való használatához a modellazonosítóikra van szükség. A modellazonosító t a modell részleteinek vizsgálatakor kérheti le. Az üzembe helyezett egyéni végpont nem szükséges a kötegelt átírási szolgáltatáshoz.

## <a name="the-batch-transcription-api"></a>A kötegelt átírás API

### <a name="supported-formats"></a>Támogatott formátumok

A Batch Transcription API a következő formátumokat támogatja:

| Formátum | Codec | Bitráta | Mintavételi arány                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bites  | 8 kHz vagy 16 kHz, monó vagy sztereó |
| Mp3    | PCM   | 16 bites  | 8 kHz vagy 16 kHz, monó vagy sztereó |
| Ogg    | Opus  | 16 bites  | 8 kHz vagy 16 kHz, monó vagy sztereó |

Sztereó hangfolyamok esetén a bal és a jobb csatorna felosztása az átírás során történik. Minden csatorna hoz létre egy JSON-eredményfájlt. Az utterance (kifejezés) létrehozott időbélyegek lehetővé teszik a fejlesztő számára, hogy egy rendezett végleges átiratot hozzon létre.

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

### <a name="configuration-properties"></a>Konfigurációtulajdonságai

Az átírás konfigurálásához használja az alábbi választható tulajdonságokat:

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
      A káromkodás kezelésének módját határozza meg a felismerési eredményekben. Az elfogadott `None` értékek a káromkodásszűrés `Masked` letiltása, a káromkodás csillagokkal való helyettesítése, `Removed` az `Tags` összes káromkodás eltávolítása az eredményből, vagy "káromkodás" címkék hozzáadása. Az alapértelmezett beállítás: `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Itt adható meg, hogyan kell kezelni az írásjeleket a felismerési eredményekben. Az elfogadott `None` értékek az írásjelek letiltása, `Dictated` explicit (szóbeli) írásjelek reklezése, `Automatic` a dekóder írásjelek kezelése, vagy `DictatedAndAutomatic` diktált és automatikus írásjelek használata. Az alapértelmezett beállítás: `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Itt adható meg, hogy a program hozzáadja-e a szószintű időbélyegeket a kimenethez. Az elfogadott `true` értékek a szószintű `false` időbélyegek és (az alapértelmezett érték) letiltásának engedélyezése.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Itt adható meg, hogy a rendszer alkalmazza-e a véleményelemzést az utterance (kifejezés) kifejezésre. Az elfogadott `true` értékek `false` et kell engedélyezni, és (az alapértelmezett érték) letiltani azt.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Itt adható meg, hogy diarizációs elemzést kell végezni a bemeneten, amely várhatóan két hangot tartalmazó mono csatorna lesz. Az elfogadott `true` értékek lehetővé `false` teszik a diarization és (az alapértelmezett érték) letiltását. Azt is `AddWordLevelTimestamps` megköveteli, hogy kell állítani, hogy igaz.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Választható [URL-cím a szolgáltatás SAS](../../storage/common/storage-sas-overview.md) egy írható tároló az Azure-ban. Az eredmény ebben a tárolóban tárolódik.
:::row-end:::

### <a name="storage"></a>Storage

A kötegelt átírás támogatja az [Azure Blob storage-ot](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) a hangolvasáshoz és az átiratok tárolásba írásához.

## <a name="the-batch-transcription-result"></a>A kötegelt átírás eredménye

A mono bemeneti hang hoz létre egy átíráseredmény-fájlt. A sztereó bemeneti hang esetében két átírási eredményfájl jön létre. Mindegyiknek van ez a szerkezete:

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

Az eredmény a következő űrlapokat tartalmazza:

| Űrlap        | Tartalom                                                                                                                                                  |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Lexical`   | A tényleges szavakat felismerték.                                                                                                                             |
| `ITN`       | A felismert szöveg inverz-szöveg-normalizált formája. Rövidítések ("doctor smith" a "dr smith"), telefonszámok, és egyéb átalakítások alkalmazzák. |
| `MaskedITN` | A káromkodással ellátott ITN-űrlap alkalmazása.                                                                                                             |
| `Display`   | A felismert szöveg megjelenítési formája. A hozzáadott írásjelek és a nagybetűs írásjelek is benne vannak.                                                             |

## <a name="speaker-separation-diarization"></a>Hangszóró elválasztása (diarizáció)

Diarization az a folyamat, elválasztó hangszórók egy darab audio. Batch csővezetékünk támogatja a diarizációt, és képes felismerni két hangszórót mono csatornás felvételeken. A funkció sztereó felvételeken nem érhető el.

Minden átírás kimenet `SpeakerId`tartalmaz egy . Ha nem használ diarizációt, az a JSON kimenetben jelenik meg. `"SpeakerId": null` A diarizáció hozunk két hang, így `"1"` `"2"`a hangszórók azonosítása vagy .

A diarization kéréséhez egyszerűen hozzá kell adnia a megfelelő paramétert a HTTP-kérelemhez az alábbiak szerint.

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

A word szintű időbélyegeket is "be kell kapcsolni", ahogy a fenti kérelemben szereplő paraméterek jelzik.

## <a name="sentiment-analysis"></a>Hangulatelemzés

A hangulatfunkció a hangban kifejezett érzést becsüli meg. A vélemény 0 és 1 közötti `Negative`értékkel van kifejezve a , `Neutral`és `Positive` a hangulat. A véleményelemzés például hívásközpont-forgatókönyvekben használható:

- Betekintést nyerhet az ügyfelek elégedettségébe
- Betekintést nyerhet az ügynökök teljesítményéről (a hívásokat fogadó csapat)
- Találja meg azt az időpontot, amikor egy hívás negatív irányba fordult
- Mi ment jól, amikor fordult a negatív hívás egy pozitív irányba
- Azonosítsa, hogy az ügyfelek mit szeretnek, és mit nem szeretnek egy termékben vagy szolgáltatásban

A hangulat pontozott hangszegmensenként a lexikális űrlap alapján. Az adott hangszegmensen belüli teljes szöveg a hangulat kiszámítására szolgál. A teljes átírásra nem számít összesített véleményt. Jelenleg hangulatelemzés csak az angol nyelv.

A JSON kimeneti minta az alábbihoz hasonlóan néz ki:

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

Az átírási szolgáltatás képes kezelni a nagy számú benyújtott átírások. Az átiratok állapotát lekérdezheti az `GET` [átírási módszeren](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)keresztül. A paraméter (néhány száz) megadásával `take` tartsa meg az ésszerű méretűre visszaadott információkat. Az eredmények lekérése után rendszeresen törölje az [átiratokat](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) a szolgáltatásból. Ez garantálja az átíráskezelési hívások gyors válaszait.

## <a name="sample-code"></a>Mintakód

A teljes minták az `samples/batch` alkönyvtáron belüli [GitHub-mintatárban](https://aka.ms/csspeech/samples) érhetők el.

Testre kell szabnia a mintakódot az előfizetési adatokkal, a szolgáltatási régióval, az átírandó hangfájlra mutató SAS URI-val és a modellazonosítókkal abban az esetben, ha egyéni akusztikai vagy nyelvi modellt szeretne használni.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

A mintakód beállítja az ügyfelet, és elküldi az átírási kérelmet. Ezután lekérdezi az állapotinformációkat, és kinyomtatja az átírás előrehaladásának részleteit.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Az előző hívásokról a [Swagger-dokumentumban](https://westus.cris.ai/swagger/ui/index)talál részletes információt. Az itt látható teljes minta az alkönyvtárban található [GitHub.For](https://aka.ms/csspeech/samples) the full sample shown here, go to GitHub in the `samples/batch` subdirectory.

Vegye figyelembe az aszinkron beállítás a kiküldetés audio és fogadása átírás állapotát. A létrehozott ügyfél egy .NET HTTP-ügyfél. Van egy `PostTranscriptions` módszer a hangfájl részleteinek `GetTranscriptions` elküldésére és az eredmények fogadására. `PostTranscriptions`visszaad egy `GetTranscriptions` leírót, és azt használja egy leíró létrehozásához az átírási állapot leolvasásához.

Az aktuális mintakód nem ad meg egyéni modellt. A szolgáltatás az alapmodelleket használja a fájl vagy fájlok átírásához. A modellek megadásához ugyanazt a módszert adhatja át, mint az akusztikai és nyelvi modell modellazonosítóit.

> [!NOTE]
> A kiindulási átírások, nem kell deklarálni a kiindulási modellek azonosítóját. Ha csak egy nyelvi modellazonosítót ad meg (és nincs akusztikai modellazonosító), automatikusan kiválaszt egy megfelelő akusztikai modellt. Ha csak akusztikai modellazonosítót ad meg, a program automatikusan kiválasztegy megfelelő nyelvi modellt.

## <a name="download-the-sample"></a>A minta letöltése

A minta a `samples/batch` [GitHub mintatárban](https://aka.ms/csspeech/samples)található könyvtárban található.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
