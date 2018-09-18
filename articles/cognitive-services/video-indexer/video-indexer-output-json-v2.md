---
title: A v2 API által előállított Videóindexelő kimenetének vizsgálata
titlesuffix: Azure Cognitive Services
description: Ez a témakör a Video Indexer kimeneti v2 API által előállított megvizsgálja.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 76f83e7ad70e3e1906bc1aa90c74d600053aeb6f
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985644"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>A v2 API által előállított Videóindexelő kimenetének vizsgálata

> [!Note]
> A Video Indexer V1 API 2018. augusztus 1-től. a rendszer elavult. Most már a Video Indexer v2 API-t kell használnia. <br/>A Video Indexer v2 API-k fejlesztéséhez, tekintse meg a útmutatását [Itt](https://api-portal.videoindexer.ai/). 

Meghívásakor a **első videó Index** API és a válasz állapota OK, a részletes JSON-kimenet, a válasz tartalma kapunk. A JSON-tartalmak a megadott feltárását részleteit tartalmazza. Az insights közé tartozik például a dimenziók: szövegekben felismerése, arcok, témakörök, blokkok, stb. A dimenziók időtartományok azt mutatják be, amikor a videó minden dimenzió jelentek meg a példányban.  

Vizuálisan is megvizsgálhatja a videó összefoglaló insights billentyűkombináció lenyomásával a **lejátszása** videó gombot a [Video Indexer](https://www.videoindexer.ai/) webhelyén. További információkért lásd: [megtekintés és Szerkesztés feltárását](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Ez a cikk által visszaadott JSON-tartalmak megvizsgálja a **első videó Index** API-t. 

> [!NOTE]
> A Video Indexer a hozzáférési jogkivonatok lejárati érték egy óra.


## <a name="root-elements"></a>Legfelső szintű elemek

|Name (Név)|Leírás|
|---|---|
|Fiókazonosító|A lista VI fiók azonosítóját írja.|
|id|A lista azonosítóját.|
|név|A lista neve.|
|leírás|A lista leírása.|
|Felhasználónév|A felhasználó, aki létrehozta a lista neve.|
|létrehozva|A lista létrehozási ideje.|
|privacyMode|A lista adatvédelmi üzemmód (privát vagy nyilvános).|
|state|A lista (feltöltött, feldolgozás, feldolgozás, sikertelen, karanténba helyezett).|
|isOwned|Azt jelzi, hogy a lista az aktuális felhasználó hozta-e.|
|isEditable|Azt jelzi, hogy a jelenlegi felhasználó jogosult a lista szerkesztése.|
|isBase|Azt jelzi, hogy a lista a kiindulási lista (videó) vagy egy listát alkotó más videók (származtatott).|
|durationInSeconds|A lista teljes időtartamát.|
|summarizedInsights|Legalább egy [summarizedInsights](#summarizedinsights).
|videók|Listáját [videók](#videos) hozhat létre, a lista.<br/>Ha ezt a listát, amely más videók (származtatott), idő tartomány kialakítani, a lista a videók csak a csomagban foglalt időtartományok származó adatokat tartalmaz.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

Ez a szakasz az insights összegzését jeleníti meg.

|Attribútum | Leírás|
|---|---|
|név|A videó neve. Ha például az Azure Monitor.|
|shortId|A videó azonosítója. Ha például 63c6d532ff.|
|privacyMode|A részletezése a következő módok egyike lehet: **privát**, **nyilvános**. **Nyilvános** – a videót a fiókját, és bárki, amely rendelkezik a videóra mutató hivatkozást mindenki számára látható-e. **Privát** – a videót a fiókjában mindenki számára látható-e.|
|időtartam|Egy időtartam, amely leírja a időpontja egy elemzést tartalmaz. Időtartam másodpercen belül van.|
|thumbnailVideoId|A videót, amelyből a miniatűr hibaállapota azonosítója.
|thumbnailId|A videó miniatűrje azonosítóját. A tényleges miniatűr lekéréséhez hívja a Get-miniatűr (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail) és thumbnailVideoId és thumbnailId adja át.|
|arcok|Nulla vagy több arcokat is tartalmazhat. Részletesebb információkért lásd: [arcok](#faces).|
|a kulcsszavak|Nulla vagy több kulcsszavak tartalmazhat. Részletesebb információkért lásd: [kulcsszavak](#keywords).|
|hangulati|Nulla vagy több hangulati tartalmazhat. Részletesebb információkért lásd: [hangulati](#sentiments).|
|audioEffects| Nulla vagy több audioEffects tartalmazhat. Részletesebb információkért lásd: [audioEffects](#audioeffects).|
|címkék| Nulla vagy több címkéket tartalmazhat. További információ részletes: [címkék](#labels).|
|márka| Nulla vagy több márkái tartalmazhat. Részletesebb információkért lásd: [márkái](#brands).|
|statisztika | Részletesebb információkért lásd: [statisztika](#statistics).|
|Érzelmek| Nulla vagy több érzelmek tartalmazhat. Részletesebb információkért lásd: [érzelmek](#emotions).|
|kapcsolatos témakörök|Nulla vagy több témakörök is tartalmazhat. A [témakörök](#topics) dimenzió.|

## <a name="videos"></a>videók

|Name (Név)|Leírás|
|---|---|
|Fiókazonosító|A videó VI fiók azonosítóját írja.|
|id|A videó azonosítóját.|
|név|A videó neve.
|state|A videó állapota (feltöltött, feldolgozás, feldolgozás, sikertelen, karanténba helyezett).|
|processingProgress|A feldolgozási folyamat (például 20 %-os) feldolgozása közben.|
|failureCode|Ha nem tudta feldolgozni (például UnsupportedFileType) hibakód.|
|failureMessage|Ha nem tudta feldolgozni a hibaüzenet.|
|externalId|A videó külső azonosítója (Ha a felhasználó által megadott).|
|externalUrl|A videó külső URL-cím (Ha a felhasználó által megadott).|
|metaadatok|A videó külső metaadat (Ha a felhasználó által megadott).|
|isAdult|Jelzi, hogy manuálisan lett-e felül a videót, és azonosította az eseményt egy felnőtteknek szóló videót.|
|megállapítások|Az insights objektum. További információkért lásd: [insights](#insights).|
|thumbnailId|A videó miniatűrje azonosítóját. A tényleges miniatűr hívás Get-miniatűr beolvasásához (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail) és adja át azt a videó Azonosítóját és thumbnailId.|
|publishedUrl|A Videó-URL-címe.|
|publishedUrlProxy|(Az Apple-eszközök esetén), a Videó-URL-címe.|
|viewToken|Egy rövid élettartamú nézet jogkivonatot a videó streameléshez.|
|sourceLanguage|A videó Forrásnyelv.|
|Nyelv|A videó tényleges nyelv (fordítás).|
|indexingPreset|A készletet, a videó indexelésére használja.|
|streamingPreset|A készlet tesz közzé a videót.|
|linguisticModelId|A videó lefényképezze használt CRIS modell.|
|statisztika | További információkért lásd: [statisztika](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>megállapítások

Az insights olyan dimenzió (például, átirat sorok, arcok, márkákat, stb.), ahol minden dimenzió egyedi elemeket (például face1, felszín 2, felszín 3), és minden egyes elemben meg van saját metaadatait és a példányok listáját, (amelyeket a időtartományok További nem kötelező metaadatok).

Előfordulhat, hogy egy ARC Azonosítóját, nevét, a miniatűr, más metaadatokat és a historikus példányok listáját (például: 00:00:05 – 00:00:10, 00:01:00 – 00:02:30 és 00:41:21 – 00:41:49.) Minden historikus példánya további metaadatokat is lehet. Például az arcok téglalap koordinálja (20,230,60,60).

|Verzió|A kód verziója|
|---|---|
|sourceLanguage|A videó Forrásnyelv (feltéve, hogy egy fő nyelvet). Formájában egy [BCP-47](https://tools.ietf.org/html/bcp47) karakterlánc.|
|Nyelv|Az insights nyelv (a forrás nyelvről lefordított). Formájában egy [BCP-47](https://tools.ietf.org/html/bcp47) karakterlánc.|
|a szövegben|A [átirat](#transcript) dimenzió.|
|optikai karakterfelismerés|A [ocr](#ocr) dimenzió.|
|a kulcsszavak|A [kulcsszavak](#keywords) dimenzió.|
|blokkok|Tartalmazhat egy vagy több [blokkok](#blocks)|
|arcok|A [arcok](#faces) dimenzió.|
|címkék|A [címkék](#labels) dimenzió.|
|helyességének|A [helyességének](#shots) dimenzió.|
|márka|A [márkái](#brands) dimenzió.|
|audioEffects|A [audioEffects](#audioEffects) dimenzió.|
|hangulati|A [hangulati](#sentiments) dimenzió.|
|visualContentModeration|A [visualContentModeration](#visualcontentmoderation) dimenzió.|
|textualConentModeration|A [textualConentModeration](#textualconentmoderation) dimenzió.|
|Érzelmek| A [érzelmek](#emotions) dimenzió.|
|kapcsolatos témakörök|A [témakörök](#topics) dimenzió.|

Példa:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualConentModeration": ...
}
```

#### <a name="blocks"></a>blokkok

Attribútum | Leírás
---|---
id|A blokk-azonosítója.|
példányok|A blokk időtartományok listája.|

#### <a name="transcript"></a>a szövegben

|Name (Név)|Leírás|
|---|---|
|id|A sor azonosítója.|
|szöveg|Az átirat magát.|
|Nyelv|A szöveges nyelvet. Szánt támogatásához, átirat, ahol az egyes sorok rendelkezhet egy másik nyelvet.|
|példányok|Amikor ezt a sort jelent meg időt a tartományok listája. Ha a példány a szövegben, 1 példánnyal fog rendelkezni.|

Példa:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

#### <a name="ocr"></a>optikai karakterfelismerés

|Name (Név)|Leírás|
|---|---|
|id|Az optikai Karakterfelismerés sor azonosítója.|
|szöveg|Az optikai Karakterfelismerés szöveg.|
|magabiztosan|Elismerés magabiztosan.|
|Nyelv|Az optikai Karakterfelismerés nyelv.|
|példányok|Amikor jelent meg az optikai Karakterfelismeréssel időt tartományok listája (az azonos OCR többször is megjelenhetnek).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>a kulcsszavak

|Name (Név)|Leírás|
|---|---|
|id|A kulcsszó-azonosítót.|
|szöveg|A kulcsszó szöveg.|
|magabiztosan|A kulcsszó felismerés magabiztosan.|
|Nyelv|A kulcsszó nyelv (Ha a fordítás melyik változatot).|
|példányok|Amikor ezt a kulcsszót jelent meg időt a tartományok listája (kulcsszó többször is megjelenhetnek).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="faces"></a>arcok

|Name (Név)|Leírás|
|---|---|
|id|A face azonosítója.|
|név|A face neve. "Ismeretlen #0", az azonosított hírességek vagy ügyfél betanított személy is lehet.|
|magabiztosan|Arcok azonosítása magabiztosan.|
|leírás|A hírességek leírása. |
|thumbnalId|A miniatűr képét, arc azonosítója.|
|knownPersonId|Ha ez egy ismert személy, a belső azonosítója.|
|a referenceid megadása|Ha a Bing hírességek, a Bing-azonosító.|
|referenceType|Jelenleg csak a Bing.|
|cím|Ha egy hírességek, akkor annak címe (például: "a Microsoft Vezérigazgatójával").|
|imageUrl|Ha a hírességek, a kép URL-címe.|
|példányok|Ezek a példányok, ahol az arcfelismerés jelent meg a megadott időtartományban. Minden példány egy thumbnailsId is tartalmaz. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>címkék

|Name (Név)|Leírás|
|---|---|
|id|A címke azonosítója.|
|név|A címke nevét (például "Számítógép", "TV").|
|Nyelv|A címke neve nyelv, (Ha a fordítás melyik változatot). BCP-47|
|példányok|Amikor ezt a címkét jelent meg időt a tartományok listája (címkék többször is megjelenhetnek). Minden példány egy megbízhatósági mező tartozik. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="shots"></a>helyességének

|Name (Név)|Leírás|
|---|---|
|id|A képernyőkép-azonosítót.|
|Kulcsképek|Kulcs keretek belül a képernyőkép-készítés (megvannak-azonosító és példányok idő tartományok listája) listája. Kulcs keretek példányok rendelkeznek egy thumbnailId mezőt, a Kulcsképkocka miniatűr-azonosítóját.|
|példányok|A képernyőkép-készítés ideje tartományainak listáját (a helyességének 1 példánnyal rendelkezik).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

#### <a name="brands"></a>márka

Üzleti és a termék márkáját nevet észlelt a szöveget szöveges és/vagy videó OCR-hang transzformációs. Ez nem tartalmaz a vizuális elismerését márkái vagy embléma észlelési.

|Name (Név)|Leírás|
|---|---|
|id|A márka azonosítója.|
|név|A márka neve.|
|a referenceid megadása | A márka wikipedia URL-címének utótagja. Például a "Target_Corporation" pedig az utótag [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | A márka a Wikipedia URL-címet, ha létezik. Például: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|leírás|A márka leírása.|
|tags|Előre definiált ez csökkenése társított címkék listája.|
|magabiztosan|A Video Indexer márka detector használatával (0-1) megbízhatósági értéke.|
|példányok|A márka időtartományok listája. Minden példány egy brandType, ami azt jelzi, hogy ez csökkenése jelentek meg a szöveges vagy OCR rendelkezik.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statisztika

|Name (Név)|Leírás|
|---|---|
|CorrespondenceCount|A videóban megfelelések száma.|
|WordCount|A speaker kiszolgálónként szavak számát.|
|SpeakerNumberOfFragments|A videó szerepel a beszélő töredék mennyisége.|
|SpeakerLongestMonolog|A beszélő leghosszabb monolog. Ha a beszélő belül a monolog silences része. Csend elején és végén a monolog törlődik.| 
|SpeakerTalkToListenRatio|A számítás időt a speaker monolog (nélkül a csend köztes) elosztva a videó teljes időtartama alapján. Az idő a harmadik tizedesvesszőtől lesz kerekítve.|

#### <a name="audioeffects"></a>audioEffects

|Name (Név)|Leírás|
|---|---|
|id|A hang érvénybe azonosítója.|
|type|A hang hatás típusa (például Clapping, beszédfelismerés, csend).|
|példányok|Amikor jelent meg a hang hatás időtartományok listája.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>hangulati

Összesítjük hangulati azok sentimentType mező (pozitív/Neutral/negatív) szerint. Például, 0 – 0.1, 0.2-0.1-es.

|Name (Név)|Leírás|
|---|---|
|id|A róluk szóló véleményeket azonosítója.|
|Átlag |Az összes pontszámok szoftverpéldányok száma a róluk szóló véleményeket típusa – pozitív/Neutral/negatív átlaga|
|példányok|Amikor a véleményt jelent meg időt a tartományok listája.|
|sentimentType |A típus "Pozitív", "Semleges" vagy "Negatív" lehet.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

A visualContentModeration blokk tartalmaz idő-címtartományokat, amelyek a Video Indexer, potenciálisan rendelkezik a felnőtt tartalom található. Ha visualContentModeration üres, nincs nincs felnőtt tartalom azonosított.

Lehet, hogy videókat, amelyek tartalmazzák a felnőtt vagy pikáns tartalmak csak a privát nézetben érhető el. Felhasználók van arra, hogy a tartalom, amelyben a IsAdult attribútum eset tartalmazni fogja az emberi ellenőrző eredménye egy emberi vizsgálóeszközt igényelnie.

|Name (Név)|Leírás|
|---|---|
|id|A vizuális tartalom-jóváhagyás azonosítója.|
|adultScore|A felnőtt pontszám (a content moderator).|
|racyScore|A pikáns pontszám (a tartalom-jóváhagyás).|
|példányok|Amikor jelent meg a vizuális tartalom-jóváhagyás időtartományok listája.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualconentmoderation"></a>textualConentModeration 

|Name (Név)|Leírás|
|---|---|
|id|A szöveges tartalom-jóváhagyás azonosítója.|
|bannedWordsCount |A kitiltott szavak számát.|
|bannedWordsRatio |A aránya az összes szó.|

#### <a name="emotions"></a>Érzelmek

Video Indexer – érzelmek beszéd- és audio jelek alapján azonosítja. Az azonosított emotion lehet: tudatjuk, szomorúság, düh vagy lehetőségének.

|Name (Név)|Leírás|
|---|---|
|id|Az emotion azonosítója.|
|type|Az emotion videórészletet azonosított beszéd- és audiotartalmak jelek alapján. Az emotion lehet: tudatjuk, szomorúság, düh vagy lehetőségének.|
|példányok|Amikor jelent meg az emotion időtartományok listája.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>kapcsolatos témakörök

Video Indexer – lehetővé teszi a legfontosabb témakörök az átiratok következtetésekhez. Ha lehetséges, az 1. szintű [IPTC](https://iptc.org/standards/media-topics/) besorolás részét képezi. 

|Name (Név)|Leírás|
|---|---|
|id|A témakör azonosítóját.|
|név|A témakör nevét, például: "Pharmaceuticals".|
|a referenceid megadása|A témakörök hierarchia tükröző útkövetés. Például: "állapotát és jólétének / orvosi és egészségügyi / Pharmaceuticals".|
|magabiztosan|A [0,1] tartományban konfidencia-pontszám. Újabb sokkal magabiztosabb a munkában.|
|Nyelv|A témakörben használt nyelv.|
|iptcName|A IPTC adathordozó-neve, kód észlelésekor.|
|példányok |Video Indexer jelenleg nem indexeli témakörre való időintervallumok, így a teljes videó szolgál az időközt.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>További lépések

[Video Indexer – fejlesztői portálon](https://api-portal.videoindexer.ai)

Widgetek beágyazása az alkalmazásba kapcsolatos információkért lásd: [az alkalmazásokba történő beágyazása a Video Indexer widgetek](video-indexer-embed-widgets.md). 

