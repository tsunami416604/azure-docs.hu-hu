---
title: Vizsgálja meg a v2 API által létrehozott Azure videó indexelő kimenet |} Microsoft Docs
description: Ez a témakör a videó indexelő kimeneti v2 API által előállított megvizsgálja.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cfowler
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 6c410b054ba98961d15a4db0ff7eaa2804245cb0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349866"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>Vizsgálja meg a videó indexelő kimenetét v2 API által előállított

> [!Note]
> A videó indexelő V1-es API-k mára elavult, és eltávolítja a 2018 augusztus 1-jétől. El kell kezdenie a videó indexelő v2 API-k használatával zavarokat elkerülése érdekében.
>
> Fejlesztéséhez videó indexelő v2 API-khoz, tekintse meg a útmutatását [Itt](https://api-portal.videoindexer.ai/). 

A hívás esetén a **beolvasása videó Index** API és a response állapot rendben, a válasz tartalmának részletes JSON-kimenetét le. A JSON-tartalmak megadott videó feltárása részleteit tartalmazza. Az elemzések közé tartozik például a dimenziók: ki felismerése, arc, témakörök, blokkok, stb. A Dimenziók megjelenítése, ha a videó megjelent minden dimenzió idő tartományok példánya lehet.  

A videó röviden bemutatják azok insights billentyűkombináció lenyomásával is vizuálisan ellenőrizheti a **lejátszása** a videó indexelő portálon videó gombjára. További információkért lásd: [megtekintés és Szerkesztés videó insights](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Ez a cikk a JSON-tartalmak által visszaadott megvizsgálja a **beolvasása videó Index** API. 

> [!NOTE]
> Videó indexelő a hozzáférési jogkivonatok lejárati egy óra.


## <a name="root-elements"></a>Gyökérelemet tartalmaz

|Name (Név)|Leírás|
|---|---|
|accountId|A lista VI fiók azonosítóját.|
|id|A lista azonosítóját.|
|név|A lista neve.|
|leírás|A lista leírása.|
|Felhasználónév|A lista létrehozó felhasználó neve.|
|létrehozva|A lista létrehozási ideje.|
|privacyMode|A lista adatvédelmi üzemmódban (privát vagy nyilvános).|
|state|A lista (feltöltött, feldolgozásra, feldolgozott, nem sikerült, a karanténba helyezett).|
|isOwned|Hogy a lista az aktuális felhasználó hozta létre.|
|isEditable|Hogy az aktuális felhasználó jogosult a lista szerkesztése.|
|isBase|Hogy a lista egy kiinduló lista (videó) vagy egy lista szeretné tenni az egyéb videók (származtatott).|
|durationInSeconds|A lista teljes időtartamát.|
|summarizedInsights|Tartalmaz egy [summarizedInsights](#summarizedinsights).
|videók|Listája [videók](#videos) hozhat létre, a lista.<br/>Ha az egyéb videók (származtatott), idő tartomány létrehozni ezt a listát, amely a videókhoz, amelyeket ebben a listában csak a belefoglalt időtartományhoz adatait fogja tartalmazni.|

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

Ez a szakasz a insights összegzését jeleníti meg.

|Attribútum | Leírás|
|---|---|
|név|A videó neve. Például az Azure figyelő.|
|shortId|A videó Azonosítóját. Például 63c6d532ff.|
|privacyMode|A részletes információkat a következő módok egyike lehet: **titkos**, **nyilvános**. **Nyilvános** -a videó fiókja és bárki, aki egy hivatkozást a video mindenki számára látható. **Személyes** -a videó fiókja a mindenki számára látható.|
|időtartam|Egy időtartam, amely leírja a egyet történt időt tartalmazza. DURATION érték, másodpercben.|
|thumbnailUrl|A videó miniatűr teljes URL-CÍMÉT. Például "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Figyelje meg, hogy ha a videó titkos, az URL-cím egy egy órán keresztül hozzáférési jogkivonatot tartalmazza. Egy óra elteltével az URL-cím már nem érvényes, és azt lekérése újra egy új URL-címmel rendelkező bontásban tartalmazza, vagy hívja az beszerzése egy új jogkivonatot, és a teljes URL-címet manuálisan GetAccessToken kell ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [ accessToken] ").|
|felületei|Egy vagy több felületei is tartalmazhat. További információkért lásd: [lapok](#faces).|
|Kapcsolatos témakörök|Tartalmazhat egy vagy több témaköröket. További információkért lásd: [témakörök](#topics).|
|hangulati elemek|Tartalmazhat egy vagy több hangulati elemek. További információkért lásd: [hangulati elemek](#sentiments).|
|audioEffects| Egy vagy több audioEffects tartalmazhat. További információkért lásd: [audioEffects](#audioeffects).|
|márka| Nulla vagy több márkákat tartalmazhat. További információkért lásd: [márkákat](#brands).|
|Statisztika | További információkért lásd: [statisztika](#statistics).|

### <a name="statistics"></a>Statisztika

|Name (Név)|Leírás|
|---|---|
|CorrespondenceCount|A videó megfelelések száma.|
|WordCount|Beszélőfelismerési szót száma.|
|SpeakerNumberOfFragments|Videó rendelkezik-e a beszélőre töredék mennyisége.|
|SpeakerLongestMonolog|A beszélőre leghosszabb monolog. Ha a beszélőre belül a monolog silences szerepel. A rendszer eltávolítja a csend elején és végén a monolog.| 
|SpeakerTalkToListenRatio|A számítás a beszélőre monolog (nélkül a csend a között) a teljes időnek a videó osztva a fordított idő alapul. Az idő a harmadik tizedesjel lesz kerekítve.|

## <a name="videos"></a>videók

|Name (Név)|Leírás|
|---|---|
|accountId|A videó VI fiók azonosítóját.|
|id|A videó azonosítóját.|
|név|A videó neve.
|state|A videó állapota (feltöltött, feldolgozásra, feldolgozott, nem sikerült, a karanténba helyezett).|
|processingProgress|A feldolgozási folyamatban (például 20 %-át) feldolgozása során.|
|failureCode|Ha nem tudta feldolgozni (például UnsupportedFileType) a következő hibakód.|
|failureMessage|A hibaüzenet, ha nem sikerült feldolgozni.|
|externalId|A videó külső azonosítója (Ha a felhasználó által megadott).|
|Alkalmazásazonosítójának|A videó külső URL-címe (Ha a felhasználó által megadott).|
|metaadatok|A videó külső metaadatok (Ha a felhasználó által megadott).|
|isAdult|Hogy a videó manuálisan volt-e felülvizsgálata, és egy felnőtt video azonosítottak.|
|megállapítások|Az elemzések objektum.|
|thumbnailUrl|A videó miniatűr teljes URL-CÍMÉT. Például "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Figyelje meg, hogy ha a videó titkos, az URL-cím egy egy órán keresztül hozzáférési jogkivonatot tartalmazza. Egy óra elteltével az URL-cím már nem érvényes, és azt lekérése újra egy új URL-címmel rendelkező bontásban tartalmazza, vagy hívja az beszerzése egy új jogkivonatot, és a teljes URL-címet manuálisan GetAccessToken kell ("https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [ accessToken] ").|
|publishedUrl|Folyamatos egy URL-címe.|
|publishedUrlProxy|(Az Apple-eszközök) videót adatfolyam egy URL-címe.|
|viewToken|Egy rövid élettartamú nézet jogkivonatot az adatfolyamként történő a videót.|
|sourceLanguage|A videó adatforrás nyelvi.|
|nyelv|A videó tényleges nyelv (fordítás).|
|indexingPreset|A készlet a videó index használatával.|
|streamingPreset|A készlet tesz közzé a videót.|
|linguisticModelId|A videó átírni alkalmazott CRIS modell.|

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

Az elemzések olyan dimenziók (például beszélgetés szövegének sorok, arc, márkákat, stb.), ahol minden dimenzió az egyedi elemeket (például face1, felszín 2, felszín 3), és egyes elemei a saját metaadatok és a példányok listáját (amelyeket a időtartományhoz További nem kötelező metaadatok).

Előfordulhat, hogy egy ARC Azonosítóját, nevét, a miniatűr, egyéb metaadatokat és a historikus példányok listáját (például: 00:00:05 – 00:00:10, 00:01:00 – 00:02:30 és 00:41:21 – 00:41:49.) Minden egyes historikus példánya további metaadatokat is lehet. Például a tapasztalt téglalap koordinálja (20,230,60,60).

|Verzió|A kód verziója|
|---|---|
|sourceLanguage|A videó adatforrás nyelvi (feltéve, hogy egy fő nyelvet). Formájában egy [BCP-47](https://tools.ietf.org/html/bcp47) karakterlánc.|
|nyelv|A insights nyelv (fordítás forrás nyelvével). Formájában egy [BCP-47](https://tools.ietf.org/html/bcp47) karakterlánc.|
|Beszélgetés szövegének|A [Beszélgetés szövegének](#transcript) dimenzió.|
|OCR|A [ocr](#ocr) dimenzió.|
|Kulcsszavak|A [kulcsszavak](#keywords) dimenzió.|
|felületei|A [lapok](#faces) dimenzió.|
|Címkék|A [címkék](#labels) dimenzió.|
|pillanatfelvételek|A [pillanatfelvételek](#shots) dimenzió.|
|márka|A [márkákat](#brands) dimenzió.|
|audioEffects|A [audioEffects](#audioEffects) dimenzió.|
|hangulati elemek|A [hangulati elemek](#sentiments) dimenzió.|
|visualContentModeration|A [visualContentModeration](#visualcontentmoderation) dimenzió.|
|textualConentModeration|A [textualConentModeration](#textualconentmoderation) dimenzió.|

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

#### <a name="transcript"></a>Beszélgetés szövegének

|Name (Név)|Leírás|
|---|---|
|id|A sor azonosítója.|
|szöveg|A Beszélgetés szövegének magát.|
|nyelv|A Beszélgetés szövegének nyelv. Szánt Beszélgetés szövegének támogatásához, amely minden sor egy másik nyelvet is rendelkezhet.|
|példányok|Amikor a sor jelent meg időtartományhoz listáját. Ha a példány Beszélgetés szövegének, csak az 1-példány lesz.|

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

#### <a name="ocr"></a>OCR

|Name (Név)|Leírás|
|---|---|
|id|A OCR sor azonosítója.|
|szöveg|A Felismert szöveg.|
|megbízhatóság|A felismerési vetett bizalmat.|
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

#### <a name="keywords"></a>Kulcsszavak

|Name (Név)|Leírás|
|---|---|
|id|A kulcsszó azonosítója.|
|szöveg|A kulcsszó szöveget.|
|megbízhatóság|A kulcsszó felismerés vetett bizalmat.|
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

#### <a name="faces"></a>felületei

|Name (Név)|Leírás|
|---|---|
|id|A tapasztalt azonosítóját.|
|név|A betűtípusának neve. Lehet, hogy a "Ismeretlen #0", az azonosított celebrity vagy egy felhasználói képzett személy.|
|megbízhatóság|A tapasztalt azonosító vetett bizalmat.|
|leírás|Lehet, ha egy celebrity, annak leírását: "Satya Nadella született...". |
|thumbnalId|Az azonosítója, amely a miniatűr.|
|knownPersonId|Ha az ismert személy, a belső azonosítója.|
|Hivatkozás azonosítója|Ha a Bing celebrity, a Bing-azonosító.|
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

#### <a name="labels"></a>Címkék

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

#### <a name="shots"></a>pillanatfelvételek

|Name (Név)|Leírás|
|---|---|
|id|A hibaüzenetet azonosítóját.|
|Kulcsképek|A hibaüzenetet (mindegyik rendelkezik-e egy Azonosítót és egy címtartománylista példányok idő) belül hozzon listáját. Kulcs keretek célpéldánynál thumbnailId mezővel a keyFrame miniatűr azonosítóját.|
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

Üzleti és a termék márkáját nevek szöveg Beszélgetés szövegének és/vagy videó OCR beszéd észlelhető. Visual felismerését a márka vagy embléma észlelése nem tartalmaz.

|Name (Név)|Leírás|
|---|---|
|id|A márka azonosítóját.|
|név|A márka neve.|
|wikiId | A márka wikipedia URL-utótagot. Például a "Target_Corporation" pedig a utótag [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
|wikiUrl | A márka tartozó Wikipedia URL-címe, ha létezik-e. Például: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|leírás|A márka leírása.|
|tags|A márka társított előre meghatározott címkék listáját.|
|megbízhatóság|A abban, hogy a videó indexelő márka jelentő (0-1) értéke.|
|példányok|A márka idő tartományok listáját. Minden példány rendelkezik egy brandType, amely azt jelzi, hogy a márka szövegének vagy OCR megjelent-e.|

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
    "wikiId": "Microsoft",
    "wikiUrl": "http: //en.wikipedia.org/wiki/Microsoft",
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

#### <a name="audioeffects"></a>audioEffects

|Name (Név)|Leírás|
|---|---|
|id|A hang hatás azonosítóját.|
|type|A hang hatás típusa (például Clapping, beszéd átalakítás, csend).|
|példányok|Amikor jelent meg a hang lépéséhez idő tartományok listáját.|

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

#### <a name="sentiments"></a>hangulati elemek

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

A visualContentModeration blokk tartalmaz idő-címtartományokat, amelyek a videó indexelő található vélhetően a felnőtt tartalommal rendelkezik. Ha visualContentModeration üres, nincs azonosított felnőtt tartalom.

Lehet, hogy a videók, amelyek tartalmazzák a felnőtt vagy ellopható tartalmat csak a személyes nézet érhető el. Felhasználók lehetősége nyílik a tartalom, amelyben eset a IsAdult attribútum fogja tartalmazni az emberi nézze át az eredmény emberi áttekintése igénylésének küldése.

|Name (Név)|Leírás|
|---|---|
|id|A vizuális tartalom moderálás azonosítóját.|
|adultScore|A felnőtt pontszám (a tartalom moderátor).|
|racyScore|(A tartalom moderálás) ellopható pontszámot.|
|példányok|Amikor jelent meg a visual tartalom moderálás idő tartományok listáját.|

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
|id|A szöveges tartalom moderálás azonosítóját.|
|bannedWordsCount |A tiltott szavak száma.|
|bannedWordsRatio |A arány szavak a teljes száma.|


## <a name="next-steps"></a>További lépések

[Videó indexelő API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

További widgetek beágyazása az alkalmazás információ: [beágyazása videó indexelő widgeteket saját alkalmazásaiba](video-indexer-embed-widgets.md). 

