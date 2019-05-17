---
title: A munkafolyamat-definíciós nyelv – Azure Logic Apps adatbázisséma hivatkozása
description: Az útmutatót az Azure Logic Apps munkafolyamat-definíciós nyelv séma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 05/13/2019
ms.openlocfilehash: 3b0ad33ea6348f24079b3c88f972437244c0bc93
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596760"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Az Azure Logic Apps munkafolyamat-definíciós nyelv séma referenciája

A Logic Apps-alkalmazás létrehozásakor [Azure Logic Apps](../logic-apps/logic-apps-overview.md), a logikai alkalmazás, amely leírja a tényleges logika, amely futtatja a logikai alkalmazás az alapul szolgáló munkafolyamat definícióval rendelkezik. A munkafolyamat-definíció használ [JSON](https://www.json.org/) , és érvényesíti a munkafolyamat-definíciós nyelvséma struktúrát követi. Ezt a hivatkozást nyújt áttekintést, ez a struktúra, és hogyan a séma meghatározza az attribútum a munkafolyamat-definícióban.

## <a name="workflow-definition-structure"></a>A munkafolyamat szabályzatdefiníciók struktúrája

Munkafolyamat-definíció mindig tartalmaz egy eseményindítót a logikai alkalmazás, valamint egy vagy több műveletet, amely az eseményindító elindulása után hárítható el.

A magas szintű munkafolyamat-definíció struktúráját a következő:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Attribútum | Szükséges | Leírás |
|-----------|----------|-------------|
| `definition` | Igen | A kezdő elem esetében a munkafolyamat-definíció |
| `$schema` | Csak akkor, ha a külsőleg hivatkozik egy munkafolyamat-definíció | A JSON-fájl, amely leírja a munkafolyamat-definíciós nyelv verziót, amely itt találja a helye: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Nem | Egy vagy több műveletet végrehajtani a munkafolyamat futtatáskor definíciói. További információkért lásd: [triggereket és műveleteket](#triggers-actions). <p><p>Maximális műveletek: 250 |
| `contentVersion` | Nem | A munkafolyamat-definíció, amely alapértelmezés szerint a "1.0.0.0" verziószám. Könnyebben azonosíthatja és a egy munkafolyamat üzembe helyezésekor, győződjön meg arról, hogy a megfelelő definíciós, adja meg a használni kívánt érték. |
| `outputs` | Nem | A kimenetek, amelyek egy munkafolyamat-futtatási visszaadásához a definíciókat. További információkért lásd: [kimenetek](#outputs). <p><p>A kimenetek maximális: 10 |
| `parameters` | Nem | Egy vagy több paraméter, amely adatokat átadni a munkafolyamatba a definíciókat. További információkért lásd: [paraméterek](#parameters). <p><p>Maximális paraméterek: 50 |
| `staticResults` | Nem | Ha statikus eredmények engedélyezve vannak ezek a műveletek utánzatként funkcionáló kimenetként műveletek által visszaadott egy vagy több statikus eredmények definíciói. Minden művelet-definícióban a `runtimeConfiguration.staticResult.name` attribútum hivatkozik, az annak megfelelő definíciót belül `staticResults`. További információkért lásd: [statikus eredmények](#static-results). |
| `triggers` | Nem | Egy vagy több olyan eseményindítókat, amelyek a munkafolyamat-példányt létrehozni a definíciókat. Több mint egy eseményindítót, csak az a munkafolyamat-definíciós nyelv, a Logic Apps Designer segítségével vizuálisan nem határozhatja meg. További információkért lásd: [triggereket és műveleteket](#triggers-actions). <p><p>Maximális eseményindítók: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Triggerek és műveletek

A munkafolyamat-definíció a `triggers` és `actions` szakaszok határozza meg a hívás, amely a munkafolyamat végrehajtása során. Szintaxist és ezek a szakaszok további információt lásd: [munkafolyamat triggerei és műveletei](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="outputs"></a>

## <a name="outputs"></a>Kimenetek

Az a `outputs` szakaszban, a munkafolyamat adhatnak vissza, ha befejeződött az adatok meghatározásához futtatása. Az egyes futtatások érték vagy egy adott állapotú követéséhez adja meg például, hogy a munkafolyamat kimenete adja vissza az adatokat.

> [!NOTE]
> Amikor a bejövő kérésekre válaszol egy szolgáltatás REST API-ból, ne használjon `outputs`. Ehelyett használja a `Response` művelet típusa. További információkért lásd: [munkafolyamat triggerei és műveletei](../logic-apps/logic-apps-workflow-actions-triggers.md).

Az általános struktúráját egy kimeneti definíciót a következő:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Attribútum | Szükséges | Típus | Leírás |
|-----------|----------|------|-------------|
| <*key-name*> | Igen | String | A kulcs nevét, a kimeneti értéket adnak vissza. |
| <*key-type*> | Igen | int, lebegőpontos, string, securestring, bool, tömböt, JSON-objektum | A kimeneti visszatérési érték típusát |
| <*key-value*> | Igen | Ugyanaz, mint a <*kulcs-típus*> | A kimeneti visszatérési érték |
|||||

Egy munkafolyamat-futtatási lekérheti a kimenetét, tekintse át a logikai alkalmazás futtatási előzmények és részletek az Azure Portalon, vagy használja a [munkafolyamat REST API-val](https://docs.microsoft.com/rest/api/logic/workflows). Is átadhat kimeneti a külső rendszerekkel, például a Power bi-ban, hogy irányítópultokat hozhat létre.

<a name="parameters"></a>

## <a name="parameters"></a>Paraméterek

Az a `parameters` területén adja meg a központi telepítési bemenetek fogadásához használ a munkafolyamat-definíció az összes munkafolyamat-paraméterek. Paraméter deklarációinak és a paraméterértékek szükségesek üzembe helyezéskor. Ezeket a paramétereket a többi munkafolyamat szakasz használata előtt győződjön meg arról, hogy, hogy a ezekben a szakaszokban a paramétereket. 

Íme egy paraméterdefinícióhoz általános struktúrát:

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": {
      "key": {
        "name": "<key-value>"
      }
    }
  }
},
```

| Attribútum | Szükséges | Típus | Leírás |
|-----------|----------|------|-------------|
| <*parameter-type*> | Igen | int, lebegőpontos, string, securestring, bool, tömböt, JSON-objektum, secureobject <p><p>**Megjegyzés**: Az összes jelszavak, kulcsok és titkos kulcsokat, használja a `securestring` és `secureobject` típusokat, mert a `GET` művelet nem ad vissza ezeket a típusokat. Paraméterek védelmével kapcsolatos további információkért lásd: [a logikai alkalmazás védelme](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | A paraméter típusa |
| <*alapértelmezett paraméterértékek*> | Igen | Ugyanaz, mint `type` | Az alapértelmezett paraméter értéke, ha nem ad meg értéket, ha a munkafolyamat példányosítja |
| <*array-with-permitted-parameter-values*> | Nem | Tömb | Egy tömb, amely a paraméter elfogadhat értékekkel |
| `metadata` | Nem | JSON-objektum | Bármely más paraméter részleteit, például a nevét vagy a logikai alkalmazás vagy folyamat, vagy a Visual Studio vagy más eszközök által használt tervezési idejű adatok olvasható leírását |
||||

<a name="static-results"></a>

## <a name="static-results"></a>Statikus eredmények

Az a `staticResults` attribútumot, adja meg egy műveletet helyettem `outputs` és `status` , amely a művelet ad vissza, ha a művelet a statikus eredmény beállítás be van kapcsolva. A művelet-definícióban a `runtimeConfiguration.staticResult.name` attribútum hivatkozik, a neve, a statikus eredmény definíciójában belül `staticResults`. Megtudhatja, hogyan zajlik [próbaadatokat rendelkező logikai alkalmazások tesztelése statikus eredmények beállításával](../logic-apps/test-logic-apps-mock-data-static-results.md).

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Attribútum | Szükséges | Típus | Leírás |
|-----------|----------|------|-------------|
| <*static-result-definition-name*> | Igen | String | A nevet egy statikus eredmény-definíciót, amely egy művelet definíció keresztül hivatkozhat egy `runtimeConfiguration.staticResult` objektum. További információkért lásd: [modul konfigurációs beállítások](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Bármely egyedi név, amelyeket szeretne is használhatja. Alapértelmezés szerint ez az egyedi név hozzáíródik a száma, amely a szükséges mértékben növekszik. |
| <*output-attributes-and-values-returned*> | Igen | Változó | Ezek az attribútumok vonatkozó követelmények eltérőek lehetnek különböző feltételek alapján. Például, ha a `status` van `Succeeded`, a `outputs` attribútum tartalmaz olyan attribútumokat, és a művelet által visszaadott szerint utánzatként funkcionáló kimenetek értékek. Ha a `status` van `Failed`, a `outputs` attribútum tartalmazza a `errors` attribútuma, amely egy vagy több hibával tömb `message` hibaadatok rendelkező objektumok. |
| <*header-values*> | Nem | JSON | A művelet által visszaadott értékeket fejléc |
| <*status-code-returned*> | Igen | String | A művelet által visszaadott állapotkódot követi |
| <*action-status*> | Igen | String | A művelet állapota, például `Succeeded` vagy `Failed` |
|||||

Például a HTTP-művelet definíció, a a `runtimeConfiguration.staticResult.name` hivatkozások attribútum `HTTP0` belül a `staticResults` attribútumot, ahol definiálva vannak az utánzatként funkcionáló művelet kimeneteire. A `runtimeConfiguration.staticResult.staticResultOptions` attribútum meghatározza, hogy a statikus eredmény beállítás `Enabled` a HTTP-művelet.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

A HTTP-művelet a kimenetek a adja vissza a `HTTP0` belül definíció `staticResults`. Ebben a példában az állapot kódot utánzatként funkcionáló kimenete `OK`. Fejléc-értékek utánzatként funkcionáló kimenete `"Content-Type": "application/JSON"`. A művelet állapotát, utánzatként funkcionáló kimenete `Succeeded`.

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Kifejezések

A JSON-t akkor konstansértékekkel létező már a tervezés során, például:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Futtatási időpontig nem létező értékeket is rendelkezhet. Ezeket az értékeket jelölik, használható *kifejezések*, amely értékeli ki a futási időben. Egy kifejezés, amely tartalmazhat egy vagy több olyan feladatütemezési [funkciók](#functions), [operátorok](#operators), változókat, explicit értéket vagy konstansok. A munkafolyamat-definíció, a kifejezés bárhol használhatja a JSON-karakterlánc értéken a kifejezés a következő bejelentkezés előtaggal (\@). Egy kifejezés, amely egy JSON-értéket képvisel kiértékelésekor a kifejezés törzsében eltávolításával kivonjuk a \@ karaktert, és mindig egy másik JSON-értéket eredményez.

Például a korábban meghatározott `customerName` tulajdonságot használja, megtekintheti a tulajdonság értéke az a [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) a kifejezések működnek, és rendelje hozzá ezt az értéket a `accountName` tulajdonság:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Karakterlánc-interpolációs* is lehetővé teszi több kifejezésének csomagol be, amelyek belül használhatja a \@ karaktert, és kapcsos zárójelek közé ({}). A szintaxis a következő:

```json
@{ "<expression1>", "<expression2>" }
```

Az eredmény mindig egy karakterláncot, így ez a funkció hasonló a `concat()` működnek, például: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Ha rendelkezik egy konstans sztring kezdetű a \@ karakter, előtag a \@ karaktert, egy másik \@ karakter az escape-karakter: \@\@

Ezek a példák bemutatják, hogyan értékeli ki a kifejezések:

| JSON-értéket | Eredmény |
|------------|--------|
| "Sophia Owen" | Vissza a következő karaktereket: "Sophia Owen" |
| "array [1]" | Vissza a következő karaktereket: "array [1]" |
| "\@\@" | Ezek a karakterek visszaadása egy karakterből álló karakterlánc: "\@" |
| " \@" | Két karakter karakterláncként adja vissza a ezek a karakterek: ' \@" |
|||

Az ezekben a példákban tegyük fel, hogy a "January" egyenlő "myBirthMonth" és "myAge" 42 ahány adjon meg:

```json
"myBirthMonth": "January",
"myAge": 42
```

Ezek a példák bemutatják, hogyan értékeli ki az alábbi kifejezések:

| JSON-kifejezés | Eredmény |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | Ezt a karakterláncot adja vissza: "January" |
| "\@{parameters('myBirthMonth')}" | Ezt a karakterláncot adja vissza: "January" |
| "\@parameters('myAge')" | Ezt a számot adja vissza: 42 |
| "\@{parameters('myAge')}" | A szám visszaadása egy karakterlánc: "42" |
| "Megterhelése és életkor van \@{parameters('myAge')}" | Ezt a karakterláncot adja vissza: "Megterhelése és életkor is 42" |
| "\@(" megterhelése és életkor ", string(parameters('myAge'))) concat" | Ezt a karakterláncot adja vissza: "Megterhelése és életkor is 42" |
| "Saját kora \@ \@{parameters('myAge')}" | Ezt a karakterláncot, amely magában foglalja a kifejezést adja vissza: ' Megterhelése és életkor van \@{parameters('myAge')}' |
|||

Ha a Logic Apps Designerben vizuálisan dolgozik, például hozhat létre a Kifejezésszerkesztő keresztül kifejezések:

![Logic Apps Designerben > Kifejezésszerkesztő](./media/logic-apps-workflow-definition-language/expression-builder.png)

Ha elkészült, a kifejezés jelenik meg a megfelelő tulajdonságot a munkafolyamat-definícióban, például a `searchQuery` tulajdonság itt:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operátorok

A [kifejezések](#expressions) és [funkciók](#functions), operátorok elvégzendő feladataikhoz, például a hivatkozás egy tulajdonságot vagy egy érték a tömbben.

| Operátor | Feladat |
|----------|------|
| ' | Egy szöveges karakterlánc bemenetként, vagy a kifejezések és függvények használatához burkolhatja a karakterlánc csak egyetlen idézőjelek között, például `'<myString>'`. Ne használja a dupla idézőjelek (""), amelyek ütköznek, a JSON formázását teljes kifejezés körül. Példa: <p>**Igen**: length('Hello') </br>**Nem**: length("Hello") <p>Ha tömbök, vagy számokat adja át, nem szükséges alkalmazásburkoló absztrakt. Példa: <p>**Igen**: hossza ([1, 2, 3]) </br>**Nem**: hossza ("[1, 2, 3]") |
| [] | Egy értéket egy adott pozícióban (index) tömbben lévő hivatkozni, használjon szögletes zárójeleket. Ha például a második elem beolvasása a tömböt: <p>`myArray[1]` |
| . | Egy tulajdonság az objektum hivatkozik, a pont operátort használja. Például lekérése a `name` tulajdonsága egy `customer` JSON-objektum: <p>`"@parameters('customer').name"` |
| ? | A futásidejű hiba nélkül objektum null értékű tulajdonságok hivatkozik, használja a kérdőjel operátort. Például egy eseményindítóból null kimeneti kezelése érdekében használhatja a kifejezést: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funkciók

Némely kifejezés le futásidejű előfordulhat, hogy még nem léteznek futtatásához a munkafolyamat-definíció indításakor végrehajtandó azok értékeit. Hivatkozhat, illetve ezekkel az értékekkel a kifejezésekben dolgozhat, használhatja a [ *funkciók* ](../logic-apps/workflow-definition-language-functions-reference.md) , amely a munkafolyamat-definíciós nyelv biztosít.

## <a name="next-steps"></a>További lépések

* Ismerje meg [munkafolyamat-definíciós nyelv műveletek és triggerek](../logic-apps/logic-apps-workflow-actions-triggers.md)
* További tudnivalók programozott módon létrehozásáról és rendelkező logikai alkalmazások kezelése a [munkafolyamat REST API](https://docs.microsoft.com/rest/api/logic/workflows)
