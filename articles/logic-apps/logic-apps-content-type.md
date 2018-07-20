---
title: Tartalomtípusok kezelése – Azure Logic Apps |} A Microsoft Docs
description: Ismerje meg, hogyan kezeli a Logic Apps tartalomtípus a tervezés során, és a futási idő
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 82eb9c895f016efe569651dc89885d2e4850fd59
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159091"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Tartalomtípusok kezelése az Azure Logic Appsben

Különböző egy logikai alkalmazást, például JSON, XML, egybesimított fájlok és a bináris adatok is áthaladhat. Logic Apps minden tartalom típust támogat, míg egyes natív támogatással rendelkezik, és nem igényel, döntő vagy átalakítása a logic Apps. Más típusú konvertálási vagy átalakítási szükség szerint lehet szükség. Ez a cikk bemutatja, hogyan kezeli a Logic Apps a tartalomtípusokat, és hogyan megfelelően alakítható vagy szükség esetén ezek a típusok konvertálni.

Tartalomtípusok kezelése megfelelő módszert meghatározni, a Logic Apps támaszkodik a `Content-Type` fejléc értéke HTTP-hívások, például:

* [az Application/json](#application-json) (nativní typ)
* [text/plain](#text-plain) (nativní typ)
* [Application/xml és application/octet-stream](#application-xml-octet-stream)
* [Más típusú tartalomhoz](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

A Logic Apps tárolja, és minden olyan kérésekre, és kezeli a *application/json* tartalomtípus egy JavaScript Notation (JSON) objektum. Alapértelmezés szerint minden döntő nélkül JSON-tartalmak elemezhetők. Egy kérelmet, amely a "application/json" tartalomtípus-fejléc elemezni, használhatja a kifejezésben. Ebben a példában az értéket adja vissza `dog` származó a `animal-type` nélkül döntő tömb: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Ha nem adja meg a fejléc JSON-adatok dolgozik, is manuálisan szavazattal JSON-adatok használatával a [json() függvény](../logic-apps/workflow-definition-language-functions-reference.md#json), például: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>A JSON-tulajdonságokkal-jogkivonat készítése

A Logic Apps lehetővé teszi, hogy így hivatkoznak, és könnyebben használja azokat a tulajdonságokat a logikai alkalmazás munkafolyamat a tulajdonságokat a JSON-tartalmak képviselő felhasználóbarát jogkivonatokat hoz létre.

* **Kérelem típusú trigger**

  A Logic App Designerben az eseményindító használatakor, megadhat egy JSON-sémát, amely leírja a hasznos adatokat kaphat. 
  A Tervező JSON-tartalmak elemzi a séma használatával, és felhasználóbarát, amelyek a tulajdonságokat a JSON-tartalmak a jogkivonatokat állít elő. 
  Ezután könnyedén hivatkoznak, és azokat a tulajdonságokat, a logikai alkalmazás munkafolyamat során használja. 
  
  Ha nem rendelkezik a séma, a séma is létrehozhat. 
  
  1. Válassza ki a kérelem eseményindítójában **Mintaadat használata séma létrehozásához**.  
  
  2. A **írja vagy illessze be a JSON hasznosadat-minta**, adjon meg egy hasznosadat-minta, és válassza a **kész**. Példa: 

     ![Adja meg a JSON hasznosadat-minta](./media/logic-apps-content-type/request-trigger.png)

     Ekkor megjelenik a létrehozott sémát az eseményindító.

     ![Adja meg a JSON hasznosadat-minta](./media/logic-apps-content-type/generated-schema.png)

     Itt látható a kérelem típusú trigger a kódszerkesztőben megtekintése az alapul szolgáló definíciója:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. A kérés esetén adja meg egy `Content-Type` fejlécet és a fejléc értékét állítsa `application/json`.

* **A művelet JSON elemzése**

  Ez a művelet használatakor a Logic App Designerben elemzése JSON-kimenetet, és felhasználóbarát, amelyek a tulajdonságokat a JSON-tartalmak a jogkivonatokat hoz létre. 
  Ezután könnyedén hivatkoznak, és azokat a tulajdonságokat, a logikai alkalmazás munkafolyamat során használja. A kérelem típusú trigger hasonlóan is biztosítanak, vagy hozzon létre egy JSON-sémát, amely leírja az elemezni kívánt JSON-tartalmak. 
  Így könnyebben az adatokat felhasználhatja az Azure Service Bus, az Azure Cosmos DB és így tovább.

  ![JSON elemzése](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>egyszerű szöveg

Ha a logikai alkalmazás fogadja a HTTP-üzenetek, amelyek rendelkeznek a `Content-Type` fejléc beállítása `text/plain`, a logikai alkalmazás nyers formában tárolja ezeket az üzeneteket. Ha az ezt követő műveletek döntő nélkül adja meg ezeket az üzeneteket, a napból kéréseket a `Content-Type` fejléc beállítása `text/plain`. 

Például dolgozik egy egybesimított fájlt, előfordulhat, hogy kap a HTTP-kérést a `Content-Type` fejléc beállítása `text/plain` tartalom típusa:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Ha ezután a kérelmet küld egy újabb műveletet egy másik kérelem törzsében szereplő például `@body('flatfile')`, a második kérés is rendelkezik egy `Content-Type` fejlécet, amely értékre van állítva `text/plain`. Ha az egyszerű szöveges formátumban, de nem adott meg a fejléc adatokkal dolgozik, is manuálisan szavazattal szöveges adatok használatával a [string() függvény](../logic-apps/workflow-definition-language-functions-reference.md#string) például a következő kifejezést: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Application/xml és application/octet-stream

A Logic Apps mindig megőrzi a `Content-Type` kapott HTTP-kérelem vagy válasz. Igen, ha a logikai alkalmazás a tartalmat megkap `Content-Type` beállítása `application/octet-stream`, és azt, hogy tartalom döntő nélkül újabb művelettel, a kimenő kérelem is rendelkezik `Content-Type` beállítása `application/octet-stream`. Ezzel a módszerrel a Logic Apps tud garantálni, hogy adatok nem vesznek el a munkafolyamaton keresztül áthelyezés közben. Azonban a művelet állapotát, vagy bemeneteit és kimeneteit, tárolódik egy JSON-objektum, amíg az állapot keresztül halad át a munkafolyamat. 

## <a name="converter-functions"></a>Converter funkciók

Egyes adattípusok megőrzéséhez a Logic Apps tartalom alakítja egy bináris base64-kódolású karakterlánc, amely megőrzi az is megfelelő metaadatok a `$content` hasznos és a `$content-type`, amely automatikusan átalakítva. 

Ez a lista ismerteti a Logic Apps hogyan alakítja át a tartalmat a ezek használatakor [funkciók](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: Adatok típuskonverziók `application/json`
* `xml()`: Adatok típuskonverziók `application/xml`
* `binary()`: Adatok típuskonverziók `application/octet-stream`
* `string()`: Adatok típuskonverziók `text/plain`
* `base64()`: Tartalom alakítja a base64-karakterlánc
* `base64toString()`: A base64-kódolású sztring átalakítása kisbetűssé `text/plain`
* `base64toBinary()`: A base64-kódolású sztring átalakítása kisbetűssé `application/octet-stream`
* `encodeDataUri()`: Egy karakterláncot kódol, egy dataUri bajtového Pole
* `decodeDataUri()`: Dekódol egy `dataUri` be egy bajtového Pole

Például, ha HTTP-kérést kap ahol `Content-Type` beállítása `application/xml`, például a tartalom:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Ez a tartalom használatával is szavazattal az `@xml(triggerBody())` kifejezések a `xml()` és `triggerBody()` működik, majd a tartalom később. Vagy használhatja a `@xpath(xml(triggerBody()), '/CustomerName')` kifejezések a `xpath()` és `xml()` funkciók. 

## <a name="other-content-types"></a>Más típusú tartalomhoz

A Logic Apps együttműködik és támogatja a más típusú tartalmakhoz, de előfordulhat, hogy manuálisan kap az üzenet törzse által dekódolás a `$content` változó.

Tegyük fel például, a logikai alkalmazás lekérdezi váltott egy kérelmet a `application/x-www-url-formencoded` tartalom típusa. Az adatok megőrzése érdekében a `$content` változó a kérelem törzsében szereplő rendelkezik egy Base64 kódolású karakterláncként kódolt hasznos:

`CustomerName=Frank&Address=123+Avenue`

A kérelem nem egyszerű szöveg- vagy JSON, mert a kérelem módon tárolja a művelet:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

A Logic Apps natív funkciókat biztosít az űrlapadatokat, például: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Vagy például ebben a példában egy kifejezés használatával manuálisan is elérhetők az adatok:

`@string(body('formdataAction'))` 

Ha szeretné, hogy ugyanaz a kimenő kérelem `application/x-www-url-formencoded` tartalomtípus-fejléc, adhat hozzá a kérelem a műveleti szövegtörzs bármely döntő nélkül kifejezés használatával `@body('formdataAction')`. Azonban ez a módszer csak akkor működik, a szervezetnek az a egyetlen paraméter a `body` bemeneti. Ha megpróbálja használni a `@body('formdataAction')` található kifejezés egy `application/json` kérést, mivel a szervezet kódolt küldött futásidejű hiba kap.
