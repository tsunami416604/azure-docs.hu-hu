---
title: Az Azure media eszközintelligencia |} Microsoft Docs
description: Azure Media Services használata esetén a hang- és contnet AudioAnalyzerPreset és VideoAnalyzerPreset használatával elemezheti.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: c488060b9db0ba482d12eee2394e5149b918950e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331520"
---
# <a name="media-intelligence"></a>Médiaintelligencia

Az Azure Media Services REST v3 API lehetővé teszi hang-és elemzéséhez. A tartalom elemzéséhez, hozzon létre egy **átalakítási** , és küldje el a **feladat** ezek készletek egyikét használja, amely: **AudioAnalyzerPreset** vagy **VideoAnalyzerPreset** . 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** lehetővé teszi hang-vagy több hang insights kibontani. A parancs kimenete egy JSON-fájl (az összes feltárása) és a hang szövegének VTT fájlt. Ezt a beállítást olyan tulajdonságon, amely meghatározza a bemeneti fájl nyelvét formájában fogad el egy [BCP47](https://tools.ietf.org/html/bcp47) karakterlánc. A hang insights a következők:

* Hang írjanak elő – a Beszélgetés szövegének Timestamp szóbeli szó. Több nyelv használata támogatott.
* Beszélőfelismerési indexelő – leképezéseket a hangszórók és a megfelelő kiejtett szavakat
* Beszéd véleményeket elemzés – a kimeneti véleményeket elemzési végre hang írjanak elő
* Kulcsszavak – kulcsszavakat, amelyekre a hang írjanak elő kinyert.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** lehetővé teszi több hang- és elemzések kinyerése videofájl. A parancs kimenete egy JSON fájlt (az összes feltárása), VTT videó szövegének és miniatűrök gyűjteménye. Ezt a beállítást is fogad el egy [BCP47](https://tools.ietf.org/html/bcp47) tulajdonságként (a videó nyelvének megfelelő) karakterláncot. A videó insights fent említett hang insights és a következő további elemeket tartalmazza:

* Arcfelismerési követési – az idő során, ami lapok szerepelnek a videó. Minden lapot tartalmaz a tapasztalt azonosítót és a miniatűrök megfelelő gyűjteménye
* Visual szöveg – a szöveg, keresztül optikai észlel. A szöveg megjelölve, és bontsa ki a kulcsszavak (mellett hang szövegének) segítségével is idő
* Kulcsképek –, amelyek a videó kinyert kulcsképek gyűjteménye
* Visual tartalom moderálás – a videókhoz, amelyeket a felnőtt vagy ellopható jellegű megjelölt része
* Megjegyzés – alapján egy előre definiált hálózatiobjektum-modellt a videók ellátása megjegyzésekkel eredménye

##  <a name="insightsjson-elements"></a>insights.JSON elemek

A kimeneti JSON-fájl (insights.json) tartalmaz, amelynek a videót, illetve a hangfelvételen hallható található összes feltárása. A json az alábbi elemeket tartalmazza:

### <a name="transcript"></a>Beszélgetés szövegének

|Name (Név)|Leírás|
|---|---|
|id|A sor azonosítója.|
|szöveg|A Beszélgetés szövegének magát.|
|nyelv|A Beszélgetés szövegének nyelv. Szánt Beszélgetés szövegének támogatásához, amely minden sor egy másik nyelvet is rendelkezhet.|
|példányok|Amikor a sor jelent meg időtartományhoz listáját. Ha a példány egy Beszélgetés szövegének, csak az 1-példány lesz.|

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

### <a name="ocr"></a>OCR

|Name (Név)|Leírás|
|---|---|
|id|A OCR sor azonosítója.|
|szöveg|A Felismert szöveg.|
|Megbízhatóság|A felismerési vetett bizalmat.|
|nyelv|A OCR nyelv.|
|példányok|Amikor ez OCR jelent meg időtartományhoz listája (az azonos OCR többször jelenhet meg).|

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

### <a name="keywords"></a>kulcsszavak

|Name (Név)|Leírás|
|---|---|
|id|A kulcsszó azonosítóját.|
|szöveg|A kulcsszó szöveget.|
|Megbízhatóság|A kulcsszó felismerés vetett bizalmat.|
|nyelv|A kulcsszó nyelv (bájttartományára lefordítva).|
|példányok|Amikor ezt a kulcsszót jelent meg időtartományhoz listáját (kulcsszó többször is szerepelhet).|

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

### <a name="faces"></a>Felületei

|Name (Név)|Leírás|
|---|---|
|id|A tapasztalt azonosítóját.|
|név|A betűtípusának neve. Lehet, hogy a "Ismeretlen #0", az azonosított celebrity vagy egy felhasználói képzett személy.|
|Megbízhatóság|A tapasztalt azonosító vetett bizalmat.|
|leírás|Ha egy celebrity, annak leírását. |
|thumbnalId|Az azonosítója, amely a miniatűr.|
|knownPersonId|Ha egy ismert személy, a belső azonosítója.|
|hivatkozás azonosítója|Esetén a Bing celebrity, a Bing-azonosító.|
|referenceType|Jelenleg csak a Bing.|
|cím|Ha egy celebrity címét (például "CEO Microsoft").|
|imageUrl|Ha egy celebrity, a kép URL-címe.|
|példányok|Ezek a példányok, ahol maga az adott időtartományt jelent meg. Minden példány is rendelkezik egy thumbnailsId. |

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

### <a name="labels"></a>címkék

|Name (Név)|Leírás|
|---|---|
|id|A címkeazonosító.|
|név|A címke nevét (például "Számítógép", "TV").|
|nyelv|A címke neve nyelve, (bájttartományára lefordítva). BCP-47|
|példányok|Amikor ezt a címkét jelent meg időtartományhoz listáját (a címke többször jelenhet meg). Minden példány abban, hogy mezőt tartalmaz. |


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

### <a name="shots"></a>pillanatfelvételek

|Name (Név)|Leírás|
|---|---|
|id|A hibaüzenetet azonosítóját.|
|Kulcsképek|A hibaüzenetet (mindegyik rendelkezik-e egy Azonosítót és egy címtartománylista példányok idő) belül hozzon listáját.|
|példányok|Ezt a hibaüzenetet idő tartományok listáját (a pillanatfelvételek csak 1 példánya van).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
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
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```


### <a name="sentiments"></a>Hangulati elemek

Hangulati elemek összesítése a sentimentType mező (pozitív/Neutral/negatív) szerint. Ha például 0-0,1, 0,1-0,2.

|Name (Név)|Leírás|
|---|---|
|id|A céggel kapcsolatos véleményeket azonosítóját.|
|Átlag |A céggel kapcsolatos véleményeket típusú - pozitív/Neutral/negatív szoftverpéldányok összes pontszámok átlaga|
|példányok|Amikor jelent meg a céggel kapcsolatos véleményeket idő tartományok listáját.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Media Services videók elemzése](analyze-videos-tutorial-with-api.md)
