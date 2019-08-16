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
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 101cfacf071292d00556656b0df9c6bf9c15f414
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515886"
---
# <a name="why-use-batch-transcription"></a>Miért érdemes használni a Batch beszédátírási?

Batch beszédátírási ideális, ha azt szeretné, a storage szolgáltatással, például az Azure-Blobok hang nagy mennyiségű lefényképezze. A dedikált REST API használatával hangfájlok egy közös hozzáférésű jogosultságkód (SAS) URI-mutasson, és aszinkron módon fogadni az beszédátírás.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscription-key"></a>Előfizetői azonosító

A beszédfelismerési szolgáltatás összes funkcióját, és egy előfizetési kulcsot a létrehozásakor a [az Azure portal](https://portal.azure.com) a következő a [útmutató első lépésekhez](get-started.md). Ha azt tervezi, a beszédátírás kérhet az eredeti modellt, a kulcs létrehozása még minden kell tennie.

>[!NOTE]
> Beszédszolgáltatások (S0) standard előfizetést kell használnia a batch beszédátírási. Ingyenes előfizetési kulcsok (F0) nem fog működni. További információkért lásd: [díjszabás és korlátok](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Egyéni modellek

Ha az akusztikus vagy nyelvi modellek testreszabását tervezi, kövesse az [akusztikus modellek testreszabása](how-to-customize-acoustic-models.md) és a [nyelvi modellek testreszabása](how-to-customize-language-model.md)című témakör lépéseit. Ha a létrehozott modelleket a Batch-átírásban szeretné használni, szüksége lesz a modell-azonosítóra. Ez az azonosító nem a végpontok részletes nézetében megtalált végpont-azonosító, ez a modell azonosítója, amelyet a modellek részleteinek kiválasztásakor kérhet le.

## <a name="the-batch-transcription-api"></a>A Batch Beszédátírási API

A Batch Beszédátírási API aszinkron hang-szöveg transzformációs átírást, valamint olyan kiegészítő funkciókat kínál. Egy REST API-t, amely metódusokat tárja fel:

1. Kötegelt feldolgozási kérelmek létrehozása
1. Lekérdezés állapota
1. Beszédátírás letöltése

> [!NOTE]
> A Batch Beszédátírási API telefonos ügyfélszolgálatok, amely általában a több ezer órás hanganyagra gyűlnek ideális. Megkönnyíti a nagy mennyiségű hangfelvételek átírását.

### <a name="supported-formats"></a>Támogatott formátumok

A Batch Beszédátírási API támogatja a következő formátumok:

| Formátum | Kodek | Átviteli sebesség | Mintavételi frekvencia |
|--------|-------|---------|-------------|
| WAV | A PCM | 16-bit | 8 vagy 16 kHz, Monó, sztereó |
| MP3 | A PCM | 16-bit | 8 vagy 16 kHz, Monó, sztereó |
| OGG | OPUS | 16-bit | 8 vagy 16 kHz, Monó, sztereó |

Sztereó audiostreamek lejátszásával, a a Batch API beszédátírási bontja a bal és jobb csatorna a beszédátírási során. A két JSON-fájlok az eredmény az egyes jönnek létre egyetlen csatornákon. Az utterance (kifejezés) / időbélyegeket köszönhetően a fejlesztő hozzon létre egy rendezett végleges átiratok. Ez a példa a káromkodás szűrésére, írásjelekre és a Word szintű időbélyegekre vonatkozó tulajdonságokat tartalmazza.

### <a name="configuration"></a>Konfiguráció

A konfigurációs paraméterek JSON-ként vannak megadva:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
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
> A Batch Beszédátírási API egy REST-szolgáltatás beszédátírás, állapotát és kapcsolódó eredmények kérő használja. Az API bármilyen nyelvet is használhat. Ez a szakasz azt ismerteti, hogyan használja fel az API-t.

### <a name="configuration-properties"></a>Konfigurációs tulajdonságok

Ezeket a választható tulajdonságokat az átírás konfigurálásához használhatja:

| Paraméter | Leírás |
|-----------|-------------|
| `ProfanityFilterMode` | Adja meg a felismerési eredményeket cenzúrázása kezelése. Elfogadott értékek a következők `None` amely letiltja a vulgáris szűréshez `masked` csillag, amely lecseréli cenzúrázása `removed` amely eltávolítja az összes cenzúrázása az eredményből vagy `tags` ami ad "cenzúrázása" címkék. Az alapértelmezett beállítás `masked`. |
| `PunctuationMode` | Adja meg a felismerési eredményeket írásjelek kezelése. Elfogadott értékek a következők `None` ami letiltja az absztrakt, `dictated` explicit írásjelek, amiből `automatic` , amellyel a dekóder írásjelek, kezelése vagy `dictatedandautomatic` írásjelek vagy automatikus azt jelenti, amely során. |
 | `AddWordLevelTimestamps` | Megadja, hogy a rendszer hozzáadja-e a Word szintű időbélyegeket a kimenethez. Az elfogadott értékek `true` lehetővé teszik a Word szintű időbélyegek és `false` (az alapértelmezett érték) letiltását. |
 | `AddSentiment` | Azt adja meg, hogy a rendszer milyen érzést kell hozzáadnia a teljes értékhez. Az elfogadott értékek `true` lehetővé teszik a vélemények kiírását `false` és (az alapértelmezett érték) letiltását. |
 | `AddDiarization` | Meghatározza, hogy a diarization-elemzést a bemeneten kell végrehajtani, amely két hangból álló mono-csatornának kellene lennie. Az elfogadott értékek `true` lehetővé teszik a diarization `false` és (az alapértelmezett érték) letiltását. Azt is megköveteli `AddWordLevelTimestamps` , hogy igaz értékre legyen állítva.|

### <a name="storage"></a>Storage

A Batch átirata támogatja az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) -t, hogy hang-és írási átírásokat olvasson a tárolóba.

## <a name="webhooks"></a>webhooks

Előfordulhat, hogy az átírási állapot lekérdezése nem a legnagyobb teljesítményű, vagy a legjobb felhasználói élményt nyújtja. Az állapot lekérdezéséhez visszahívásokat regisztrálhat, ami értesíti az ügyfelet, ha a hosszan futó átírási feladatok befejeződtek.

További részletekért lásd: [](webhooks.md)webhookok.

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

Azt is vegye figyelembe, hogy a Diarization nem érhető el a sztereó felvételekben. Emellett minden JSON-kimenet tartalmazni fogja a beszélő címkét. Ha a diarization nincs használatban, akkor a "beszélő: NULL "a JSON-kimenetben.

> [!NOTE]
> A Diarization minden régióban és minden területi beállításban elérhető.

## <a name="sentiment"></a>Hangulat

A hangulat a Batch átírási API új funkciója, amely a Call Center tartomány egyik fontos funkciója. Az ügyfelek a `AddSentiment` következő paramétereket használhatják a kéréseiket

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

A teljes minták a [GitHub minta adattárában](https://aka.ms/csspeech/samples) érhetők el az `samples/batch` alkönyvtáron belül.

Testre kell szabnia a mintakód előfizetési adatait, a szolgáltatási régiót, az olyan SAS URI-t, amely a hangfájlra mutat, és a modell-azonosítókat arra az esetre, ha egyéni akusztikai vagy nyelvi modellt szeretne használni.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

A mintakód beállíthatja az ügyfelet, és elküldheti az átírási kérelmet. Ezután lekérdezi az állapotadatok állapotát, és kinyomtatja az átírási folyamat részleteit.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Az előző hívásokkal kapcsolatos részletes információkért tekintse meg a [hencegő dokumentumot](https://westus.cris.ai/swagger/ui/index). Az itt látható teljes minta esetében lépjen a [githubra](https://aka.ms/csspeech/samples) az `samples/batch` alkönyvtárban.

Jegyezze fel a könyvelési hang és a fogadás beszédátírási állapot aszinkron beállítása. Az ügyfél, amely létrehoz egy .NET-HTTP-alapú. Van egy `PostTranscriptions` módszer küldi a hangfájl részleteit és a egy `GetTranscriptions` metódus az eredményeket fogadására. `PostTranscriptions` a fogópont adja vissza és `GetTranscriptions` annak használatával hozzon létre egy leírót beszédátírási állapotának beolvasása.

A jelenlegi mintakód nem adja meg egy egyéni modell. A szolgáltatás használ a kiindulási modelleket alkalmazhatnak lefényképezheti a fájlt vagy fájlokat. A modellek megadásához meg ugyanezt a módszert, a modell azonosítóját a akusztikai és a nyelvi modell adhat át.

> [!NOTE]
> Alapértékek átírásakor nem kell deklarálnia az alapmodellek AZONOSÍTÓját. Ha csak a nyelvi modell AZONOSÍTÓját (és az akusztikus modell AZONOSÍTÓját) adta meg, a rendszer automatikusan kiválasztja a megfelelő akusztikus modellt. Ha csak az akusztikus modell AZONOSÍTÓját adta meg, a rendszer automatikusan kiválasztja a megfelelő nyelvi modellt.

## <a name="download-the-sample"></a>A minta letöltése

A mintát `samples/batch` a [GitHub-minta adattárában](https://aka.ms/csspeech/samples)található könyvtárban találja.

> [!NOTE]
> A Batch-átírási feladatok ütemezése a legjobb megoldás, ha a feladat futási állapotra változik. A futási állapotot követően a tényleges átírás feldolgozása gyorsabb, mint a hang valós ideje.

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
