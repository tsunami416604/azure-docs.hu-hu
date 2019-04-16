---
title: Batch Beszédátírási – beszédszolgáltatások használata
titlesuffix: Azure Cognitive Services
description: Batch beszédátírási ideális, ha azt szeretné, a storage szolgáltatással, például az Azure-Blobok hang nagy mennyiségű lefényképezze. A dedikált REST API használatával hangfájlok egy közös hozzáférésű jogosultságkód (SAS) URI-mutasson, és aszinkron módon fogadni az beszédátírás.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b389d86fe4d23e3f4ee1c66e4270a74351098129
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579359"
---
# <a name="why-use-batch-transcription"></a>Miért érdemes használni a Batch beszédátírási?

Batch beszédátírási ideális, ha azt szeretné, a storage szolgáltatással, például az Azure-Blobok hang nagy mennyiségű lefényképezze. A dedikált REST API használatával hangfájlok egy közös hozzáférésű jogosultságkód (SAS) URI-mutasson, és aszinkron módon fogadni az beszédátírás.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscription-key"></a>Előfizetői azonosító

A beszédfelismerési szolgáltatás összes funkcióját, és egy előfizetési kulcsot a létrehozásakor a [az Azure portal](https://portal.azure.com) a következő a [útmutató első lépésekhez](get-started.md). Ha azt tervezi, a beszédátírás kérhet az eredeti modellt, a kulcs létrehozása még minden kell tennie.

>[!NOTE]
> Beszédszolgáltatások (S0) standard előfizetést kell használnia a batch beszédátírási. Ingyenes előfizetési kulcsok (F0) nem fog működni. További információkért lásd: [díjszabás és korlátok](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Egyéni modellek

Ha azt tervezi, testre szabhatja akusztikai és nyelvi modelljeit, lépésekkel [akusztikai modell testreszabása](how-to-customize-acoustic-models.md) és [nyelvi modell testreszabásával](how-to-customize-language-model.md). A létrehozott modellek használata a batch beszédátírási van szüksége a modell azonosítók. Ez az azonosító nem a végpont-azonosító a végpont részletei nézeten talált, hogy a Modellazonosító lekérhető a modellek részleteit kiválasztásakor.

## <a name="the-batch-transcription-api"></a>A Batch Beszédátírási API

A Batch Beszédátírási API aszinkron hang-szöveg transzformációs átírást, valamint olyan kiegészítő funkciókat kínál. Egy REST API-t, amely metódusokat tárja fel:

1. Kötegelt feldolgozási kérelmek létrehozása
1. Lekérdezés állapota
1. Beszédátírás letöltése

> [!NOTE]
> A Batch Beszédátírási API telefonos ügyfélszolgálatok, amely általában a több ezer órás hanganyagra gyűlnek ideális. Lehetővé teszi, hogy egyszerűen lefényképezze hanganyag nagy mennyiségű.

### <a name="supported-formats"></a>Támogatott formátumok

A Batch Beszédátírási API támogatja a következő formátumok:

| Formátum | Kodek | Átviteli sebesség | Mintavételi frekvencia |
|--------|-------|---------|-------------|
| WAV | A PCM | 16-bit | 8 vagy 16 kHz, Monó, sztereó |
| MP3 | A PCM | 16-bit | 8 vagy 16 kHz, Monó, sztereó |
| OGG | OPUS | 16-bit | 8 vagy 16 kHz, Monó, sztereó |

Sztereó audiostreamek lejátszásával, a a Batch API beszédátírási bontja a bal és jobb csatorna a beszédátírási során. A két JSON-fájlok az eredmény az egyes jönnek létre egyetlen csatornákon. Az utterance (kifejezés) / időbélyegeket köszönhetően a fejlesztő hozzon létre egy rendezett végleges átiratok. A minta kérelem cenzúrázása szűrést, írásjelek és word szintű időbélyegeket tulajdonságait tartalmazza. 

### <a name="configuration"></a>Konfiguráció

Konfigurációs paraméterek JSON-fájlként áll rendelkezésre:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": ["<optional acoustic model ID>, <optional language model ID>"],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  }
}
```

> [!NOTE]
> A Batch Beszédátírási API egy REST-szolgáltatás beszédátírás, állapotát és kapcsolódó eredmények kérő használja. Az API bármilyen nyelvet is használhat. Ez a szakasz azt ismerteti, hogyan használja fel az API-t.

### <a name="configuration-properties"></a>Konfiguráció tulajdonságai

| Paraméter | Leírás | Kötelező / választható |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Adja meg a felismerési eredményeket cenzúrázása kezelése. Elfogadott értékek a következők `none` amely letiltja a vulgáris szűréshez `masked` csillag, amely lecseréli cenzúrázása `removed` amely eltávolítja az összes cenzúrázása az eredményből vagy `tags` ami ad "cenzúrázása" címkék. Az alapértelmezett beállítás `masked`. | Optional |
| `PunctuationMode` | Adja meg a felismerési eredményeket írásjelek kezelése. Elfogadott értékek a következők `none` ami letiltja az absztrakt, `dictated` explicit írásjelek, amiből `automatic` , amellyel a dekóder írásjelek, kezelése vagy `dictatedandautomatic` írásjelek vagy automatikus azt jelenti, amely során. | Optional |
 | `AddWordLevelTimestamps` | Itt adhatja meg, ha word szintű időbélyeggel kell adni a kimenetet. Elfogadott értékek a következők `true` lehetővé teszi a word szintű időbélyegeket és `false` (az alapértelmezett érték) letiltja azt. | Optional |

### <a name="storage"></a>Storage

Batch-átírási támogatja [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) hang- és Storage írása beszédátírás olvasásához.

## <a name="webhooks"></a>Webhookok 

A beszédátírási állapotának lekérdezése nem, a legtöbb nagy teljesítményű, vagy a legjobb felhasználói élményt. A állapotának lekérdezéséhez, regisztrálhat visszahívást, amelyet az ügyfél értesíti, amikor a hosszú ideig futó beszédátírási tevékenységei befejeződtek.

További részletekért lásd: [Webhookok](webhooks.md).

## <a name="sample-code"></a>Mintakód

A teljes minta megtalálható a [GitHub-mintaadattárból](https://aka.ms/csspeech/samples) belül a `samples/batch` alkönyvtárat.

Testre szabhatja a mintakódot az előfizetési adatokkal, a szolgáltatás a régióban, az SAS URI-t a hangfájl mutató alapuló átírás és modellezheti az azonosítók, abban az esetben, ha egy egyéni akusztikai és nyelvi modell használni kívánt kell. 

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

A mintakód fog beállítása az ügyfél és a beszédátírási kérelmet. Majd elindítja a lekérdezést állapotadatait és beszédátírási végrehajtási nyomtatási részleteit.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Részletes információ az előző hívás, tekintse meg a [Swagger-dokumentumok](https://westus.cris.ai/swagger/ui/index). Itt látható a teljes minta, nyissa meg [GitHub](https://aka.ms/csspeech/samples) a a `samples/batch` alkönyvtárat.

Jegyezze fel a könyvelési hang és a fogadás beszédátírási állapot aszinkron beállítása. Az ügyfél, amely létrehoz egy .NET-HTTP-alapú. Van egy `PostTranscriptions` módszer küldi a hangfájl részleteit és a egy `GetTranscriptions` metódus az eredményeket fogadására. `PostTranscriptions` a fogópont adja vissza és `GetTranscriptions` annak használatával hozzon létre egy leírót beszédátírási állapotának beolvasása.

A jelenlegi mintakód nem adja meg egy egyéni modell. A szolgáltatás használ a kiindulási modelleket alkalmazhatnak lefényképezheti a fájlt vagy fájlokat. A modellek megadásához meg ugyanezt a módszert, a modell azonosítóját a akusztikai és a nyelvi modell adhat át.

> [!NOTE]
> Alapkonfiguráció beszédátírás nem kell deklarálja a kiindulási modelleket alkalmazhatnak azonosítója. Ha csak megadja a nyelvi modell (és nem akusztikai modell-azonosító), egy megfelelő akusztikai modell automatikusan ki van jelölve. Ha csak egy akusztikai modell Azonosítóját adja meg, akkor a megfelelő nyelvi modell automatikusan ki van jelölve.

## <a name="download-the-sample"></a>A minta letöltése

A mintában találja a `samples/batch` könyvtárat a a [GitHub-mintaadattárból](https://aka.ms/csspeech/samples).

> [!NOTE]
> A Batch beszédátírási típusú feladatok vannak ütemezve az elérhető legjobb lehetőség alapján, és van nincs becsült időtartam számára, amikor egy feladat fog módosítsa a futó állapotot. Egyszer fut, a tényleges beszédátírási feldolgozása gyorsabb, mint a hang valós időben.

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
