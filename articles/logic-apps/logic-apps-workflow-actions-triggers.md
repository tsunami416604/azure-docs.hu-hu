---
title: A munkafolyamat-definíciós nyelvben lévő trigger-és műveleti típusok referenciája – Azure Logic Apps
description: Útmutató az trigger-és Művelettípus-típusokhoz a munkafolyamat-definíciós nyelvben Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/19/2019
ms.openlocfilehash: df1b03d5fbb5b8ef8cda9407e4a595bc2de8ce54
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918962"
---
# <a name="reference-for-trigger-and-action-types-in-workflow-definition-language-for-azure-logic-apps"></a>Az aktiválási és a műveleti típusok referenciája a munkafolyamat-definíciós nyelvben Azure Logic Apps

Ez a hivatkozás azokat a általános típusokat ismerteti, amelyek a logikai alkalmazás alapjául szolgáló munkafolyamat-definícióban szereplő eseményindítók és műveletek azonosítására szolgálnak, amelyet a [munkafolyamat-definíciós nyelv](../logic-apps/logic-apps-workflow-definition-language.md)ismertet és érvényesít.
A logikai alkalmazásokban használható összekötő-eseményindítók és műveletek megtalálásához tekintse meg az [Összekötők áttekintés](https://docs.microsoft.com/connectors/)alatt található listát.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Triggerek – áttekintés

Minden munkafolyamat tartalmaz egy triggert, amely meghatározza a munkafolyamatot létrehozó és indító hívásokat. Az általános trigger-kategóriák a következők:

* Egy *lekérdezési* trigger, amely rendszeres időközönként ellenőrzi a szolgáltatás végpontját

* Egy Leküldéses eseményindító, amely előfizetést hoz létre egy végpont számára, és visszahívási *URL-címet* biztosít, hogy a végpont értesítse az eseményindítót, ha a megadott esemény bekövetkezik, vagy elérhető az információ. A trigger ezután megvárja a végpont válaszát a tüzelés előtt. 

Az eseményindítók ezekkel a legfelső szintű elemekkel rendelkeznek, bár egyesek nem kötelezőek:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*trigger – név*> | String | Az trigger neve | 
| <*trigger-type*> | String | Az trigger típusa (például "http" vagy "ApiConnection") | 
| <*trigger – bemenetek*> | JSON-objektum | Az trigger viselkedését meghatározó bemenetek | 
| <*időegység*> | String | Az az időegység, amely leírja, hogy az eseményindító milyen gyakran: "Second", "minute", "Hour", "Day", "Week", "Month" | 
| <*number-of-time-units*> | Integer | Egy érték, amely azt határozza meg, hogy az eseményindító milyen gyakran indul el a gyakoriság alapján, amely a megvárni kívánt időegységek száma <p>Itt láthatók a minimális és a maximális intervallumok: <p>Hónap 1-16 hónap </br>Nap 1-500 nap </br>Óra 1 – 12000 óra </br>Percenként 1 – 72000 perc </br>Második 1 – 9999999 másodperc<p>Ha például az intervallum 6, és a gyakoriság értéke "Month", az ismétlődés 6 havonta történik. | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*array-with-conditions*> | Array | Olyan tömb, amely egy vagy több [](#trigger-conditions) olyan feltételt tartalmaz, amely meghatározza, hogy futtatni kell-e a munkafolyamatot. Csak eseményindítók számára érhető el. | 
| <*runtime-config-options*> | JSON-objektum | Az aktiválási futtatókörnyezet viselkedését a tulajdonságok `runtimeConfiguration` beállításával módosíthatja. További információ: [futásidejű konfigurációs beállítások](#runtime-config-options). | 
| <*splitOn-expression*> | String | Egy tömböt visszaadó eseményindítók esetében megadhat egy olyan kifejezést, amely a tömb elemeit több munkafolyamat-példányba feldarabolja [vagy ](#split-on-debatch) lebontja feldolgozásra. | 
| <*művelet – beállítás*> | String | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információ: [üzemeltetési beállítások](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Trigger típusok listája

Minden trigger típushoz eltérő illesztőfelület és bemenet tartozik, amelyek meghatározzák az trigger viselkedését. 

### <a name="built-in-triggers"></a>Beépített eseményindítók

| Trigger típusa | Leírás | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Ellenőrzi vagy lekérdezi a végpontokat. A végpontnak egy "202" aszinkron minta vagy egy tömb visszaadása révén kell megfelelnie egy adott trigger-szerződésnek. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Létrehoz egy hívható végpontot a logikai alkalmazáshoz, de meghívja a megadott URL-címet a regisztrációhoz vagy a regisztráció megszüntetéséhez. |
| [**Megismétlődésének**](#recurrence-trigger) | Egy meghatározott ütemterv alapján következik be. Megadhatja az trigger kiégetésének jövőbeli dátumát és időpontját. A gyakoriság alapján a munkafolyamat futtatásának idejét és napját is megadhatja. | 
| [**Kérelem**](#request-trigger)  | Létrehoz egy hívható végpontot a logikai alkalmazáshoz, és "manuális" triggerként is ismert. Lásd például a munkafolyamatok [hívása, triggere vagy beágyazása http](../logic-apps/logic-apps-http-endpoint.md)-végpontokkal című témakört. | 
||| 

### <a name="managed-api-triggers"></a>Felügyelt API-eseményindítók

| Trigger típusa | Leírás | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | A [Microsoft által felügyelt API-](../connectors/apis-list.md)k használatával ellenőrzi vagy lekérdezi a végpontot. | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Létrehoz egy hívható végpontot a logikai alkalmazáshoz a [Microsoft által felügyelt API-](../connectors/apis-list.md) k felhívásával és lemondásával. | 
||| 

## <a name="triggers---detailed-reference"></a>Triggerek – részletes útmutató

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection-trigger  

Ez az aktiválás a [Microsoft által felügyelt API-](../connectors/apis-list.md) k használatával ellenőrzi vagy lekérdezi a végpontot, hogy az adott trigger paramétereinek a végponttól függően eltérhetnek. Ebben az trigger-definícióban sok szakaszt nem kötelező megadni. Az trigger viselkedése attól függ, hogy a szakaszt tartalmazza-e.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | String | Az trigger neve | 
| <*kapcsolattípus*> | String | A munkafolyamat által használt felügyelt API-hoz való kapcsolódás neve | 
| <*metódus típusa*> | String | A felügyelt API-val való kommunikáció HTTP-metódusa: A "GET", A "PUT", A "POST", A "JAVÍTÁS", A "DELETE" | 
| <*api-operation*> | String | A meghívni kívánt API-művelet | 
| <*időegység*> | String | Az az időegység, amely leírja, hogy az eseményindító milyen gyakran: "Second", "minute", "Hour", "Day", "Week", "Month" | 
| <*number-of-time-units*> | Integer | Egy érték, amely azt határozza meg, hogy az eseményindító milyen gyakran indul el a gyakoriság alapján, amely a megvárni kívánt időegységek száma <p>Itt láthatók a minimális és a maximális intervallumok: <p>Hónap 1-16 hónap </br>Nap 1-500 nap </br>Óra 1 – 12000 óra </br>Percenként 1 – 72000 perc </br>Második 1 – 9999999 másodperc<p>Ha például az intervallum 6, és a gyakoriság értéke "Month", az ismétlődés 6 havonta történik. | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testreszabja az újrapróbálkozási viselkedést olyan időszakos hibák esetén, amelyeknél a 408, a 429 és a 5XX állapotkód, valamint a kapcsolódási kivételek. További információt az újrapróbálkozási [szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*lekérdezés – paraméterek*> | JSON-objektum | Az API-hívással felvenni kívánt lekérdezési paraméterek. Az `"queries": { "api-version": "2018-01-01" }` objektum például hozzáadja `?api-version=2018-01-01` a hívást. | 
| <*max-runs*> | Integer | Alapértelmezés szerint a munkafolyamat-példányok egy időben futnak, vagy párhuzamosan az [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha módosítani szeretné ezt a korlátot úgy, hogy új <*count*> értéket állít be, tekintse meg az [trigger egyidejűségének módosítása](#change-trigger-concurrency)című témakört. | 
| <*max-runs-queue*> | Integer | Ha a munkafolyamata már futtatja a példányok maximális számát, amelyet a `runtimeConfiguration.concurrency.runs` tulajdonság alapján módosíthat, minden új Futtatás az [alapértelmezett korlátba](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kerül ebbe a várólistába. Az alapértelmezett korlát módosításához tekintse meg a [várakozó futtatások korlátjának módosítása](#change-waiting-runs)című témakört. | 
| <*splitOn-expression*> | String | A tömböket visszaadó eseményindítók esetében ez a kifejezés arra a tömbre hivatkozik, amelyet az egyes tömbökhöz tartozó munkafolyamat-példányok létrehozásához és futtatásához kell használni, nem pedig "for each" ciklust. <p>Ez a kifejezés például a tömbben az trigger szövegtörzsében visszaadott elemre vonatkozik:`@triggerbody()?['value']` |
| <*művelet – beállítás*> | String | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információ: [üzemeltetési beállítások](#operation-options). |
||||

*Kimenetek*
 
| Elem | Type | Leírás |
|---------|------|-------------|
| fejlécek | JSON-objektum | A válaszból származó fejlécek |
| törzs | JSON-objektum | A válasz törzse |
| Állapotkód | Integer | A válaszban szereplő állapotkód |
|||| 

*Példa*

Ez az trigger-definíció a Beérkezett üzenetek mappájában naponta ellenőrzi az e-maileket egy Office 365 Outlook-fiókhoz: 

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook-trigger

Ez az trigger egy [Microsoft által felügyelt API-](../connectors/apis-list.md)val küld előfizetési kérést egy végpontnak, amely visszahívási *URL-címet* biztosít a végpont számára a válasz küldéséhez, és megvárja, amíg a végpont válaszol. További információ: [végpont](#subscribe-unsubscribe)-előfizetések.

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*kapcsolattípus*> | String | A munkafolyamat által használt felügyelt API-hoz való kapcsolódás neve | 
| <*body-content*> | JSON-objektum | Bármely, a felügyelt API-nak adattartalomként küldendő üzenet | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testreszabja az újrapróbálkozási viselkedést olyan időszakos hibák esetén, amelyeknél a 408, a 429 és a 5XX állapotkód, valamint a kapcsolódási kivételek. További információt az újrapróbálkozási [szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*lekérdezés – paraméterek*> | JSON-objektum | Az API-hívással belefoglalható lekérdezési paraméterek <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például hozzáadja `?api-version=2018-01-01` a hívást. | 
| <*max-runs*> | Integer | Alapértelmezés szerint a munkafolyamat-példányok egy időben futnak, vagy párhuzamosan az [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha módosítani szeretné ezt a korlátot úgy, hogy új <*count*> értéket állít be, tekintse meg az [trigger egyidejűségének módosítása](#change-trigger-concurrency)című témakört. | 
| <*max-runs-queue*> | Integer | Ha a munkafolyamata már futtatja a példányok maximális számát, amelyet a `runtimeConfiguration.concurrency.runs` tulajdonság alapján módosíthat, minden új Futtatás az [alapértelmezett korlátba](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kerül ebbe a várólistába. Az alapértelmezett korlát módosításához tekintse meg a [várakozó futtatások korlátjának módosítása](#change-waiting-runs)című témakört. | 
| <*splitOn-expression*> | String | A tömböket visszaadó eseményindítók esetében ez a kifejezés arra a tömbre hivatkozik, amelyet az egyes tömbökhöz tartozó munkafolyamat-példányok létrehozásához és futtatásához kell használni, nem pedig "for each" ciklust. <p>Ez a kifejezés például a tömbben az trigger szövegtörzsében visszaadott elemre vonatkozik:`@triggerbody()?['value']` |
| <*művelet – beállítás*> | String | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információ: [üzemeltetési beállítások](#operation-options). | 
|||| 

*Példa*

Ez az aktiválási definíció előfizet az Office 365 Outlook API-ra, visszahívási URL-címet biztosít az API-végpont számára, és megvárja, amíg a végpont válaszol, amikor új e-mail érkezik.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP-eseményindító

Ez az eseményindító ellenőrzi vagy lekérdezi a megadott végpontot a megadott ismétlődési ütemterv alapján. A végpont válasza határozza meg, hogy fut-e a munkafolyamat.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<endpoint-URL>",
      "headers": { "<header-content>" },
      "body": "<body-content>",
      "authentication": { "<authentication-method>" },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*metódus típusa*> | String | A megadott végpont lekérdezéséhez használandó HTTP-metódus: A "GET", A "PUT", A "POST", A "JAVÍTÁS", A "DELETE" | 
| <*végpont – URL*> | String | A lekérdezéshez használt végpont HTTP-vagy HTTPS-URL-címe <p>Karakterlánc maximális mérete: 2 KB | 
| <*időegység*> | String | Az az időegység, amely leírja, hogy az eseményindító milyen gyakran: "Second", "minute", "Hour", "Day", "Week", "Month" | 
| <*number-of-time-units*> | Integer | Egy érték, amely azt határozza meg, hogy az eseményindító milyen gyakran indul el a gyakoriság alapján, amely a megvárni kívánt időegységek száma <p>Itt láthatók a minimális és a maximális intervallumok: <p>Hónap 1-16 hónap </br>Nap 1-500 nap </br>Óra 1 – 12000 óra </br>Percenként 1 – 72000 perc </br>Második 1 – 9999999 másodperc<p>Ha például az intervallum 6, és a gyakoriság értéke "Month", az ismétlődés 6 havonta történik. | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*header-content*> | JSON-objektum | A kérelemmel küldendő fejlécek <p>Például egy kérelem nyelvének és típusának megadásához: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | String | A kéréssel adattartalomként küldendő üzenet tartalma | 
| <*hitelesítés – metódus*> | JSON-objektum | A kérelem által a hitelesítéshez használt metódus. További információ: [Scheduler kimenő hitelesítés](../scheduler/scheduler-outbound-authentication.md). A feladatütemezőn kívül `authority` a tulajdonság támogatott. Ha nincs megadva, az alapértelmezett érték `https://login.windows.net`a, de más értéket is használhat,`https://login.windows\-ppe.net`például:. |
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testreszabja az újrapróbálkozási viselkedést olyan időszakos hibák esetén, amelyeknél a 408, a 429 és a 5XX állapotkód, valamint a kapcsolódási kivételek. További információt az újrapróbálkozási [szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. |  
 <*lekérdezés – paraméterek*> | JSON-objektum | A kérelemben szerepeltetni kívánt lekérdezési paraméterek <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például hozzáadja `?api-version=2018-01-01` a kérést. | 
| <*max-runs*> | Integer | Alapértelmezés szerint a munkafolyamat-példányok egy időben futnak, vagy párhuzamosan az [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha módosítani szeretné ezt a korlátot úgy, hogy új <*count*> értéket állít be, tekintse meg az [trigger egyidejűségének módosítása](#change-trigger-concurrency)című témakört. | 
| <*max-runs-queue*> | Integer | Ha a munkafolyamata már futtatja a példányok maximális számát, amelyet a `runtimeConfiguration.concurrency.runs` tulajdonság alapján módosíthat, minden új Futtatás az [alapértelmezett korlátba](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kerül ebbe a várólistába. Az alapértelmezett korlát módosításához tekintse meg a [várakozó futtatások korlátjának módosítása](#change-waiting-runs)című témakört. | 
| <*művelet – beállítás*> | String | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információ: [üzemeltetési beállítások](#operation-options). | 
|||| 

*Kimenetek*

| Elem | Type | Leírás |
|---------|------|-------------| 
| fejlécek | JSON-objektum | A válaszból származó fejlécek | 
| törzs | JSON-objektum | A válasz törzse | 
| Állapotkód | Integer | A válaszban szereplő állapotkód | 
|||| 

*A bejövő kérelmekre vonatkozó követelmények*

Ahhoz, hogy megfelelően működjön a logikai alkalmazással, a végpontnak meg kell felelnie egy adott trigger-mintának vagy szerződésnek, és ismernie kell a következő tulajdonságokat:  
  
| Válasz | Kötelező | Leírás | 
|----------|----------|-------------| 
| Állapotkód | Igen | A "200 OK" állapotkód elindítja a futtatást. Minden más állapotkód nem indít el futtatást. | 
| Újrapróbálkozás a fejléc után | Nem | Azon másodpercek száma, ameddig a logikai alkalmazás újra le nem kérdezi a végpontot | 
| Hely fejléce | Nem | A következő lekérdezési intervallumban hívni kívánt URL-cím. Ha nincs megadva, a rendszer az eredeti URL-címet használja. | 
|||| 

*Példa a különböző kérelmek viselkedésére*

| Állapotkód | Újrapróbálkozás | Viselkedés | 
|-------------|-------------|----------|
| 200 | nEz egy | Futtassa a munkafolyamatot, majd a definiált ismétlődés után további információért próbálkozzon újra. | 
| 200 | 10 másodperc | Futtassa a munkafolyamatot, majd 10 másodperc elteltével további információért próbálkozzon újra. |  
| 202 | 60 másodperc | Ne aktiválja a munkafolyamatot. A következő kísérlet egy percen belül megtörténik a definiált ismétlődéstől függően. Ha a definiált ismétlődés kevesebb mint egy percet vesz igénybe, az újrapróbálkozási fejléc elsőbbséget élvez. Ellenkező esetben a rendszer a definiált ismétlődést használja. | 
| 400 | nEz egy | Hibás kérés, ne futtassa a munkafolyamatot. Ha nincs `retryPolicy` megadva, a rendszer az alapértelmezett házirendet használja. Az újrapróbálkozások számának elérésekor az eseményindító újra ellenőrzi a megadott ismétlődés utáni adatmennyiséget. | 
| 500 | nEz egy| Kiszolgálóhiba, ne futtassa a munkafolyamatot. Ha nincs `retryPolicy` megadva, a rendszer az alapértelmezett házirendet használja. Az újrapróbálkozások számának elérésekor az eseményindító újra ellenőrzi a megadott ismétlődés utáni adatmennyiséget. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook trigger  

Ez az aktiválás a logikai alkalmazás meghívását teszi lehetővé egy olyan végpont létrehozásával, amely regisztrálhatja az előfizetést a megadott végpont URL-címének meghívásával. Amikor létrehozza ezt az triggert a munkafolyamatban, a kimenő kérelem az előfizetés regisztrálását kezdeményezi. Így az trigger megkezdheti az események figyelését. Ha egy művelet érvénytelenvé válik, a kimenő kérelmek automatikusan megszakítják az előfizetést. További információ: [végpont](#subscribe-unsubscribe)-előfizetések.

**HTTPWebhook** -trigger esetében is megadhat [aszinkron korlátokat](#asynchronous-limits) .
Az trigger viselkedése a használt vagy kihagyott elemektől függ. 

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Egyes értékek, például a <*módszer típusú*> elérhetők mind a, mind az `"subscribe"` `"unsubscribe"` objektumhoz.

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*metódus típusa*> | String | Az előfizetési kérelemhez használandó HTTP-metódus: "GET", "PUT", "POST", "PATCH" vagy "DELETE" | 
| <*végpont – előfizetés – URL*> | String | Az előfizetési kérést küldő végpont URL-címe | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*metódus típusa*> | String | A lemondási kérelemhez használandó HTTP-metódus: "GET", "PUT", "POST", "PATCH" vagy "DELETE" | 
| <*végpont – leiratkozás – URL*> | String | Az a végpont URL-címe, ahová el kell küldeni a lemondási kérelmet | 
| <*body-content*> | String | Az előfizetéshez vagy a lemondási kérelemhez küldendő üzenet tartalma | 
| <*hitelesítés – metódus*> | JSON-objektum | A kérelem által a hitelesítéshez használt metódus. További információ: [Scheduler kimenő hitelesítés](../scheduler/scheduler-outbound-authentication.md). |
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testreszabja az újrapróbálkozási viselkedést olyan időszakos hibák esetén, amelyeknél a 408, a 429 és a 5XX állapotkód, valamint a kapcsolódási kivételek. További információt az újrapróbálkozási [szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*max-runs*> | Integer | Alapértelmezés szerint a munkafolyamat-példányok mindegyike egy időben fut, vagy párhuzamosan az [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha módosítani szeretné ezt a korlátot úgy, hogy új <*count*> értéket állít be, tekintse meg az [trigger egyidejűségének módosítása](#change-trigger-concurrency)című témakört. | 
| <*max-runs-queue*> | Integer | Ha a munkafolyamata már futtatja a példányok maximális számát, amelyet a `runtimeConfiguration.concurrency.runs` tulajdonság alapján módosíthat, minden új Futtatás az [alapértelmezett korlátba](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kerül ebbe a várólistába. Az alapértelmezett korlát módosításához tekintse meg a [várakozó futtatások korlátjának módosítása](#change-waiting-runs)című témakört. | 
| <*művelet – beállítás*> | String | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információ: [üzemeltetési beállítások](#operation-options). | 
|||| 

*Kimenetek* 

| Elem | Type | Leírás |
|---------|------|-------------| 
| fejlécek | JSON-objektum | A válaszból származó fejlécek | 
| törzs | JSON-objektum | A válasz törzse | 
| Állapotkód | Integer | A válaszban szereplő állapotkód | 
|||| 

*Példa*

Ez az trigger létrehoz egy előfizetést a megadott végpontra, egyedi visszahívási URL-címet biztosít, és megvárja az újonnan közzétett technológiai cikkeket.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Ismétlődési eseményindító  

Ez az eseményindító a megadott ismétlődési ütemterv alapján fut, és egyszerű módszert biztosít a rendszeresen futó munkafolyamatok létrehozásához. 

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*időegység*> | String | Az az időegység, amely leírja, hogy az eseményindító milyen gyakran: "Second", "minute", "Hour", "Day", "Week", "Month" | 
| <*number-of-time-units*> | Integer | Egy érték, amely azt határozza meg, hogy az eseményindító milyen gyakran indul el a gyakoriság alapján, amely a megvárni kívánt időegységek száma <p>Itt láthatók a minimális és a maximális intervallumok: <p>Hónap 1-16 hónap </br>Nap 1-500 nap </br>Óra 1 – 12000 óra </br>Percenként 1 – 72000 perc </br>Második 1 – 9999999 másodperc<p>Ha például az intervallum 6, és a gyakoriság értéke "Month", az ismétlődés 6 havonta történik. | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*kezdő-dátum-idő-formátum-éééé-hh-NNTóó: PP: mm*> | String | A kezdő dátum és idő ebben a formátumban: <p>ÉÉÉÉ-hh-NNTóó: PP: mm, ha időzónát ad meg <p>– vagy – <p>ÉÉÉÉ-hh-NNTóó: PP: ssZ, ha nem ad meg időzónát <p>Így például, ha a szeptember 18., 2017 at 2:00 PM-t szeretné használni, adja meg a "2017-09-18T14:00:00" parancsot, és adja meg az időzónát, például "csendes-óceáni téli idő", vagy adja meg a "2017-09-18T14:00:00Z" időzóna nélküli beállítását. <p>**Megjegyzés:** Ez a kezdési időpont legfeljebb 49 évvel későbbi, és az [ISO 8601 dátum-idő specifikációt](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) kell követnie [UTC dátum és idő formátumban](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül. Ha nem ad meg időzónát, fel kell vennie a "Z" betűt a végén szóköz nélkül. Ez a "Z" a megfelelő [tengeri](https://en.wikipedia.org/wiki/Nautical_time)időpontra hivatkozik. <p>Az egyszerű ütemtervek esetében a kezdési időpont az első előfordulás, míg a komplex ütemtervek esetében az trigger nem a kezdési időpontnál hamarabb következik be. A kezdő dátummal és időpontokkal kapcsolatos további információkért lásd: a [rendszeresen futó feladatok létrehozása és rendszeres futtatása](../connectors/connectors-native-recurrence.md). | 
| <*time-zone*> | String | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az trigger nem fogad el [UTC](https://en.wikipedia.org/wiki/UTC_offset)-eltolást. Itt adhatja meg az alkalmazni kívánt időzónát. | 
| <*egy vagy több órás jelölés*> | Egész vagy egész tömb | Ha a (z) "Day" vagy a `frequency`"Week" értéket adja meg, akkor megadhat egy vagy több egész számot 0 és 23 között, vesszővel elválasztva, a nap azon órájában, amikor futtatni kívánja a munkafolyamatot. <p>Ha például a "10", a "12" és a "14" lehetőséget választja, akkor az óra jelzése 10 ÓRAKOR, 12 ÓRAKOR és 2 óráig is megadható. | 
| <*egy vagy több perces jelek*> | Egész vagy egész tömb | Ha a (z) "Day" vagy a `frequency`"Week" értéket adja meg, megadhat egy vagy több egész számot 0 és 59 között, vesszővel elválasztva, az óra percében, amikor futtatni szeretné a munkafolyamatot. <p>Megadhatja például a "30" percet a perces jelölésként, és az előző példában a nap órájában a következőt kapja: 10:30, 12:30 PM és 2:30 PM. | 
| weekDays | Karakterlánc-vagy karakterlánc-tömb | Ha a "Week" `frequency`lehetőséget adja meg, megadhat egy vagy több napot, vesszővel elválasztva, ha a munkafolyamatot szeretné futtatni: "Hétfő", "kedd", "szerda", "csütörtök", "Friday", "szombat" és "vasárnap" | 
| <*max-runs*> | Integer | Alapértelmezés szerint a munkafolyamat-példányok mindegyike egy időben fut, vagy párhuzamosan az [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha módosítani szeretné ezt a korlátot úgy, hogy új <*count*> értéket állít be, tekintse meg az [trigger egyidejűségének módosítása](#change-trigger-concurrency)című témakört. | 
| <*max-runs-queue*> | Integer | Ha a munkafolyamata már futtatja a példányok maximális számát, amelyet a `runtimeConfiguration.concurrency.runs` tulajdonság alapján módosíthat, minden új Futtatás az [alapértelmezett korlátba](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kerül ebbe a várólistába. Az alapértelmezett korlát módosításához tekintse meg a [várakozó futtatások korlátjának módosítása](#change-waiting-runs)című témakört. | 
| <*művelet – beállítás*> | String | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információ: [üzemeltetési beállítások](#operation-options). | 
|||| 

*1. példa*

Ez az alapszintű ismétlődési eseményindító naponta fut:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*2. példa*

Megadhatja az trigger kiégetésének kezdő dátumát és időpontját. Ez az ismétlődési eseményindító a megadott dátumon kezdődik, majd naponta tüzek:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*3. példa*

Ez az ismétlődési eseményindító szeptember 9-én kezdődik, 2017-kor, 2:00 ÓRAKOR, és hetente, a 10:30-as, a 12:30-es és az 2:30-i csendes-óceáni téli idő szerint.

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

További információ és példák ehhez az triggerhez: [rendszeresen futó feladatok létrehozása és rendszeres futtatása](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Kérelem triggere

Ez az aktiválás a logikai alkalmazás meghívását teszi lehetővé egy olyan végpont létrehozásával, amely fogadja a bejövő kérelmeket. Ehhez az triggerhez adjon meg egy JSON-sémát, amely leírja és érvényesíti a bejövő kérelemtől kapott adattartalmat vagy bemeneteket. A séma emellett megkönnyíti a trigger-tulajdonságok hivatkozását a munkafolyamat későbbi műveleteiről. 

Az trigger meghívásához az `listCallbackUrl` API-t kell használnia, amelyet a munkafolyamat- [szolgáltatás REST API](https://docs.microsoft.com/rest/api/logic/workflows)ismertet. Ha meg szeretné tudni, hogyan használhatja ezt az triggert HTTP-végpontként, tekintse meg a munkafolyamatok [hívása, elindítása vagy beágyazása http](../logic-apps/logic-apps-http-endpoint.md)-végpontokkal című témakört.

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*tulajdonság neve*> | String | A JSON-sémában található egyik tulajdonság neve, amely leírja a hasznos adatokat | 
| <*tulajdonság típusa*> | String | A tulajdonság típusa | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*metódus típusa*> | String | A beérkező kérelmek által a logikai alkalmazás meghívásához használt metódus: A "GET", A "PUT", A "POST", A "JAVÍTÁS", A "DELETE" |
| <*relative-path-for-accepted-parameter*> | String | Annak a paraméternek a relatív elérési útja, amelyet a végpont URL-címe el tud fogadni | 
| <*kötelező – tulajdonságok*> | Array | Egy vagy több olyan tulajdonság, amely értékeket igényel | 
| <*max-runs*> | Integer | Alapértelmezés szerint a munkafolyamat-példányok mindegyike egy időben fut, vagy párhuzamosan az [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha módosítani szeretné ezt a korlátot úgy, hogy új <*count*> értéket állít be, tekintse meg az [trigger egyidejűségének módosítása](#change-trigger-concurrency)című témakört. | 
| <*max-runs-queue*> | Integer | Ha a munkafolyamata már futtatja a példányok maximális számát, amelyet a `runtimeConfiguration.concurrency.runs` tulajdonság alapján módosíthat, minden új Futtatás az [alapértelmezett korlátba](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kerül ebbe a várólistába. Az alapértelmezett korlát módosításához tekintse meg a [várakozó futtatások korlátjának módosítása](#change-waiting-runs)című témakört. | 
| <*művelet – beállítás*> | String | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információ: [üzemeltetési beállítások](#operation-options). | 
|||| 

*Példa*

Ez az aktiválás azt adja meg, hogy egy bejövő kérelemnek a HTTP POST metódust kell használnia az trigger meghívásához, és tartalmaz egy sémát, amely ellenőrzi a bejövő kérelem bemenetét: 

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Kiváltó feltételek

Bármely eseményindító esetében, és csak eseményindítók esetén olyan tömböt is megadhat, amely egy vagy több kifejezést tartalmaz olyan feltételekhez, amelyek meghatározzák, hogy a munkafolyamat fusson-e. Ha hozzá szeretné `conditions` adni a tulajdonságot egy triggerhez a munkafolyamatban, nyissa meg a logikai alkalmazást a Code View Editorban.

Megadhatja például, hogy az eseményindító csak akkor induljon el, ha egy webhely belső kiszolgálóhiba visszaadásával az eseményindító állapotkódot `conditions` a tulajdonságban:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Alapértelmezés szerint az eseményindító csak az "200 OK" válasz beolvasása után következik be. Ha egy kifejezés egy trigger állapotkódot hivatkozik, a rendszer lecseréli az trigger alapértelmezett viselkedését. Tehát ha azt szeretné, hogy a trigger egynél több állapotkódot tartalmazzon, például a "200" és a "201" állapotkódot, a kifejezést a következő feltételnek kell megadnia: 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Több Futtatás elindítása

Ha az trigger egy tömböt ad vissza a logikai alkalmazás feldolgozásához, időnként előfordulhat, hogy egy "for each" ciklus túl sokáig tart az egyes tömbök feldolgozásához. Ehelyett használhatja az trigger **SplitOn** tulajdonságát a tömb letételéhez . A leválasztás megszakítja a tömb elemeit, és elindít egy új munkafolyamat-példányt, amely minden egyes tömb elemnél fut. Ez a megközelítés akkor lehet hasznos, ha például olyan végpontot szeretne lekérdezni, amely több új elemet ad vissza a lekérdezési intervallumok között.
A **SplitOn** által egyetlen logikai alkalmazás futtatásával feldolgozható tömbök maximális száma: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> A **SplitOn** nem használható szinkron válasz mintával. Minden olyan munkafolyamat, amely a **SplitOn** -t használja, és a válasz művelete aszinkron `202 ACCEPTED` módon fut, és azonnal választ küld.

Ha az trigger hencegő fájlja olyan adattartalmat ír le, amely tömb, a rendszer automatikusan hozzáadja a **SplitOn** tulajdonságot az triggerhez. Ellenkező esetben adja hozzá ezt a tulajdonságot a kiválasztani kívánt tömböt tartalmazó válasz adattartalomhoz. 

*Példa*

Tegyük fel, hogy rendelkezik egy API-val, amely visszaadja ezt a választ: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```
 
A logikai alkalmazásnak csak a tömbben lévő `Rows`tartalomra van szüksége, így az alábbi példához hasonlóan hozhat létre egy triggert:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Ha a `SplitOn` parancsot használja, nem tudja lekérni a tömbön kívüli tulajdonságokat. Tehát ebben a példában nem lehet beolvasni `status` az API által visszaadott válasz tulajdonságát.
> 
> Ha a `Rows` tulajdonság nem létezik, a hiba elkerüléséhez használja az `?` operátort.

A munkafolyamat-definíciója mostantól az első `name` futtatáskor és `"customer-name-two"` a `"customer-name-one"` második futtatáskor használt `@triggerBody().name` értékeket is lekérheti. Így az trigger kimenetei a következő példához hasonlóan jelennek meg:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Műveletek áttekintése

A Azure Logic Apps különböző típusú műveleteket biztosít – mindegyiket különböző bemenetekkel, amelyek egy művelet egyedi viselkedését határozzák meg. 

A műveletek ezekkel a magas szintű elemekkel rendelkeznek, bár egyesek nem kötelezőek:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------|
| <*művelet – név*> | String | A művelet neve | 
| <*művelet típusa*> | String | A művelet típusa, például "http" vagy "ApiConnection"| 
| <*bemenet – név*> | String | A művelet viselkedését definiáló bemenet neve | 
| <*input-value*> | Különböző | A bemeneti érték, amely lehet karakterlánc, egész szám, JSON-objektum stb. | 
| <*previous-trigger-or-action-status*> | JSON-objektum | Azon trigger vagy művelet neve és eredményül kapott állapota, amelynek az aktuális művelet futtatása előtt azonnal futnia kell | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------|
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testreszabja az újrapróbálkozási viselkedést olyan időszakos hibák esetén, amelyeknél a 408, a 429 és a 5XX állapotkód, valamint a kapcsolódási kivételek. További információt az újrapróbálkozási szabályzatok című témakörben talál. | 
| <*runtime-config-options*> | JSON-objektum | Egyes műveletek esetében a tulajdonságok beállításával `runtimeConfiguration` módosíthatja a művelet viselkedését futás közben. További információ: [futásidejű konfigurációs beállítások](#runtime-config-options). | 
| <*művelet – beállítás*> | String | Egyes műveletek esetében a `operationOptions` tulajdonság beállításával módosíthatja az alapértelmezett viselkedést. További információ: [üzemeltetési beállítások](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Tevékenységtípusok listája

Íme néhány gyakran használt Művelettípus: 

* [Beépített Művelettípus-típusok](#built-in-actions) , például példák és egyebek: 

  * [**Http**](#http-action) a végpontok http vagy HTTPS protokollon keresztüli meghívásához

  * [**Válasz**](#response-action) a kérelmekre való válaszadásra

  * [**JavaScript-kód végrehajtása**](#run-javascript-code) a JavaScript-kódrészletek futtatásához

  * [**Függvény**](#function-action) a Azure functions meghívásához

  * Adatműveleti műveletek, például [**Csatlakozás**](#join-action), [**összeállítás**](#compose-action), [**tábla**](#table-action), [**kijelölés**](#select-action)és egyéb adatok, amelyek különböző bemenetekről hoznak létre vagy alakítanak át adatokat.

  * Egy másik logikai alkalmazás munkafolyamatának hívására szolgáló [**munkafolyamat**](#workflow-action)

* [Felügyelt API-műveleti típusok](#managed-api-actions) , például [**ApiConnection**](#apiconnection-action) és [**ApiConnectionWebHook**](#apiconnectionwebhook-action) , amelyek a Microsoft által felügyelt különböző összekötőket és API-kat hívhatják, például: Azure Service Bus, Office 365 Outlook, Power bi, Azure Blob Storage, OneDrive, GitHub és egyebek

* A munkafolyamatok [műveleti típusai](#control-workflow-actions) , például az [**IF**](#if-action), a [**foreach**](#foreach-action), a [**switch**](#switch-action), a [**scope**](#scope-action)és a [**ig**](#until-action), amelyek más műveleteket tartalmaznak, és segítenek a munkafolyamat-végrehajtás rendszerezésében

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Beépített műveletek

| Művelettípus | Leírás | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Egyetlen kimenetet hoz létre bemenetből, amely különböző típusokkal rendelkezhet. | 
| [**JavaScript-kód végrehajtása**](#run-javascript-code) | JavaScript-kódrészletek futtatása, amelyek megfelelnek a megadott feltételeknek. A kódokra vonatkozó követelmények és további információk: kódrészletek [hozzáadása és futtatása beágyazott kóddal](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Függvény**](#function-action) | Meghív egy Azure-függvényt. | 
| [**HTTP**](#http-action) | HTTP-végpontot hív meg. | 
| [**Csatlakozás**](#join-action) | Létrehoz egy karakterláncot egy tömb összes eleméről, és elválasztja ezeket az elemeket egy megadott elválasztói karakterrel. | 
| [**JSON-elemzés**](#parse-json-action) | Felhasználóbarát jogkivonatokat hoz létre a JSON-tartalmak tulajdonságaiban. Ezeket a tulajdonságokat a logikai alkalmazásban található jogkivonatokkal is hivatkozhat. | 
| [**Lekérdezés**](#query-action) | Tömböt hoz létre egy másik tömb elemeiből egy feltétel vagy szűrő alapján. | 
| [**Válasz**](#response-action) | Egy bejövő hívásra vagy kérelemre vonatkozó választ hoz létre. | 
| [**Válassza**](#select-action) | JSON-objektumokkal rendelkező tömböt hoz létre egy másik tömb elemeinek a megadott Térkép alapján történő átalakításával. | 
| [**Tábla**](#table-action) | CSV-vagy HTML-táblázatot hoz létre egy tömbből. | 
| [**Felmondhatja**](#terminate-action) | Leállítja egy aktívan futó munkafolyamatot. | 
| [**várj**](#wait-action) | Szünetelteti a munkafolyamatot egy adott időtartamra vagy a megadott dátumra és időpontra vonatkozóan. | 
| [**Munkafolyamat**](#workflow-action) | Munkafolyamatot ágyaz be egy másik munkafolyamaton belül. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Felügyelt API-műveletek

| Művelettípus | Leírás | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | HTTP-végpontot hív meg egy [Microsoft által felügyelt API](../connectors/apis-list.md)használatával. | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Ugyanúgy működik, mint a HTTP-webhook, de egy [Microsoft által felügyelt API-](../connectors/apis-list.md)t használ. | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Munkafolyamat-műveletek vezérlése

Ezek a műveletek segítenek a munkafolyamat-végrehajtás szabályozásában, és más műveleteket is tartalmazhatnak. A vezérlési munkafolyamat műveletein kívül közvetlenül is hivatkozhat a műveletekre a vezérlés munkafolyamat-művelet belsejében. Ha például egy `Http` művelet egy hatókörön belül van, a kifejezésre hivatkozhat a `@body('Http')` munkafolyamat tetszőleges pontjáról. A vezérlési munkafolyamaton belül meglévő műveletek azonban csak "Futtatás után" futhatnak, amelyek ugyanabban a vezérlési munkafolyamat-struktúrában találhatók.

| Művelettípus | Leírás | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Futtasson ugyanazokat a műveleteket egy hurokban egy tömb minden eleméhez. | 
| [**If**](#if-action) | Műveletek futtatása attól függően, hogy a megadott feltétel igaz vagy hamis. | 
| [**Hatókör**](#scope-action) | Műveletek futtatása a csoport állapota alapján. | 
| [**Kapcsoló**](#switch-action) | Olyan esetekben is futtathat műveleteket, amikor kifejezésekből, objektumokból vagy tokenekről származó értékek egyeznek az egyes esetekben megadott értékekkel. | 
| [**Amíg**](#until-action) | Futtasson műveleteket egy hurokban, amíg a megadott feltétel nem teljesül. | 
|||  

## <a name="actions---detailed-reference"></a>Műveletek – részletes útmutató

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection művelet

Ez a művelet HTTP-kérést küld egy [Microsoft által felügyelt API-](../connectors/apis-list.md) nak, és információt kér az API-ról és a paraméterekről, valamint egy érvényes kapcsolatra mutató hivatkozást. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*művelet – név*> | String | Az összekötő által megadott művelet neve | 
| <*api-name*> | String | A kapcsolatban használt Microsoft által felügyelt API neve | 
| <*metódus típusa*> | String | Az API meghívásának HTTP-metódusa: "GET", "PUT", "POST", "PATCH" vagy "DELETE" | 
| <*api-operation*> | String | A meghívni kívánt API-művelet | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*egyéb művelet-specifikus – input – tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb beviteli tulajdonságok | 
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testreszabja az újrapróbálkozási viselkedést olyan időszakos hibák esetén, amelyeknél a 408, a 429 és a 5XX állapotkód, valamint a kapcsolódási kivételek. További információt az újrapróbálkozási [szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*lekérdezés – paraméterek*> | JSON-objektum | Az API-hívással felvenni kívánt lekérdezési paraméterek. <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például hozzáadja `?api-version=2018-01-01` a hívást. | 
| <*egyéb – művelet-specifikus – tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb tulajdonságok | 
|||| 

*Példa*

Ez a definíció az Office 365 Outlook Connector **e-mail-címének küldése** műveletét ismerteti, amely egy Microsoft által felügyelt API: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>APIConnectionWebhook művelet

Ez a művelet egy [Microsoft által felügyelt API](../connectors/apis-list.md)használatával küldi el a HTTP-n keresztüli előfizetési kérelmet egy végpontra, amely visszahívási *URL-címet* biztosít, ahol a végpont választ küldhet, és megvárja, amíg a végpont válaszol. További információ: [végpont](#subscribe-unsubscribe)-előfizetések.

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Egyes értékek, például a <*módszer típusú*> elérhetők mind a, mind az `"subscribe"` `"unsubscribe"` objektumhoz.

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*művelet – név*> | String | Az összekötő által megadott művelet neve | 
| <*metódus típusa*> | String | A végpontról való feliratkozáshoz vagy leiratkozáshoz használandó HTTP-metódus: "GET", "PUT", "POST", "PATCH" vagy "DELETE" | 
| <*API – előfizetés – URL*> | String | Az API-ra való feliratkozáshoz használandó URI | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | String | Az API-ból való leiratkozáshoz használandó URI | 
| <*header-content*> | JSON-objektum | A kérelemben küldendő fejlécek <p>Például a nyelv és a típus megadásához a kérelemben: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON-objektum | A kérelemben küldendő üzenet tartalma | 
| <*hitelesítés – metódus*> | JSON-objektum | A kérelem által a hitelesítéshez használt metódus. További információ: [Scheduler kimenő hitelesítés](../scheduler/scheduler-outbound-authentication.md). |
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testreszabja az újrapróbálkozási viselkedést olyan időszakos hibák esetén, amelyeknél a 408, a 429 és a 5XX állapotkód, valamint a kapcsolódási kivételek. További információt az újrapróbálkozási [szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*lekérdezés – paraméterek*> | JSON-objektum | Az API-hívással belefoglalható lekérdezési paraméterek <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például hozzáadja `?api-version=2018-01-01` a hívást. | 
| <*egyéb művelet-specifikus – input – tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb beviteli tulajdonságok | 
| <*egyéb – művelet-specifikus – tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb tulajdonságok | 
|||| 

A **ApiConnectionWebhook** műveletekre vonatkozó korlátokat ugyanúgy is megadhatja, mint a [http aszinkron korlátokat](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Összeállítási művelet

Ez a művelet egyetlen kimenetet hoz létre több bemenetből, beleértve a kifejezéseket is. A kimenetnek és a bemeneteknek bármilyen típusa lehet, amely Azure Logic Apps natív módon támogatja, például a tömböket, a JSON-objektumokat, az XML-t és a bináris fájlt.
Ezután a művelet kimenetét más műveletekben is használhatja. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Kötelező* 

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*bemenetek és összeállítások*> | Any | Egyetlen kimenet létrehozására szolgáló bemenetek | 
|||| 

*1. példa*

<!-- markdownlint-disable MD038 -->
Ez a műveleti definíció egy `abcdefg ` záró szóközzel és az értékkel `1234`egyesül:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

A művelet által létrehozott kimenet a következő:

`abcdefg 1234`

*2. példa*

Ez a műveleti definíció egy karakterlánc-változót egyesít `abcdefg` , amely tartalmazza a következőt `1234`: és egy Integer változót, amely a következőket tartalmazza:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

A művelet által létrehozott kimenet a következő:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>JavaScript-kód végrehajtása művelet

Ez a művelet egy JavaScript kódrészletet futtat, és visszaadja az `Result` eredményeket egy olyan jogkivonaton keresztül, amelyet később a műveletek hivatkozhatnak.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás |
|-------|------|-------------|
| <*JavaScript – kódrészlet*> | Változó | A futtatni kívánt JavaScript-kód. A kódokra vonatkozó követelmények és további információk: kódrészletek [hozzáadása és futtatása beágyazott kóddal](../logic-apps/logic-apps-add-run-inline-code.md). <p>Az `code` attribútumban a kódrészlet a írásvédett `workflowContext` objektumot bemenetként használhatja. Ez az objektum olyan altulajdonságokkal rendelkezik, amelyek lehetővé tennék a kód elérését a munkafolyamatban lévő trigger és előző műveletek eredményeihez. További információ az `workflowContext` objektumról: az [trigger és a művelet eredményei a kódban](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Bizonyos esetekben szükséges*

Az `explicitDependencies` attribútum azt adja meg, hogy explicit módon kívánja-e megadni az eredményeket az triggerből, a korábbi műveletekből vagy mindkettőből a kódrészlet függőségeinek megfelelően. A függőségek hozzáadásával kapcsolatos további információkért lásd: [Paraméterek hozzáadása a beágyazott kódhoz](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Az attribútum `includeTrigger` esetében megadhatja `true` vagy `false` megadhatja az értékeket.

| Value | Type | Leírás |
|-------|------|-------------|
| <*előző műveletek*> | Karakterlánc-tömb | Egy tömb a megadott műveleti nevekkel. Használja a munkafolyamat-definícióban megjelenő műveletek neveit, ahol a műveleti nevek aláhúzást (_) használnak, nem szóközöket (""). |
||||

*1. példa*

Ez a művelet olyan kódot futtat, amely beolvassa a logikai alkalmazás nevét, és eredményként \<visszaadja a "Helló világ a Logic-app-Name >" szöveget. Ebben a példában a kód a munkafolyamat nevére hivatkozik úgy, hogy a `workflowContext.workflow.name` tulajdonságot a csak `workflowContext` olvasható objektumon keresztül éri el. További információ az `workflowContext` objektum használatáról: az [trigger és a művelet eredményei a kódban](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*2. példa*

Ez a művelet kódot futtat egy logikai alkalmazásban, amely elindítja, amikor új e-mail érkezik egy Office 365 Outlook-fiókba. A logikai alkalmazás egy küldési jóváhagyási e-mail-műveletet is használ, amely a kapott e-mailben továbbítja a tartalmat a jóváhagyásra vonatkozó kéréssel együtt. 

A kód kibontja az e-mail `Body` -címeket az trigger tulajdonságból, és visszaadja az e-mail-címeket a jóváhagyási műveletben szereplő `SelectedOption` tulajdonság értékével együtt. A művelet explicit módon tartalmazza a jóváhagyás küldése e-mailben műveletet az `explicitDependencies`  >  `actions` attribútumban található függőségként.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Függvény művelete

Ez a művelet egy korábban létrehozott [Azure](../azure-functions/functions-create-first-azure-function.md)-függvényt hív meg.

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------|  
| <*Azure-function-ID*> | String | A hívni kívánt Azure-függvény erőforrás-azonosítója. Itt látható az érték formátuma:<p>"/Subscriptions/<*Azure-előfizetés-azonosító*>/ResourceGroups/<*Azure-resource-group*>/Providers/Microsoft.Web/Sites/<*Azure-function-app-Name*>/functions/<*Azure-Function-Name*> " | 
| <*metódus típusa*> | String | A függvény meghívásához használandó HTTP-metódus: "GET", "PUT", "POST", "PATCH" vagy "DELETE" <p>Ha nincs megadva, az alapértelmezett érték a "POST" metódus. | 
||||

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------|  
| <*header-content*> | JSON-objektum | A hívással küldendő fejlécek <p>Például a nyelv és a típus megadásához a kérelemben: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON-objektum | A kérelemben küldendő üzenet tartalma | 
| <*lekérdezés – paraméterek*> | JSON-objektum | Az API-hívással belefoglalható lekérdezési paraméterek <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például hozzáadja `?api-version=2018-01-01` a hívást. | 
| <*egyéb művelet-specifikus – input – tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb beviteli tulajdonságok | 
| <*egyéb – művelet-specifikus – tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb tulajdonságok | 
||||

A logikai alkalmazás mentésekor a Logic Apps motor hajtja végre ezeket az ellenőrzéseket a hivatkozott függvényen:

* A munkafolyamatnak hozzáféréssel kell rendelkeznie a függvényhez.

* A munkafolyamat csak szabványos HTTP-triggert vagy általános JSON webhook-triggert használhat. 

  A Logic Apps motor lekérdezi és gyorsítótárazza az trigger URL-címét, amely futásidőben használatos. Ha azonban bármelyik művelet érvényteleníti a gyorsítótárazott URL-címet, a **függvény** futtatása futásidőben meghiúsul. A probléma megoldásához mentse újra a logikai alkalmazást, hogy a logikai alkalmazás újra megkapja és gyorsítótárazza az trigger URL-címét.

* A függvénynek nincs definiált útvonala.

* Csak a "Function" és a "névtelen" engedélyezési szintek engedélyezettek. 

*Példa*

Ez a műveleti definíció a korábban létrehozott "GetProductID" függvényt hívja meg:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP-művelet

Ez a művelet egy kérelmet küld a megadott végpontnak, és ellenőrzi a választ, hogy a munkafolyamat fusson-e. 

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>"
   },
   "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*metódus típusa*> | String | A kérelem küldéséhez használandó metódus: "GET", "PUT", "POST", "PATCH" vagy "DELETE" | 
| <*HTTP-or-HTTPS-endpoint-URL*> | String | A hívni kívánt HTTP-vagy HTTPS-végpont. Karakterlánc maximális mérete: 2 KB | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*header-content*> | JSON-objektum | A kérelemmel küldendő fejlécek <p>Például a nyelv és a típus megadásához: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON-objektum | A kérelemben küldendő üzenet tartalma | 
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testreszabja az újrapróbálkozási viselkedést olyan időszakos hibák esetén, amelyeknél a 408, a 429 és a 5XX állapotkód, valamint a kapcsolódási kivételek. További információt az újrapróbálkozási [szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*lekérdezés – paraméterek*> | JSON-objektum | A kérelemben szerepeltetni kívánt lekérdezési paraméterek <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például hozzáadja `?api-version=2018-01-01` a hívást. | 
| <*egyéb művelet-specifikus – input – tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb beviteli tulajdonságok | 
| <*egyéb – művelet-specifikus – tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb tulajdonságok | 
|||| 

*Példa*

Ez a műveleti definíció a legfrissebb híreket kéri le egy kérelem küldésével a megadott végpontra:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Csatlakozási művelet

Ez a művelet létrehoz egy karakterláncot egy tömb összes eleméről, és elválasztja ezeket az elemeket a megadott elválasztói karakterrel. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*tömb*> | Array | A forrás elemeket biztosító tömb vagy kifejezés. Ha megad egy kifejezést, a kifejezést idézőjelek közé kell foglalni. | 
| <*elválasztó*> | Egyetlen karakterből álló karakterlánc | A karakterláncban szereplő egyes elemeket elválasztó karakter | 
|||| 

*Példa*

Tegyük fel, hogy rendelkezik egy korábban létrehozott "myIntegerArray" változóval, amely tartalmazza ezt az egész tömböt: 

`[1,2,3,4]` 

Ez a művelet egy kifejezésben szereplő `variables()` függvény használatával beolvassa a változó értékeit, és a karakterláncot a következő értékekkel hozza létre, amelyek vesszővel vannak elválasztva:`"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>JSON-művelet elemzése

Ez a művelet felhasználóbarát mezőket vagy jogkivonatokat hoz létre a JSON-tartalmak tulajdonságaiban. Ezeket a tulajdonságokat a logikai alkalmazásban is elérheti a tokenek használatával. Ha például olyan szolgáltatásokból származó JSON-kimenetet szeretne használni, mint például a Azure Service Bus és a Azure Cosmos DB, akkor ezt a műveletet a logikai alkalmazásban is használhatja, így könnyebben hivatkozhat az adott kimenetben lévő adatokra. 

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*JSON-forrás*> | JSON-objektum | Az elemezni kívánt JSON-tartalom | 
| <*JSON-schema*> | JSON-objektum | Az alapul szolgáló JSON-tartalmat leíró JSON-séma, amelyet a művelet a forrás JSON-tartalom elemzéséhez használ. <p>**Tipp**: Logic Apps Designerben megadhatja a sémát, vagy megadhat egy minta adattartalmat, hogy a művelet előállíthatja a sémát. | 
|||| 

*Példa*

Ez a műveleti definíció létrehozza ezeket a jogkivonatokat, amelyeket használhat a munkafolyamatban, de csak olyan műveletekben, amelyek a **JSON** elemzése műveletet követően futnak: 

`FirstName`, `LastName`és`Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

Ebben a példában a "Content" tulajdonság határozza meg az elemezni kívánt művelet JSON-tartalmát. Ezt a JSON-tartalmat is megadhatja a séma generálásához szükséges minta adattartalomként.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

A "Schema" tulajdonság a JSON-tartalom leírásához használt JSON-sémát adja meg:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Lekérdezési művelet

Ez a művelet egy tömböt hoz létre egy másik tömb elemeiből egy megadott feltétel vagy szűrő alapján.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*tömb*> | Array | A forrás elemeket biztosító tömb vagy kifejezés. Ha megad egy kifejezést, a kifejezést idézőjelek közé kell foglalni. |
| <*condition-or-filter*> | String | A forrás tömbben lévő elemek szűréséhez használt feltétel <p>**Megjegyzés**: Ha egyetlen érték sem felel meg a feltételnek, akkor a művelet egy üres tömböt hoz létre. |
|||| 

*Példa*

Ez a műveleti definíció olyan tömböt hoz létre, amely a megadott értéknél nagyobb értékkel rendelkezik, amely kettő:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Válasz művelet  

Ez a művelet létrehozza a HTTP-kérelemre adott válasz adattartalmát. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*response-status-code*> | Integer | A bejövő kérelembe küldendő HTTP-állapotkód. Az alapértelmezett kód "200 OK", de a kód bármely érvényes állapotkód lehet, amely 2xx, 4xx vagy 5xx, de nem a 3XXX szint or-vel kezdődik. | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*Válasz – fejlécek*> | JSON-objektum | Egy vagy több, a válaszban szerepeltetni kívánt fejléc | 
| <*Válasz – törzs*> | Különböző | A válasz törzse, amely lehet karakterlánc, JSON-objektum vagy akár bináris tartalom egy korábbi műveletből | 
|||| 

*Példa*

Ez a műveleti definíció egy HTTP-kérelemre adott választ hoz létre a megadott állapotkód, üzenet törzse és üzenet fejléce alapján:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Korlátozások*

A többi művelettől eltérően a **Válasz** művelet speciális korlátozásokkal rendelkezik: 

* A munkafolyamat csak akkor használhatja a **Válasz** műveletet, ha a munkafolyamat http-kérési triggerrel indul, ami azt jelenti, hogy a munkafolyamatot http-kéréssel kell elindítani.

* A munkafolyamat bárhol használhatja a **Válasz** műveletet, *kivéve* a **foreach** hurkokat, **amíg** a hurkok, beleértve a szekvenciális hurkokat és a párhuzamos ágakat is. 

* Az eredeti HTTP-kérelem csak akkor kapja meg a munkafolyamat válaszát, ha a **Válasz** művelethez szükséges összes művelet befejeződik a [http-kérelem időtúllépési korlátján](../logic-apps/logic-apps-limits-and-config.md#request-limits)belül.

  Ha azonban a munkafolyamat egy másik logikai alkalmazást egy beágyazott munkafolyamatként hív meg, a szülő munkafolyamat addig várakozik, amíg a beágyazott munkafolyamat be nem fejeződik, függetlenül attól, hogy mennyi idő telik el a beágyazott munkafolyamat befejeződése előtt.

* Ha a munkafolyamat a **Válasz** műveletet és egy szinkron válasz mintát használ, a munkafolyamat nem használhatja a **splitOn** parancsot az trigger definíciójában, mert a parancs több futtatást is létrehoz. Ellenőrizze ezt az esetet, ha a PUT metódust használja, és ha igaz, adja vissza a "hibás kérés" választ.

  Ellenkező esetben, ha a munkafolyamat a **splitOn** parancsot és egy **Response** műveletet használ, a munkafolyamat aszinkron módon fut, és azonnal visszaadja a "202 elfogadva" választ.

* Ha a munkafolyamat végrehajtása eléri a **Válasz** műveletet, de a bejövő kérelem már kapott választ, a **Válasz** a "sikertelen" állapotúként van megjelölve az ütközés miatt. Ennek eredményeképpen a logikai alkalmazás futtatása "sikertelen" állapottal is megjelölve van.

<a name="select-action"></a>

### <a name="select-action"></a>Művelet kiválasztása

Ez a művelet JSON-objektumokból álló tömböt hoz létre egy másik tömb elemeinek a megadott Térkép alapján történő átalakításával. A kimeneti tömb és a forrás tömb mindig azonos számú elemet tartalmaz. Bár az objektumok száma nem módosítható a kimeneti tömbben, hozzáadhat vagy eltávolíthat tulajdonságokat és azok értékeit ezen objektumok között. A `select` tulajdonság legalább egy olyan kulcs-érték párokat határoz meg, amely meghatározza a forrás tömbben lévő elemek átalakításának leképezését. A kulcs-érték párok egy tulajdonságot és annak értékét jelölik a kimeneti tömbben lévő összes objektumon. 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Kötelező* 

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*tömb*> | Array | A forrás elemeket biztosító tömb vagy kifejezés. Győződjön meg arról, hogy idézőjelek közé kell foglalnia egy kifejezést. <p>**Megjegyzés**: Ha a forrás tömb üres, a művelet egy üres tömböt hoz létre. | 
| <*kulcs neve*> | String | Az eredményhez hozzárendelt < kifejezésből származó tulajdonság neve> <p>Ha új tulajdonságot szeretne hozzáadni a kimeneti tömb összes objektumához, adjon meg egy <*kulcsnév*> a tulajdonsághoz, valamint egy <*kifejezést*> a tulajdonság értékeként. <p>Ha el szeretne távolítani egy tulajdonságot a tömbben lévő összes objektumból, hagyja ki az adott tulajdonsághoz tartozó <*Key-name*>. | 
| <*kifejezés*> | String | Az a kifejezés, amely átalakítja a forrás tömbben lévő elemeket, és hozzárendeli az eredményt <*Key-Name* értékhez> | 
|||| 

A **Select** művelet kimenetként létrehoz egy tömböt, ezért a kimenetet használni kívánó műveletnek el kell fogadnia egy tömböt, vagy konvertálnia kell a tömböt arra a típusra, amelyet a fogyasztó művelet elfogad. Ha például a kimeneti tömböt karakterlánccá szeretné alakítani, átadhatja ezt a tömböt az **összeállítás** műveletnek, majd hivatkozhat a kimenetre a más műveletekben szereplő **összeállítási** műveletből.

*Példa*

Ez a műveleti definíció egy JSON-objektum tömbjét hozza létre egész tömbből. A művelet megismétli a forrás-tömböt, lekéri az összes egész értéket `@item()` a kifejezés használatával, és minden egyes értéket hozzárendel az`number`egyes JSON-objektumok "" tulajdonságához: 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

A művelet által létrehozott tömb a következő:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Ha a tömb kimenetét más műveletekben szeretné használni, adja át ezt a kimenetet egy összeállítási műveletnek:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Ezután használhatja az összeállítási művelet kimenetét más műveletekben, például az **Office 365 Outlook – e-mail küldése** művelet:

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Tábla művelete

Ez a művelet létrehoz egy CSV-vagy HTML-táblázatot egy tömbből. A JSON-objektumokkal rendelkező tömbök esetében ez a művelet automatikusan létrehozza az oszlopok fejléceit az objektumok tulajdonságlapján. Más adattípusú tömbök esetében meg kell adnia az oszlopfejléceket és az értékeket. Ez a tömb például tartalmazza a "ID" és a "terméknév" tulajdonságot, amelyet ez a művelet használhat az oszlopfejlécek neveként:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Kötelező* 

| Value | Type | Leírás | 
|-------|------|-------------| 
| \<CSV *vagy* html >| String | A létrehozni kívánt tábla formátuma | 
| <*tömb*> | Array | A táblához tartozó forrásadatokat biztosító tömb vagy kifejezés <p>**Megjegyzés**: Ha a forrás tömb üres, akkor a művelet üres táblát hoz létre. | 
|||| 

*Választható*

Az oszlopfejlécek és az értékek megadásához és testreszabásához `columns` használja a tömböt. Ha `header-value` a pároknak ugyanaz a fejléc neve, azok értékei ugyanabban az oszlopban jelennek meg, mint a fejléc neve. Ellenkező esetben az egyes egyedi fejlécek egyedi oszlopot határoznak meg.

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*oszlop neve*> | String | Egy oszlop fejlécének neve | 
| <*oszlop – érték*> | Any | Az oszlopban szereplő érték | 
|||| 

*1. példa*

Tegyük fel, hogy rendelkezik egy korábban létrehozott "myItemArray" változóval, amely jelenleg a következő tömböt tartalmazza: 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Ez a műveleti definíció egy CSV-táblázatot hoz létre a "myItemArray" változóból. A `from` tulajdonság által használt kifejezés beolvassa a tömböt a "myItemArray" típusból a `variables()` következő függvény használatával: 

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Itt látható a művelet által létrehozott CSV-táblázat: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*2. példa*

Ez a műveleti definíció egy HTML-táblázatot hoz létre a "myItemArray" változóból. A `from` tulajdonság által használt kifejezés beolvassa a tömböt a "myItemArray" típusból a `variables()` következő függvény használatával: 

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Itt látható a művelet által létrehozott HTML-táblázat: 

<table><thead><tr><th>id</th><th>Terméknév</th></tr></thead><tbody><tr><td>0</td><td>Almák</td></tr><tr><td>1</td><td>Narancsok</td></tr></tbody></table>

*3. példa*

Ez a műveleti definíció egy HTML-táblázatot hoz létre a "myItemArray" változóból. Ez a példa azonban felülbírálja az alapértelmezett oszlopfejlécek nevét a "Stock_ID" és a "Description" kifejezéssel, és hozzáadja a "Organic" szót a "Description" (Leírás) oszlopban szereplő értékekhez.

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Itt látható a művelet által létrehozott HTML-táblázat: 

<table><thead><tr><th>Stock_ID</th><th>Leírás</th></tr></thead><tbody><tr><td>0</td><td>Organikus Alma</td></tr><tr><td>1</td><td>Organikus narancs</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Művelet megszakítása

Ez a művelet leállítja a munkafolyamat-példány futtatását, megszakítja a folyamatban lévő műveleteket, kihagyja a hátralévő műveleteket, és visszaadja a megadott állapotot. Használhatja például a **megszakítás** műveletet, ha a logikai alkalmazásnak teljesen ki kell lépnie egy hibás állapotból. Ez a művelet nincs hatással a már befejezett műveletekre, és nem szerepelhet a **foreach** belül, és **amíg** a hurkok, beleértve a szekvenciális hurkokat is. 

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*status*> | String | A futtatáshoz visszaadni kívánt állapot: "Sikertelen", "megszakított" vagy "sikeres" |
|||| 

*Választható*

A "runStatus" objektum tulajdonságai csak akkor érvényesek, ha a "runStatus" tulajdonság "sikertelen" állapotra van állítva.

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*error-code-or-name*> | String | A hiba kódja vagy neve |
| <*hiba – üzenet*> | String | A hibát leíró üzenet vagy szöveg, valamint az alkalmazás felhasználója által elvégezhető műveletek | 
|||| 

*Példa*

Ez a műveleti definíció leállítja a munkafolyamat futtatását, a futtatási állapotot "sikertelen" értékre állítja, és visszaadja az állapotot, a hibakódot és a hibaüzenetet:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Várakozási művelet  

Ez a művelet szünetelteti a munkafolyamat-végrehajtást a megadott intervallumban, vagy a megadott időpontig, de nem mindkettőt. 

*Megadott intervallum*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Megadott idő*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*egységek száma*> | Integer | A **késleltetési** művelethez a várakozási egységek száma | 
| <*időköz*> | String | A **késleltetési** művelethez a várakozási időköz: "Second", "minute", "Hour", "Day", "Week", "Month" | 
| <*date-time-stamp*> | String | A végrehajtás folytatásához szükséges dátum és idő **a művelethez** . Az értéknek az [UTC dátum és idő formátumát](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)kell használnia. | 
|||| 

*1. példa*

Ez a műveleti definíció 15 percig szünetelteti a munkafolyamatot:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*2. példa*

Ez a művelet a megadott időpontig szünetelteti a munkafolyamatot:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Munkafolyamat-művelet

Ez a művelet egy másik korábban létrehozott logikai alkalmazást hív meg, ami azt jelenti, hogy más logikai alkalmazások munkafolyamatait is felveheti és újra felhasználhatja. A alárendelt vagy *beágyazott* logikai alkalmazás kimeneteit is használhatja a beágyazott logikai alkalmazást követő műveletekben, ha a gyermek logikai alkalmazás választ ad vissza.

A Logic Apps motor ellenőrzi a hívni kívánt trigger elérését, ezért győződjön meg arról, hogy hozzáfér az adott triggerhez. Emellett a beágyazott logikai alkalmazásnak meg kell felelnie a következő feltételeknek:

* Egy triggerrel meghívható a beágyazott logikai alkalmazás, például egy [kérelem](#request-trigger) vagy egy [http](#http-trigger) -trigger.

* Ugyanaz az Azure-előfizetés, mint a szülő logikai alkalmazás

* Ha a beágyazott logikai alkalmazás kimeneteit szeretné használni a szülő logikai alkalmazásban, a beágyazott logikai alkalmazásnak rendelkeznie kell egy [Response](#response-action) művelettel. 

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*nested-logic-app-name*> | String | A hívni kívánt logikai alkalmazás neve | 
| <*trigger – név*> | String | A meghívni kívánt beágyazott logikai alkalmazásban lévő trigger neve | 
| <*Azure-subscription-ID*> | String | A beágyazott logikai alkalmazás Azure-előfizetési azonosítója |
| <*Azure-resource-group*> | String | Az Azure-erőforráscsoport neve a beágyazott logikai alkalmazáshoz |
| <*nested-logic-app-name*> | String | A hívni kívánt logikai alkalmazás neve |
||||

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------|  
| <*header-content*> | JSON-objektum | A hívással küldendő fejlécek | 
| <*body-content*> | JSON-objektum | A hívással küldendő üzenet tartalma | 
||||

*Kimenetek*

A művelet kimenetei a beágyazott logikai alkalmazás válasza alapján változnak. Ha a beágyazott logikai alkalmazás nem tartalmaz válasz műveletet, a kimenetek üresek.

*Példa*

A "Start_search" művelet sikeres befejezése után a munkafolyamat-művelet definíciója egy "Get_product_information" nevű másik logikai alkalmazást hív meg, amely a megadott bemeneteken halad át: 

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Munkafolyamat-művelet részleteinek ellenőrzése

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach művelet

Ez a Looping művelet egy tömbön keresztül ismétli meg a műveleteket, és minden egyes tömb elemen végrehajtja a műveleteket. Alapértelmezés szerint a "for each" ciklusok párhuzamosan, legfeljebb maximális számú hurkot futtatnak. Ehhez a maximumhoz lásd: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ismerje meg [, hogyan hozhat létre "for each" hurkokat](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Kötelező* 

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*művelet – 1... n*> | String | Az egyes tömb elemein futó műveletek nevei | 
| <*művelet-definíció-1... n*> | JSON-objektum | A-t futtató műveletek definíciói | 
| <*for-each-expression*> | String | A megadott tömb minden elemére hivatkozó kifejezés | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*száma*> | Integer | Alapértelmezés szerint az "minden" hurok-iteráció egy időben fut, vagy párhuzamosan az [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha módosítani szeretné ezt a korlátot úgy, hogy új <*count*> értéket állít be, tekintse meg a ["minden" hurok egyidejűségének módosítása](#change-for-each-concurrency)című témakört. | 
| <*művelet – beállítás*> | String | Ha egymás után párhuzamosan szeretné futtatni a "minden" ciklust, állítsa be a <*művelet – lehetőség*> `Sequential` vagy <*darabszám*> a értékre `1`, de mindkettő nem lehet. További információ: ["az egyes ciklusok egymás utáni futtatása](#sequential-for-each)". | 
|||| 

*Példa*

Ez a "minden" ciklushoz e-mailt küld a tömb minden eleméhez, amely egy bejövő e-mailből tartalmaz mellékleteket. A hurok egy e-mailt küld, amely tartalmazza a mellékletet, és a mellékletet felülvizsgáló személynek.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Ha csak olyan tömböt szeretne megadni, amely kimenetként lett átadva az triggerből, a kifejezés beolvassa a <*Array-name*> tömböt az trigger törzsében. Ha a tömb nem létezik, a kifejezés a `?` következő operátort használja:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>If művelet

Ez a művelet, amely *feltételes utasítás*, kiértékel egy kifejezést, amely egy feltételt jelöl, és egy másik ágat futtat, attól függően, hogy a feltétel igaz vagy hamis. Ha a feltétel igaz, a feltétel "sikeres" állapottal van megjelölve. Útmutató [feltételes utasítások létrehozásához](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*feltétel*> | JSON-objektum | A feltétel, amely lehet egy kifejezés, hogy kiértékelje | 
| <*művelet – 1*> | JSON-objektum | A < feltételnél futtatandóművelet > igaz értékre való kiértékelése | 
| <*action-definition*> | JSON-objektum | A művelet definíciója | 
| <*művelet – 2*> | JSON-objektum | A < feltételnél futtatandóművelet, > hamis értéket ad vissza | 
|||| 

A vagy `actions` `else` az objektumok műveletei a következő állapotokat kapják meg:

* "Sikeres", ha futnak és sikeresek
* A "sikertelen" művelet futtatásakor és meghibásodásakor
* "Kihagyva", ha a megfelelő ág nem fut

*Példa*

Ez az állapot azt adja meg, hogy ha az egész szám változó értéke nullánál nagyobb, a munkafolyamat ellenőrzi a webhelyet. Ha a változó értéke nulla vagy kevesebb, a munkafolyamat egy másik webhelyet keres.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Feltételek használata kifejezések

Íme néhány példa, amely bemutatja, hogyan használhatja a kifejezéseket a feltételekben:
  
| JSON | Eredmény | 
|------|--------| 
| "kifejezés": "@parameters(" <*hasSpecialAction*> ")" | Csak logikai kifejezések esetén a feltétel minden olyan értékre érvényes, amely az igaz értéket adja vissza. <p>Ha más típusú logikai értéket szeretne átalakítani, használja a következő `empty()` függvényeket: vagy `equals()`. | 
| "kifejezés": "@greater(műveletek (" <*művelet*> "). output. Value, parameters (' <*küszöbérték*> ')) | Az összehasonlító függvények esetében a művelet csak akkor fut le, ha <*művelet*kimenete > nagyobb, mint a <*küszöbérték*> érték. | 
| "Expression": "@or(nagyobb (művelet (" <*művelet*> "). output. Value, parameters (" <*küszöbérték*> ")), kevesebb (művelet (" <*same-Action*> "). output. Value, 100)" | A Logic functions és a beágyazott logikai kifejezések létrehozásakor a művelet akkor fut le, ha <*művelet*kimenete > nagyobb, mint a <*küszöbérték*> értéke vagy a 100. | 
| "kifejezés": "@equals(hossz (művelet (" <*művelet*> "). outputs. errors), 0))" | A Array függvények segítségével ellenőrizheti, hogy a tömb tartalmaz-e elemeket. A művelet akkor fut le `errors` , amikor a tömb üres. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Hatókör művelet

Ez a művelet logikailag csoportosítjaa műveleteket hatókörökre, amelyek az adott hatókör műveleteinek befejezését követően kapják meg a saját állapotukat. Ezután a hatókör állapota alapján meghatározhatja, hogy más műveletek futnak-e. Megtudhatja [, hogyan hozhat létre hatóköröket](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------|  
| <*belső művelet – 1... n*> | JSON-objektum | Egy vagy több olyan művelet, amely a hatókörön belül fut |
| <*művelet – bemenetek*> | JSON-objektum | Az egyes műveletek bemenetei |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Kapcsoló művelet

Ez a művelet, más néven *switch-utasítás*, más műveleteket is szervez az *esetekre*, és egy értéket rendel az egyes esetekhez, kivéve az alapértelmezett esetet, ha van ilyen. A munkafolyamat futtatásakor a **kapcsoló** művelet összehasonlítja az értéket egy kifejezésből, objektumból vagy jogkivonatból az egyes esetekben megadott értékek alapján. Ha a **kapcsoló** művelet megkeresi a megfelelő esetet, a munkafolyamat csak az adott eset műveleteit futtatja. A **kapcsoló** műveletének minden futtatásakor vagy csak egy egyező eset létezik, vagy nem létezik egyezés. Ha nem létezik egyezés, a **kapcsoló** művelet az alapértelmezett műveleteket futtatja. Útmutató [a Switch utasítások létrehozásához](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*expression-object-or-token*> | Változó | A kiértékelni kívánt kifejezés, JSON-objektum vagy jogkivonat | 
| <*művelet – név*> | String | Az egyező esethez futtatandó művelet neve | 
| <*action-definition*> | JSON-objektum | A megfelelő esethez futtatandó művelet definíciója | 
| <*egyező érték*> | Változó | A kiértékelt eredménnyel összehasonlítandó érték | 
|||| 

*Választható*

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*default-action-name*> | String | A futtatandó alapértelmezett művelet neve, ha nem létezik egyező eset | 
| <*default-action-definition*> | JSON-objektum | Annak a műveletnek a definíciója, amely akkor fut, ha nem létezik egyező eset | 
|||| 

*Példa*

Ez a műveleti definíció kiértékeli, hogy a jóváhagyási kérelem e-mail-címére válaszoló személy a "jóváhagyás" vagy az "elutasítás" lehetőséget választotta-e. Ezen döntés alapján a **kapcsoló** végrehajtja a megfelelő eset műveleteit, azaz egy másik e-mailt küld a válaszadónak, de az egyes esetekben eltérő szöveggel. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>A műveletig

Ez a hurok-művelet olyan műveleteket tartalmaz, amelyek addig futnak, amíg a megadott feltétel nem teljesül. A hurok az utolsó lépésként ellenőrzi a feltételt az összes többi művelet futtatása után. Az `"actions"` objektumban több műveletet is megadhat, és a műveletnek legalább egy korlátot meg kell határoznia. Megtudhatja [, hogyan hozhat létre "ig" hurkokat](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Value | Type | Leírás | 
|-------|------|-------------| 
| <*művelet – név*> | String | A hurokon belül futtatni kívánt művelet neve | 
| <*művelet típusa*> | String | A futtatni kívánt Művelettípus | 
| <*művelet – bemenetek*> | Különböző | A futtatandó művelet bemenetei | 
| <*feltétel*> | String | Annak a feltételnek vagy kifejezésnek a kiértékelése, amelyet a hurok befejezésének befejezése után kell kiértékelni | 
| <*loop-count*> | Integer | A művelet által futtatható legtöbb hurok maximális száma. Az alapértelmezett `count` érték a 60. | 
| <*hurok – időtúllépés*> | String | A hurok futtatásának leghosszabb időtartamára vonatkozó korlát. Az alapértelmezett `timeout` `PT1H`érték a, amely a szükséges [ISO 8601-formátum](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Példa*

Ez a hurok-műveleti definíció HTTP-kérést küld a megadott URL-címre, amíg teljesül a következő feltételek valamelyike: 

* A kérelem az "200 OK" állapotkód válaszát kapja meg.
* A hurok 60 alkalommal futott.
* A hurok egy órára futott.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhookok és előfizetések

A webhook-alapú eseményindítók és műveletek nem rendszeresen keresik a végpontokat, de az adott eseményekre vagy a végpontokon lévő adatpontokra várnak. Ezek az eseményindítók és műveletek előfizetnek a végpontokra egy visszahívási *URL-cím* megadásával, amelyben a végpont választ küldhet.

A `subscribe` hívás akkor történik meg, amikor a munkafolyamat bármilyen módon megváltozik, például a hitelesítő adatok megújításakor, vagy ha a bemeneti paraméterek egy triggerre vagy műveletre változnak. Ez a hívás ugyanazokat a paramétereket használja, mint a szabványos HTTP-műveletek. 

A `unsubscribe` hívás automatikusan történik, ha egy művelet elvégzi az indítást vagy a műveletet, például:

* Az trigger törlése vagy letiltása. 
* A munkafolyamat törlése vagy letiltása. 
* Az előfizetés törlése vagy letiltása. 

A hívások támogatásához a `@listCallbackUrl()` kifejezés egy egyedi "visszahívási URL-címet" ad vissza az triggerhez vagy művelethez. Ez az URL-cím a szolgáltatás REST APIét használó végpontok egyedi azonosítóját jelöli. A függvény paraméterei megegyeznek a webhook-triggerrel vagy-művelettel.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Aszinkron időtartam módosítása

Az eseményindítók és műveletek esetében a `limit.timeout` tulajdonság hozzáadásával korlátozhatja az aszinkron minta időtartamát egy adott időintervallumra. Így ha a művelet nem fejeződött be, amikor az intervallum lejár, a művelet állapota a `Cancelled` `ActionTimedOut` kóddal együtt van megjelölve. A `timeout` tulajdonság [ISO 8601 formátumot](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)használ. 

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Futásidejű konfigurációs beállítások

Az eseményindítók és műveletek `runtimeConfiguration` alapértelmezett futásidejű viselkedését az eseményindító vagy a művelet definíciója alapján módosíthatja.

| Tulajdonság | Típus | Leírás | Trigger vagy művelet | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Módosítsa az egy időben vagy párhuzamosan futtatható munkafolyamat-példányok számának [*alapértelmezett korlátját*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) . Ez az érték segít korlátozni a háttérrendszer által fogadott kérelmek számát. <p>Ha a `runs` tulajdonságot `1` úgy állítja be, hogy az ugyanúgy `operationOptions` működjön, `SingleInstance`mint a tulajdonság beállítása. Beállíthatja az egyik tulajdonságot, de mindkettőt nem. <p>Az alapértelmezett korlát módosításához lásd: [trigger egyidejűségének módosítása](#change-trigger-concurrency) vagy [példányok elindítása szekvenciálisan](#sequential-trigger). | Minden trigger | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Módosítsa az [*alapértelmezett korlátot*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) azon munkafolyamat-példányok számánál, amelyek a futtatásra várnak, ha a munkafolyamat már futtatja az egyidejű példányok maximális számát. Módosíthatja a egyidejűségi korlátot a `concurrency.runs` tulajdonságban. <p>Az alapértelmezett korlát módosításához tekintse meg a [várakozó futtatások korlátjának módosítása](#change-waiting-runs)című témakört. | Minden trigger | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Módosítsa a "for each" ciklusok [*alapértelmezett korlátját*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) , amely egy időben vagy párhuzamosan futtatható. <p>Ha a `repetitions` tulajdonságot `1` úgy állítja be, hogy az ugyanúgy `operationOptions` működjön, `SingleInstance`mint a tulajdonság beállítása. Beállíthatja az egyik tulajdonságot, de mindkettőt nem. <p>Az alapértelmezett korlát módosításához tekintse meg [](#change-for-each-concurrency) az egyes "egyidejűségek és futtatások" módosítását egymás [utáni ciklusokban](#sequential-for-each). | Művelet: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Integer | A (z) és a (z) rendszerű tördelést támogató műveletek esetében ez az érték határozza meg a beolvasandó eredmények *minimális* számát. <p>A tördelés bekapcsolásához tekintse meg a [tömeges adatok, elemek vagy eredmények beolvasása a tördelés használatával című cikket](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) . | Művelet: Változatos |
| `runtimeConfiguration.secureData.properties` | Array | Számos eseményindító és művelet esetén ezek a beállítások elrejtik a logikai alkalmazás futtatási előzményeinek bemeneteit, kimeneteit vagy mindkettőt. <p>Az adatok védelméről lásd: [bemenetek és kimenetek elrejtése a futtatási előzményekből](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | A legtöbb eseményindító és művelet |
| `runtimeConfiguration.staticResult` | JSON-objektum | A és a [statikus eredményhalmaz](../logic-apps/test-logic-apps-mock-data-static-results.md) beállítását támogató műveletek esetében az objektum a `staticResult` következő attribútumokkal rendelkezik: <p>- `name`, amely az aktuális művelet statikus eredményének definíciójának nevére hivatkozik, amely a `staticResults` logikai alkalmazás `definition` munkafolyamatának attribútumában megjelenő attribútumon belül jelenik meg. További információ: [statikus eredmények – séma-hivatkozás a munkafolyamat](../logic-apps/logic-apps-workflow-definition-language.md#static-results)-definíciós nyelvhez. <p> - `staticResultOptions`, amely meghatározza, hogy az aktuális `Enabled` művelethez statikus eredmények vagy nem. <p>A statikus eredmények bekapcsolásához lásd: [Logic Apps-alkalmazások tesztelése statikus eredmények beállításával](../logic-apps/test-logic-apps-mock-data-static-results.md) | Művelet: Változatos |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Művelet beállításai

Megváltoztathatja az eseményindítók és műveletek alapértelmezett viselkedését az `operationOptions` eseményindító vagy a művelet definíciójában található tulajdonsággal.

| Művelet beállítása | Type | Leírás | Trigger vagy művelet | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | String | Aszinkron módon futtassa a HTTP-alapú műveleteket szinkronban. <p><p>A beállítás megadásához tekintse meg a [Műveletek szinkron](#asynchronous-patterns)módon történő futtatását ismertető témakört. | Műveletek <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Válasz](#response-action) | 
| `OptimizedForHighThroughput` | String | Módosítsa a műveletek végrehajtásának [alapértelmezett korlátját](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) 5 percenként a [maximális korlátra](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>A beállítás megadásához lásd: [Futtatás nagy teljesítményű módban](#run-high-throughput-mode). | Minden művelet | 
| `Sequential` | String | A "minden" hurok-iterációhoz egyenként futtassa a parancsot egyszerre, nem pedig párhuzamosan. <p>Ez a beállítás ugyanúgy működik, mint a `runtimeConfiguration.concurrency.repetitions` `1`tulajdonság beállítása. Beállíthatja az egyik tulajdonságot, de mindkettőt nem. <p><p>A beállítás megadásához tekintse meg egymás [után a "minden" ciklus futtatását](#sequential-for-each).| Művelet: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | String | Minden egyes logikai alkalmazás-példány esetében futtassa az triggert, majd várjon, amíg a korábban aktív Futtatás befejeződik, mielőtt aktiválja a következő Logic app-példányt. <p><p>Ez a beállítás ugyanúgy működik, mint a `runtimeConfiguration.concurrency.runs` `1`tulajdonság beállítása. Beállíthatja az egyik tulajdonságot, de mindkettőt nem. <p>A beállítás megadásához tekintse meg a [példányok egymás utáni](#sequential-trigger)elindítását ismertető témakört. | Minden trigger | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Az trigger egyidejűségének módosítása

Alapértelmezés szerint a Logic app-példányok egyszerre, párhuzamosan vagy párhuzamosan futnak az [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Így minden eseményindító-példány az előző munkafolyamat-példány futása előtt következik be. Ez a korlát segíti a háttérrendszer által fogadott kérelmek számának szabályozását. 

Az alapértelmezett korlát módosításához használhatja a Code View Editort vagy a Logic apps designert, mert a tervezőn keresztüli egyidejűségi beállítás módosításakor a rendszer hozzáadja `runtimeConfiguration.concurrency.runs` vagy frissíti a tulajdonságot az alapul szolgáló trigger definíciójában, és fordítva. Ez a tulajdonság határozza meg a párhuzamosan futtatható munkafolyamat-példányok maximális számát. 

> [!NOTE] 
> Ha úgy állítja be a triggert, hogy a tervező vagy a Code View Editor használatával szekvenciálisan fusson, ne állítsa az `operationOptions` trigger `SingleInstance` tulajdonságát a kód nézet szerkesztőjére. Ellenkező esetben érvényesítési hiba jelenik meg. További információért lásd a [példányok egymás utáni](#sequential-trigger)elindítását ismertető témakört.

#### <a name="edit-in-code-view"></a>Szerkesztés kód nézetben 

Az alapul szolgáló trigger definíciójában adja hozzá vagy frissítse `runtimeConfiguration.concurrency.runs` a tulajdonságot egy, `1` a `50` és a között lévő értékre.

Íme egy példa, amely az egyidejű futtatásokat 10 példányra korlátozza:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Szerkesztés Logic Apps Designerben

1. Az trigger jobb felső sarkában kattintson a három pontra (...), majd válassza a **Beállítások**lehetőséget.

2. A **Egyidejűség vezérlőelem**alatt állítsa be **a**korlátot be értékre. 

3. Húzza a **párhuzamossági fok** csúszkát a kívánt értékre. A logikai alkalmazás szekvenciális futtatásához húzza a csúszkát **1**értékre.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Az egyes "párhuzamosságok" módosítása

Alapértelmezés szerint az "minden" hurok-iteráció egy időben, vagy párhuzamosan, az [alapértelmezett korlátig](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)fut. Az alapértelmezett korlát módosításához használhatja a Code View Editort vagy a Logic apps designert, mert a tervezőn keresztüli egyidejűségi beállítás módosításakor a rendszer hozzáadja `runtimeConfiguration.concurrency.repetitions` vagy frissíti a tulajdonságot a mögöttes "for each" művelet definíciójában, és fordítva. Ez a tulajdonság határozza meg a párhuzamosan futtatható ismétlések maximális számát.

> [!NOTE] 
> Ha a "for each" műveletet úgy állítja be, hogy a tervező vagy a Code View Editor használatával egymás után fusson, ne állítsa a művelet `operationOptions` `Sequential` tulajdonságát a kód nézet szerkesztőjére. Ellenkező esetben érvényesítési hiba jelenik meg. További információ: ["az egyes ciklusok egymás utáni futtatása](#sequential-for-each)".

#### <a name="edit-in-code-view"></a>Szerkesztés kód nézetben 

Az egyes definíciók alapjául szolgáló "for each" kifejezésben adja `runtimeConfiguration.concurrency.repetitions` hozzá vagy frissítse a tulajdonságot `50` egy `1` értékre és a bezárólag. 

Íme egy példa, amely az egyidejű futtatásokat 10 iterációra korlátozza:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Szerkesztés Logic Apps Designerben

1. A **minden** műveletnél a jobb felső sarokban kattintson a három pontra (...), majd válassza a **Beállítások**lehetőséget.

2. A **Egyidejűség vezérlőelem**alatt állítsa be **a** **Egyidejűség** -vezérlést be értékre. 

3. Húzza a **párhuzamossági fok** csúszkát a kívánt értékre. A logikai alkalmazás szekvenciális futtatásához húzza a csúszkát **1**értékre.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Várakozó futtatások korlátjának módosítása

Alapértelmezés szerint a Logic app-munkafolyamatok példányai egyszerre, egyidejűleg vagy párhuzamosan futnak az [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Minden eseményindító-példány a korábban aktív munkafolyamat-példány futásának befejeződése előtt következik be. Bár ezt az [alapértelmezett korlátot](#change-trigger-concurrency)megváltoztathatja, ha a munkafolyamat-példányok száma eléri az új egyidejűségi korlátot, minden más új példánynak várnia kell a futtatásra. 

A megvárható futtatások száma egy [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)is rendelkezik, amelyet megadhat. Azonban miután a logikai alkalmazás elérte a várakozási korlátot, a Logic Apps motor már nem fogad új futtatásokat. A kérelem-és webhook-eseményindítók 429 hibát adnak vissza, és az ismétlődő eseményindítók elkezdik kihagyni a lekérdezési kísérleteket.

A várakozó futtatások alapértelmezett korlátjának módosításához a mögöttes trigger definíciójában adja hozzá a `runtimeConfiguration.concurency.maximumWaitingRuns` (z) és `100`közötti `0` értéket a tulajdonsághoz. 

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Példányok egymás utáni elindítása

Ha csak az előző példány futtatása után szeretné futtatni az egyes logikai alkalmazások munkafolyamat-példányait, állítsa az indítást egymás után. Használhatja a Code View Editort vagy a Logic apps designert, mert a tervezőn keresztüli egyidejűségi beállítás módosításakor az `runtimeConfiguration.concurrency.runs` alapul szolgáló trigger definíciójában is hozzáadja vagy frissíti a tulajdonságot, és fordítva. 

> [!NOTE] 
> Ha úgy állítja be egy triggert, hogy szekvenciálisan fusson a tervező vagy a kód nézet szerkesztőjével, ne állítsa be `operationOptions` az trigger `Sequential` tulajdonságát a kód nézet szerkesztőjébe. Ellenkező esetben érvényesítési hiba jelenik meg. 

#### <a name="edit-in-code-view"></a>Szerkesztés kód nézetben

Az trigger definíciójában állítsa be a tulajdonságok egyikét, de ne mindkettőt. 

Állítsa a `runtimeConfiguration.concurrency.runs` `1`tulajdonságot a következőre:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*vagy*

Állítsa a `operationOptions` `SingleInstance`tulajdonságot a következőre:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Szerkesztés Logic Apps Designerben

1. Az trigger jobb felső sarkában kattintson a három pontra (...), majd válassza a **Beállítások**lehetőséget.

2. A **Egyidejűség vezérlőelem**alatt állítsa be **a**korlátot be értékre. 

3. Húzza a **párhuzamossági fok** csúszkát a számra `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Az "összes" ciklus futtatása egymás után

Ha "for each" ciklusos iterációt szeretne futtatni, csak az előző iteráció futtatása után, állítsa a "for each" műveletet egymás után futtatva. Használhatja a Code View Editort vagy a Logic apps designert, mert a művelet egyidejűségének a tervezőn keresztül történő módosítása a `runtimeConfiguration.concurrency.repetitions` tulajdonságot a mögöttes művelet definíciójában is hozzáadja vagy frissíti, és fordítva. 

> [!NOTE] 
> Ha egy "for each" műveletet állít be, amely egymás után fut a tervező vagy a kód nézet szerkesztőjével, ne állítsa a művelet `operationOptions` `Sequential` tulajdonságát a kód nézet szerkesztőjére. Ellenkező esetben érvényesítési hiba jelenik meg. 

#### <a name="edit-in-code-view"></a>Szerkesztés kód nézetben

A művelet definíciójában állítsa be a tulajdonságok egyikét, de ne mindkettőt. 

Állítsa a `runtimeConfiguration.concurrency.repetitions` `1`tulajdonságot a következőre:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*vagy*

Állítsa a `operationOptions` `Sequential`tulajdonságot a következőre:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Szerkesztés Logic Apps Designerben

1. Az **egyes** műveletek jobb felső sarkában kattintson a három pontra (...), majd válassza a **Beállítások**lehetőséget.

2. A **Egyidejűség vezérlőelem**alatt állítsa be **a** **Egyidejűség** -vezérlést be értékre. 

3. Húzza a **párhuzamossági fok** csúszkát a számra `1`. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Műveletek párhuzamos futtatása

Alapértelmezés szerint az összes HTTP-alapú művelet a normál aszinkron műveleti mintát követi. Ez a minta azt adja meg, hogy amikor egy HTTP-alapú művelet kérelmet küld a megadott végpontnak, a távoli kiszolgáló "202 elfogadott" választ küld vissza. Ez a válasz azt jelenti, hogy a kiszolgáló elfogadta a feldolgozásra irányuló kérelmet. A Logic Apps motor ellenőrzi a válasz Location fejlécében megadott URL-címet, amíg a feldolgozás leáll, ami nem 202-válasz.

Azonban a kérelmek időtúllépési korláttal rendelkeznek, így a hosszan futó műveletek esetében letilthatja az aszinkron viselkedést úgy, hogy `operationOptions` `DisableAsyncPattern` hozzáadja a tulajdonságot a művelet bemenetéhez, és beállítja azt.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Futtatás nagy teljesítményű módban

Egyetlen logikai alkalmazás definíciójában az 5 percenként végrehajtandó műveletek száma [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)rendelkezik. Ha ezt a korlátot a lehető [legnagyobb](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) értékre szeretné `operationOptions` emelni, `OptimizedForHighThroughput`állítsa a tulajdonságot a következőre:. Ezzel a beállítással a logikai alkalmazást "nagy átviteli sebesség" módba helyezheti. 

> [!NOTE]
> A nagy adatátviteli mód előzetes verzióban érhető el. Szükség szerint több logikai alkalmazásban is terjesztheti a számítási feladatokat.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>HTTP-eseményindítók és-műveletek hitelesítése

A HTTP-végpontok különböző típusú hitelesítést támogatnak. A következő HTTP-eseményindítók és műveletek hitelesítését állíthatja be:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)

A következőkben állíthatja be a beállított hitelesítés típusát:

* [Alapszintű hitelesítés](#basic-authentication)
* [Ügyféltanúsítvány-alapú hitelesítés](#client-certificate-authentication)
* [Azure Active Directory (Azure AD) OAuth-hitelesítés](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> Ügyeljen arra, hogy megvédje a logikai alkalmazás munkafolyamat-definíciója által kezelt bizalmas adatokat. Használjon biztonságos paramétereket, és szükség szerint kódolja az adatok mennyiségét. A paraméterek használatával és biztonságossá tételével kapcsolatos további információkért lásd: [a logikai alkalmazás biztonságossá tétele](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Alapszintű hitelesítés

Az Azure Active Directory használatával történő [egyszerű hitelesítéshez](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) az trigger vagy a művelet definíciója tartalmazhat `authentication` egy JSON-objektumot, amely a következő táblázatban megadott tulajdonságokkal rendelkezik. Ha futásidőben szeretné elérni a `@parameters('parameterName')` paramétereket, használhatja azt a kifejezést, amelyet a munkafolyamat-definíciós [nyelv](https://aka.ms/logicappsdocs)biztosít. 

| Tulajdonság | Kötelező | Value | Leírás | 
|----------|----------|-------|-------------| 
| **type** | Igen | Alapvető | A használni kívánt hitelesítési típus, amely itt az "alapszintű" | 
| **felhasználónév** | Igen | "@parameters(" userNameParam ")" | A cél szolgáltatási végponthoz való hozzáférés hitelesítéséhez használt Felhasználónév |
| **jelszó** | Igen | "@parameters(" passwordParam ")" | A cél szolgáltatási végponthoz való hozzáférés hitelesítéséhez használt jelszó |
||||| 

Ebben a példában a http-művelet definíciójában a `Basic` szakasz a `authentication` hitelesítést határozza meg. A paraméterek használatával és biztonságossá tételével kapcsolatos további információkért lásd: [a logikai alkalmazás biztonságossá tétele](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

> [!IMPORTANT]
> Ügyeljen arra, hogy megvédje a logikai alkalmazás munkafolyamat-definíciója által kezelt bizalmas adatokat. Használjon biztonságos paramétereket, és szükség szerint kódolja az adatok mennyiségét. A paraméterek biztonságossá tételével kapcsolatos további információkért lásd [a logikai alkalmazás biztonságossá tétele](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)című témakört.

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Ügyféltanúsítvány-alapú hitelesítés

A [tanúsítványalapú hitelesítés](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) Azure Active Directory használatával az trigger vagy a művelet definíciója tartalmazhat egy `authentication` JSON-objektumot, amely a következő táblázatban megadott tulajdonságokkal rendelkezik. Ha futásidőben szeretné elérni a `@parameters('parameterName')` paramétereket, használhatja azt a kifejezést, amelyet a munkafolyamat-definíciós [nyelv](https://aka.ms/logicappsdocs)biztosít. A használható Ügyféltanúsítványok számának korlátozásai: [Azure Logic apps korlátozásai és konfigurálása](../logic-apps/logic-apps-limits-and-config.md).

| Tulajdonság | Kötelező | Value | Leírás |
|----------|----------|-------|-------------|
| **type** | Igen | ClientCertificate | A SSL-(SSL-) Ügyféltanúsítványok esetében használandó hitelesítési típus. Míg az önaláírt tanúsítványok támogatottak, az önaláírt tanúsítványok nem támogatottak az SSL-hez. |
| **pfx** | Igen | "@parameters(" pfxParam ") | A Base64 kódolású tartalom egy személyes információcsere (PFX) fájlból |
| **jelszó** | Igen | "@parameters(" passwordParam ")" | A PFX-fájl eléréséhez használt jelszó |
||||| 

Ebben a példában a http-művelet definíciójában a `ClientCertificate` szakasz a `authentication` hitelesítést határozza meg. A paraméterek használatával és biztonságossá tételével kapcsolatos további információkért lásd: [a logikai alkalmazás biztonságossá tétele](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Ügyeljen arra, hogy megvédje a logikai alkalmazás munkafolyamat-definíciója által kezelt bizalmas adatokat. Használjon biztonságos paramétereket, és szükség szerint kódolja az adatok mennyiségét. A paraméterek biztonságossá tételével kapcsolatos további információkért lásd [a logikai alkalmazás biztonságossá tétele](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)című témakört.

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Azure Active Directory (AD) OAuth-hitelesítés

Az [Azure ad OAuth-hitelesítés](../active-directory/develop/authentication-scenarios.md)esetében az trigger vagy a művelet definíciója `authentication` tartalmazhat egy JSON-objektumot, amely az alábbi táblázatban megadott tulajdonságokkal rendelkezik. Ha futásidőben szeretné elérni a `@parameters('parameterName')` paramétereket, használhatja azt a kifejezést, amelyet a munkafolyamat-definíciós [nyelv](https://aka.ms/logicappsdocs)biztosít.

| Tulajdonság | Kötelező | Value | Leírás |
|----------|----------|-------|-------------|
| **type** | Igen | `ActiveDirectoryOAuth` | A használni kívánt hitelesítési típus, amely az Azure AD-OAuth "ActiveDirectoryOAuth" |
| **hitelesítésszolgáltató** | Nem | <*URL-cím-a-Authority-token-kiállító*> | A hitelesítési jogkivonatot biztosító szolgáltató URL-címe |
| **Bérlő** | Igen | <*tenant-ID*> | Az Azure AD-bérlő bérlői azonosítója |
| **célközönség** | Igen | <*erőforrás-engedélyezés*> | Az engedélyezéshez használni kívánt erőforrás, például:`https://management.core.windows.net/` |
| **clientId** | Igen | <*client-ID*> | Az engedélyezést kérő alkalmazás ügyfél-azonosítója |
| **credentialType** | Igen | "Tanúsítvány" vagy "titkos" | Az ügyfél által a hitelesítés kérelmezéséhez használt hitelesítő adat típusa. Ez a tulajdonság és az érték nem jelenik meg az alapul szolgáló definícióban, de meghatározza a hitelesítő adatok típusához szükséges paramétereket. |
| **pfx** | Igen, csak a "tanúsítvány" hitelesítő adatok típusa esetén | "@parameters(" pfxParam ") | A Base64 kódolású tartalom egy személyes információcsere (PFX) fájlból |
| **jelszó** | Igen, csak a "tanúsítvány" hitelesítő adatok típusa esetén | "@parameters(" passwordParam ")" | A PFX-fájl eléréséhez használt jelszó |
| **titkos** | Igen, csak a "titkos" hitelesítő adatok típusához | "@parameters(" secretParam ")" | Az engedélyezést kérő ügyfél titka |
|||||

Ebben a példában a http-művelet definíciójában a `ActiveDirectoryOAuth` szakasz a `authentication` hitelesítés és a "titkos" hitelesítő adatok típusát adja meg. A paraméterek használatával és biztonságossá tételével kapcsolatos további információkért lásd: [a logikai alkalmazás biztonságossá tétele](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
         "audience": "https://management.core.windows.net/",
         "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Ügyeljen arra, hogy megvédje a logikai alkalmazás munkafolyamat-definíciója által kezelt bizalmas adatokat. Használjon biztonságos paramétereket, és szükség szerint kódolja az adatok mennyiségét. A paraméterek biztonságossá tételével kapcsolatos további információkért lásd [a logikai alkalmazás biztonságossá tétele](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)című témakört.

## <a name="next-steps"></a>További lépések

* További információ a [munkafolyamat](../logic-apps/logic-apps-workflow-definition-language.md) -definíciós nyelvről
