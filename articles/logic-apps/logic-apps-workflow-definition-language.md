---
title: Munkafolyamat definícióját nyelvi séma - Azure Logic Apps |} Microsoft Docs
description: Egyéni munkafolyamat definíciója az Azure Logic Apps Munkafolyamatdefiníciós nyelve a írása
services: logic-apps
author: ecfan
manager: cfowler
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
ms.openlocfilehash: efbfffec10b665ebab230375e774e476199c4ad5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886805"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>Logic Apps munkafolyamat-definícióhoz, a munkafolyamat-definíció nyelvi séma

Amikor létrehoz egy logic app munkafolyamat [Azure Logic Apps](../logic-apps/logic-apps-overview.md), alapul szolgáló a munkafolyamat-definíciót a tényleges logika, amely futtatja a logikai alkalmazásnak ismerteti. A leírást a következő olyan struktúrában, amelynek van definiálva, és érvényesíti a Munkafolyamatdefiníciós nyelve séma, amely használja [JavaScript Object Notation (JSON)](https://www.json.org/) formátumban. 
  
## <a name="workflow-definition-structure"></a>A munkafolyamat szerkezete

Egy munkafolyamat-definíciót tartalmaz, amely a Logic Apps alkalmazást példányosítja legalább egy eseményindító, valamint egy vagy több műveletet a Logic Apps alkalmazást futtató. 

A munkafolyamat-definíció magas szintű struktúra a következő:  
  
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
| definíció | Igen | A munkafolyamat-definíciót a kezdő elem | 
| $schema | Csak akkor, ha egy munkafolyamat-definíciót külsőleg hivatkozik | A JSON-fájl, amely leírja a Munkafolyamatdefiníciós nyelve verziója, amely itt található helyét: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | Nem | A munkafolyamat-definíciót, amely alapértelmezés szerint a "1.0.0.0" verziószáma. Az azonosítása, és egy munkafolyamat telepítésekor, győződjön meg arról, hogy a megfelelő definíciós, adja meg egy értéket használja. | 
| paraméterek | Nem | A definíciók egy vagy több paraméter, amely adatokat továbbítja a munkafolyamat <p><p>Maximális paraméterek: 50 | 
| Eseményindítók | Nem | A definíciók hozható létre a munkafolyamat egy vagy több eseményindító. Egynél több eseményindító, csak az a Munkafolyamatdefiníciós nyelve, a Logic Apps tervező segítségével vizuálisan nem adhat meg. <p><p>Maximális eseményindítók: 10 | 
| műveletek | Nem | Egy vagy több művelet végrehajtása munkafolyamat futásidőben definíciói <p><p>Maximális műveletek: 250 | 
| kimenetek | Nem | A kimenetek, amelyek a munkafolyamat futtatása visszaadásának definíciói <p><p>Maximális kimenetek: 10 |  
|||| 

## <a name="parameters"></a>Paraméterek

Az a `parameters` területen adja meg a Logic Apps alkalmazást használ a központi telepítés bemeneti adatokat fogad az összes munkafolyamat-paraméterek. Központi telepítés szükség, paraméterdeklaráció és a paraméterértékek. Ezek a paraméterek a más munkafolyamat szakaszok használata előtt győződjön meg arról, hogy deklarálhatja ezekben a szakaszokban a paramétereket. 

Az általános struktúra paraméter definícióját a következő:  

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
| type | Igen | int, lebegőpontos, string, securestring, bool, tömb, JSON-objektumból, secureobject <p><p>**Megjegyzés:**: az összes jelszavak, kulcsok és titkos kulcsok, használja a `securestring` és `secureobject` típusokat, mert a `GET` művelet nem ad vissza a következő típusok. | A paraméter típusa |
| DefaultValue érték | Nem | Ugyanaz, mint `type` | Az alapértelmezett paraméter értéke, ha nincs érték megadva, ha a munkafolyamat példányosítja | 
| Storageaccount_accounttype | Nem | Ugyanaz, mint `type` | Egy tömb, amelyek a paramétert fogad el |  
| metaadatok | Nem | JSON-objektum | Bármely egyéb paraméter részletek, például a nevét vagy a logikai alkalmazást, vagy a Visual Studio vagy egyéb eszközök által használt tervezési idejű adatok olvasható leírását |  
||||

## <a name="triggers-and-actions"></a>Triggerek és műveletek  

Egy munkafolyamat-definícióban a `triggers` és `actions` szakaszok határozza meg a hívásokat, amely a munkafolyamat végrehajtása során kerül sor. Szintaxis és ezek a szakaszok további információ: [munkafolyamat eseményindítók és műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Kimenetek 

Az a `outputs` szakaszban, a munkafolyamat visszatérhet, amikor befejeződött az adatok meghatározásához futtatása. Futni, hanem minden egyes érték vagy egy adott állapotú követéséhez adja meg például, hogy a munkafolyamat kimeneti adott adatait jeleníti meg. 

> [!NOTE]
> Amikor az válaszol a beérkező kéréseket egy szolgáltatás REST API-n, ne használjon `outputs`. Ehelyett használja a `Response` művelet típusa. További információkért lásd: [munkafolyamat eseményindítók és műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md).

Itt található általános szerkezetének megadása egy kimeneti definíciója: 

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
| <*a kulcs-neve*> | Igen | Karakterlánc | A kulcs nevét a kimeneti oldal számára ad vissza értéket |  
| type | Igen | egész, lebegőpontos, string, securestring, bool, tömb, JSON-objektum | A kimeneti visszatérési érték típusa | 
| érték | Igen | Ugyanaz, mint `type` | A kimeneti visszatérési érték |  
||||| 

Ahhoz, hogy a kimenet egy munkafolyamat futtatásához, tekintse át a logikai alkalmazás futtatási előzményei és az Azure-portálon részletes adatait, vagy használja a [munkafolyamat REST API](https://docs.microsoft.com/rest/api/logic/workflows). Is átadhatja kimeneti a külső rendszerekkel, például a Power BI, hogy az irányítópultok hozhatók létre. 

<a name="expressions"></a>

## <a name="expressions"></a>Kifejezések

A JSON-ban akkor literálértékek létező tervezéskor, például:

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

Értékek, amelyek nem léteznek, amíg a futási idő is lehet. Ezeket az értékeket képviselő, használhatja a *kifejezések*, amely értékeli ki a rendszer futási időben. Egy kifejezés, amely tartalmazhat egy vagy több olyan feladatütemezési [funkciók](#functions), [operátorok](#operators), változókat, explicit értékek vagy konstans. A munkafolyamat-definícióban kifejezés bárhol használhatja JSON karakterlánc értékét a kifejezés a következő-bejelentkezési illesztésével (@). Kiértékel egy kifejezést, egy JSON-érték jelölő, ha a kifejezés törzsében eltávolításával kivonjuk a @ karakter, és mindig egy másik JSON-értéket eredményez. 

Például a korábban meghatározott `customerName` tulajdonság, kaphat a tulajdonság értékének használatával a [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kifejezésben funkciót, és rendelje hozzá ezt az értéket a `accountName` tulajdonság:

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

*Karakterlánc-köztes* is lehetővé teszi több kifejezések által becsomagolt karakterláncokon belül használja a @ karakter és a kapcsos zárójelek ({}). Ez a szintaxis:

```json
@{ "<expression1>", "<expression2>" }
```

Az eredménye mindig egy karakterlánc, így ez a funkció hasonló a `concat()` működnek, például: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Ha egy karakterlánckonstanssal kezdetű a @ karakter, előtag a @ karakter egy másik @ karakter helyettesítő karakterek: @@

Ezek a példák azt szemléltetik, hogyan kifejezések kiértékelése:

| JSON-érték | Eredmény |
|------------|--------| 
| "Sophia Owen" | Térjen vissza a következő karaktereket: "Sophia Owen" |
| "[1] array" | Térjen vissza a következő karaktereket: "[1] array" |
| "\@\@" | Ezek a karakterek vissza egy karakterből álló karakterlánc: "\@" |   
| " \@" | Ezek a karakterek két karakter karakterláncként vissza: ' \@' |
|||

Az ezekben a példákban tegyük fel, hogy a "myBirthMonth" egyenlő "Január" és "myAge" akkora 42 megadása:  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

A példákból látható, hogyan az alábbi kifejezések kiértékelése:

| JSON-kifejezés | Eredmény |
|-----------------|--------| 
| "@parameters("myBirthMonth")" | Vissza a karakterláncot: "Január" |  
| "@{parameters('myBirthMonth')}" | Vissza a karakterláncot: "Január" |  
| "@parameters("myAge")" | Térjen vissza Ez a szám: 42 |  
| "@{parameters('myAge')}" | Térjen vissza Ez a szám karakterláncként: "42" |  
| "A kora @{parameters('myAge')}" | Vissza a karakterláncot: "saját kor értéke 42" |  
| "@concat(" Saját kor ", string(parameters('myAge')))" | Vissza a karakterláncot: "saját kor értéke 42" |  
| "A kora @@ {parameters('myAge')}" | Ez a karakterlánc, amely tartalmazza a kifejezés vissza: "saját kora @{parameters('myAge')}" | 
||| 

Használata közben vizuálisan a Logic Apps-tervezőben, például a Kifejezésszerkesztő keresztül kifejezések hozhat létre: 

![Logic Apps Designer > Kifejezésszerkesztő](./media/logic-apps-workflow-definition-language/expression-builder.png)

Amikor elkészült, a kifejezés jelenik meg a megfelelő tulajdonságot a munkafolyamat-definícióban, például a `searchQuery` tulajdonság itt:

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

A [kifejezések](#expressions) és [funkciók](#functions), operátorok folyamatban speciális feladatokat, például a hivatkozási tulajdonság vagy értékét a tömbben. 

| Operátor | Tevékenység | 
|----------|------|
| ' | Egy szöveges karakterlánc bemenetként, vagy a kifejezések és a funkciók használatához burkolása csak a karakterlánc idézőjelek között, például `'<myString>'`. Ne használjon dupla idézőjelek (""), amely ütközik a JSON formázás körül egy teljes kifejezést. Példa: <p>**Igen**: length('Hello') </br>**Nem**: length("Hello") <p>Ha tömb, vagy számokat adja meg, nincs szükség alkalmazásburkoló absztrakt. Példa: <p>**Igen**: hossza ([1, 2, 3]) </br>**Nem**: hossza ("[1, 2, 3]") | 
| [] | Egy tömbben egy adott helyen (index) értékre hivatkoznak, használja a szögletes zárójelek között. Ha például a második elem beolvasása tömbben: <p>`myArray[1]` | 
| . | Egy tulajdonság az objektum hivatkozik, a pont operátort használja. Ahhoz például, hogy az beszerzése a `name` tulajdonsága egy `customer` JSON-objektum: <p>`"@parameters('customer').name"` | 
| ? | Null objektum futásidejű hiba nélkül hivatkozhat, használja a kérdőjel operátort. Például egy null kimenetek kezelésére, használhatja a kifejezés: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Functions

Egyes kifejezések értékeik beolvasása sikertelen lehet, hogy még nem léteznek futtatásához logikai alkalmazás indulásakor futásidejű műveleteket. Ezek az értékek kifejezések dolgozom, illetve hivatkoznak, használhatja [ *funkciók*](../logic-apps/workflow-definition-language-functions-reference.md). Például használhatja matematikai függvények számítások, például a [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) függvénynek, amely az egész számnak vagy úszó összegét adja vissza. Minden függvény kapcsolatos részletes információkért tekintse meg a [szedett áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).
Vagy folytathatja az funkciók és az általános célú.

Csupán néhány példa feladatokat hajthat végre a funkciók a következők: 

| Tevékenység | Függvény szintaxis | Eredmény | 
| ---- | --------------- | -------------- | 
| Csupa kisbetűssé formátumban adja vissza. | toLower ("<*szöveg*>") <p>Például: toLower('Hello') | "Hello"szövegrészt. | 
| A globálisan egyedi azonosítóját (GUID) adja vissza. | GUID() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

Ez a példa bemutatja, hogyan férhetnek a értéket a `customerName` paraméter, és rendelje hozzá, hogy egy érték a `accountName` tulajdonság használatával a [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) függvény a kifejezésben:

```json
"accountName": "@parameters('customerName')"
```

Itt látható általános módokon használható funkciók kifejezésében:

| Tevékenység | A kifejezésben függvény szintaxis | 
| ---- | -------------------------------- | 
| Hajtsa végre a munkahelyi cikket úgy, hogy ez az elem a következő függvényt. | "@<*függvénynév*> (<*elem*>)" | 
| 1. Beolvasása a *parameterName*tartozó értéket használva a beágyazott `parameters()` függvény. </br>2. Ennek eredményeként feladatok végrehajtására úgy, hogy ezt az értéket *függvénynév*. | "@<*függvénynév*> (paraméter (" <*parameterName*> "))" | 
| 1. Az eredményt kapja a beágyazott belső függvény *függvénynév*. </br>2. Az eredményt adnak át a külső függvény *functionName2*. | "@<*functionName2*> (<*függvénynév*> (<*elem*>))" | 
| 1. Az eredmény *függvénynév*. </br>2. Fényében, hogy a tulajdonság az objektum eredménye *propertyName*, a tulajdonság értékének beolvasása. | "@<*függvénynév*>(<*item*>). <*propertyName*>" | 
||| 

Például a `concat()` függvény a két vagy több paraméter karakterlánc-értékek is igénybe vehet. Ez a függvény egy karakterlánc ezek karakterláncok ezeket. Ön továbbíthatja a karakterlánc-literálnak, például "Sophia" és "Owen", hogy a kombinált karakterlánc, a "SophiaOwen" beolvasása:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Vagy az paraméterek kérheti le a karakterlánc-értékek. Ez a példa a `parameters()` függvény minden egyes `concat()` paraméter és a `firstName` és `lastName` paraméterek. Majd adja meg az eredményül kapott karakterláncok a `concat()` függvényt, hogy a kombinált karakterlánc, például "SophiaOwen" elérhetővé:

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Mindkét módszer esetén mindkét példák rendelje hozzá az eredményt a `customerName` tulajdonság. 

Minden függvény kapcsolatos részletes információkért tekintse meg a [szedett áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).
Vagy folytathatja az általános céljuk funkciók.

<a name="string-functions"></a>

### <a name="string-functions"></a>Karakterlánc

Karakterláncok dolgozni, használhatja a karakterlánc-funkciók és is néhány [gyűjtemény funkciók](#collection-functions). Karakterlánc csak olyan karakterláncok használhatók. 

| Karakterlánc függvény | Tevékenység | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Két vagy több karakterlánc kombinálhatja, és térjen vissza a kombinált karakterlánc. | 
| [megadott módon végződő](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Ellenőrizze, hogy egy karakterláncot végződik-e a megadott karakterláncrészlet. | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | A globálisan egyedi azonosítóját (GUID) készítése karakterláncként. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | A kezdő pozíciójának karakterláncrész visszaadása. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | A záró pozíciójának megadása egy substring visszaadása. | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Cserélje le a megadott karakterláncot egy substring, és térjen vissza a frissített karakterlánc. | 
| [felosztás](../logic-apps/workflow-definition-language-functions-reference.md#split) | Térjen vissza olyan tömb, amely egy karakterlánc karakterből áll, és az egyes karakterek elválasztja a megadott elválasztó karakterrel. | 
| [startswith elemnek](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Ellenőrizze, hogy a karakterlánc egy adott substring indításakor. | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Egy karakterlánc, a megadott pozíciónál kezdve karaktert adja vissza. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Csupa kisbetűssé formátumban adja vissza. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Nagybetűk formátumban adja vissza. | 
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Kezdő és záró szóközök eltávolítása a karakterláncot, és térjen vissza a frissített karakterlánc. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Gyűjtemény-funkciók

Gyűjtemények, általában tömbök, karakterláncok és egyes esetekben szótárak használatához, a gyűjtemény funkciókat használhatja. 

| Gyűjtemény függvény | Tevékenység | 
| ------------------- | ---- | 
| [tartalmazza](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Ellenőrizze, hogy egy gyűjtemény van-e egy adott cikk. |
| [üres](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Ellenőrizze, hogy egy gyűjtemény üres. | 
| [első](../logic-apps/workflow-definition-language-functions-reference.md#first) | Térjen vissza az első elem a gyűjteményből. | 
| [metszetének](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Térjen vissza olyan gyűjtemény, amelyikhez *csak* a közös elemek a megadott gyűjtemények között. | 
| [csatlakozás](../logic-apps/workflow-definition-language-functions-reference.md#join) | Adja vissza, amely rendelkezik *összes* a tömbben, elemek a megadott karakter elválasztva. | 
| [utolsó](../logic-apps/workflow-definition-language-functions-reference.md#last) | Egy gyűjtemény utolsó elemének visszaadása. | 
| [Hossza](../logic-apps/workflow-definition-language-functions-reference.md#length) | Egy karakterlánc vagy tömb elemek számának visszaadása. | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Elem eltávolítása a gyűjtemény elejére, és térjen vissza *összes többi* elemeket. | 
| [hajtsa végre a megfelelő](../logic-apps/workflow-definition-language-functions-reference.md#take) | Az első gyűjtemény elemek visszaadása. | 
| [a UNION](../logic-apps/workflow-definition-language-functions-reference.md#union) | Térjen vissza olyan gyűjtemény, amelyikhez *összes* az elemek a megadott ügyfélgyűjteményekből. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Összehasonlítás funkciók

Feltételek dolgozni, értékek és a kifejezés eredménye összehasonlítani vagy programot a különféle értékelését, összehasonlítás funkcióhoz is használhat. Kapcsolatos minden funkció teljes referenciáért lásd: a [szedett áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Összehasonlító függvény | Tevékenység | 
| ------------------- | ---- | 
| [És](../logic-apps/workflow-definition-language-functions-reference.md#and) | Ellenőrzése, hogy összes kifejezés igaz. | 
| [egyenlő](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Ellenőrzése, hogy mindkét értéket egyenértékű. | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Ellenőrizze, hogy az első érték nagyobb, mint a második érték. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Ellenőrizze, hogy az első érték kisebb, mint a második érték. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Ellenőrizze, hogy egy kifejezés igaz vagy hamis. Az eredmény alapján, a megadott érték visszaadása. | 
| [kevesebb](../logic-apps/workflow-definition-language-functions-reference.md#less) | Ellenőrizze, hogy van-e az első érték kisebb, mint a második érték. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Ellenőrizze, hogy az első érték nagyobb, mint a második érték. | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Ellenőrizze, hogy egy kifejezés értéke false. | 
| [vagy](../logic-apps/workflow-definition-language-functions-reference.md#or) | Ellenőrizze, hogy legalább egy kifejezés igaz-e. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Átalakítás funkciók

Egy érték típus vagy formátum megváltoztatásához átalakítás funkcióhoz is használhat. Például módosítható egy érték logikai érték egész számra. A Logic Apps kezelésének tartalomtípusok átalakítás során, lásd: [tartalomtípusok kezelni](../logic-apps/logic-apps-content-type.md). Kapcsolatos minden funkció teljes referenciáért lásd: a [szedett áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Dátumkonverziós függvényben | Tevékenység | 
| ------------------- | ---- | 
| [A tömb](../logic-apps/workflow-definition-language-functions-reference.md#array) | Térjen vissza a megadott egyetlen bemeneti tömb. Több bemenetek, lásd: [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | A base64-kódolású verzió karakterlánc visszaadása. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | A bináris verzió base64 kódolású karakterlánc visszaadása. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Térjen vissza a karakterlánc-verzió a base64 kódolású karakterlánc. | 
| [Bináris](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Térjen vissza a bemeneti érték bináris verzióját. | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Térjen vissza a bemeneti érték logikai verzióját. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | A több bemenetei olyan tömböt adjon vissza. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Az adat-URI azonosító egy bemeneti érték visszaadása. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Egy adat-URI azonosító bináris verzióját adja vissza. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Térjen vissza a karakterlánc verziója egy adat-URI azonosító. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Térjen vissza a karakterlánc-verzió a base64 kódolású karakterlánc. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Egy adat-URI azonosító bináris verzióját adja vissza. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Visszatérés egy karakterlánc, hogy cserél escape-karakter dekódolt verzióival. | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Adja vissza, amely kiváltja a URL-cím nem biztonságos karakterek escape-karakterrel. | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Térjen vissza lebegőpontos számnak egy bemeneti érték. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Az egész verzió karakterlánc visszaadása. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | A JavaScript Object Notation (JSON) típusú érték, vagy egy karakterlánc- vagy XML-objektumot vissza. | 
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Térjen vissza a bemeneti érték karakterlánc verzióját. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Az URI-kódolt verzió egy bemeneti érték a visszatérési URL-cím nem biztonságos karakterek helyett a escape-karakter. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | A bináris verzió az URI-kódolású karakterlánc visszaadása. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Térjen vissza a karakterlánc-verzió az URI-kódolású karakterlánc. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Az XML-verzió karakterlánc visszaadása. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Matematikai függvények

Egész számok és úszó dolgozik, ezeket a matematikai funkciókat is használhat. Kapcsolatos minden funkció teljes referenciáért lásd: a [szedett áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Matematikai függvény | Tevékenység | 
| ------------- | ---- | 
| [Hozzáadása](../logic-apps/workflow-definition-language-functions-reference.md#add) | Az eredményt adjanak hozzá két számot. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Két szám osztásával kapott eredményt. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Számok vagy tömb a legmagasabb érték visszaadása. | 
| [Perc](../logic-apps/workflow-definition-language-functions-reference.md#min) | A számok vagy tömb a legalacsonyabb értéket adnak vissza. | 
| [MOD](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Térjen vissza a többi két szám osztásával kapott. | 
| [MUL számú](../logic-apps/workflow-definition-language-functions-reference.md#mul) | A termék visszaadásának két szám szorzásával. | 
| [VÉL](../logic-apps/workflow-definition-language-functions-reference.md#rand) | A megadott tartományból egy véletlenszerű egész számot adjon vissza. | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Térjen vissza olyan egész tömb, amely elindítja a megadott egész szám. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Vissza az eredmény az első szám a második szám különbsége. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Dátum és időpont függvényeinek

Dátum és idő dolgozni, a dátum és időpont függvényeinek is használhat.
Kapcsolatos minden funkció teljes referenciáért lásd: a [szedett áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Dátum vagy idő függvény | Tevékenység | 
| --------------------- | ---- | 
| [napokHozzaadasa](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adja hozzá a napok számát időbélyeg. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adja hozzá egy Timestamp típusú órák száma. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adja hozzá a percek száma időbélyeg. | 
| [masodpercekHozzaadasa](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adja hozzá a másodpercek számát időbélyeg. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Időbélyeg időegységek számát adja hozzá. Lásd még: [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Időbélyeg az idő egyezményes világidő (UTC) konvertálja a célként megadott időzóna. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | A célként megadott időzóna a forrás időzóna átalakítása időbélyeg. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konvertálása időbélyeg a forrás időzóna az idő egyezményes világidő (UTC). | 
| [DayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Az időbélyeg hónap összetevőjének napjának visszaadása. | 
| [DayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | A hét összetevő napjának visszaadása a időbélyeg. | 
| [DayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Az időbélyeg év összetevőjének napjának visszaadása. | 
| [FormatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | A dátum visszaadásának időbélyeg. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Az aktuális időbélyeg és a megadott mértékegységét adja vissza. Lásd még: [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | A megadott időegységek mínusz aktuális időbélyeg visszaadása. Lásd még: [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Időbélyeg nap kezdetének visszaadása. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Az időbélyeg óra kezdetének visszaadása. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Az adott hónapban az időbélyeg kezdetének visszaadása. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Az időbélyeg mértékegységét számos kivonandó időnél. Lásd még: [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [Ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Térjen vissza a `ticks` megadott időbélyeg-tulajdonság értéke. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Térjen vissza az aktuális időbélyeg karakterláncként. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Munkafolyamat-funkciók

A munkafolyamat funkciók segítségével:

* Ezzel adatokat kapjon a munkafolyamat-példányok futási időben. 
* A logic apps példányának használt bemenetek működik.
* A kimenetek eseményindítók és műveletek hivatkozik.

Például a használatával hivatkozik a kimenetek egy műveletet, és az adatokat későbbi művelettel. Kapcsolatos minden funkció teljes referenciáért lásd: a [szedett áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Munkafolyamat-funkció | Tevékenység | 
| ----------------- | ---- | 
| [A művelet](../logic-apps/workflow-definition-language-functions-reference.md#action) | Az aktuális műveleti kimenet futásidejű, illetve értékeket visszaadásának más JSON-név és érték párokat. Lásd még: [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Egy műveletet vissza `body` kimeneti futásidőben. Lásd még: [törzs](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Egy műveleti kimenet futásidőben visszaadása. Lásd: [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [műveletek](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Egy műveleti kimenet futásidejű, illetve értékeket visszaadásának más JSON-név és érték párokat. Lásd még: [művelet](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [Törzs](#body) | Egy műveletet vissza `body` kimeneti futásidőben. Lásd még: [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Hozzon létre egy tömb az értékeket, amelyek megfelelnek a kulcs nevét *űrlapadat* vagy *űrlap-kódolású* művelet kimenetének létrehozása. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Egyetlen érték, amely megfelel a kulcs nevét, egy műveletben visszaadnia *űrlapadat* vagy *űrlap-kódolású kimeneti*. | 
| [Elem](../logic-apps/workflow-definition-language-functions-reference.md#item) | Ismétlődő művelet keresztül egy tömb, belül vissza az aktuális elem a tömbben a művelet aktuális iteráció során. | 
| [Elemek](../logic-apps/workflow-definition-language-functions-reference.md#items) | Belül egy tartozó minden vagy tegye-ig-hurkot állítana elő, térjen vissza az aktuális elem a megadott hurok a.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | A "visszahívási URL-címe", amely behívja egy eseményindító vagy a művelet vissza. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | A szervezet egy adott részére, amely több részből áll egy műveleti kimenet visszaadása. | 
| [paraméterek](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | A logic app-definíciót egy paraméter ismertetett visszaadása. | 
| [Eseményindító](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Térjen vissza a trigger elem kimeneti futásidőben, vagy más JSON-név és érték párokat. Lásd még: [triggerOutputs](#triggerOutputs) és [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Térjen vissza a trigger elem `body` kimeneti futásidőben. Lásd: [eseményindító](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | A kulcs nevét megfelelő egyetlen értéket visszaadnia *űrlapadat* vagy *űrlap-kódolású* kimenetek indítható el. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | A szervezet egy adott részére trigger elem több részből álló kimeneti adja vissza. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Hozzon létre egy tömb, amelynek megegyeznek a kulcs nevét *űrlapadat* vagy *űrlap-kódolású* kimenetek indítható el. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Egy eseményindító kimeneti futásidejű, illetve értékeket visszaadásának más JSON-név és érték párokat. Lásd: [eseményindító](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [változók](../logic-apps/workflow-definition-language-functions-reference.md#variables) | A megadott változó értékének visszaadása. | 
| [munkafolyamat](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Térjen vissza a munkafolyamat magát a részleteit futásidőben. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>URI-elemzési funkciók

Használata az egységes erőforrás-azonosítók (URI), és különböző tulajdonságértékek lekérése az URI-k, ezek URI elemzési funkciókat is használhat. Kapcsolatos minden funkció teljes referenciáért lásd: a [szedett áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| URI-elemzési függvény | Tevékenység | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Térjen vissza a `host` egységes erőforrás-azonosítója (URI) értékét. | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Térjen vissza a `path` egységes erőforrás-azonosítója (URI) értékét. | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Térjen vissza a `path` és `query` egységes erőforrás-azonosító (URI) értékeket. | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Térjen vissza a `port` egységes erőforrás-azonosítója (URI) értékét. | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Térjen vissza a `query` egységes erőforrás-azonosítója (URI) értékét. | 
| [UriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Térjen vissza a `scheme` egységes erőforrás-azonosítója (URI) értékét. | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>JSON- és XML-funkciók

JSON-objektumok és az XML-csomópontnak dolgozik, használhatja a függvényeket. Kapcsolatos minden funkció teljes referenciáért lásd: a [szedett áttekintésével foglalkozó cikkben](../logic-apps/workflow-definition-language-functions-reference.md).

| Adatkezelési függvényt | Tevékenység | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Tulajdonság és az érték vagy a név-érték pár hozzáadása egy JSON-objektum, és térjen vissza a frissített objektum. | 
| [Egyesítés](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Az első nem üres érték visszaadása egy vagy több paramétert. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Távolítsa el a tulajdonság egy JSON-objektumból, majd térjen vissza a frissített objektum. | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | A JSON-objektum tulajdonság értékét, és térjen vissza a frissített objektum. | 
| [XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Ellenőrizze az XML-kódot a csomópontok vagy egyező (XML Path Language) XPath kifejezés, és térjen vissza a megfelelő csomópontok vagy értékeket. | 
||| 

## <a name="next-steps"></a>További lépések

* További tudnivalók [Munkafolyamatdefiníciós nyelve műveletek és eseményindítók](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Programozott módon létrehozása és a logikai alkalmazások kezelését a [munkafolyamat REST API-n](https://docs.microsoft.com/rest/api/logic/workflows)
