---
title: Migrálás v2-ről v3 REST API-Speech szolgáltatásra
titleSuffix: Azure Cognitive Services
description: A v2-höz képest az új, v3-as verziójú API-verzió kisebb megszakítási változásokat tartalmaz. Ez a dokumentum segítséget nyújt az új főverzióra való Migrálás időpontjának hiányában.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2020
ms.locfileid: "96737992"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>Migrálás v 2.0-ról v 3.0-ra szöveggé REST API

A Speech REST API v3 verziója a megbízhatóság és a könnyű használat érdekében a korábbi API-verzióval bővült. Az API-elrendezés szorosabban igazodik a többi Azure-hoz vagy Cognitive Services API-khoz. Ez segít a meglévő képességek alkalmazásában a beszédfelismerési API használatakor.

Az API áttekintése [hencegő dokumentumként](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)érhető el. Ez ideális megoldás az API-k áttekintésére és az új API tesztelésére.

A C# és a Python esetében mintákat biztosítunk. A Batch-átírások esetében a mintákat a [GitHub minta adattárában](https://aka.ms/csspeech/samples) találja meg az `samples/batch` alkönyvtáron belül.

## <a name="forward-compatibility"></a>Továbbítási kompatibilitás

A v3-ra történő zökkenőmentes Migrálás érdekében a v2-től származó összes entitás a V3 API-ban is megtalálható ugyanazon identitás alatt. Ha egy eredményhalmaz-séma módosul (például átírások), az API-hoz tartozó GET in v3 verziójának válaszai a v3 sémában lesznek. Ha az API GET in v2 verzióját használja, az eredmény sémája v2 formátumú lesz. A v3-es újonnan létrehozott entitások **nem** érhetők el a v2-ben.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

A feltörési változások listáját az alkalmazkodáshoz szükséges módosítások nagyságrendje alapján rendezi a lista. Csak néhány módosításra van szükség, amely nem triviális változást igényel a hívó kódban. A legtöbb módosításhoz egyszerű nevek szükségesek. A csapatoknak a v2-ről v3-re való átváltásához szükséges idő néhány óra és néhány nap között változhat. A megnövekedett stabilitás, az egyszerűbb kód és a gyorsabb reagálás előnye azonban gyorsan ellensúlyozza a befektetést. 

### <a name="host-name-changes"></a>Állomásnév módosítása

Az állomásnevek módosultak a (z) {Region}. Cris. AI és a (z) {Region}. API. kognitív. microsoft. com néven. Ebben a változásban az elérési utak már nem tartalmazzák az "API/" karaktert, mert az az állomásnév részét képezi. A régiók és elérési utak teljes leírását a [hencegő dokumentum](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) tartalmazza.

### <a name="identity-of-an-entity"></a>Entitás identitása

A tulajdonságot `id` lecserélték `self` . A v2-ben az API-felhasználóknak tudnia kellett, hogyan jönnek létre az API-k elérési útjai. Ez nem bővíthető és szükségtelen munkát igényelt a felhasználótól. A tulajdonságot ( `id` UUID) a `self` (karakterlánc) helyettesíti, amely az entitás (URL) helye. Az érték továbbra is egyedi az összes entitás között. Ha a a `id` kódban karakterláncként van tárolva, egy egyszerű Átnevezés elegendő az új séma támogatásához. Mostantól a `self` tartalmat URL-címként is használhatja az entitás összes Rest-hívásához (get, patch, DELETE).

Ha az entitás más elérési utakon elérhető további funkciókkal rendelkezik, a lista alatt találhatók `links` . Jó példa egy átírásra, amely külön módszert tartalmaz az `GET` átírás tartalmára.

v2 átirat:

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

v3 átirat:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Az ügyfél-implementációtól függően előfordulhat, hogy nem elég a tulajdonság átnevezése. Javasoljuk, hogy használja ki a visszaadott értékek `self` és `links` a REST-hívások célként megadott URL-címeinek előnyeit, és ne adja meg az elérési utakat az ügyfélen. A visszaadott URL-címek használatával biztos lehet abban, hogy az elérési utakban a jövőbeli változások nem bontják le az ügyfélszoftvert.

### <a name="working-with-collections-of-entities"></a>Entitások gyűjteményének használata

Korábban a v2 API a válasz összes elérhető entitását visszaadotta. Annak érdekében, hogy részletesebben szabályozható legyen a várt válaszok mérete, v3-ban a gyűjtemények összes válasza szerepel a tördelésen. Ön szabályozhatja a visszaadott entitások számát és az oldal eltolását. Ez a viselkedés megkönnyíti a válasz-feldolgozó futtatókörnyezetének előrejelzését, és a többi Azure API-val konzisztens.

A válasz alapszintű formája megegyezik az összes gyűjtemény esetében:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

A tulajdonság a `values` rendelkezésre álló gyűjtemény entitások egy részhalmazát tartalmazza. A darabszám és az eltolás a lekérdezési paraméterekkel és a használatával vezérelhető `skip` `top` . Ha a `@nextLink` értéke nem null, a Get on használatával több információ is elérhető, a következő adatköteg pedig lekérhető `$.@nextLink` .

Ehhez a változáshoz az `GET` összes elem visszaadását követően egy hurokban kell meghívnia a gyűjteményt.

### <a name="creating-transcriptions"></a>Átiratok létrehozása

Az átírás létrehozásával kapcsolatos részletes leírást a [Batch-átírási útmutatóban](./batch-transcription.md)találhat.

Az átírások létrehozása a v3 verzióban megtörtént, hogy az adott átírási beállítások explicit módon legyenek beállítva. Az összes (opcionális) konfigurációs tulajdonság mostantól beállítható a `properties` tulajdonságban.
A v3-as verzió mostantól több bemeneti fájlt is támogat, ezért az URL-címek listáját kell megadni, és nem egyetlen URL-címet, amelyet a v2 szükséges. A tulajdonság neve a következő helyről lett átnevezve: `recordingsUrl` `contentUrls` . A rendszer eltávolította a (z) és a (z) v3-es verziójában található érzelmek elemzésének funkcióit. Javasoljuk, hogy Ehelyett használja a Microsoft kognitív szolgáltatás [szöveges elemzését](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) .

Az új tulajdonság `timeToLive` a `properties` -ban segíthet a meglévő befejezett entitások kimetszésében. A `timeToLive` meghatározza azt az időtartamot, amely után a rendszer automatikusan törli a befejezett entitást. Állítsa be magas értékre (például), `PT12H` Ha az entitásokat folyamatosan nyomon követik, felhasználják és törölik, és ezért általában hosszú ideig, 12 óra elteltével dolgozzák fel.

v2 átirat utáni kérelem törzse:

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

v3 átirat POST kérelem törzse:

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```

### <a name="format-of-v3-transcription-results"></a>V3 átirat eredményeinek formátuma

Az átírási eredmények sémája némileg megváltozott a valós idejű végpontok által létrehozott átírásokhoz való igazításhoz. Az új formátum részletes leírása megtalálható a [Batch átírása című témakörben](./batch-transcription.md). Az eredmény sémáját a rendszer a GitHub- [minta adattárában](https://aka.ms/csspeech/samples) teszi közzé `samples/batch/transcriptionresult_v3.schema.json` .

A tulajdonságok neve mostantól a Camel-tokozású, a csatorna és a beszélő értékei egész számokat használnak. Ha az időtartamok formátumát más Azure API-kkal szeretné igazítani, már formázva van az ISO 8601-ben leírtak szerint.

Egy v3 átírási eredmény mintája. A különbségeket a megjegyzések írják le.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

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

### <a name="getting-the-content-of-entities-and-the-results"></a>Az entitások és az eredmények tartalmának beolvasása

A v2-ben a bemeneti vagy az eredmény-fájlokra mutató hivatkozások a többi entitás metaadataival vannak befoglalva. A v3 fejlesztése során egyértelmű elkülönítés van az entitás metaadatainak között, amelyet a beolvasás `$.self` és a részletek és a hitelesítő adatok az eredmény fájljainak eléréséhez ad vissza. Ez az elkülönítés segít az ügyféladatok védelmében, és lehetővé teszi a hitelesítő adatok érvényességi időtartamának részletes szabályozását.

A v3-as verzióban van egy tulajdonság, amely a `files` hivatkozások területen található, ha az entitás adatokat (adatkészleteket, átírásokat, végpontokat, értékeléseket) tesz elérhetővé. A GET on (Beolvasás `$.links.files` ) a fájlok és Sas URL-címek listáját fogja visszaadni az egyes fájlok tartalmának eléréséhez. A SAS URL-címek érvényességi időtartamának szabályozásához a lekérdezési paraméter használható `sasValidityInSeconds` az élettartam megadásához.

v2 átirat:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

v3 átirat:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

Ezután a beszerzés a `$.links.files` következőt eredményezi:

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

A a `kind` fájl tartalmának formátumát jelzi. Az átírások esetében a fajta fájl `TranscriptionReport` összefoglalása a feladatoknak és a fajta fájloknak az `Transcription` eredménye.

### <a name="customizing-models"></a>Modellek testreszabása

A v3 előtt különbséget kapott egy "akusztikus modell" és egy "nyelvi modell" között a modell képzése során. Ez a különbség azt eredményezte, hogy a végpontok vagy átírások létrehozásakor több modellt kell megadnia. A hívó számára a folyamat leegyszerűsítése érdekében eltávolítjuk a különbségeket, és a modell betanításához használt adatkészletek tartalmától függünk. Ezzel a módosítással a modell létrehozása mostantól támogatja a vegyes adatkészleteket (a nyelvi adatokat és az akusztikai adatokat). A végpontoknak és az átírásoknak most csak egy modellre van szükségük.

Ezzel a módosítással a `kind` bejegyzést eltávolították a bejegyzésből, és a `datasets[]` mostantól több azonos vagy vegyes típusú adatkészletet is tartalmazhat.

A betanított modell eredményeinek javítása érdekében a rendszer automatikusan a nyelvi képzéshez használja a belső akusztikai adatmennyiséget. Általánosságban elmondható, hogy a V3 API-val létrehozott modellek pontosabb eredményeket biztosítanak, mint a v2 API-val létrehozott modellek.

### <a name="retrieving-base-and-custom-models"></a>Alapszintű és egyéni modellek beolvasása

A rendelkezésre álló modellek leegyszerűsítése érdekében a v3 elválasztta a "alapmodellek" gyűjteményeit az ügyfél "testreszabott modelljei" alapján. A két útvonal most `GET /speechtotext/v3.0/models/base` és `GET /speechtotext/v3.0/models/` .

Korábban az összes modell egyetlen válaszban lett visszaadva.

### <a name="name-of-an-entity"></a>Entitás neve

A tulajdonság neve a következőre `name` lesz átnevezve: `displayName` . Ez más Azure API-khoz van igazítva, hogy ne jelezze az identitás tulajdonságait. Ennek a tulajdonságnak az értéke nem lehet egyedi, és az entitás létrehozása után módosítható `PATCH` .

v2 átirat:

```json
{
    "name": "Transcription using locale en-US"
}
```

v3 átirat:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>A hivatkozott entitások elérése

A v2-ben hivatkozott entitások mindig beágyazottak voltak, például egy végpont használt modelljei. Az entitások beágyazásával nagy válaszokat okoztak, és a felhasználók ritkán használják fel a beágyazott tartalmat. A válasz méretének csökkentése és az összes API-felhasználó teljesítményének növelése érdekében a hivatkozott entitások már nem jelennek meg a válaszban. Ehelyett a rendszer a másik entitásra mutató hivatkozást használja, amely közvetlenül használható. Ez a hivatkozás a következőhöz használható `GET` (URL-cím is), amely a hivatkozással megegyező minta alapján érhető el `self` .

v2 átirat:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
          
        }
      ]
    },
  ]
}
```

v3 átirat:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Ha a fenti példában látható módon kell felhasználnia egy hivatkozott modell részleteit, egyszerűbbé teheti a GET beszerzését `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>Végponti naplók beolvasása

A szolgáltatás v2-es verziója támogatott a végpontok válaszának naplózása. Ha egy végpont eredményét szeretné lekérni egy v2-vel, az egyiknek létre kellett hoznia egy "adatexportálást", amely az adott időtartomány által meghatározott eredmények pillanatképét jelképezi. Az adatkötegek exportálásának folyamata nem lesz rugalmas. A V3 API hozzáférést biztosít az egyes fájlokhoz, és lehetővé teszi a rajtuk való iterációt.

A v3-végpont sikeres futtatása:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

Válasz a GET `$.links.logs` :

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

A végponti naplók tördelése az összes többi gyűjteményhez hasonlóan működik, azzal a különbséggel, hogy nem lehet eltolást megadni. A nagy rendelkezésre álló adatmennyiség miatt a kiszolgáló által vezérelt tördelést kellett megvalósítani.

A v3-as verzióban minden végponti napló egyenként törölhető egy fájl TÖRLÉSének kiadásával `self` , vagy a törlés használatával `$.links.logs` . A befejezési érték megadásához a lekérdezési paramétert `endDate` hozzá lehet adni a kérelemhez.

### <a name="using-custom-properties"></a>Az "egyéni" tulajdonságok használata

Ha az egyéni tulajdonságokat a választható konfigurációs tulajdonságok közül szeretné választani, az összes explicit módon elnevezett tulajdonság mostantól a `properties` tulajdonságban található, és a hívók által definiált összes tulajdonság már a `customProperties` tulajdonságban található.

v2 átirat entitás

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

v3 átirat entitás

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Ez a módosítás azt is lehetővé teszi, hogy a megfelelő típusok használata az összes explicit módon megnevezett tulajdonság alatt `properties` (például a string helyett a bool) legyen.

### <a name="response-headers"></a>Válaszfejlécek

a v3-as verzió már nem adja vissza a fejlécet a `Operation-Location` `Location` post kérések fejlécén kívül. Mindkét fejléc értéke pontosan ugyanaz. A rendszer most már csak `Location` a visszaadott értéket adja vissza.

Mivel az új API-verziót mostantól az Azure API Management (APIM) felügyeli, a szabályozáshoz kapcsolódó fejlécek, `X-RateLimit-Limit` `X-RateLimit-Remaining` és `X-RateLimit-Reset` nem szerepelnek a válasz fejlécében.

### <a name="accuracy-tests"></a>Pontossági tesztek

A pontossági tesztek átnevezve lettek az értékelésre, mert az új név jobban leírja, hogy mit jelentenek. A hírek elérési útjai például a "https://{Region}. API. kognitív. microsoft. com/speechtotext/v 3.0/értékelések".
