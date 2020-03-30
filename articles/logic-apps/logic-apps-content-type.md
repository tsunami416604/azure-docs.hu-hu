---
title: Tartalomtípusok kezelése
description: Ismerje meg, hogyan kezelhetők a különböző tartalomtípusok a munkafolyamatokban a tervezési idő és a futási idő során az Azure Logic Apps-ben
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: ae0abe288edda2ce01311d8533b1f104409efce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666873"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Tartalomtípusok kezelése az Azure Logic Apps alkalmazásban

Különböző tartalomtípusok is átfolyhatnak egy logikai alkalmazáson, például JSON-on, XML-en, síkfájlokon és bináris adatokon. Míg a Logic Apps támogatja az összes tartalomtípust, néhány natív támogatást, és nem igényel casting vagy átalakítás a logikai alkalmazásokban. Más típusok szükség esetén öntést vagy átalakítást igényelhetnek. Ez a cikk azt ismerteti, hogy a Logic Apps hogyan kezeli a tartalomtípusokat, és hogyan megfelelően elvezetheti vagy konvertálhatja ezeket a típusokat, ha szükséges.

A tartalomtípusok kezelésének megfelelő módjának meghatározásához `Content-Type` a Logic Apps a HTTP-hívások fejlécértékére támaszkodik, például:

* [alkalmazás/json](#application-json) (natív típus)
* [szöveg/egyszerű](#text-plain) (natív szöveg)
* [alkalmazás/xml és alkalmazás/oktettfolyam](#application-xml-octet-stream)
* [Egyéb tartalomtípusok](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

A Logic Apps tárolja és kezeli az *alkalmazás/json* tartalomtípussal kapcsolatos kéréseket JavaScript-jelölési (JSON) objektumként. Alapértelmezés szerint a JSON-tartalmat öntés nélkül is elemezheti. Az "application/json" tartalomtípussal rendelkező fejléccel rendelkező kérelmek elemzéséhez használhat egy kifejezést. Ez a példa `dog` a `animal-type` tömb értékét adja vissza öntés nélkül: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Ha olyan JSON-adatokkal dolgozik, amelyek nem adnak meg fejlécet, manuálisan is leadhatja ezeket az adatokat a [JSON függvény](../logic-apps/workflow-definition-language-functions-reference.md#json)használatával, például: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Tokenek létrehozása JSON-tulajdonságokhoz

A Logic Apps lehetővé teszi, hogy felhasználóbarát jogkivonatokat hozzon létre, amelyek a JSON-tartalom tulajdonságait képviselik, így könnyebben hivatkozhat ezekre a tulajdonságokra a logikai alkalmazás munkafolyamatában.

* **Kérelem eseményindító**

  Ha ezt az eseményindítót a Logic App Designer, megadhat egy JSON-séma, amely leírja a hasznos várhatóan kapni. 
  A tervező elemzi a JSON-tartalmat ezzel a sémával, és felhasználóbarát jogkivonatokat hoz létre, amelyek a JSON-tartalom tulajdonságait képviselik. 
  Ezután könnyedén hivatkozhat ezekre a tulajdonságokra a logikai alkalmazás munkafolyamatában. 
  
  Ha nem rendelkezik sémával, létrehozhatja a sémát. 
  
  1. A kérelem eseményindító, válassza **a Minta hasznos adat létrehozása séma.**  
  
  2. Az **Enter (JSON-tartalom) megadása vagy beillesztése**csoportban adjon meg egy mintahasznos t, majd válassza **a Kész**lehetőséget. Példa: 

     ![Minta JSON-hasznos teher biztosítása](./media/logic-apps-content-type/request-trigger.png)

     A létrehozott séma most megjelenik az eseményindítóban.

     ![Minta JSON-hasznos teher biztosítása](./media/logic-apps-content-type/generated-schema.png)

     A kérelemeseményindító alapjául szolgáló definícióa a kódnézet-szerkesztőben található:

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

  3. A kérelemben győződjön meg `Content-Type` arról, hogy tartalmaz `application/json`egy fejlécet, és állítsa a fejléc értékét .

* **JSON-művelet elemzése**

  Ha ezt a műveletet a Logic App Designer, elemezheti a JSON-kimenet, és felhasználóbarát jogkivonatok, amelyek a JSON-tartalom tulajdonságait képviseli. 
  Ezután könnyedén hivatkozhat ezekre a tulajdonságokra a logikai alkalmazás munkafolyamatában. A Kérelem eseményindítóhoz hasonlóan megadhat vagy létrehozhat egy JSON-sémát, amely leírja az elemezni kívánt JSON-tartalmat. 
  Így könnyebben felhasználhatja az Azure Service Bus, az Azure Cosmos DB stb.

  ![JSON értelmezése](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>szöveg/egyszerű

Amikor a logikai alkalmazás olyan `Content-Type` HTTP-üzeneteket kap, amelyek fejléce a fejlécére `text/plain`van állítva, a logikai alkalmazás nyers formában tárolja ezeket az üzeneteket. Ha ezeket az üzeneteket a későbbi műveletekben `Content-Type` öntés `text/plain`nélkül adja meg, a kérelmek fejlécével a . 

Ha például egy sima fájllal dolgozik, előfordulhat, hogy `Content-Type` http-kérelmet kap, amelyben a fejléc tartalomtípusra `text/plain` van állítva:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Ha ezt a kérést egy későbbi műveletben egy másik kérés `@body('flatfile')`törzseként küldi `Content-Type` el, például `text/plain`a második kérés fejléce is. Ha egyszerű szöveges adatokkal dolgozik, de nem adott meg fejlécet, manuálisan is eldobhatja az adatokat a szövegbe a [karakterlánc() függvény](../logic-apps/workflow-definition-language-functions-reference.md#string) használatával, például a kifejezéssel: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>alkalmazás/xml és alkalmazás/oktettfolyam

Logic Apps mindig `Content-Type` megőrzi a fogadott HTTP-kérelem vagy válasz. Ha tehát a logikai `Content-Type` alkalmazás `application/octet-stream`a beállítással rendelkező tartalmat kap, és a tartalmat `Content-Type` egy `application/octet-stream`későbbi műveletben szereplő tartalmat öntés nélkül is felvesz, a kimenő kérelem is a .. Így a Logic Apps garantálja, hogy az adatok nem vesznek el a munkafolyamaton keresztül. A művelet állapota, illetve a bemenetek és kimenetek azonban egy JSON-objektumban tárolódnak, miközben az állapot a munkafolyamaton halad. 

## <a name="converter-functions"></a>Konverter függvények

Bizonyos adattípusok megőrzése érdekében a Logic Apps bináris base64 kódolású karakterláncsá alakítja `$content` a tartalmat `$content-type`és a készletet is megőrző megfelelő metaadatokkal rendelkező tartalmat. 

Ez a lista azt ismerteti, hogy a Logic Apps hogyan konvertálja a tartalmat a [következő függvények](../logic-apps/workflow-definition-language-functions-reference.md)használatakor:

* `json()`: Adatokat vet be a`application/json`
* `xml()`: Adatokat vet be a`application/xml`
* `binary()`: Adatokat vet be a`application/octet-stream`
* `string()`: Adatokat vet be a`text/plain`
* `base64()`: Tartalom átalakítása base64 kódolású karakterláncmá
* `base64toString()`: Base64 kódolású karakterláncot konvertál`text/plain`
* `base64toBinary()`: Base64 kódolású karakterláncot konvertál`application/octet-stream`
* `dataUri()`: Karakterlánc átalakítása adatURI-vá
* `dataUriToBinary()`: AdatURI-t bináris karakterlánclá alakít át
* `dataUriToString()`: AdatURI-t karakterláncmá alakít át

Ha például http-kérést `Content-Type` `application/xml`kap, ahol a beállítása a , például ez a tartalom:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Ezt a tartalmat úgy `@xml(triggerBody())` is `xml()` leadhatja, hogy a kifejezést a és `triggerBody()` a függvényekkel használja, majd később ezt a tartalmat használja. Vagy használhatja a `@xpath(xml(triggerBody()), '/CustomerName')` kifejezést `xpath()` a `xml()` és a függvényekkel. 

## <a name="other-content-types"></a>Egyéb tartalomtípusok

A Logic Apps más tartalomtípusokkal működik, és támogatja azokat, de `$content` előfordulhat, hogy manuálisan kell beszereznie az üzenettörzset a változó dekódolásával.

Tegyük fel például, hogy a logikai `application/x-www-url-formencoded` alkalmazást egy tartalomtípussal rendelkező kérelem váltja ki. Az összes adat megőrzése érdekében a `$content` kérelem törzsében lévő változó egy base64-karakterláncként kódolt hasznos adattal rendelkezik:

`CustomerName=Frank&Address=123+Avenue`

Mivel a kérelem nem egyszerű szöveges vagy JSON, a kérelem a művelet a következőképpen tárolódik:

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

Az adatokat manuálisan is elérheti egy ilyen kifejezés használatával, például ezzel a példával:

`@string(body('formdataAction'))` 

Ha azt szeretné, hogy a `application/x-www-url-formencoded` kimenő kérelem azonos tartalomtípus-fejléccel legyen, a kérelmet hozzáadhatja a `@body('formdataAction')`művelet törzséhez öntés nélkül, például a kifejezés használatával. Ez a módszer azonban csak akkor működik, `body` ha a törzs az egyetlen paraméter a bemenetben. Ha megpróbálja használni `@body('formdataAction')` a kifejezést `application/json` egy kérelemben, futásidejű hibát kap, mert a törzs kódolt.
