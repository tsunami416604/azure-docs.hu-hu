---
title: A v2 API által létrehozott Video Indexer kimenet vizsgálata – Azure
titleSuffix: Azure Media Services
description: Ez a témakör az Azure Media Services videoindexelő kimenetét vizsgálja v2 API által.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/09/2019
ms.author: juliako
ms.openlocfilehash: 2fac5e07f9646c4fc0fac7b1be53b5a5ac1ea803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245927"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>Az API által létrehozott Video Indexer kimenet vizsgálata

Amikor meghívja a **Get Video Index** API-t, és a válasz állapota rendben van, részletes JSON-kimenetet kap választartalomként. A JSON-tartalom a megadott videóelemzések részleteit tartalmazza. Az elemzések a következők: átiratok, OCRs, arcok, témák, blokkok, stb. Minden egyes elemzési típus olyan időtartományokpéldányokat tartalmaz, amelyek azt mutatják, hogy az elemzés mikor jelenik meg a videóban. 

1. A JSON-fájl beolvasásához hívja meg a [Videoindex API beolvasása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Ha bizonyos összetevők is érdekelnek, hívja meg [a Videoartifact Download URL API-t](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    Az API-hívásban adja meg a kért műtermék típusát (OCR, Arcok, Kulcskeretek stb.)

Vizuálisan is megvizsgálhatod a videó összesített elemzési **adatait,** ha megnyomod a [Videó indexelő](https://www.videoindexer.ai/) webhelyén található Videó gombját. További információt a [Videoelemzések megtekintése és szerkesztése című témakörben talál.](video-indexer-view-edit.md)

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Ez a cikk a Get Video **Index** API által visszaadott JSON-tartalmat vizsgálja. 

> [!NOTE]
> A Video Indexer összes hozzáférési jogkivonatának lejárata egy óra.


## <a name="root-elements"></a>Gyökérelemek

|Név|Leírás|
|---|---|
|accountId|A lejátszási lista VI-fiókazonosítója.|
|id|A lejátszási lista azonosítója.|
|név|A lejátszási lista neve.|
|leírás|A lejátszási lista leírása.|
|userName (Felhasználónév)|A lejátszási listát létrehozó felhasználó neve.|
|Létrehozott|A lejátszási lista létrehozási ideje.|
|privacyMode (adatvédelem)|A lejátszási lista adatvédelmi módja (privát/nyilvános).|
|state|A lejátszási lista (feltöltve, feldolgozva, feldolgozva, sikertelenen, karanténba helyezve).|
|isOwned|Azt jelzi, hogy a listát az aktuális felhasználó hozta-e létre.|
|szerkeszthető|Azt jelzi, hogy az aktuális felhasználó jogosult-e a lista szerkesztésére.|
|isBase|Azt jelzi, hogy a lejátszási lista alap lejátszási lista (videó) vagy más videókból készült lejátszási lista (származtatott).|
|durationInSeconds|A lejátszási lista teljes időtartama.|
|summarizedInsights|Tartalmaz egy [summarizedInsights](#summarizedinsights).
|Videók|A lejátszási listát építő [videók](#videos) listája.<br/>Ha ez a lista más videók időtartományait tartalmazza, a listában szereplő videók csak a mellékelt időtartományok adatait tartalmazzák.|

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

Ez a szakasz az elemzések összegzését mutatja.

|Attribútum | Leírás|
|---|---|
|név|A videó neve. Például az Azure Monitor.|
|id|A videó azonosítója. Például: 63c6d532ff.|
|privacyMode (adatvédelem)|A bontás a következő módok egyikével rendelkezhet: **Privát**, **Nyilvános**. **Nyilvános** – a videó mindenki számára látható a fiókodban, és mindenki számára, akinek van linkje a videóra. **Privát** – a videó a fiókod minden tagjának látható.|
|duration|Egy időtartamot tartalmaz, amely leírja a betekintés bekövetkezésének idejét. Az időtartam másodpercben van.|
|thumbnailVideoId|Annak a videónak az azonosítója, amelyből a miniatűr készült.
|thumbnailId|A videó miniatűr azonosítója. Ahhoz, hogy a tényleges miniatűr, hívja [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) és adja át thumbnailVideoId és thumbnailId.|
|Arcok|Nulla vagy több arcot tartalmazhat. További információt az Arcok című [témakörben talál.](#faces)|
|kulcsszavak|Nulla vagy több kulcsszót tartalmazhat. További információt a [kulcsszavak](#keywords)című témakörben talál.|
|Érzelmeket|Nulla vagy több érzést tartalmazhat. További információt az érzelmek című témakörben [talál.](#sentiments)|
|audioEffects| Tartalmazhat nulla vagy több audioEffects. További részletes információt a [audioEffects .](#audioEffects)|
|Címkék| Nulla vagy több címkét tartalmazhat. További információt a [címkék](#labels)között talál.|
|Márkák| Nulla vagy több márkát tartalmazhat. További információkért lásd a [márkákat.](#brands)|
|statisztika | További információkért lásd a [statisztikákat.](#statistics)|
|Érzelmek| Tartalmazhat nulla vagy több érzelmeket. További részletes információkért lásd [az érzelmeket.](#emotions)|
|Témák|Nulla vagy több témakört tartalmazhat. A [témák](#topics) betekintést.|

## <a name="videos"></a>Videók

|Név|Leírás|
|---|---|
|accountId|A videó VI-fiókazonosítója.|
|id|A videó azonosítója.|
|név|A videó neve.
|state|A videó állapota (feltöltve, feltöltve, feldolgozva, feldolgozva, sikertelenül, karanténba helyezve).|
|feldolgozás, folyamat|A feldolgozás folyamata a feldolgozás során (például 20%).|
|hibakód|A hibakód, ha nem sikerült feldolgozni (például "UnsupportedFileType").|
|hibaÜzenet|A hibaüzenet, ha nem sikerült feldolgozni.|
|külső azonosító|A videó külső azonosítója (ha a felhasználó ezt adja meg).|
|externalUrl|A videó külső URL-címét (ha a felhasználó megvan adva).|
|metaadatok|A videó külső metaadatai (ha a felhasználó ezt adja meg).|
|isFelnőtt|Azt jelzi, hogy a videót manuálisan nézték-e felül, és felnőtt videóként azonosították-e.|
|Betekintést|Az elemzési objektum. További információ: [Insights](#insights).|
|thumbnailId|A videó miniatűr azonosítója. Ahhoz, hogy a tényleges miniatűr hívás [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) és adja át a videó azonosítóés thumbnailId.|
|publishedUrl|Egy URL-t a videó streameléséhez.|
|publishedUrlProxy|A videó streameléséhez használható URL (Apple-eszközök esetén).|
|viewToken|Rövid élettartamú nézettoken a videó streameléséhez.|
|sourceLanguage|A videó forrásnyelve.|
|language|A videó tényleges nyelve (fordítás).|
|indexingPreset|A videó indexeléséhez használt készlet.|
|streamingPreset|A videó közzétételéhez használt készlet.|
|linguisticModelId|A CRIS modell a videó átírására szolgál.|
|statisztika | További információ: [statistics](#statistics).|

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
### <a name="insights"></a>Betekintést

Minden egyes betekintés (például átirati sorok, arcok, márkák stb.) egyedi elemek listáját tartalmazza (például face1, face2, face3), és minden elemnek saját metaadatai és példányai listája van (amelyek további választható metaadatokat tartalmazó időtartományok).

Az arc nak lehet azonosítója, neve, miniatűrje, egyéb metaadatai és időbeli példányainak listája (például: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 és 00:41:21 – 00:41:49.) Minden időbeli példány további metaadatokkal rendelkezhet. Például az arc téglalapkoordinátái (20 230,60,60).

|Verzió|A kód verziója|
|---|---|
|sourceLanguage|A videó forrásnyelve (egy mesternyelvet feltételezve). [BCP-47](https://tools.ietf.org/html/bcp47) karakterlánc formájában.|
|language|Az elemzési nyelv (lefordítva a forrásnyelv). [BCP-47](https://tools.ietf.org/html/bcp47) karakterlánc formájában.|
|Átirat|Az [átirat](#transcript) betekintést.|
|Ocr|Az [OCR](#ocr) betekintést.|
|kulcsszavak|A [kulcsszavak](#keywords) betekintést.|
|blocks|Egy vagy több [blokkot](#blocks) tartalmazhat|
|Arcok|Az [arcok](#faces) éleslátás.|
|Címkék|A [címkék](#labels) betekintést.|
|Lövések|A [lövések](#shots) betekintést.|
|Márkák|A [márkák](#brands) betekintést.|
|audioEffects|A [audioEffects](#audioEffects) betekintést.|
|Érzelmeket|Az [érzelmek](#sentiments) betekintést.|
|visualContentModeration (vizuális tartalommoderálás)|A [visualContentModeration](#visualcontentmoderation) betekintést.|
|textualContentModeration (szövegezési tartalommoderálás)|A [textualContentModeration](#textualcontentmoderation) betekintést.|
|Érzelmek| Az [érzelmek éleslátása.](#emotions)|
|Témák|A [témák](#topics) betekintést.|

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
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

Attribútum | Leírás
---|---
id|A blokk azonosítója.|
Példányok|A blokk időtartományainak listája.|

#### <a name="transcript"></a>Átirat

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

#### <a name="ocr"></a>Ocr

|Név|Leírás|
|---|---|
|id|Az OCR vonal azonosítója.|
|szöveg|Az OCR szöveg.|
|megbízhatóság|Az elismerési bizalom.|
|language|Az OCR nyelv.|
|Példányok|Azoknak az időtartományoknak a listája, ahol ez az OCR megjelent (ugyanaz az OCR többször is megjelenhet).|
|magasság|Az OCR téglalap magassága|
|felül|A felső hely a px|
|balra| A bal oldali hely px-ben|
|szélesség|Az OCR téglalap szélessége|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>kulcsszavak

|Név|Leírás|
|---|---|
|id|A kulcsszó azonosítója.|
|szöveg|A kulcsszó szövege.|
|megbízhatóság|A kulcsszó felismerési megbízhatósága.|
|language|A kulcsszó nyelv (ha lefordítva).|
|Példányok|Azoknak az időtartományoknak a listája, ahol ez a kulcsszó megjelent (a kulcsszó többször is megjelenhet).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>Arcok

|Név|Leírás|
|---|---|
|id|Az arcazonosító.|
|név|Az arc neve. Ez lehet "Ismeretlen #0, egy azonosított híresség vagy egy ügyfél képzett személy.|
|megbízhatóság|Az arcazonosítási magabiztosság.|
|leírás|A híresség leírása. |
|thumbnailId|Az arc miniatűrjének azonosítója.|
|knownPersonId|Ha ismert személy, akkor a belső azonosítója.|
|referenciaazonosító|Ha ez egy Bing híresség, a Bing ID.|
|referenceType (hivatkozási típus)|Jelenleg csak Bing.|
|cím|Ha hírességről van szó, akkor a címe (például "A Microsoft vezérigazgatója").|
|imageUrl|Ha ez egy híresség, a kép url.|
|Példányok|Ezek olyan esetek, amikor az arc megjelent az adott időtartományban. Minden példánynak van egy thumbnailsId azonosítója is. |

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

#### <a name="labels"></a>Címkék

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

#### <a name="scenes"></a>Jelenetek

|Név|Leírás|
|---|---|
|id|A helyszín azonosítója.|
|Példányok|A jelenet időtartományainak listája (egy jelenetnek csak 1 példánya lehet).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>Lövések

|Név|Leírás|
|---|---|
|id|A lövés azonosítója.|
|Kulcsképek|A képképkulcs-képkockák listája (mindegyiknek van azonosítója és példányok időtartományainak listája). Minden keyFrame-példányrendelkezik egy thumbnailId mezővel, amely a keyFrame miniatűr azonosítóját tartalmazza.|
|Példányok|A felvétel időtartományainak listája (a felvételnek csak 1 példánya lehet).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>Márkák

A beszédben szöveges átiratban és/vagy videoocr-ben észlelt üzleti és termékmárkanevek. Ez nem foglalja magában a márkák vizuális felismerését vagy az embléma észlelését.

|Név|Leírás|
|---|---|
|id|A márka azonosítója.|
|név|A márka neve.|
|referenciaazonosító | Az utótag a márka wikipedia url. Például a "Target_Corporation" a . [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)
|referenceUrl | A márka Wikipedia url, ha létezik. [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)Például.
|leírás|A márkák leírása.|
|címkét|A márkához társított előre definiált címkék listája.|
|megbízhatóság|A Video Indexer márkadetektor megbízhatósági értéke (0-1).|
|Példányok|A márka időtartományainak listája. Minden példánynak van egy brandType-ja, amely azt jelzi, hogy ez a márka megjelent-e az átiratban vagy az OCR-ben.|

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

|Név|Leírás|
|---|---|
|CorrespondenceCount (Levelezésszáma)|A videóban lévő levelek száma.|
|SpeakerWordCount (Szószám)|A szavak száma hangszórónként.|
|SpeakerNumberOfTöredékek|A hangszóró által a videóban lévő töredékek mennyisége.|
|HangszóróLeghosszabbMonolog|A beszélő leghosszabb monológja. Ha a hangszóró a monológban csendben van, az benne van. Csend elején és végén a monolog eltávolítjuk.| 
|HangszóróTalkToListenratio|A számítás a hangszóró monológján töltött időn alapul (a kettő között a csend nélkül) osztva a videó teljes idejével. Az idő a harmadik tizedesvesszőre lesz kerekítve.|

#### <a name="audioeffects"></a><a id="audioEffects"/>audioEffects

|Név|Leírás|
|---|---|
|id|A hangeffektus azonosítója.|
|type|A hangeffektus típusa (például Taps, Beszéd, Csend).|
|Példányok|Azoknak az időtartományoknak a listája, ahol ez a hanghatás megjelent.|

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

#### <a name="sentiments"></a>Érzelmeket

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

#### <a name="visualcontentmoderation"></a>visualContentModeration (vizuális tartalommoderálás)

A visualContentModeration blokk olyan időtartományokat tartalmaz, amelyekről a Video Indexer potenciálisan felnőtt tartalommal rendelkezik. Ha a visualContentModeration üres, nincs azonosított felnőtt tartalom.

Előfordulhat, hogy a felnőtteknek szóló vagy pikáns tartalmat tartalmazó videók csak privát nézetben érhetők el. A felhasználóknak lehetőségük van arra, hogy kérelmet nyújtsanak be a tartalom emberi felülvizsgálatára, amely esetben az IsAdult attribútum tartalmazza az emberi felülvizsgálat eredményét.

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

#### <a name="textualcontentmoderation"></a>textualContentModeration (szövegezési tartalommoderálás) 

|Név|Leírás|
|---|---|
|id|A szöveges tartalommoderálás azonosítója.|
|bannedWordsCount |A tiltott szavak száma.|
|tiltottWordsRatio |Az arány a szavak teljes számához viszonyítva.|

#### <a name="emotions"></a>Érzelmek

A Video Indexer beszéd- és hangjelzések alapján azonosítja az érzelmeket. Az azonosított érzelem lehet: öröm, szomorúság, harag, vagy a félelem.

|Név|Leírás|
|---|---|
|id|Az érzelmi azonosító.|
|type|Az érzelem pillanat, hogy azonosították alapján beszéd és audio dákó. Az érzelem lehet: öröm, szomorúság, harag, vagy a félelem.|
|Példányok|Egy lista az időtartományokról, ahol ez az érzelem megjelent.|

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

#### <a name="topics"></a>Témák

Video Indexer teszi következtetni a fő témák átiratok. Ha lehetséges, a 2. [IPTC](https://iptc.org/standards/media-topics/) 

|Név|Leírás|
|---|---|
|id|A téma azonosítója.|
|név|A téma neve, például: "Gyógyszerek".|
|referenciaazonosító|A témakörhierarchiát tükröző zsemlemorzsa. Például: "Egészség és jólét / Orvostudomány és egészségügy / Gyógyszerek".|
|megbízhatóság|A megbízhatósági pontszám a tartományban [0,1]. A magasabb magabiztosabb.|
|language|A témában használt nyelv.|
|iptcName|Az IPTC médiakód neve, ha észlelve van.|
|Példányok |Jelenleg a Video Indexer nem indexel egy témakört időintervallumokba, így a teljes videó időközként szolgál.|

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

[Video indexelő fejlesztői portál](https://api-portal.videoindexer.ai)

A widgetek alkalmazásba ágyazásáról a [Video Indexer widgetek beágyazása az alkalmazásokba című témakörben](video-indexer-embed-widgets.md)talál további információt. 

