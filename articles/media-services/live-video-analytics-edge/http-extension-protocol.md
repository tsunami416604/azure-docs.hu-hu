---
title: HTTP-bővítményi protokoll – Azure
description: Ebből a cikkből megtudhatja, hogyan küldhet üzeneteket az élő videó elemzési modulja és az AI-vagy CV-modulja között a HTTP-bővítmény protokoll használatával.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: bfc8672185d284abc8f2985b44cea92000bc73db
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89448358"
---
# <a name="http-extension-protocol"></a>HTTP-bővítmény-protokoll

Ebből a cikkből megtudhatja, hogyan küldhet üzeneteket az élő videó elemzési modulja és az AI-vagy CV-modulja között a HTTP-bővítmény protokoll használatával.

A HTTP-szerződés a következő két összetevő között van definiálva:

* HTTP-kiszolgáló
* A IoT Edge-modul Live Video Analytics szolgáltatása HTTP-ügyfélként működik

## <a name="request"></a>Kérés

A Live Video Analytics-modulból a HTTP-kiszolgálóra irányuló kérelmek a következőképpen alakulnak:

|Kulcs|Érték|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|Elfogadás|alkalmazás/JSON,  */*|
|Engedélyezés|Alapszintű, kivonatoló, tulajdonos (Egyéni fejléc-támogatással)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>rendszerkép/x-RAW|
|Content-Length test hossza (bájt)|
|User-Agent|Azure Media Services|
|Törzs|Képbájtok, a bináris fájl az egyik támogatott tartalomtípusban van kódolva.|

### <a name="example"></a>Példa

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>Reagálás

A modulról az élő videó elemzési modulra adott válaszok a következők:

|Kulcs|Érték|
|---|---|
|Állapotkódok|200 OK – következtetések eredményei találhatók<br/>204 nincs tartalom – a mesterséges intelligencia nem talált tartalmat<br/>400 hibás kérelem – nem várt<br/>500 belső kiszolgálóhiba – nem várt<br/>503 a kiszolgáló foglalt – az AMS az "újrapróbálkozási" fejléc alapján, vagy egy alapértelmezett időtartamon alapul, ha nincs előre beállítva fejléc.|
|Content-Type|application/json|
|Content-Length|Törzs hossza (bájt)|
|Törzs|JSON-objektum egyetlen "következtetéssel" tulajdonsággal.|

### <a name="example"></a>Példa

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

Ha a modul "Application/JSON" típusú választ ad vissza, de a JSON-séma nem követi az alább vázolt hivatkozási metaadatok sémáját, akkor az üzenet tartalma a folyamaton keresztül továbbítódik, az együttműködési képesség pedig csökken.

> [!NOTE]
> Ha a modul nem eredményez eredményt, akkor a HTTP 204 állapotkódot (nincs tartalom) üres válasz törzstel kell visszaadnia. Az élő videó Analytics ezt az üres eredményt fogja érteni, és nem továbbítja az eseményt a folyamat során.

## <a name="data-contracts---class-hierarchy"></a>Adategyezmények – osztály-hierarchia

![osztály-hierarchia](./media/http-extension-protocol/class-hierarchy.png)

## <a name="next-steps"></a>További lépések

[gRPC-adategyezmény](grpc-data-contract.md)
