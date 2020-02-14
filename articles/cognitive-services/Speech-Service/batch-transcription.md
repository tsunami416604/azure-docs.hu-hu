---
title: A Batch transzkripció használata – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Batch beszédátírási ideális, ha azt szeretné, a storage szolgáltatással, például az Azure-Blobok hang nagy mennyiségű lefényképezze. A dedikált REST API használatával hangfájlok egy közös hozzáférésű jogosultságkód (SAS) URI-mutasson, és aszinkron módon fogadni az beszédátírás.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: dc473c814cdd69204cddd976bc77f19b5db567b1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200078"
---
# <a name="how-to-use-batch-transcription"></a>A Batch-átírás használata

A Batch-átírás ideális megoldás nagy mennyiségű hang tárolásához. A dedikált REST API használatával a hangfájlok közös hozzáférésű aláírási (SAS) URI-val, az átírási eredmények aszinkron fogadásával is megadhatók.

Az API aszinkron beszéd-szöveg átírást és egyéb funkciókat kínál. A következő módszerekkel teheti elérhetővé a metódusokat a REST API használatával:

- Batch-feldolgozási kérelmek létrehozása
- Az állapot lekérdezése
- Átirat eredményeinek letöltése
- Átírási adatok törlése a szolgáltatásból

A részletes API a `Custom Speech transcriptions`vámtarifaszám alá tartozó, [hencegő dokumentumként](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)érhető el.

A Batch-átírási feladatok ütemezése a lehető legjobb megoldás szerint történik. Jelenleg nincs becslés arra az időpontra, amikor egy adott feladatnak a futó állapotba kell változnia. A normál rendszerterhelés alatt perceken belül meg kell történnie. A futó állapotban a tényleges átírást a rendszer gyorsabban dolgozza fel, mint a valós időben.

A könnyen használható API mellett nem kell egyéni végpontokat telepítenie, és nem rendelkezik egyidejűségi követelményekkel a megfigyeléshez.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscription-key"></a>Előfizetési kulcs

Ahogy a Speech Service összes funkciója esetében, létrehozhat egy előfizetési kulcsot a [Azure Portal](https://portal.azure.com) az első [lépéseket ismertető útmutatóban](get-started.md).

>[!NOTE]
> A Batch-átírás használatához standard előfizetés (S0) szükséges a Speech Service-hez. Ingyenes előfizetési kulcsok (F0) nem fog működni. További információ: [díjszabás és korlátok](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Egyéni modellek

Ha az akusztikai vagy nyelvi modellek testreszabását tervezi, kövesse az [akusztikus modellek testreszabása](how-to-customize-acoustic-models.md) és a [testreszabási nyelvi modellek](how-to-customize-language-model.md)testreszabása című témakör lépéseit. Ha a létrehozott modelleket a Batch-átírásban szeretné használni, szüksége lesz a modell-azonosítóra. A modell AZONOSÍTÓját a modell részleteinek vizsgálatakor kérheti le. A Batch átíró szolgáltatáshoz nem szükséges egy telepített egyéni végpont.

## <a name="the-batch-transcription-api"></a>A Batch Beszédátírási API

### <a name="supported-formats"></a>Támogatott formátumok

A Batch Beszédátírási API támogatja a következő formátumok:

| Formátum | Kodek | Átviteli sebesség | Mintavételi frekvencia |
|--------|-------|---------|-------------|
| WAV | A PCM | 16-bit | 8 kHz vagy 16 kHz, monó vagy sztereó |
| MP3 | A PCM | 16-bit | 8 kHz vagy 16 kHz, monó vagy sztereó |
| OGG | OPUS | 16-bit | 8 kHz vagy 16 kHz, monó vagy sztereó |

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

| Paraméter | Leírás |
|-----------|-------------|
| `ProfanityFilterMode` | Adja meg a felismerési eredményeket cenzúrázása kezelése. Az elfogadott értékek olyan `None`, amelyek letiltják a káromkodások szűrését, `Masked` amely a csillagokkal való káromkodást váltja fel, `Removed`, amely eltávolítja az eredményből az összes káromkodást, vagy `Tags`, amely a "káromkodás" címkét adja meg. Az alapértelmezett beállítás a `Masked`. |
| `PunctuationMode` | Adja meg a felismerési eredményeket írásjelek kezelése. Az elfogadott értékek olyan `None`, amelyek letiltják a központozást, `Dictated` amely explicit írásjeleket feltételez, `Automatic`, amely lehetővé teszi, hogy a dekóder a központozás vagy a `DictatedAndAutomatic`, amely a diktált írásjeleket vagy az automatikus értéket jelenti. |
| `AddWordLevelTimestamps` | Megadja, hogy a rendszer hozzáadja-e a Word szintű időbélyegeket a kimenethez. Az elfogadott értékek olyan `true`, amelyek lehetővé teszik a Word szintű időbélyegek és a `false` (az alapértelmezett érték) letiltását. |
| `AddSentiment` | Azt adja meg, hogy a rendszer milyen érzést kell hozzáadnia a teljes értékhez. Az elfogadott értékek `true`, amelyek lehetővé teszik a vélemények kiértékelését és a `false` (az alapértelmezett érték) letiltását. |
| `AddDiarization` | Meghatározza, hogy a diarization-elemzést a bemeneten kell végrehajtani, amely két hangból álló mono-csatornának kellene lennie. Az elfogadott értékek olyan `true`, amelyek lehetővé teszik a diarization és a `false` (az alapértelmezett érték) letiltását. Azt is megköveteli, hogy a `AddWordLevelTimestamps` True értékre legyen állítva.|
|`TranscriptionResultsContainerUrl`|Opcionális URL-cím a [Service sas](../../storage/common/storage-sas-overview.md) -vel egy írható tárolóhoz az Azure-ban. Az eredmény ebben a tárolóban lesz tárolva.

### <a name="storage"></a>Tárterület

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

|Űrlap|Tartalom|
|-|-|
|`Lexical`|A tényleges szavak felismerhetők.
|`ITN`|Inverz szöveg – a felismert szöveg normalizált formája. A rövidítések ("doktor Smith" a "Dr Smith"), a telefonszámok és más átalakítások alkalmazása.
|`MaskedITN`|A ITN űrlap a káromkodás maszkolásával lett alkalmazva.
|`Display`|A felismert szöveg megjelenítési formája Ez magában foglalja a hozzáadott írásjeleket és a nagybetűket.

## <a name="speaker-separation-diarization"></a>Beszélő elkülönítése (Diarization)

A Diarization a hangszórók elválasztásának folyamata egy hanganyagban. A Batch-folyamat támogatja a diarization-t, és képes a Mono Channel-felvételek két hangszórójának felismerésére. A szolgáltatás nem érhető el a sztereó felvételeken.

Minden átírási kimenet tartalmaz egy `SpeakerId`. Ha a diarization nincs használatban, a JSON-kimenetben `"SpeakerId": null` jelenik meg. A diarization két hang használatát támogatjuk, így a hangszórók `"1"`ként vagy `"2"`ként lesznek azonosítva.

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

A hangulati funkció a hangban kifejezett érzést becsüli. Az érzést 0 és 1 közötti érték fejezi ki `Negative`, `Neutral`és `Positive` hangulatban. Az érzelmi elemzés például a Call Center-forgatókönyvekben használható:

- Az ügyfelek elégedettségének megismerése
- Az ügynökök teljesítményének megismerése (a hívások fogadása)
- Megkeresi a pontos időpontot, amikor a hívás negatív irányba vált
- Mi volt a negatív hívás pozitív irányba való bekapcsolásakor
- Azonosítsa az ügyfeleket, mint a terméket vagy szolgáltatást?

A hangulat egy hangszegmensen alapul, a lexikális elem alapján. Az adott hangszegmens teljes szövege az érzelmek kiszámítására szolgál. A teljes átíráshoz nincs kiszámítva összesített hangulat.

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

Az átírási szolgáltatás nagy számú beküldött átírást képes kezelni. Az átírások állapotát lekérdezheti egy `GET` a [transzkripciós metóduson](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)keresztül. A `take` paraméter (néhány száz) megadásával megtarthatja az adatok megfelelő méretre való visszaküldését. Az eredmények lekérése után a szolgáltatásból rendszeresen [törölje az átírásokat](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) . Ez az átírási felügyeleti hívások gyors válaszait biztosítja.

## <a name="sample-code"></a>Mintakód

A teljes minták a GitHub- [minta adattárában](https://aka.ms/csspeech/samples) érhetők el a `samples/batch` alkönyvtáron belül.

Testre kell szabnia a mintakód előfizetési adatait, a szolgáltatási régiót, az olyan SAS URI-t, amely a hangfájlra mutat, és a modell-azonosítókat arra az esetre, ha egyéni akusztikai vagy nyelvi modellt szeretne használni.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

A mintakód beállítja az ügyfelet, és elküldi az átírási kérelmet. Ezután lekérdezi az állapotadatok állapotát, és kinyomtatja az átírási folyamat részleteit.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Az előző hívásokkal kapcsolatos részletes információkért tekintse meg a [hencegő dokumentumot](https://westus.cris.ai/swagger/ui/index). Az itt látható teljes minta esetében lépjen a [githubra](https://aka.ms/csspeech/samples) a `samples/batch` alkönyvtárban.

Jegyezze fel a könyvelési hang és a fogadás beszédátírási állapot aszinkron beállítása. Az ügyfél, amely létrehoz egy .NET-HTTP-alapú. Létezik egy `PostTranscriptions` módszer a hangfájl részleteinek elküldésére és egy `GetTranscriptions` metódusra az eredmények fogadásához. a `PostTranscriptions` egy leírót ad vissza, és `GetTranscriptions` használja egy leíró létrehozásához az átirat állapotának lekéréséhez.

A jelenlegi mintakód nem adja meg egy egyéni modell. A szolgáltatás használ a kiindulási modelleket alkalmazhatnak lefényképezheti a fájlt vagy fájlokat. A modellek megadásához meg ugyanezt a módszert, a modell azonosítóját a akusztikai és a nyelvi modell adhat át.

> [!NOTE]
> Alapértékek átírásakor nem kell deklarálnia az alapmodellek AZONOSÍTÓját. Ha csak a nyelvi modell AZONOSÍTÓját (és az akusztikus modell AZONOSÍTÓját) adta meg, a rendszer automatikusan kiválasztja a megfelelő akusztikus modellt. Ha csak az akusztikus modell AZONOSÍTÓját adta meg, a rendszer automatikusan kiválasztja a megfelelő nyelvi modellt.

## <a name="download-the-sample"></a>A minta letöltése

A minta a `samples/batch` könyvtárban található a [GitHub-minta adattárában](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Következő lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
