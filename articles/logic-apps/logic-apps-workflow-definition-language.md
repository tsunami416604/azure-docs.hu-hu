---
title: Munkafolyamat-definíciós nyelv – Azure Logic Apps séma referenciája |} A Microsoft Docs
description: A munkafolyamat-definíciós nyelv az Azure Logic Apps egyéni munkafolyamat-definíciókhoz írása
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 04/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: d5dadd054f95e61626942a1cab7d95ba8c9182e1
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060173"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Az Azure Logic Apps munkafolyamat-definíciós nyelv séma referenciája

Amikor létrehoz egy logikaialkalmazás-munkafolyamat a [Azure Logic Apps](../logic-apps/logic-apps-overview.md), alapul szolgáló a munkafolyamat-definíció a tényleges logika, amely a logikai alkalmazás ismerteti. Ez a leírás meghatározott és érvényesített struktúrát követi a munkafolyamat-definíciós nyelv séma, amely használja [JavaScript Object Notation (JSON)](https://www.json.org/). 
  
## <a name="workflow-definition-structure"></a>A munkafolyamat szabályzatdefiníciók struktúrája

Egy munkafolyamat-definíció van legalább egy triggert, amely a logikai alkalmazás példányosítja, valamint egy vagy több műveletet, amely a logikai alkalmazást. 

A magas szintű munkafolyamat-definíció struktúráját a következő:  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| Elem | Szükséges | Leírás | 
|---------|----------|-------------| 
| definíció | Igen | A kezdő elem esetében a munkafolyamat-definíció | 
| $schema | Csak akkor, ha a külsőleg hivatkozik egy munkafolyamat-definíció | A JSON-fájl, amely leírja a munkafolyamat-definíciós nyelv verziót, amely itt találja a helye: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | Nem | A munkafolyamat-definíció, amely alapértelmezés szerint a "1.0.0.0" verziószám. Könnyebben azonosíthatja és a egy munkafolyamat üzembe helyezésekor, győződjön meg arról, hogy a megfelelő definíciós, adja meg a használni kívánt érték. | 
| paraméterek | Nem | Egy vagy több paraméter, amely adatokat átadni a munkafolyamatba definíciói <p><p>Maximális paraméterek: 50 | 
| Eseményindítók | Nem | Egy vagy több olyan eseményindítókat, amelyek a munkafolyamat-példányt létrehozni a definíciókat. Több mint egy eseményindítót, csak az a munkafolyamat-definíciós nyelv, a Logic Apps Designer segítségével vizuálisan nem határozhatja meg. <p><p>Maximális eseményindítók: 10 | 
| Műveletek | Nem | Egy vagy több műveletet végrehajtani a munkafolyamat futtatáskor definíciói <p><p>Maximális műveletek: 250 | 
| kimenetek | Nem | A kimenetek, amelyek egy munkafolyamat-futtatási visszaadásához definíciói <p><p>A kimenetek maximális: 10 |  
|||| 

## <a name="parameters"></a>Paraméterek

Az a `parameters` szakaszban, a logikai alkalmazás üzembe helyezési bemenetek fogadásához használ az összes munkafolyamat-paraméterek megadása. Paraméter deklarációinak és a paraméterértékek szükségesek üzembe helyezéskor. Ezeket a paramétereket a többi munkafolyamat szakasz használata előtt győződjön meg arról, hogy, hogy a ezekben a szakaszokban a paramétereket. 

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

| Elem | Szükséges | Típus | Leírás |  
|---------|----------|------|-------------|  
| type | Igen | int, lebegőpontos, string, securestring, bool, tömböt, JSON-objektum, secureobject <p><p>**Megjegyzés:**: minden jelszavak, kulcsok és titkos kulcsokat, használja a `securestring` és `secureobject` típusokat, mert a `GET` művelet nem ad vissza ezeket a típusokat. | A paraméter típusa |
| DefaultValue érték | Nem | Ugyanaz, mint `type` | Az alapértelmezett paraméter értéke, ha nem ad meg értéket, ha a munkafolyamat példányosítja | 
| allowedValues | Nem | Ugyanaz, mint `type` | Egy tömb, amely a paraméter elfogadhat értékekkel |  
| metaadatok | Nem | JSON-objektum | Bármely más paraméter részleteit, például a nevét vagy a logikai alkalmazás vagy a Visual Studio vagy más eszközök által használt tervezési idejű adatok olvasható leírása |  
||||

## <a name="triggers-and-actions"></a>Triggerek és műveletek  

A munkafolyamat-definíció a `triggers` és `actions` szakaszok határozza meg a hívás, amely a munkafolyamat végrehajtása során. Szintaxist és ezek a szakaszok további információt lásd: [munkafolyamat triggerei és műveletei](../logic-apps/logic-apps-workflow-actions-triggers.md).
  
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

| Elem | Szükséges | Típus | Leírás | 
|---------|----------|------|-------------| 
| <*kulcs neve*> | Igen | Sztring | A kulcs nevét, a kimeneti értéket adnak vissza. |  
| type | Igen | int, lebegőpontos, string, securestring, bool, tömböt, JSON-objektum | A kimeneti visszatérési érték típusát | 
| érték | Igen | Ugyanaz, mint `type` | A kimeneti visszatérési érték |  
||||| 

Egy munkafolyamat-futtatási lekérheti a kimenetét, tekintse át a logikai alkalmazás futtatási előzmények és részletek az Azure Portalon, vagy használja a [munkafolyamat REST API-val](https://docs.microsoft.com/rest/api/logic/workflows). Is átadhat kimeneti a külső rendszerekkel, például a Power bi-ban, hogy irányítópultokat hozhat létre. 

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
| "\@parameters('myBirthMonth')" | Ezt a karakterláncot ad vissza: "January" |  
| "\@{parameters('myBirthMonth')}" | Ezt a karakterláncot ad vissza: "January" |  
| "\@parameters('myAge')" | A szám visszaadása: 42 |  
| "\@{parameters('myAge')}" | Ez egy karakterlánc, szám vissza: "42" |  
| "Megterhelése és életkor van \@{parameters('myAge')}" | Ezt a karakterláncot ad vissza: "megterhelése és életkor is 42" |  
| "\@(" megterhelése és életkor ", string(parameters('myAge'))) concat" | Ezt a karakterláncot ad vissza: "megterhelése és életkor is 42" |  
| "Saját kora \@ \@{parameters('myAge')}" | Ezt a karakterláncot, amely magában foglalja a kifejezést ad vissza: "saját kora \@{parameters('myAge')}' | 
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

| Művelet | Tevékenység | 
|----------|------|
| ' | Egy szöveges karakterlánc bemenetként, vagy a kifejezések és függvények használatához burkolhatja a karakterlánc csak egyetlen idézőjelek között, például `'<myString>'`. Ne használja a dupla idézőjelek (""), amelyek ütköznek, a JSON formázását teljes kifejezés körül. Példa: <p>**Igen**: length('Hello') </br>**Nem**: length("Hello") <p>Ha tömbök, vagy számokat adja át, nem szükséges alkalmazásburkoló absztrakt. Példa: <p>**Igen**: hossza ([1, 2, 3]) </br>**Nem**: hossza ("[1, 2, 3]") | 
| [] | Egy értéket egy adott pozícióban (index) tömbben lévő hivatkozni, használjon szögletes zárójeleket. Ha például a második elem beolvasása a tömböt: <p>`myArray[1]` | 
| . | Egy tulajdonság az objektum hivatkozik, a pont operátort használja. Például lekérése a `name` tulajdonsága egy `customer` JSON-objektum: <p>`"@parameters('customer').name"` | 
| ? | A futásidejű hiba nélkül objektum null értékű tulajdonságok hivatkozik, használja a kérdőjel operátort. Például egy eseményindítóból null kimeneti kezelése érdekében használhatja a kifejezést: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Functions

Némely kifejezés le, előfordulhat, hogy még nem léteznek, amikor a Logic Apps-alkalmazás futni kezd futásidejű műveletek azok értékeit. Hivatkozhat, illetve ezekkel az értékekkel a kifejezésekben dolgozhat, használhatja a [ *funkciók* ](../logic-apps/workflow-definition-language-functions-reference.md) , amely a munkafolyamat-definíciós nyelv biztosít. 

## <a name="next-steps"></a>További lépések

* Ismerje meg [munkafolyamat-definíciós nyelv műveletek és triggerek](../logic-apps/logic-apps-workflow-actions-triggers.md)
* További tudnivalók programozott módon létrehozásáról és rendelkező logikai alkalmazások kezelése a [munkafolyamat REST API](https://docs.microsoft.com/rest/api/logic/workflows)
