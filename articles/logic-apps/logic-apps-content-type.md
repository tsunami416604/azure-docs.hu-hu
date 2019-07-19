---
title: Tartalom típusának kezelése – Azure Logic Apps
description: Ismerje meg, hogyan kezeli a Logic Apps a tervezési időt és a futási időt a tartalomtípusok esetében
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 97897da13c70c29834b1fc276829b316416efd8d
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868913"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Tartalomtípusok kezelése Azure Logic Appsban

A különböző tartalomtípusok egy logikai alkalmazáson keresztül, például JSON-, XML-, egyszerű és bináris adatokat is elhelyezhetnek. Míg a Logic Apps támogatja az összes tartalomtípust, néhány natív támogatással rendelkezik, és nem igényel öntést vagy átalakítást a logikai alkalmazásokban. Más típusok esetén szükség lehet a castingra vagy a konverzióra. Ez a cikk azt ismerteti, hogyan kezeli a Logic Apps a tartalomtípusokat, és hogy miként lehet ezeket a típusokat megfelelően feldolgozni vagy átalakítani, ha szükséges.

A tartalomtípusok kezelésére szolgáló megfelelő módszer meghatározásához Logic apps a http-hívások `Content-Type` fejlécének értékére támaszkodik, például:

* [alkalmazás/JSON](#application-json) (natív típus)
* [szöveg/egyszerű](#text-plain) (natív típus)
* [alkalmazás/XML és alkalmazás/oktett – Stream](#application-xml-octet-stream)
* [Egyéb tartalomtípusok](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

A Logic Apps az *Application/JSON* tartalomtípusú kérelmeket JavaScript-jelölésű (JSON) objektumként tárolja és kezeli. Alapértelmezés szerint a JSON-tartalom elemzése nélkül is elemezhető. Egy "Application/JSON" típusú fejléctel rendelkező kérelem elemzéséhez használhat kifejezést. Ez a példa a `dog` `animal-type` tömb értékét adja vissza a casting nélkül: 
 
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
  
  2. Az **írja be vagy illessze be a minta JSON**-adattartalmat területen adja meg a minta hasznos adatait, majd válassza a **kész**lehetőséget. Példa: 

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

  3. A kérelemben győződjön meg róla, hogy tartalmaz `Content-Type` egy fejlécet, és állítsa be a `application/json`fejléc értékét a következőre:.

* **JSON-művelet elemzése**

  Ha ezt a műveletet a Logic app Designerben használja, elemezheti a JSON-kimenetet, és létrehozhat olyan felhasználóbarát tokeneket, amelyek a JSON-tartalomban szereplő tulajdonságokat jelölik. 
  Ezután egyszerűen hivatkozhat és felhasználhatja ezeket a tulajdonságokat a logikai alkalmazás munkafolyamataiban. A kérelem triggerhez hasonlóan egy JSON-séma is megadható vagy létrehozható, amely leírja az elemezni kívánt JSON-tartalmat. 
  Így könnyebben használhatja fel Azure Service Bus, Azure Cosmos DB stb. adatait.

  ![JSON-elemzés](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>szöveg/egyszerű

Ha a logikai alkalmazás olyan http `Content-Type` `text/plain`-üzeneteket kap, amelyeken a fejléc van beállítva, a logikai alkalmazás ezeket az üzeneteket nyers formában tárolja. Ha ezeket az üzeneteket a következő műveletekben a casting nélkül is felveszi, `Content-Type` a kérések `text/plain`a fejlécre vannak beállítva. 

Ha például egy egyszerű fájllal dolgozik, HTTP-kérést kap, amelynek a fejléce `Content-Type` `text/plain` a következő:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Ha ezt követően egy későbbi műveletben elküldi ezt a kérést, `@body('flatfile')`mint egy másik kérelem törzse, akkor a második kéréshez egy `Content-Type` `text/plain`fejléc is tartozik, amely a következőre van beállítva:. Ha egyszerű szöveggel dolgozik, de nem adott meg fejlécet, manuálisan is elvégezheti az adat szövegbe írását a [karakterlánc () függvény](../logic-apps/workflow-definition-language-functions-reference.md#string) használatával, például a következő kifejezéssel: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>alkalmazás/XML és alkalmazás/oktett – Stream

Logic apps mindig megőrzi a `Content-Type` kapott HTTP-kérést vagy választ. Így ha `Content-Type` a logikai alkalmazás a `application/octet-stream`készlettel együtt fogadja a tartalmat, és egy későbbi műveletbe belefoglalja a tartalmat, a kimenő `application/octet-stream`kérelem `Content-Type` is be van állítva. Így a Logic Apps garantálhatja, hogy az adatvesztés a munkafolyamaton keresztül nem vész el. A műveleti állapotot, a bemeneteket és a kimeneteket azonban egy JSON-objektum tárolja, miközben az állapot áthalad a munkafolyamaton. 

## <a name="converter-functions"></a>Átalakító függvények

Egyes adattípusok megőrzése érdekében Logic apps átalakítja a tartalmat egy bináris Base64 kódolású karakterlánccá a megfelelő metaadatokkal, amelyek megőrzik mind `$content` a hasznos adatokat `$content-type`, mind a-t, amelyeket a rendszer automatikusan konvertál. 

Ez a lista leírja, hogyan alakítja át a Logic Apps tartalmakat a [függvények](../logic-apps/workflow-definition-language-functions-reference.md)használatakor:

* `json()`: A következőre küldi az adatátvitelt`application/json`
* `xml()`: A következőre küldi az adatátvitelt`application/xml`
* `binary()`: A következőre küldi az adatátvitelt`application/octet-stream`
* `string()`: A következőre küldi az adatátvitelt`text/plain`
* `base64()`: Tartalom konvertálása Base64 kódolású karakterlánccá
* `base64toString()`: Base64 kódolású karakterlánc konvertálása`text/plain`
* `base64toBinary()`: Base64 kódolású karakterlánc konvertálása`application/octet-stream`
* `dataUri()`: Karakterlánc konvertálása adaturi-értékre
* `dataUriToBinary()`: Az adaturi-t bináris karakterlánccá alakítja
* `dataUriToString()`: Adaturi konvertálása karakterlánccá

Ha például olyan HTTP-kérést kap `Content-Type` `application/xml`, ahol a be van állítva, például a következő tartalom:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Ezt a tartalmat a és `@xml(triggerBody())` `triggerBody()` a `xml()` függvények kifejezés használatával is elvégezheti, majd később is használhatja ezt a tartalmat. Vagy használhatja `@xpath(xml(triggerBody()), '/CustomerName')` a kifejezést a és `xml()` a `xpath()` függvényekkel. 

## <a name="other-content-types"></a>Egyéb tartalomtípusok

A Logic apps együttműködik a szolgáltatással, és támogatja a többi tartalomtípust is, de előfordulhat, hogy manuálisan kell lekérnie az üzenet törzsét a `$content` változó dekódolásával.

Tegyük fel például, hogy a logikai alkalmazás egy, a `application/x-www-url-formencoded` tartalomtípussal ellátott kérelemben aktiválódik. Az összes adat megőrzése érdekében a `$content` kérelem törzsében lévő változó egy Base64-karakterláncként kódolt adattartalmat tartalmaz:

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

Ha azt szeretné, hogy a kimenő kérelem ugyanazzal `application/x-www-url-formencoded` a tartalomtípus-fejléccel rendelkezzen, a kérést felveheti a művelet törzsébe anélkül, hogy olyan kifejezést kellene használnia, amely `@body('formdataAction')`például a következő:. Ez a módszer azonban csak akkor működik, ha a törzs az egyetlen paraméter `body` a bemenetben. Ha egy `@body('formdataAction')` `application/json` kérelemben megpróbálja használni a kifejezést, futásidejű hibaüzenetet kap, mert a törzs küldése kódolt.
