---
title: Tartalomtípusok kezelése
description: Megtudhatja, hogyan kezelheti a munkafolyamatokban a különböző tartalomtípusokat a tervezési idő és a Futtatás ideje alatt Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: ae0abe288edda2ce01311d8533b1f104409efce0
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666873"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Tartalomtípusok kezelése Azure Logic Appsban

A különböző tartalomtípusok egy logikai alkalmazáson keresztül, például JSON-, XML-, egyszerű és bináris adatokat is elhelyezhetnek. Míg a Logic Apps támogatja az összes tartalomtípust, néhány natív támogatással rendelkezik, és nem igényel öntést vagy átalakítást a logikai alkalmazásokban. Más típusok esetén szükség lehet a castingra vagy a konverzióra. Ez a cikk azt ismerteti, hogyan kezeli a Logic Apps a tartalomtípusokat, és hogy miként lehet ezeket a típusokat megfelelően feldolgozni vagy átalakítani, ha szükséges.

A tartalomtípusok kezelésére szolgáló megfelelő módszer meghatározásához Logic Apps a HTTP-hívások `Content-Type` fejlécének értékére támaszkodik, például:

* [alkalmazás/JSON](#application-json) (natív típus)
* [szöveg/egyszerű](#text-plain) (natív típus)
* [alkalmazás/XML és alkalmazás/oktett – Stream](#application-xml-octet-stream)
* [Egyéb tartalomtípusok](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

A Logic Apps az *Application/JSON* tartalomtípusú kérelmeket JavaScript-jelölésű (JSON) objektumként tárolja és kezeli. Alapértelmezés szerint a JSON-tartalom elemzése nélkül is elemezhető. Egy "Application/JSON" típusú fejléctel rendelkező kérelem elemzéséhez használhat kifejezést. Ez a példa a `animal-type` tömbből `dog` értéket adja vissza a casting nélkül: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Ha olyan JSON-adattal dolgozik, amely nem ad meg fejlécet, a JSON [() függvény](../logic-apps/workflow-definition-language-functions-reference.md#json)használatával manuálisan is elvégezheti ezeket az adatátvitelt a JSON-ban, például: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Tokenek létrehozása JSON-tulajdonságokhoz

A Logic Apps lehetővé teszi a JSON-tartalomban szereplő tulajdonságokat jelképező felhasználóbarát tokenek létrehozását, hogy a logikai alkalmazás munkafolyamataiban könnyebben hivatkozhat és használhassa ezeket a tulajdonságokat.

* **Kérelem triggere**

  Ha ezt az triggert a Logic app Designerben használja, megadhat egy JSON-sémát, amely leírja a várt adattartalmat. 
  A tervező elemzi a JSON-tartalmat a séma használatával, és olyan felhasználóbarát jogkivonatokat hoz létre, amelyek a JSON-tartalomban szereplő tulajdonságokat jelölik. 
  Ezután egyszerűen hivatkozhat és felhasználhatja ezeket a tulajdonságokat a logikai alkalmazás munkafolyamataiban. 
  
  Ha nem rendelkezik sémával, előállíthatja a sémát. 
  
  1. A kérelem triggerben válassza a **minta hasznos adatok használata a séma létrehozásához**lehetőséget.  
  
  2. Az **írja be vagy illessze be a minta JSON-adattartalmat**területen adja meg a minta hasznos adatait, majd válassza a **kész**lehetőséget. Példa: 

     ![Minta JSON-adattartalom megadása](./media/logic-apps-content-type/request-trigger.png)

     A létrehozott séma most megjelenik az triggerben.

     ![Minta JSON-adattartalom megadása](./media/logic-apps-content-type/generated-schema.png)

     Itt látható a kérelem triggerének alapjául szolgáló definíció a Code View Editorban:

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

  3. A kérelemben győződjön meg róla, hogy `Content-Type` fejlécet tartalmaz, és a fejléc értékét `application/json`értékre állítja.

* **JSON-művelet elemzése**

  Ha ezt a műveletet a Logic app Designerben használja, elemezheti a JSON-kimenetet, és létrehozhat olyan felhasználóbarát tokeneket, amelyek a JSON-tartalomban szereplő tulajdonságokat jelölik. 
  Ezután egyszerűen hivatkozhat és felhasználhatja ezeket a tulajdonságokat a logikai alkalmazás munkafolyamataiban. A kérelem triggerhez hasonlóan egy JSON-séma is megadható vagy létrehozható, amely leírja az elemezni kívánt JSON-tartalmat. 
  Így könnyebben használhatja fel Azure Service Bus, Azure Cosmos DB stb. adatait.

  ![JSON értelmezése](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/plain

Ha a logikai alkalmazás olyan HTTP-üzeneteket fogad, amelyeken a `Content-Type` fejléce `text/plain`re van beállítva, a logikai alkalmazás ezeket az üzeneteket nyers formában tárolja. Ha ezeket az üzeneteket a következő műveletekben a casting nélkül is felveszi, a kérések a `Content-Type` fejlécet `text/plain`értékre kerülnek. 

Ha például egy sima fájllal dolgozik, HTTP-kérést kaphat a `Content-Type` fejlécet `text/plain` tartalomtípushoz:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Ha ezt követően egy későbbi műveletben elküldi ezt a kérést, mint egy másik kérelem törzse, például `@body('flatfile')`, akkor a második kérelemhez `Content-Type` fejléc is tartozik, amely `text/plain`ra van beállítva. Ha egyszerű szöveggel dolgozik, de nem adott meg fejlécet, manuálisan is elvégezheti az adat szövegbe írását a [karakterlánc () függvény](../logic-apps/workflow-definition-language-functions-reference.md#string) használatával, például a következő kifejezéssel: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>alkalmazás/XML és alkalmazás/oktett – Stream

Logic Apps mindig megőrzi a `Content-Type` egy fogadott HTTP-kérelemben vagy válaszban. Így ha a logikai alkalmazás `Content-Type` `application/octet-stream`re van beállítva, és egy későbbi műveletbe belefoglalja a tartalmat, és nem dönti el, a kimenő kérelemhez is `Content-Type` `application/octet-stream`van beállítva. Így a Logic Apps garantálhatja, hogy az adatvesztés a munkafolyamaton keresztül nem vész el. A műveleti állapotot, a bemeneteket és a kimeneteket azonban egy JSON-objektum tárolja, miközben az állapot áthalad a munkafolyamaton. 

## <a name="converter-functions"></a>Átalakító függvények

Egyes adattípusok megőrzése érdekében Logic Apps átalakítja a tartalmat egy bináris Base64 kódolású karakterlánccá a megfelelő metaadatokkal, amelyek megőrzik mind a `$content` hasznos adatokat, mind a `$content-type`, amelyeket a rendszer automatikusan konvertál. 

Ez a lista leírja, hogyan alakítja át a Logic Apps tartalmakat a [függvények](../logic-apps/workflow-definition-language-functions-reference.md)használatakor:

* `json()`: az adat`application/json`
* `xml()`: az adat`application/xml`
* `binary()`: az adat`application/octet-stream`
* `string()`: az adat`text/plain`
* `base64()`: tartalom konvertálása Base64 kódolású karakterlánccá
* `base64toString()`: Base64 kódolású karakterlánc konvertálása `text/plain`
* `base64toBinary()`: Base64 kódolású karakterlánc konvertálása `application/octet-stream`
* `dataUri()`: sztring átalakítása adaturi-értékre
* `dataUriToBinary()`: az adaturi-t bináris karakterlánccá alakítja
* `dataUriToString()`: adaturi konvertálása karakterlánccá

Ha például olyan HTTP-kérést kap, amelyben a `Content-Type` `application/xml`re van állítva, például a következő tartalomra:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Ezt a tartalmat a `@xml(triggerBody())` kifejezést használhatja a `xml()` és a `triggerBody()` függvény használatával, majd később is használhatja ezt a tartalmat. Vagy használhatja a `@xpath(xml(triggerBody()), '/CustomerName')` kifejezést a `xpath()` és a `xml()` függvényekkel. 

## <a name="other-content-types"></a>Egyéb tartalomtípusok

A Logic Apps együttműködik a szolgáltatással, és támogatja a többi tartalomtípust is, de előfordulhat, hogy az `$content` változó dekódolásával manuálisan kell lekérnie az üzenet törzsét.

Tegyük fel például, hogy a logikai alkalmazást egy, a `application/x-www-url-formencoded` tartalomtípussal rendelkező kérelem indítja el. Az összes adat megőrzése érdekében a kérelem törzsében lévő `$content` változó egy Base64-karakterláncként kódolt adattartalmat tartalmaz:

`CustomerName=Frank&Address=123+Avenue`

Mivel a kérés nem egyszerű szöveg vagy JSON, a kérelem a következő módon tárolódik a műveletben:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

A Logic Apps natív funkciókat biztosít az űrlapadatok kezeléséhez, például: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Vagy manuálisan is elérheti az adatelérést egy kifejezéssel, például a következő példával:

`@string(body('formdataAction'))` 

Ha azt szeretné, hogy a kimenő kérelem ugyanazzal a `application/x-www-url-formencoded` tartalomtípus-fejléccel rendelkezzen, a kérést felveheti a művelet törzsébe anélkül, hogy olyan kifejezést kellene használnia, mint például a `@body('formdataAction')`. Ez a módszer azonban csak akkor működik, ha a törzs az egyetlen paraméter a `body` bemenetben. Ha a `@body('formdataAction')` kifejezést próbálja használni egy `application/json`-kérelemben, futásidejű hibaüzenetet kap, mert a törzs küldése kódolt.
