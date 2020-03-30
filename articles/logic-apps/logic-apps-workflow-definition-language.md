---
title: Munkafolyamat-definíció – nyelvi séma hivatkozása
description: Útmutató a JSON-sémához és a munkafolyamat-definíciós nyelv szintaxisához, amely az Azure Logic Apps munkafolyamatait ismerteti
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283861"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Séma-útmutató az Azure Logic Apps munkafolyamat-definíciós nyelvéhez

Amikor létrehoz egy logikai alkalmazást az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)alkalmazásban, a logikai alkalmazás rendelkezik egy mögöttes munkafolyamat-definícióval, amely leírja a logikai alkalmazásban futó tényleges logikát. Ez a munkafolyamat-definíció [JSON-t](https://www.json.org/) használ, és a Munkafolyamat-definíciónyelv sémája által ellenőrzött struktúrát követ. Ez a hivatkozás áttekintést nyújt erről a struktúráról, valamint arról, hogy a séma hogyan definiálja az attribútumokat a munkafolyamat-definícióban.

## <a name="workflow-definition-structure"></a>Munkafolyamat-definíciós struktúra

A munkafolyamat-definíció mindig tartalmaz egy eseményindítót a logikai alkalmazás példányosításához, valamint egy vagy több műveletet, amelyek az eseményindító tüzek után futnak.

A munkafolyamat-definíció magas szintű struktúrája:

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

| Attribútum | Kötelező | Leírás |
|-----------|----------|-------------|
| `definition` | Igen | A munkafolyamat-definíció kezdő eleme |
| `$schema` | Csak akkor, ha külsőleg hivatkozik egy munkafolyamat-definícióra | A JSON-sémafájl helye, amely a munkafolyamat-definíciónyelv verzióját írja le, amely itt található: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Nem | A munkafolyamat-futásidőben végrehajtandó egy vagy több művelet definíciói. További információ: [Eseményindítók és műveletek](#triggers-actions). <p><p>Maximális intézkedések: 250 |
| `contentVersion` | Nem | A munkafolyamat-definíció verziószáma, amely alapértelmezés szerint "1.0.0.0" . A munkafolyamat üzembe helyezésekor a helyes definíció azonosításához és megerősítéséhez adjon meg egy használni kívánt értéket. |
| `outputs` | Nem | A munkafolyamat-futtatásból visszaadandó kimenetek definíciói. További információ: [Outputs](#outputs). <p><p>Maximális kimenet: 10 |
| `parameters` | Nem | Egy vagy több paraméter definíciói, amelyek átadják a logikai alkalmazás futásidejű értékeit. További információ: [Paraméterek](#parameters). <p><p>Maximális paraméterek: 50 |
| `staticResults` | Nem | A műveletek által álkimenetként visszaadott egy vagy több statikus eredmény definíciói, ha a statikus eredmények engedélyezve vannak ezeken a műveleteken. Az attribútum minden `runtimeConfiguration.staticResult.name` műveletdefinícióban a megfelelő `staticResults`definícióra hivatkozik. További információ: [Statikus eredmények](#static-results). |
| `triggers` | Nem | Egy vagy több eseményindító definíciója, amely a munkafolyamatot példányosítja. Több eseményindítót is definiálhat, de csak a munkafolyamat-definíciós nyelvvel, vizuálisan nem a Logic Apps Designeren keresztül. További információ: [Eseményindítók és műveletek](#triggers-actions). <p><p>Maximális kiváltó okok: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Eseményindítók és műveletek

A munkafolyamat-definícióban `triggers` `actions` a szakaszok határozzák meg a munkafolyamat végrehajtása során bekövetkező hívásokat. A szintaxisról és a szakaszokról további információt a [Munkafolyamat-eseményindítók és -műveletek című témakörben talál.](../logic-apps/logic-apps-workflow-actions-triggers.md)

<a name="parameters"></a>

## <a name="parameters"></a>Paraméterek

A központi telepítési életciklus általában különböző környezetekben fejlesztési, tesztelési, átmeneti és éles környezetben. Logikai alkalmazások különböző környezetekben történő üzembe helyezésekor valószínűleg különböző értékeket szeretne használni, például a kapcsolati karakterláncokat a központi telepítési igények alapján. Vagy előfordulhat, hogy olyan értékeket szeretne újra felhasználni a logikai alkalmazásban hardcoding nélkül, vagy gyakran változnak. A munkafolyamat-definíció `parameters` szakaszában megadhatja vagy szerkesztheti a logikai alkalmazás által futásidőben használt értékek paramétereit. Először meg kell határoznia ezeket a paramétereket, mielőtt ezeket a paramétereket a munkafolyamat-definíció más részeire hivatkozna.

Itt van a paraméterdefiníció általános szerkezete:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Attribútum | Kötelező | Típus | Leírás |
|-----------|----------|------|-------------|
| <*paraméter-név*> | Igen | Sztring | A definiálni kívánt paraméter neve |
| <*paraméter-típus*> | Igen | int, float, string, bool, tömb, tárgy, securestring, secureobject <p><p>**Megjegyzés:** Minden jelszó, kulcs és titkos `securestring` kulcs `secureobject` esetén `GET` használja a vagy típusokat, mert a művelet nem adja vissza ezeket a típusokat. A paraméterek védelméről további információt a [Biztonsági javaslatok a művelethez és a bemeneti paraméterekhez című témakörben talál.](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | A paraméter típusa |
| <*alapértelmezett paraméter-érték*> | Igen | Ugyanaz, mint`type` | Az alapértelmezett paraméterérték, amelyet akkor érdemes megadni, ha nincs megadva érték a munkafolyamat példányos kora. Az `defaultValue` attribútum szükséges, hogy a Logic App Designer helyesen tudja megjeleníteni a paramétert, de megadhat egy üres értéket. |
| <*tömb-és engedélyezett paraméter-értékek*> | Nem | Tömb | Tömb olyan értékekkel, amelyeket a paraméter el tud fogadni |
| <*paraméter-leírás*> | Nem | JSON-objektum | Bármely más paraméterrészlet, például a paraméter leírása |
||||

Ezután hozzon létre egy [Azure Resource Manager-sablont](../azure-resource-manager/templates/overview.md) a munkafolyamat-definícióhoz, határozza meg azokat a sablonparamétereket, amelyek elfogadják a telepítéssorán kívánt értékeket, cserélje le a kódolható értékeket a sablon- vagy munkafolyamat-definíciós paraméterekre való hivatkozásokkal, és tárolja a központi telepítéssorán használandó értékeket egy külön [paraméterfájlban.](../azure-resource-manager/templates/parameter-files.md) Így ezeket az értékeket könnyebben módosíthatja a paraméterfájlon keresztül anélkül, hogy frissítenie kellene és újra kellene telepítenie a logikai alkalmazást. A bizalmas vagy biztonságos, például felhasználónevek, jelszavak és titkos kulcsok, tárolhatja ezeket az értékeket az Azure Key Vaultban, és a paraméterfájl lekérni ezeket az értékeket a key vault. További információ és példák a paraméterek sablon- és munkafolyamat-definíciós szinteken történő [meghatározásáról: Áttekintés: A logikai alkalmazások üzembe helyezésének automatizálása az Azure Resource Manager-sablonokkal című témakörben.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

<a name="static-results"></a>

## <a name="static-results"></a>Statikus eredmények

Az `staticResults` attribútumban adja meg a `outputs` művelet `status` mock, és hogy a művelet visszatér, ha a művelet statikus eredmény beállítása be van kapcsolva. A művelet definíciójában az `runtimeConfiguration.staticResult.name` attribútum hivatkozik a statikus `staticResults`eredménydefiníció nevére. Megtudhatja, hogyan [tesztelheti a logikai alkalmazásokat a szimulált adatokkal statikus eredmények beállításával.](../logic-apps/test-logic-apps-mock-data-static-results.md)

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

| Attribútum | Kötelező | Típus | Leírás |
|-----------|----------|------|-------------|
| <*statikus-eredmény-definíció-név*> | Igen | Sztring | Egy statikus eredménydefiníció neve, amelyre egy `runtimeConfiguration.staticResult` műveletdefiníció egy objektumon keresztül hivatkozhat. További információt a [Futásidejű konfigurációs beállítások című](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options)témakörben talál. <p>Bármilyen egyedi nevet használhat. Alapértelmezés szerint ez az egyedi név egy számmal van hozzáfűzve, amely szükség szerint növekszik. |
| <*kimeneti attribútumok és értékek visszaadva*> | Igen | Változó | Ezeknek az attribútumoknak a követelményei a különböző feltételektől függően változnak. Ha például `status` az, `Succeeded` `outputs` az attribútum tartalmazza a művelet által álkimenetként visszaadott attribútumokat és értékeket. Ha `status` az `Failed`, `outputs` az attribútum `errors` tartalmazza az attribútumot, amely egy `message` vagy több hibainformációt tartalmazó hibaobjektummal rendelkező tömb. |
| <*fejlécértékek*> | Nem | JSON | A művelet által visszaadott fejlécértékek |
| <*állapotkód visszaadva*> | Igen | Sztring | A művelet által visszaadott állapotkód |
| <*művelet-állapot*> | Igen | Sztring | A művelet állapota például `Succeeded` vagy`Failed` |
|||||

Ebben a HTTP-műveletdefinícióban `runtimeConfiguration.staticResult.name` például az `HTTP0` attribútumhivatkozások az `staticResults` attribútumon belül találhatók, ahol a művelet mock kimenetei definiálva vannak. Az `runtimeConfiguration.staticResult.staticResultOptions` attribútum azt adja meg, `Enabled` hogy a statikus eredménybeállítás a HTTP-műveletben van.

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

A HTTP-művelet a belső `HTTP0` `staticResults`definíciókimeneteit adja vissza. Ebben a példában az állapotkód esetében `OK`a mock kimenet . A fejlécértékek esetében a `"Content-Type": "application/JSON"`mock kimenet . A művelet állapotában a mock `Succeeded`kimenet .

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

A JSON segítségével a tervezés időpontjában létező literális értékek lehetnek, például:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Olyan értékeket is megkaphat, amelyek futási időig nem léteznek. Ezeknek az értékeknek a ábrázolására használhat *olyan kifejezéseket,* amelyek et futásidőben kiértékeli a program. A kifejezés olyan sorozat, amely egy vagy több [függvényt](#functions), [operátorokat,](#operators)változókat, explicit értékeket vagy állandókat tartalmazhat. A munkafolyamat-definícióban a JSON-karakterlánc-érték bármely pontján használhat egy kifejezést úgy, hogy a kifejezést az at-sign ( .\@ Egy JSON-értéket képviselő kifejezés kiértékelésekor a kifejezéstörzs kinyerhető \@ a karakter eltávolításával, és mindig egy másik JSON-értéket eredményez.

A korábban definiált `customerName` tulajdonság esetében például a tulajdonság értékét egy kifejezés [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) függvényének `accountName` használatával szerezheti be, és hozzárendelheti a tulajdonsághoz:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*A karakterlánc-interpoláció* lehetővé teszi több kifejezés használatát \@ a karakterláncokon belül, amelyeket a karakter és a kapcsos zárójelek ({}). Itt van a szintaxis:

```json
@{ "<expression1>", "<expression2>" }
```

Az eredmény mindig egy karakterlánc, így `concat()` ez a képesség hasonló a funkcióhoz, például: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Ha van egy konstans \@ karakterlánc, amely \@ a \@ karakterrel kezdődik, előtagolja a karaktert egy másik karakterrel escape karakterként:\@\@

Ezek a példák bemutatják a kifejezések kiértékelésének módját:

| JSON-érték | Eredmény |
|------------|--------|
| "Sophia Owen" | Vissza ezeket a karaktereket: "Sophia Owen" |
| "tömb[1]" | Adja vissza a következő karaktereket: 'tömb[1]" |
| "\@\@" | Ezeket a karaktereket egykarakteres\@karakterláncként adja vissza: ' |
| " \@" | Ezeket a karaktereket kétkarakteres \@karakterláncként adja vissza: ' |
|||

Ezeknél a példáknál tegyük fel, hogy a "myBirthMonth" egyenlő a "január" és a "myAge" egyenlő a 42-es számmal:

```json
"myBirthMonth": "January",
"myAge": 42
```

Ezek a példák bemutatják a következő kifejezések kiértékelésének módját:

| JSON-kifejezés | Eredmény |
|-----------------|--------|
| "\@paraméterek(''myBirthMonth')" | Adja vissza ezt a karakterláncot: "Január" |
| "\@{parameters('myBirthMonth')}" | Adja vissza ezt a karakterláncot: "Január" |
| "\@paraméterek('myAge')" | Adja vissza ezt a számot: 42 |
| "\@{parameters('myAge')}" | Ezt a számot adja vissza karakterláncként: "42" |
| "A korom \@{parameters('myAge')}" | Adja vissza ezt a karakterláncot: "Az én koromban 42" |
| "\@concat('Az én korom ", string(parameters('myAge)))" | Adja vissza ezt a karakterláncot: "Az én koromban 42" |
| "A korom \@ \@{parameters('myAge')}" | Adja vissza ezt a karakterláncot, amely \@a következő kifejezést tartalmazza: "Az én életkorom {parameters('myAge')}' |
|||

Ha vizuálisan dolgozik a Logic Apps Designeralkalmazásban, kifejezéseket hozhat létre a Kifejezésszerkesztőn keresztül, például:

![Logic Apps Designer > Expression builder](./media/logic-apps-workflow-definition-language/expression-builder.png)

Ha elkészült, a kifejezés megjelenik a megfelelő tulajdonsághoz a `searchQuery` munkafolyamat-definícióban, például az itt lévő tulajdonsághoz:

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

<a name="outputs"></a>

## <a name="outputs"></a>Kimenetek

A `outputs` szakaszban adja meg azokat az adatokat, amelyeket a munkafolyamat visszaadhat, amikor a futás befejeződött. Ha például egy adott állapotot vagy értéket szeretne nyomon követni az egyes futtatásokból, adja meg, hogy a munkafolyamat kimenete adja vissza ezeket az adatokat.

> [!NOTE]
> Amikor válaszol a szolgáltatás REST API-jából érkező `outputs`bejövő kérésekre, ne használja a használatát. Ehelyett használja `Response` a művelettípust. További információt a [Munkafolyamat-eseményindítók és -műveletek című témakörben talál.](../logic-apps/logic-apps-workflow-actions-triggers.md)

A kimeneti definíció általános struktúrája:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Attribútum | Kötelező | Típus | Leírás |
|-----------|----------|------|-------------|
| <*kulcsnév*> | Igen | Sztring | A kimeneti visszatérési érték kulcsneve |
| <*kulcs-típus*> | Igen | int, float, string, securestring, bool, tömb, JSON objektum | A kimeneti visszatérési érték típusa |
| <*kulcs-érték*> | Igen | Megegyezik <*kulcstípussal*> | A kimeneti visszatérési érték |
|||||

A kimenet lehívásához egy munkafolyamat-futtatásból tekintse át a logikai alkalmazás futtatási előzményeit és adatait az Azure Portalon, vagy használja a [Workflow REST API-t.](https://docs.microsoft.com/rest/api/logic/workflows) A kimenetet külső rendszereknek is átadhatja, például a Power BI-nak, hogy irányítópultokat hozhasson létre.

<a name="operators"></a>

## <a name="operators"></a>Operátorok

[Kifejezésekben](#expressions) és [függvényekben](#functions)az operátorok meghatározott feladatokat hajtanak végre, például egy tulajdonságra vagy egy tömb ben lévő értékre hivatkoznak.

| Művelet | Tevékenység |
|----------|------|
| ' | Ha egy karakterláncot konstansbevitelként vagy kifejezésként és függvényben szeretne `'<myString>'`használni, a karakterláncot csak aposztrófokkal csomagolja be, például . Ne használjon idézőjeleket (""), amelyek ütköznek a JSON-formázással a teljes kifejezés körül. Példa: <p>**Igen**: length('Hello') </br>**Nem**: hossz("Hello") <p>Tömbök vagy számok átírásakor nincs szükség tördelési írásjelekre. Példa: <p>**Igen:** hossz([1, 2, 3]) </br>**Nem**: hossz("[1, 2, 3]") |
| [] | Ha egy tömbben egy adott pozícióban (indexben) szeretne hivatkozni egy értékre, használjon szögletes zárójeleket. Például a tömb második elemének bekéséhez: <p>`myArray[1]` |
| . | Ha egy objektum egy tulajdonságára szeretne hivatkozni, használja a pontoperátort. Egy `name` `customer` JSON-objektum tulajdonságának lekérnie például: <p>`"@parameters('customer').name"` |
| ? | Ha egy objektum null tulajdonságaira szeretne hivatkozni futásidejű hiba nélkül, használja a kérdőjel operátort. Ha például egy eseményindító null kimeneteit szeretné kezelni, ezt a kifejezést használhatja: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

Egyes kifejezések olyan futásidejű műveletekből kapják meg az értékeiket, amelyek a munkafolyamat-definíció futtatásakor még nem létezhetnek. Ha ezeket az értékeket kifejezésekben szeretné hivatkozni vagy használni szeretné, használhatja a Munkafolyamat-definíciós nyelv által biztosított [*függvényeket.*](../logic-apps/workflow-definition-language-functions-reference.md)

## <a name="next-steps"></a>További lépések

* További információ a [munkafolyamat-definíció nyelvi műveleteiről és eseményindítóiról](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Ismerje meg a logikai alkalmazások programozott létrehozását és kezelését a [Workflow REST API-val](https://docs.microsoft.com/rest/api/logic/workflows)
