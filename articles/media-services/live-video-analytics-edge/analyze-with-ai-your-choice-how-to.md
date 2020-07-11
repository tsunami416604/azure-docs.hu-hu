---
title: Élő videó elemzése az Ön által választott AI-vel – Azure
description: Ebből a cikkből megtudhatja, hogyan hozhat létre olyan IoT Edge modult, amely integrálható az élő videók elemzésével IoT Edge az élő videók elemzéséhez egy tetszőleges számítógépes látási modell használatával.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6a1ea3ebd8c7de4c691d7a982dbc08e9d08d9e38
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86182865"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Élő videó elemzése tetszőleges mesterséges intelligenciával

Ebből a cikkből megtudhatja, hogyan hozhat létre olyan IoT Edge modult, amely integrálható az élő videók elemzésével IoT Edge az élő videók elemzéséhez egy tetszőleges számítógépes látási modell használatával. 

## <a name="pre-reading"></a>Előzetes olvasás

[A Media Graph koncepciója](media-graph-concept.md)

## <a name="media-graph-extension"></a>Media Graph-bővítmény

A IoT Edge élő videó-elemzések olyan bővíthetőségi modellt határoznak meg, amely lehetővé teszi a Media Graph feldolgozási képességeinek kiterjesztését a saját Media Analytics-összetevőire a Graph bővítménnyel. Az elemzési összetevő a hagyományos képfeldolgozási technikákat vagy a Computer látási AI-modelleket is használhatja. A Graph-bővítmények engedélyezve vannak, ha a [http-bővítmény processzor](media-graph-concept.md#http-extension-processor) -csomópontját a Media Graph-ban engedélyezték. A HTTP-bővítmény processzora továbbíthatja a képkockákat egy Ön által megadott HTTP-végpontba, és az adott összetevő felületének megfelelően működik. A kapcsolódás helyi vagy távoli végponton végezhető el, és szükség esetén hitelesítéssel és TLS-titkosítással is biztonságossá tehető. Emellett a processzor lehetővé teszi a képkockák opcionális skálázását és kódolását a továbbítás előtt.

Dönthet úgy, hogy tetszőleges következtetési modellt futtat a saját Docker-tárolón, például a ONNX, a TensorFlow, a PyTorch vagy más felhasználók számára elérhető következtetéseken. Az Ön által választott programozási nyelvet és kódtárakat is használhatja, ha a saját tárolón lévő HTTP-kiszolgálót is elérhetővé kívánja tenni. A következtető tárolót az élő videó Analytics Edge-modulja mellett kell üzembe helyezni a legjobb teljesítmény érdekében, majd a HTTP-bővítmény processzorán keresztül kell meghívni a Graph-topológiában.
Emellett az egyéni modulban megjelenő hívások gyakorisága szabályozható úgy, hogy opcionálisan hozzáad egy [mozgásérzékelő processzort](media-graph-concept.md#motion-detection-processor) és egy [frame rate szűrő processzort](media-graph-concept.md#frame-rate-filter-processor) a http-bővítmény processzorához.

Az alábbi ábra a magas szintű adatfolyamot ábrázolja:

![Peremhálózati eszköz](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

Ez lehetővé teszi, hogy az Ön által választott AI-modellekkel elemezze a videót, hogy megfeleljen az egyedi üzleti igényeinek. Az AI-modellek a nyílt forráskódú Közösségből vagy a saját adatszakértőkből vagy egy speciális AI-szolgáltatóból származnak.

## <a name="media-graph-http-extension-contract-definitions"></a>A Media Graph HTTP-bővítmények szerződésének definíciói

Ez a szakasz azt a HTTP-szerződést határozza meg, amely meghatározza az adatfolyamot.

### <a name="http-extensibility-protocol"></a>HTTP-bővíthetőségi protokoll  

A HTTP-szerződés a következőképpen van definiálva:

* A modul HTTP-kiszolgálóként működik.
* A IoT Edge-modul Live Video Analytics szolgáltatása HTTP-ügyfélként működik.

### <a name="request"></a>Kérés

Az élő video Analytics-modulról a modulnak küldött kérések a következők:

| Kulcs | Érték |
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Elfogadás|alkalmazás/JSON,*/*|
|Engedélyezés| Alapszintű, kivonatoló, tulajdonos (Egyéni fejléc-támogatással)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>rendszerkép/x-RAW|
|Content-Length|Törzs hossza (bájt)|
|User-Agent|Azure Media Services|
|Törzs|Képbájtok, a bináris fájl az egyik támogatott tartalomtípusban van kódolva.|

#### <a name="example"></a>Példa

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Reagálás

A modul válaszait az élő videó elemzési moduljának a következőképpen kell megadni:

| Kulcs | Érték |
|---|---|
|Állapotkódok|200 OK – következtetések eredményei találhatók<br/>204 nincs tartalom – a mesterséges intelligencia nem talált tartalmat<br/>400 hibás kérelem – nem várt<br/>500 belső kiszolgálóhiba – nem várt<br/>503 a kiszolgáló foglalt – az AMS az "újrapróbálkozási" fejléc alapján, vagy egy alapértelmezett időtartamon alapul, ha nincs előre beállítva fejléc.|
|Content-Type|application/json|
|Content-Length|    Törzs hossza (bájt)|
|Törzs|JSON-objektum egyetlen "következtetéssel" tulajdonsággal.|

#### <a name="example"></a>Példa

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Erősen ajánlott, hogy a válaszokat érvényes JSON-dokumentumok használatával adja vissza a rendszer az alábbi előre meghatározott sémát követve. Így jobban biztosítható a más összetevőkkel való együttműködés, valamint az élő video Analytics modulhoz hozzáadott lehetséges jövőbeli képességek.

Ha a modul olyan választ ad vissza, amelyben a tartalomtípus nem "Application/JSON", a Live Video Analytics az üzenetet az 64-es alapszintű tartalomként kódolja, és az átlátszatlan JSON-adattartalmat fogja szerializálni.

Ha a modul "Application/JSON" típusú választ ad vissza, de a JSON-séma nem követi az [alább vázolt hivatkozási metaadatok sémáját](#inference-metadata-schema), akkor az üzenet tartalma a folyamaton keresztül továbbítódik, az együttműködési képesség pedig csökken.

> [!NOTE]
> Ha a modul nem eredményez eredményt, akkor a HTTP 204 állapotkódot (nincs tartalom) üres válasz törzstel kell visszaadnia. Az élő videó Analytics ezt az üres eredményt fogja érteni, és nem továbbítja az eseményt a folyamat során.

### <a name="inference-metadata-schema"></a>Metaadat-séma következtetése

Az egyes következtetési objektumok ezt a felülbíráló sémát követik:

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Adategyezmények – osztály-hierarchia

![Adategyezmények – osztály-hierarchia](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Példák  

Az alábbi példa egyetlen eseményt tartalmaz, amely az összes támogatott következtetési típust tartalmazza:

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Minta HTTP-bővítmény modulok

Néhány példa a HTTP-bővítmények elérhetővé tételére az [élő videó Analytics GitHub](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)-tárházában található. A videó- [elemzési minták](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) egyike azt mutatja be, hogyan használható az [YOLOv3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) -modell az objektumok észleléséhez IoT Edge modul létrehozásához. Egy másik [videó-elemzési minta](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx-tiny) bemutatja, hogyan használható az apró YOLOv3 a YOLOv3 ONNX-modell egyszerűsített verziójával. Ugyanezzel a módszerrel hozhat létre saját modult egy tetszőleges AI-modellel.

## <a name="next-steps"></a>További lépések

[Hibaelhárítás](troubleshoot-how-to.md)
