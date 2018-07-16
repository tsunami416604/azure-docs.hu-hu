---
title: A munkafolyamat definíciós nyelvséma – Azure Logic Apps |} A Microsoft Docs
description: A munkafolyamat-definíciós nyelv az Azure Logic Apps egyéni munkafolyamat-definíciókhoz írása
services: logic-apps
author: ecfan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: babe21db6acc2f7154857b4eb0a02356e89a8ca7
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060578"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>Logic Apps munkafolyamat-definíciók és a munkafolyamat-definíciós nyelvséma

Amikor létrehoz egy logikaialkalmazás-munkafolyamat a [Azure Logic Apps](../logic-apps/logic-apps-overview.md), alapul szolgáló a munkafolyamat-definíció a tényleges logika, amely a logikai alkalmazás ismerteti. Ez a leírás meghatározott és érvényesített struktúrát követi a munkafolyamat-definíciós nyelv séma, amely használja [JavaScript Object Notation (JSON)](https://www.json.org/) formátumban. 
  
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
| műveletek | Nem | Egy vagy több műveletet végrehajtani a munkafolyamat futtatáskor definíciói <p><p>Maximális műveletek: 250 | 
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

Egy munkafolyamat-futtatási lekérheti a kimenetét, tekintse át a logikai alkalmazás futtatási előzmények és részletek az Azure Portalon, vagy használja a [munkafolyamat REST API-val](https://docs.microsoft.com/rest/api/logic/workflows). Is átadhat kimeneti a külső rendszerekkel, például a Power BI, hogy irányítópultokat hozhat létre. 

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

Némely kifejezés le, előfordulhat, hogy még nem léteznek, amikor a Logic Apps-alkalmazás futni kezd futásidejű műveletek azok értékeit. Hivatkozhat, illetve ezekkel az értékekkel a kifejezésekben dolgozhat, használhatja a [ *funkciók*](../logic-apps/workflow-definition-language-functions-reference.md). Például használhatja a matematikai függvények számítások, például a [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) függvény, amely az egész számoknak vagy úszó összegét adja vissza. Minden függvény kapcsolatos részletes információkért tekintse meg a [betűrend szerinti rendezés áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).
Másik lehetőségként többet szeretne megtudni a functions és az általános célú.

Csak néhány példa feladatokat hajthat végre a funkciók a következők: 

| Tevékenység | Függvény-szintaxis | Eredmény | 
| ---- | --------------- | -------------- | 
| Kis formátumban adja vissza. | toLower (a(z) <*szöveg*> ") <p>Például: toLower('Hello') | "hello" | 
| Egy globálisan egyedi azonosítóját (GUID) adja vissza. | GUID() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

Ez a példa bemutatja, hogyan kezdheti az értékét a `customerName` paraméter, és rendelje hozzá ezt az értéket a `accountName` tulajdonság használatával a [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) függvény a kifejezésben:

```json
"accountName": "@parameters('customerName')"
```

Az alábbiakban néhány egyéb általános módszer használható függvény kifejezésében:

| Tevékenység | Függvény-szintaxis a kifejezés | 
| ---- | -------------------------------- | 
| Hajtanak végre munkát egy elemet az adott elem a függvénynek adja át. | "\@<*functionName*> (<*elem*>)" | 
| 1. Első a *parameterName*a használatával a beágyazott érték `parameters()` függvény. </br>2. Ezt az értéket adja át az eredmény az elvégzéséhez *functionName*. | "\@<*functionName*> (paraméter (" <*parameterName*> "))" | 
| 1. Az eredményt kapja a beágyazott belső függvény *functionName*. </br>2. Adja át az eredmény a külső függvényhez *functionName2*. | "\@<*functionName2*> (<*functionName*> (<*elem*>))" | 
| 1. Elérhető az eredmény *functionName*. </br>2. Tekintve, hogy az eredmény egy tulajdonsággal rendelkező objektumot *propertyName*, jelenik meg, hogy a tulajdonság értékét. | "\@<*functionName*>(<*item*>). <*propertyName*>" | 
||| 

Ha például a `concat()` függvény a két vagy több paraméterként karakterlánc-értékeket is igénybe vehet. Ez a függvény egy karakterlánc ezek a karakterláncok ezeket. Továbbíthatja a karakterlánc-literálnak, például "Sophia" és "Owen" annak érdekében, hogy egy kombinált karakterlánc "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Vagy, a karakterlánc-értékek kérhet le a paramétereket. Ez a példa a `parameters()` függvény az egyes `concat()` paraméter és a `firstName` és `lastName` paramétereket. Ezután adja át az eredményül kapott karakterláncok a `concat()` annak érdekében, hogy egy kombinált karakterláncot, például a "SophiaOwen" függvény:

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Mindkét módszer esetén mindkét példa hozzárendelése az eredményt a `customerName` tulajdonság. 

Minden függvény kapcsolatos részletes információkért tekintse meg a [betűrend szerinti rendezés áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).
Vagy többet szeretne megtudni a functions, az általános célú alapján.

<a name="string-functions"></a>

### <a name="string-functions"></a>Sztringfüggvények

Munka karakterláncokkal, használhatja a karakterlánc-függvények és még néhány [gyűjtemény funkciók](#collection-functions). Karakterlánc-függvények csak karakterláncok működik. 

| Karakterlánc-függvény | Tevékenység | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Két vagy több karakterláncok egyesítése, és a kombinált karakterláncot ad vissza. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Ellenőrizze, hogy egy karakterlánc végződik-e a megadott karakterláncrészlet. | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Hozzon létre egy globálisan egyedi azonosítóját (GUID) karakterlánc formájában. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Kezdő pozíciójának egy karakterláncrészt adja vissza. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | A karakterláncrész a szövegvégi pozíciót adja vissza. | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Cserélje le a megadott karakterlánc részkarakterláncot, és a frissített karakterláncot ad vissza. | 
| [felosztás](../logic-apps/workflow-definition-language-functions-reference.md#split) | Visszaadja egy tömb, amely elválasztja az egyes karakterek megadásakor a meghatározott elválasztó karakterrel és a egy karakterláncból karaktereket tartalmaz. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Annak ellenőrzése, hogy e karakterlánc kezdődik-e egy adott karakterláncrészletet. | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Karaktert adja vissza egy karakterláncból, a megadott pozíciónál kezdve. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Kis formátumban adja vissza. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Nagybetűk formátumban adja vissza. | 
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Kezdő és záró szóközök eltávolítása a karakterláncot, és a frissített karakterláncot ad vissza. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Gyűjtemény-funkciók

Gyűjtemények, általában tömbök, karakterláncok és egyes esetekben szótárak dolgozni, ezek a függvények gyűjtemény használhatja. 

| Gyűjtemény függvény | Tevékenység | 
| ------------------- | ---- | 
| [tartalmaz](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Ellenőrizze, hogy egy gyűjtemény rendelkezik-e egy adott elemet. |
| [üres](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Ellenőrizze, hogy egy gyűjtemény üres. | 
| [első](../logic-apps/workflow-definition-language-functions-reference.md#first) | Az első elem visszaadása egy gyűjteményt. | 
| [Metszet](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Vissza, amely rendelkezik *csak* a gyakori elemek a megadott gyűjtemények között. | 
| [csatlakozás](../logic-apps/workflow-definition-language-functions-reference.md#join) | Adja vissza, amely rendelkezik *összes* elemet a tömb a megadott karakter választja el. | 
| [utolsó](../logic-apps/workflow-definition-language-functions-reference.md#last) | Az utolsó elem visszaadása egy gyűjteményt. | 
| [Hossza](../logic-apps/workflow-definition-language-functions-reference.md#length) | A tömb vagy karakterlánc elemek számának visszaadása. | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | A gyűjtemény elejéről eltávolítandó elemek, és vissza *összes többi* elemek. | 
| [hajtsa végre a megfelelő](../logic-apps/workflow-definition-language-functions-reference.md#take) | Az első gyűjtemény elemek visszaadása. | 
| [Union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Vissza, amely rendelkezik *összes* elemet a megadott gyűjteményekkel a. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Összehasonlító függvények

Feltételek használata, értékek és kifejezés eredmények összehasonlítása, vagy különböző típusú logikai kiértékelése, ezek összehasonlító függvények használhatja. Kapcsolatos minden funkció teljes körű referenciáért lásd: a [betűrend szerinti rendezés áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Összehasonlító függvény | Tevékenység | 
| ------------------- | ---- | 
| [és](../logic-apps/workflow-definition-language-functions-reference.md#and) | Ellenőrzése, hogy az összes kifejezés igaz. | 
| [egyenlő](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Ellenőrizze, hogy mindkét értéket egyenértékűek. | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Ellenőrizze, hogy az első érték nagyobb, mint a második érték. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Ellenőrizze, hogy az első érték kisebb, mint a második érték egyenlő. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Ellenőrizze, hogy egy kifejezés true vagy FALSE (hamis). Az eredmény alapján a megadott érték visszaadása. | 
| [kevesebb](../logic-apps/workflow-definition-language-functions-reference.md#less) | Ellenőrizze, hogy van-e az első érték kisebb, mint a második érték. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Ellenőrizze, hogy az első érték kisebb vagy egyenlő a második érték. | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Ellenőrizze, hogy egy kifejezés false (hamis). | 
| [vagy](../logic-apps/workflow-definition-language-functions-reference.md#or) | Ellenőrizze, hogy legalább egy kifejezés igaz. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Konverziós függvények

Ha módosítani szeretné egy érték típus vagy formátum, használhatja a konverziós függvények. Például módosíthatja egy érték logikai érték beolvasása, egész. Ha szeretné megtudni, hogyan Logic Apps kezelje az átalakítás során tartalomtípusokat, lásd: [tartalomtípusok](../logic-apps/logic-apps-content-type.md). Kapcsolatos minden funkció teljes körű referenciáért lásd: a [betűrend szerinti rendezés áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Konverziós függvény | Tevékenység | 
| ------------------- | ---- | 
| [Pole](../logic-apps/workflow-definition-language-functions-reference.md#array) | A megadott egyetlen tömböt adjon vissza. Több bemenet, lásd: [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | A base64-kódolású verzióját egy karakterláncot ad vissza. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | A bináris verziójú a base64-kódolású karakterláncként adja vissza. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | A karakterlánc verziójú a base64-kódolású karakterláncként adja vissza. | 
| [Bináris](../logic-apps/workflow-definition-language-functions-reference.md#binary) | A bináris verziójára egy beviteli érték visszaadása. | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | A logikai verziójára egy beviteli érték visszaadása. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Származó több bemenet egy tömböt adnak vissza. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Az adat-URI-bemeneti érték visszaadása. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | A bináris verziójú adat-URI visszaadása. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | A karakterlánc verziójú adat-URI visszaadása. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | A karakterlánc verziójú a base64-kódolású karakterláncként adja vissza. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | A bináris verziójú adat-URI visszaadása. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Lépjen vissza, hogy lecseréli escape-karakter a dekódolt verziókkal karakterláncot. | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Adja vissza, amely kiváltja a URL-címekben nem biztonságos karakter az escape-karaktereket. | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Vissza lebegőpontos szám egy bemeneti érték. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Az egész verziója egy karakterláncot ad vissza. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | A JavaScript Object Notation (JSON) típusú érték, vagy egy karakterláncot vagy XML-objektumot ad vissza. | 
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Ez a karakterlánc verzió egy beviteli érték visszaadása. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Az URI-kódolású verzió, a egy beviteli érték visszaadása lecserélésével URL-címekben nem biztonságos karaktereket escape-karaktereket. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Az URI-ként kódolt karakterlánc bináris verziót adja vissza. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | A karakterlánc-verziót egy URI-ként kódolt karakterláncot ad vissza. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Az XML-verziója egy karakterláncot ad vissza. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Matematikai függvények

Egész számok és úszó dolgozik, matematika függvényekkel használható. Kapcsolatos minden funkció teljes körű referenciáért lásd: a [betűrend szerinti rendezés áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Matematikai függvény | Tevékenység | 
| ------------- | ---- | 
| [Hozzáadása](../logic-apps/workflow-definition-language-functions-reference.md#add) | Két szám összeadásának az eredmény visszaadása. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Két szám hányadosát adja vissza az eredményt. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | A legmagasabb érték visszaadása egy készletből, számokat, vagy egy tömb. | 
| [Min](../logic-apps/workflow-definition-language-functions-reference.md#min) | A legkisebb érték visszaadása számokat vagy a tömböt. | 
| [MOD](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Két szám hányadosát adja vissza a maradékot. | 
| [MUL számú](../logic-apps/workflow-definition-language-functions-reference.md#mul) | A termék vissza a két szám szorzásának. | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Véletlenszerű egész szám visszaadása egy megadott tartományból. | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Adja vissza, amely elindítja a megadott egész számnak egész számok tömbje. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Az első szám, a második szám kivonásának az eredmény visszaadása. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Date és time függvények

Dolgozunk a dátumok és időpontok, használhatja a date és time függvények.
Kapcsolatos minden funkció teljes körű referenciáért lásd: a [betűrend szerinti rendezés áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Dátum vagy idő függvény | Tevékenység | 
| --------------------- | ---- | 
| [napokHozzaadasa](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adja hozzá a napok számát időbélyeghez. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adja hozzá a órák száma időbélyeget. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adja hozzá a percek száma időbélyeget. | 
| [masodpercekHozzaadasa](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adja hozzá a másodpercek számát egy időbélyegző. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Adja hozzá az időegységek számos időbélyeghez. Lásd még: [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Konvertálja az időbélyegző az univerzális idő egyezményes (UTC) időzónában. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | A forrásidőzóna időbélyeg konvertálása időzónában. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | A próbaverzióról időbélyeg a forrásidőzóna univerzális idő egyezményes (UTC). | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | A hónap összetevőt napjának visszaadása a időbélyeget. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Az időbélyeg összetevő-hét napjának visszaadása. | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Az időbélyeg az év összetevőt napjának visszaadása. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | A dátum visszaadása egy időbélyegző. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Vissza az aktuális timestamp plusz a megadott időegység. Lásd még: [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | A megadott időegység mínusz az aktuális időbélyeget adja vissza. Lásd még: [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Időbélyeg nap visszaadása. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Az időbélyeg az óra kezdetét adja vissza. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Az időbélyeg a hónap kezdetét adja vissza. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Az időbélyeg mértékegységét számos kivonása. Lásd még: [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [órajel során végbemenő](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Vissza a `ticks` tulajdonság értéke a megadott időbélyeg. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Az aktuális timestamp vissza karakterláncként. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Munkafolyamat-függvények

Ezek a munkafolyamat-függvények segítségével:

* Egy munkafolyamat-példány adatainak beolvasása futási időben. 
* A bemeneti adatok hárítható el, a logic apps használt működnek.
* A használatával hivatkozik a kimenetek triggereket és műveleteket.

Például a használatával hivatkozik a kimeneteket egyetlen művelet, és az adatokat későbbi művelettel. Kapcsolatos minden funkció teljes körű referenciáért lásd: a [betűrend szerinti rendezés áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Munkafolyamat-funkció | Tevékenység | 
| ----------------- | ---- | 
| [a művelet](../logic-apps/workflow-definition-language-functions-reference.md#action) | Az aktuális művelet kimeneti runtime vagy az értékek visszaadásához más JSON-név és érték párokat. Lásd még: [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Egy művelet visszaadandó `body` kimeneti futásidőben. Lásd még: [törzs](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Futásidőben egy műveletet a hibaüzenettel reagál. Lásd: [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Egy művelet kimenetéből runtime vagy az értékek visszaadásához más JSON-név és érték párokat. Lásd még: [művelet](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [Törzs](#body) | Egy művelet visszaadandó `body` kimeneti futásidőben. Lásd még: [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Hozzon létre egy tömb azokra az értékekre, amelyek megfelelnek a kulcs nevét *űrlapadatokból* vagy *űrlapként* műveleti kimenetek. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Ad vissza, amely megfelel egy műveletet a kulcs nevét egyetlen értéket *űrlapadatokból* vagy *űrlapként kimeneti*. | 
| [Elem](../logic-apps/workflow-definition-language-functions-reference.md#item) | Ismétlődő műveletet keresztül egy tömb, belül vissza az aktuális elem a tömbben a művelet aktuális iteráció során. | 
| [elemek](../logic-apps/workflow-definition-language-functions-reference.md#items) | Belül a for-each vagy do-until-hurok, lépjen vissza az aktuális elemet a megadott ciklus a.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | A "visszahívási URL-címe", amely meghívja az eseményindítók vagy műveletek visszaadása. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Egy adott rész törzsét visszaadása egy művelet kimenete, amely több részből áll. | 
| [paraméterek](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | A logikai alkalmazás definíciójában leírt paraméter értékének visszaadása. | 
| [Az eseményindító](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Futásidőben, vagy más JSON-név-érték párok egy eseményindítót a hibaüzenettel reagál. Lásd még: [triggerOutputs](#triggerOutputs) és [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Egy eseményindító vissza `body` kimeneti futásidőben. Lásd: [eseményindító](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | A kulcs nevét egy olyan értéket ad vissza *űrlapadatokból* vagy *űrlapként* kimenetek aktiválásához. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | A trigger többrészes kimenetéből egy adott rész törzsét visszaadása. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Hozzon létre egy tömb, melynek értékei felel meg egy kiszolgálókulcsnevet a *űrlapadatokból* vagy *űrlapként* kimenetek aktiválásához. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | A trigger kimenetéből runtime vagy az értékek visszaadásához más JSON-név és érték párokat. Lásd: [eseményindító](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [Változók](../logic-apps/workflow-definition-language-functions-reference.md#variables) | A megadott változó értékének visszaadása. | 
| [A munkafolyamat](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Magáról a munkafolyamatról részleteinek vissza a futtatási idő alatt. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>URI-elemzési függvények

Egységes erőforrás-azonosítók (URI-k) és az URI-k különböző tulajdonságértékek lekérése, használhatja e URI-elemzési függvények. Kapcsolatos minden funkció teljes körű referenciáért lásd: a [betűrend szerinti rendezés áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| URI-elemzési függvény | Tevékenység | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Vissza a `host` egy egységes erőforrás-azonosítója (URI) értékét. | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Vissza a `path` egy egységes erőforrás-azonosítója (URI) értékét. | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Vissza a `path` és `query` egy egységes erőforrás-azonosítója (URI) értékeket. | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Vissza a `port` egy egységes erőforrás-azonosítója (URI) értékét. | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Vissza a `query` egy egységes erőforrás-azonosítója (URI) értékét. | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Vissza a `scheme` egy egységes erőforrás-azonosítója (URI) értékét. | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>JSON- és XML-funkciók

JSON-objektumok és az XML-csomópontnak dolgozni, ezek a műveletek függvényei használhatja. Kapcsolatos minden funkció teljes körű referenciáért lásd: a [betűrend szerinti rendezés áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Adatkezelési függvényt | Tevékenység | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Egy tulajdonság és az érték, vagy a név-érték párhoz, ad hozzá egy JSON-objektumot, és a frissített objektumot ad vissza. | 
| [Coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Az első nem üres érték visszaadása egy vagy több paramétert. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Tulajdonság eltávolítása egy JSON-objektumot, és a frissített objektumot ad vissza. | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Állítsa be egy JSON-objektum tulajdonság értékét, és a frissített objektumot ad vissza. | 
| [XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | XML ellenőrizze csomópontok és a egy (XML Path Language) XPath kifejezés megfelelő értékeket, és a megfelelő csomópontok vagy értékeket adnak vissza. | 
||| 

## <a name="next-steps"></a>További lépések

* Ismerje meg [munkafolyamat-definíciós nyelv műveletek és triggerek](../logic-apps/logic-apps-workflow-actions-triggers.md)
* További tudnivalók programozott módon létrehozásáról és rendelkező logikai alkalmazások kezelése a [munkafolyamat REST API](https://docs.microsoft.com/rest/api/logic/workflows)
