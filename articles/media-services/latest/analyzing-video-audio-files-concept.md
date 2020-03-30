---
title: Video- és hangfájlok elemzése
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan elemezheti a hang- és videotartalmakat az AudioAnalyzerPreset és a VideoAnalyzerPreset használatával az Azure Media Servicesben.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/30/2020
ms.author: juliako
ms.openlocfilehash: 1d28fc37b98493322b9e201ac899b7911dd1d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269886"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Video- és hangfájlok elemzése az Azure Media Services szolgáltatással

Az Azure Media Services v3-as eszközével betekintést nyerhet a video- és hangfájlokból a Video Indexer segítségével. Ez a cikk ismerteti a Media Services v3-elemző készletek kinyerésére használt ezeket az elemzéseket. Ha részletesebb megállapításokra van szüksége, használja közvetlenül a Video Indexert. Ha meg szeretné tudni, hogy mikor kell használni a Video Indexer és a Media Services elemzőkészleteit, tekintse meg az [összehasonlító dokumentumot.](../video-indexer/compare-video-indexer-with-media-services-presets.md)

A Media Services v3-készletek használatával történő tartalom elemzéséhez hozzon létre egy **átalakítást,** és küldjön el egy **feladatot,** amely az alábbi készletek egyikét használja: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) vagy **AudioAnalyzerPreset**. A **VideoAnalyzerPreset**használatát bemutató oktatóanyagról a [Videók elemzése az Azure Media Services szolgáltatással](analyze-videos-tutorial-with-api.md)című témakörben található.

> [!NOTE]
> Előzetes Video vagy Audio Analyzer-beállítások használata esetén állítsa be a fiókját 10 S3-as Media szolgáltatás számára fenntartott egységre az Azure Portalon. További információ: [Az adathordozó-feldolgozás méretezése](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság

Fontos emlékeztetőként be kell tartania a Video Indexer használata során érvényes összes vonatkozó jogszabályt, és nem használhatja a Video Indexert vagy bármely más Azure-szolgáltatást olyan módon, amely sérti mások jogait, vagy másokra nézve káros lehet. Mielőtt bármilyen videót, beleértve a biometrikus adatokat is, feltöltené a Video Indexer szolgáltatásba feldolgozásra és tárolásra, önnek rendelkeznie kell a videóban lévő egyén(ek) minden megfelelő jogával, beleértve az összes megfelelő hozzájárulást. Ha többet szeretne megtudni a Megfelelőségről, az adatvédelemről és a biztonságról a Video Indexerben, a Microsoft [Cognitive Services használati feltételei.](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) A Microsoft adatvédelmi kötelezettségeivel és az Ön adatainak kezelésével kapcsolatban olvassa el a Microsoft [adatvédelmi nyilatkozatát,](https://privacy.microsoft.com/PrivacyStatement)az [online szolgáltatási feltételeket](https://www.microsoft.com/licensing/product-licensing/products) ("OST") és [az adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). További adatvédelmi információk, beleértve az adatmegőrzést, törlést / megsemmisítést, elérhetők az OST-ben és [itt.](../video-indexer/faq.md) A Video Indexer használatával ön elfogadja, hogy a Cognitive Services feltételei, az OST, a DPA és az adatvédelmi nyilatkozat kötelező érvényű.

## <a name="built-in-presets"></a>Beépített készletek

A Media Services jelenleg a következő beépített elemzőkészleteket támogatja:  

|**Előre beállított név**|**Forgatókönyv**|**Részletek**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Hang elemzése|Az előre beállított a ai-alapú elemzési műveletek előre meghatározott készletét alkalmazza, beleértve a beszédátírást is. Az előre beállított beállítás jelenleg egyetlen hangsávval támogatja a tartalom feldolgozását, amely egyetlen nyelven tartalmazza a beszédfelismerést. A bemeneti adatokban megadhatja a hangtartalom nyelvét a "language tag-region" BCP-47 formátumban. A támogatott nyelvek az angol ("en-US" és "en-GB"), a spanyol ("es-ES" és "es-MX"), a francia ("fr-FR"), az olasz ("it-IT"), a japán ("ja-JP"), a portugál ("pt-BR"), a kínai ("zh-CN"), a német ("de-DE"), az arab ("ar-EG" és az "ar-SY"), az orosz (ru-RU)), Hindi ("hi-IN") és koreai ("ko-KR").<br/><br/> Ha a nyelv nincs megadva, vagy null értékre van állítva, az automatikus nyelvfelismerés kiválasztja az észlelt első nyelvet, és a fájl időtartama alatt a kiválasztott nyelvvel folytatódik. Az automatikus nyelvfelismerési funkció jelenleg az angol, a kínai, a francia, a német, az olasz, a japán, a spanyol, az orosz és a portugál nyelvet támogatja. Nem támogatja a nyelvek közötti dinamikus váltást az első nyelv észlelése után. Az automatikus nyelvfelismerési funkció a jól látható beszéddel rendelkező hangfelvételeken működik a legjobban. Ha az automatikus nyelvfelismerés nem találja a nyelvet, az átírás visszaáll angolra.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Hang- és videoanyagok elemzése|Kivonatinsights (gazdag metaadatok) mind a hang-és video-, és a kimenetegy JSON formátumú fájlt. Megadhatja, hogy csak a videofájlok feldolgozásakor szeretne-e hangelemzéseket kinyerni. További információ: [A videó elemzése](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset (ArcdetectorPreset)](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|A videóban jelen lévő arcok észlelése|Az összes jelen lévő arc felismeréséhez használt beállításokat ismerteti.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

A készlet lehetővé teszi, hogy több hangbetekintést nyerjen ki egy hang- vagy videofájlból. A kimenet tartalmaz egy JSON fájlt (az összes betekintést) és VTT fájlt a hangátirathoz. Ez a készlet elfogad egy tulajdonságot, amely a bemeneti fájl nyelvét [BCP47](https://tools.ietf.org/html/bcp47) karakterlánc formájában adja meg. A hang betekintést a következők:

* **Audio transzkripció**: A kimondott szavak átirata időbélyegekkel. Több nyelv is támogatott.
* **Hangszóró indexelése**: A hangszórók és a megfelelő kimondott szavak feltérképezése.
* **Beszédhangulat-elemzés**: A hangátíráson végzett hangulatelemzés kimenete.
* **Kulcsszavak:** Kulcsszavak, amelyek kivont audio átírás.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

A készlet lehetővé teszi, hogy több hang- és videoelemzéskinyerést nyerjen ki egy videofájlból. A kimenet tartalmaz egy JSON-fájlt (az összes elemzéssel), egy VTT-fájlt a videó átiratához, és egy miniatűrök gyűjteményét. Ez a készlet egy [BCP47](https://tools.ietf.org/html/bcp47) karakterláncot is elfogad (amely a videó nyelvét jelöli) tulajdonságként. A videó elemzési adatok tartalmazzák a fent említett összes hangbetekintést és a következő további elemeket:

* **Arckövetés**: Az az idő, amely alatt az arcok jelen vannak a videóban. Minden arcnak van egy arcazonosítója és egy megfelelő miniatűrgyűjteménye.
* **Vizuális szöveg**: Az optikai karakterfelismerés által érzékelt szöveg. A szöveg időbélyeggel van ellátva, és kulcsszavak kinyerésére is használható (az audio átirat mellett).
* **Kulcsképek**: A videóból kivont kulcsképek gyűjteménye.
* **Vizuális tartalommoderálás**: A videók felnőttként vagy pikáns természetűként megjelölt része.
* **Jegyzet**: A videók egy előre definiált objektummodell alapján történő megjegyzése

## <a name="insightsjson-elements"></a>insights.json elemek

A kimenet tartalmaz egy JSON-fájlt (insights.json) a videóban vagy a hangban található összes elemzéssel. A JSON a következő elemeket tartalmazhatja:

### <a name="transcript"></a>Átirat

|Név|Leírás|
|---|---|
|id|A vonal azonosítója.|
|szöveg|Maga az átirat.|
|language|Az átirat nyelve. Célja, hogy támogassa átirat, ahol minden sor lehet egy másik nyelvet.|
|Példányok|Azoknak az időtartományoknak a listája, ahol ez a sor megjelent. Ha a példány átirata, akkor csak 1 példánylesz.|

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

### <a name="ocr"></a>Ocr

|Név|Leírás|
|---|---|
|id|Az OCR vonal azonosítója.|
|szöveg|Az OCR szöveg.|
|megbízhatóság|Az elismerési bizalom.|
|language|Az OCR nyelv.|
|Példányok|Azoknak az időtartományoknak a listája, ahol ez az OCR megjelent (ugyanaz az OCR többször is megjelenhet).|

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

### <a name="faces"></a>Arcok

|Név|Leírás|
|---|---|
|id|Az arcazonosító.|
|név|Az arcneve. Ez lehet "Ismeretlen #0", egy azonosított híresség, vagy egy ügyfél képzett személy.|
|megbízhatóság|Az arcazonosítási magabiztosság.|
|leírás|A híresség leírása. |
|thumbnailId|Az arc miniatűrjének azonosítója.|
|knownPersonId|A belső azonosító (ha ismert személyről van szó).|
|referenciaazonosító|A Bing-azonosító (ha binghírességről van szó).|
|referenceType (hivatkozási típus)|Jelenleg csak Bing.|
|cím|A cím (ha hírességről van szó – például "A Microsoft vezérigazgatója").|
|imageUrl|A kép URL-je, ha híresség.|
|Példányok|Olyan esetek, amikor az arc megjelent az adott időtartományban. Minden példánynak van egy thumbnailsId azonosítója is. |

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

### <a name="shots"></a>Lövések

|Név|Leírás|
|---|---|
|id|A lövés azonosítója.|
|Kulcsképek|A felvételen belüli kulcsképkockák listája (mindegyiknek van azonosítója és példányok időtartományainak listája). A kulcskeretek példányai egy thumbnailId mezővel rendelkeznek a keyFrame miniatűr azonosítójával.|
|Példányok|A lista az időtartományok a lövés (lövések csak 1 példány).|

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

### <a name="statistics"></a>statisztika

|Név|Leírás|
|---|---|
|CorrespondenceCount (Levelezésszáma)|A videóban lévő levelek száma.|
|WordCount (Szószám)|A szavak száma hangszórónként.|
|SpeakerNumberOfTöredékek|A hangszóró által a videóban lévő töredékek mennyisége.|
|HangszóróLeghosszabbMonolog|A beszélő leghosszabb monológja. Ha a hangszóró csendben van a monológban, akkor az benne van. Csend elején és végén a monolog eltávolítjuk.|
|HangszóróTalkToListenratio|A számítás a hangszóró monológján töltött időn alapul (a kettő között a csend nélkül) osztva a videó teljes idejével. Az idő a harmadik tizedesvesszőre lesz kerekítve.|


### <a name="sentiments"></a>Érzelmeket

Az érzelmeket a sentimentType mező (Pozitív/Semleges/Negatív) összesíti. Például 0-0,1, 0.1-0.2.

|Név|Leírás|
|---|---|
|id|Az érzelmek azonosítója.|
|averageScore |Az adott véleménytípus összes példányának összes pontszámának átlaga - Pozitív/Semleges/Negatív|
|Példányok|Azoknak az időtartományoknak a listája, ahol ez az érzés megjelent.|
|hangulattípus |A típus lehet "Pozitív", "Semleges" vagy "Negatív".|

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

### <a name="labels"></a>Címkék

|Név|Leírás|
|---|---|
|id|A címke azonosítója.|
|név|A címke neve (például "Számítógép", "TV").|
|language|A címke név nyelve (lefordítva). BCP-47|
|Példányok|Azoknak az időtartományoknak a listája, ahol ez a címke megjelent (a címke többször is megjelenhet). Minden példánynak van egy megbízhatósági mezője. |

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

### <a name="keywords"></a>kulcsszavak

|Név|Leírás|
|---|---|
|id|A kulcsszó azonosítója.|
|szöveg|A kulcsszó szövege.|
|megbízhatóság|A kulcsszó felismerési megbízhatósága.|
|language|A kulcsszó nyelv (ha lefordítva).|
|Példányok|Azoknak az időtartományoknak a listája, ahol ez a kulcsszó megjelent (a kulcsszó többször is megjelenhet).|

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

#### <a name="visualcontentmoderation"></a>visualContentModeration (vizuális tartalommoderálás)

A visualContentModeration blokk olyan időtartományokat tartalmaz, amelyekről a Video Indexer potenciálisan felnőtt tartalommal rendelkezik. Ha a visualContentModeration üres, nincs azonosított felnőtt tartalom.

Előfordulhat, hogy a felnőtteknek szóló vagy pikáns tartalmat tartalmazó videók csak privát nézetben érhetők el. A felhasználók kérelmet nyújthatnak be a tartalom emberi `IsAdult` felülvizsgálatára, amely esetben az attribútum tartalmazza az emberi felülvizsgálat eredményét.

|Név|Leírás|
|---|---|
|id|A vizuális tartalom moderálásazonosítója.|
|felnőttPontszám|A felnőtt pontszám (a tartalom moderátor).|
|racyScore (racyScore)|A pikáns pontszám (a tartalom moderálásából).|
|Példányok|Azoknak az időtartományoknak a listája, ahol ez a vizuális tartalommoderáció megjelent.|

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
## <a name="next-steps"></a>További lépések

[Oktatóanyag: Videók elemzése az Azure Media Serviceszel](analyze-videos-tutorial-with-api.md)
