---
title: Videó-és hangfájlok elemzése a Azure Media Serviceskal | Microsoft Docs
description: Azure Media Services használatakor a AudioAnalyzerPreset és a VideoAnalyzerPreset segítségével elemezheti a hang-és videó tartalmát.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/21/2019
ms.author: juliako
ms.openlocfilehash: bc4be8eaafe805e5d9a985b005efe80bc4af1d21
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71177993"
---
# <a name="analyzing-video-and-audio-files"></a>Videó-és hangfájlok elemzése

A Azure Media Services v3 lehetővé teszi a videó-és hangfájlok elemzésének kinyerését Video Indexer a Media Services v3 Analyzer-előkészletekkel (ebben a cikkben leírtak szerint). Ha részletesebb megállapításokra van szüksége, használja közvetlenül a Video Indexert. Ha szeretne többet tudni arról, mikor érdemes a Video Indexer, illetve a Media Services-elemző előzetes beállításait használnia, tekintse meg az [összehasonlító dokumentumot](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Ha Media Services v3-es előkészletekből szeretné elemezni a tartalmakat, hozzon létre egy **átalakítást** , és küldjön el egy **feladatot** , amely az alábbi beállításkészletek egyikét használja: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) vagy **AudioAnalyzerPreset**. A következő cikk bemutatja, hogyan használható a **VideoAnalyzerPreset**: [Oktatóanyag Videók elemzése Azure Media Servicesokkal](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Előzetes Video vagy Audio Analyzer-beállítások használata esetén állítsa be a fiókját 10 S3-as Media szolgáltatás számára fenntartott egységre az Azure Portalon. További információkért olvassa el a [médiafeldolgozás méretezését](media-reserved-units-cli-how-to.md) ismertető cikket.

## <a name="built-in-presets"></a>Beépített készletek

A Media Services jelenleg a következő beépített analizátor-beállításkészleteket támogatja:  

|**Készlet neve**|**Forgatókönyv**|**Részletek**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Hang elemzése|Az előre definiált AI-alapú elemzési műveletek, beleértve a beszédfelismerést, előre meghatározott készletet alkalmaznak. Jelenleg az előre definiált tartalom egyetlen hangfelvételsel támogatja a tartalmat, amely egyetlen nyelven tartalmaz beszédet. Megadhatja a bemenetben lévő hangtartalom nyelvét a "Language tag-Region" BCP-47 formátumával. A támogatott nyelvek: angol ("en-US" és "en-GB"), spanyol ("es-ES" és "es-MX"), francia ("fr-FR"), olasz ("IT-IT"), Japán ("ja-JP"), Portugál ("PT-BR"), Kínai ("zh-CN"), német ("de-DE"), Arab ("AR-EG"), Orosz ("ru-RU"), hindi ("Hi-IN" ) és koreai ("ko-KR").<br/><br/> Ha a nyelv nincs megadva vagy NULL értékre van állítva, akkor az automatikus nyelvfelismerés kiválasztja az első nyelvet, és feldolgozza a kiválasztott nyelvet a fájl időtartamára. Az automatikus nyelvfelismerés funkció jelenleg a következőket támogatja: angol, kínai, francia, német, olasz, Japán, spanyol, orosz és portugál. Az első nyelv észlelése után jelenleg nem támogatja a nyelvek közötti dinamikus váltást. Az automatikus nyelvfelismerés funkció a hangfelvételek és a jól felismerhető beszédek esetében a legjobban működik. Ha az automatikus nyelvfelismerés nem találja a nyelvet, az átirat vissza fog térni az angol nyelvre.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Hang és videó elemzése|Kinyeri az elemzéseket (gazdag metaadatokat) a hang-és a videóból, és egy JSON formátumú fájlt ad kimenetként. Megadhatja, hogy a videofájl feldolgozásakor csak hangelemzéseket szeretne-e kinyerni. További információt a [videó elemzése](analyze-videos-tutorial-with-api.md)című témakörben talál.|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|A videóban lévő összes arc észlelése|A videó elemzésekor használandó beállításokat ismerteti a jelen lévő összes arc észlelése érdekében.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

A beállításkészlet lehetővé teszi, hogy egy hang-vagy videofájl használatával több hangelemzést is kinyerjen. A kimenet tartalmaz egy JSON-fájlt (az összes elemzéssel) és a VTT-fájlt a hangátirathoz. Ez a beállításkészlet egy olyan tulajdonságot fogad el, amely a bemeneti fájl nyelvét adja meg [BCP47](https://tools.ietf.org/html/bcp47) karakterlánc formájában. Az audio-elemzések a következők:

* Hang átirata – a kimondott szavak átirata időbélyeggel. Több nyelv is támogatott
* Beszélő indexelése – a hangszórók és a hozzájuk tartozó szóbeli szavak leképezése
* Beszéd hangulat elemzése – a hang átírásakor végzett, a hangulat elemzésének eredményei
* Kulcsszavak – a hangátiratból kinyert kulcsszavak.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

A beállításkészlet lehetővé teszi több hang-és videó-elemzés kinyerését egy videofájl használatával. A kimenet tartalmaz egy JSON-fájlt (az összes bepillantással), egy VTT-fájlt a videó átiratához, valamint egy miniatűr-gyűjteményt. Ez a beállításkészlet egy [BCP47](https://tools.ietf.org/html/bcp47) -karakterláncot is elfogad (amely a videó nyelvét jelöli) tulajdonságként. A videós elemzések tartalmazzák a fent említett összes hangelemzést, valamint a következő további elemeket:

* Arcfelismerés – az arcok a videóban való jelenlétének ideje. Minden arc rendelkezik egy arc-azonosítóval és a bélyegképek megfelelő gyűjteményéből
* Vizualizáció szövege – az optikai karakterfelismerésen keresztül észlelt szöveg. A szöveg időbélyegzővel van elválasztva, és a kulcsszavak kinyerésére is használatos (a hangátiraton kívül)
* Kulcsképek – a videóból kinyert kulcsképek gyűjteménye
* Vizuális tartalom moderálása – a videók azon része, amelyet felnőttként vagy zamatként megjelölt a természetben
* Jegyzet – a videók előre definiált objektummodell alapján való megjegyzésének eredménye

##  <a name="insightsjson-elements"></a>bepillantást. JSON-elemek

A kimenet tartalmaz egy JSON-fájlt (megállapítások. JSON) a videóban vagy a hangban található összes elemzéssel. A JSON a következő elemeket tartalmazhatja:

### <a name="transcript"></a>átirat

|Name (Név)|Leírás|
|---|---|
|id|A sor azonosítója.|
|text|Maga a átirat.|
|language|A átirat nyelve. Az olyan átiratok támogatását célozza, ahol az egyes sorok más nyelven is rendelkezhetnek.|
|példányok|Az időtartományok listája, ahol ez a sor megjelent. Ha a példány átirat, akkor csak 1 példánya lesz.|

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
|id|Az OCR-sor azonosítója.|
|text|Az OCR szövege.|
|megbízhatósági|Az elismerés megbízhatósága.|
|language|Az OCR nyelve.|
|példányok|Azon időtartományok listája, amelyekben ez az OCR megjelent (ugyanaz az OCR többször is megjelenhet).|

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

### <a name="faces"></a>arcok

|Name (Név)|Leírás|
|---|---|
|id|A Face azonosító.|
|name|Az arc neve. Ez lehet "ismeretlen #0", egy azonosított híresség vagy egy felhasználó által betanított személy.|
|megbízhatósági|Az arc azonosításának megbízhatósága.|
|description|A híresség leírása. |
|thumbnailId|Az adott arc miniatűrje.|
|knownPersonId|Ha ismert személy, a belső azonosítója.|
|referenceId|Ha ez egy Bing-híresség, a Bing-azonosítója.|
|referenceType|Jelenleg csak Bing.|
|title|Ha ez egy híresség, a címe (például "a Microsoft VEZÉRIGAZGATÓJA").|
|imageUrl|Ha ez egy híresség, a képe URL-címe.|
|példányok|Ezek a példányok, ahol az arc megjelent a megadott időtartományban. Minden példányhoz tartozik egy thumbnailsId is. |

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

### <a name="shots"></a>lövések

|Name (Név)|Leírás|
|---|---|
|id|A shot azonosítója.|
|Kulcsképek|A shot keretén belüli kulcstárolók listája (mindegyik rendelkezik egy AZONOSÍTÓval és egy példányok időtartományával). A kulcstároló-példányok egy thumbnailId-mezővel rendelkeznek, amely a kulcs miniatűrjét AZONOSÍTÓval rendelkezik.|
|példányok|A lövés időtartományának listája (a felvételek csak 1 példányt tartalmazhatnak).|

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

### <a name="statistics"></a>statisztikák

|Name (Név)|Leírás|
|---|---|
|CorrespondenceCount|A videóban található Levelezések száma.|
|WordCount|A beszélő szavak száma.|
|SpeakerNumberOfFragments|A szónak a videóban található töredékek mennyisége.|
|SpeakerLongestMonolog|A beszélő leghosszabb monologot. Ha a beszélő a monologot belül csendben van, a rendszer belefoglalja. A rendszer eltávolítja a monologot elején és végén található csendet.| 
|SpeakerTalkToListenRatio|A számítás a beszélő monologot töltött idő (a között a csend nélkül) alapján történik, a videó teljes időpontjára bontva. Az idő a harmadik tizedes pontra van kerekítve.|


### <a name="sentiments"></a>hangulati elemek

Az érzelmeket a sentimentType mező alapján összesítjük (pozitív/semleges/negatív). Például: 0-0,1, 0,1-0.2.

|Name (Név)|Leírás|
|---|---|
|id|Az érzelmi azonosító.|
|averageScore |Az adott érzelmi típus összes példányának átlaga – pozitív/semleges/negatív|
|példányok|Azon időtartományok listája, amelyekben ez a hangulat megjelent.|
|sentimentType |A típus "pozitív", "semleges" vagy "negatív" lehet.|

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

### <a name="labels"></a>címkék

|Name (Név)|Leírás|
|---|---|
|id|A címke azonosítója|
|name|A címke neve (például "számítógép", "TV").|
|language|A címke nevének nyelve (fordításkor). BCP-47|
|példányok|Azon időtartományok listája, amelyekben ez a címke megjelent (a címke többször is megjelenhet). Minden példány megbízhatósági mezővel rendelkezik. |


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

### <a name="keywords"></a>a kulcsszavak

|Name (Név)|Leírás|
|---|---|
|id|A kulcsszó azonosítója.|
|text|A kulcsszó szövege.|
|megbízhatósági|A kulcsszó felismerési megbízhatósága.|
|language|A kulcsszó nyelve (fordításkor).|
|példányok|Azon időtartományok listája, amelyekben ez a kulcsszó megjelent (a kulcsszó többször is megjelenhet).|

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

A visualContentModeration blokk olyan időtartományokat tartalmaz, amelyeknek a Video Indexer valószínűleg felnőtt tartalommal rendelkeznek. Ha a visualContentModeration üres, nincs azonosított felnőtt tartalom.

A felnőtt vagy zamatos tartalmat tartalmazó videók csak privát nézethez érhetők el. A felhasználóknak lehetősége van arra, hogy a tartalom emberi felülvizsgálatára vonatkozó kérelmet küldjenek, amely esetben a IsAdult attribútum az emberi felülvizsgálat eredményét fogja tartalmazni.

|Name (Név)|Leírás|
|---|---|
|id|A vizuális tartalom moderálásának azonosítója.|
|adultScore|A felnőtt pontszám (a tartalom moderátora).|
|racyScore|A zamatos pontszám (a tartalom moderálása alapján).|
|példányok|Azon időtartományok listája, amelyekben a vizualizációs tartalom moderálása megmutatkozott.|

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

[Oktatóanyag: Videók elemzése az Azure Media Services segítségével](analyze-videos-tutorial-with-api.md)
