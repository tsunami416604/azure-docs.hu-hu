---
title: Az eseményindító és művelet típust referencia – Azure Logic Apps |} A Microsoft Docs
description: Ismerje meg az eseményindító és művelet típusok az Azure Logic Appsben leírtak szerint a munkafolyamat-definíciós nyelvséma
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/22/2018
ms.openlocfilehash: 27c074b12d2b151015e6946c483302387726dfc5
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190822"
---
# <a name="trigger-and-action-types-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Az Azure Logic Apps munkafolyamat-definíciós nyelv eseményindító és művelet típusok referenciája

A [Azure Logic Apps](../logic-apps/logic-apps-overview.md), az összes logikai alkalmazások munkafolyamataiba kezdődnie eseményindítók műveletek követ. Ez a cikk ismerteti az eseményindító és művelet is használhatja a feladatok, folyamatok és munkafolyamatok automatizálása a logic apps létrehozásakor. Akkor is logikai alkalmazás munkafolyamatok létrehozása a Logic Apps Designerben vizuálisan vagy az alapul szolgáló munkafolyamat-definíciókhoz készítésével a [munkafolyamat-definíciós nyelv](../logic-apps/logic-apps-workflow-definition-language.md). A logic apps vagy az Azure Portalon vagy a Visual Studióban is létrehozhat. A teljes munkafolyamathoz, beleértve a trigger és műveletek, az alapul szolgáló definíciójának Javascript Object Notation (JSON) használja.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Triggerek áttekintése

Minden logikai alkalmazás egy eseményindítóval, amely meghatározza a hívásokat, hozza létre, és indítsa el a logikai alkalmazás munkafolyamatának elindításához. Az alábbiakban az általános eseményindító kategóriák:

* A *lekérdezési* eseményindító, amely rendszeres időközönként ellenőrzi egy szolgáltatásvégpont

* A *leküldéses* eseményindító, amely létrehoz egy végpontot egy előfizetést, és biztosít egy *visszahívási URL-Címének* , a végpont értesítheti az eseményindító, ha a megadott esemény történik, vagy adatok érhetők el. Az eseményindító majd megvárja, amíg a végpont válasz elsőre előtt. 

Eseményindítók rendelkezik a legfelső elemeket, bár egyes nem kötelező:  
  
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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*eseményindító-neve*> | Karakterlánc | Az eseményindító neve | 
| <*eseményindító-típus*> | Karakterlánc | A például a "Http" vagy az "ApiConnection" trigger típusa | 
| <*eseményindító-bemenetek*> | JSON-objektum | Az eseményindító viselkedését meghatározó bemenetei | 
| <*időegység*> | Karakterlánc | Az időegység, amely leírja, hogy milyen gyakran az eseményindító aktiválódik: "A második", "Minute", "Hour", "Day", "Week", "Month" | 
| <*szám, idő egységek*> | Egész szám | A gyakoriságát, és várja meg, amíg az eseményindítót újra mértékegységét száma alapján, amely meghatározza, hogy milyen gyakran az eseményindítót hodnotu <p>Az alábbiakban a minimális és maximális időközönként: <p>– Hónap: 1 – 16 hónap </br>– Nap: 1-500 nap </br>– Óra: 1 – 12 000 óra </br>– Perc: 1 – 72,000 perc </br>-Másodperc: 1 – 9,999,999 másodperc<p>Például a időköz 6, és a gyakoriság "Month", az ismétlődés esetén minden hatodik hónapban. | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*tömb-az-feltételek*> | Tömb | Egy tömb, amely tartalmaz egy vagy több [feltételek](#trigger-conditions) , határozza meg, hogy a munkafolyamat futtatásához. Csak a triggerek esetében érhető el. | 
| <*futásidejű-config-beállítások*> | JSON-objektum | Módosíthatja a trigger működését beállításával `runtimeConfiguration` tulajdonságait. További információkért lásd: [modul konfigurációs beállítások](#runtime-config-options). | 
| <*splitOn-kifejezés*> | Karakterlánc | Eseményindítók, amely egy tömböt adnak vissza, adjon meg egy kifejezést, amely [bontja vagy *debatches* ](#split-on-debatch) tömb elemeinek be több munkafolyamat-példány a feldolgozáshoz. | 
| <*művelet – beállítás*> | Karakterlánc | Az alapértelmezett viselkedés módosításához állítsa a `operationOptions` tulajdonság. További információkért lásd: [műveleti beállítások](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Eseményindító-típusok listája

Minden trigger rendelkezik egy másik felületet és az eseményindító viselkedését meghatározó bemenetei között. 

### <a name="built-in-triggers"></a>Beépített eseményindítók

| Eseményindító típusa | Leírás | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Ellenőrzi, vagy *polls* bármely végpont. Ez a végpont "202-es" aszinkron minta használatával, vagy egy tömb visszaküldi egy adott eseményindító szerződés meg kell felelnie. | 
| [**HTTPWebhook**](#http-webhook-trigger) | A logikai alkalmazás egy hívható végpontot hoz létre, de regisztrálásához vagy regisztrációjának törlése a megadott URL-hívások. |
| [**Ismétlődés**](#recurrence-trigger) | Akkor következik be, egy meghatározott ütemezés alapján. Beállíthat egy jövőbeli dátumot és időpontot az eseményindító aktiválja. Gyakorisága alapján is megadhat idők és a munkafolyamat futtatásához a napon. | 
| [**Kérelem**](#request-trigger)  | Létrehoz egy hívható végpontot a logikai alkalmazás és más néven "manual" eseményindító. Lásd a [hívása, eseményindító, vagy a HTTP-végpontokat munkafolyamatok beágyazása](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Felügyelt API-eseményindítók

| Eseményindító típusa | Leírás | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Ellenőrzi, vagy *polls* végpont használatával [a Microsoft által felügyelt API-k](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Létrehoz a logikai alkalmazás egy hívható végpont meghívásával [a Microsoft által felügyelt API-k](../connectors/apis-list.md) előfizetés és az előfizetés lemondása. | 
||| 

## <a name="triggers---detailed-reference"></a>Triggerek – részletes útmutató

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection eseményindító  

A trigger ellenőriz vagy *polls* végpont használatával [a Microsoft által felügyelt API-k](../connectors/apis-list.md) úgy a paramétereket az erre az eseményindítóra eltérőek lehetnek a végpont alapján. Sok szakaszt az eseményindító definíciójában nem kötelező. Az eseményindító viselkedése attól függ, szakaszok megtalálhatók-e.

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | Karakterlánc | Az eseményindító neve | 
| <*kapcsolat neve*> | Karakterlánc | A felügyelt API-t a munkafolyamat által használt kapcsolat neve | 
| <*eljárástípus*> | Karakterlánc | A felügyelt API-val való kommunikációhoz HTTP-metódus: "GET", "PUT", "POST", "JAVÍTÁS", "DELETE" | 
| <*API-művelet*> | Karakterlánc | Az API-művelet meghívásához | 
| <*időegység*> | Karakterlánc | Az időegység, amely leírja, hogy milyen gyakran az eseményindító aktiválódik: "A második", "Minute", "Hour", "Day", "Week", "Month" | 
| <*szám, idő egységek*> | Egész szám | A gyakoriságát, és várja meg, amíg az eseményindítót újra mértékegységét száma alapján, amely meghatározza, hogy milyen gyakran az eseményindítót hodnotu <p>Az alábbiakban a minimális és maximális időközönként: <p>– Hónap: 1 – 16 hónap </br>– Nap: 1-500 nap </br>– Óra: 1 – 12 000 óra </br>– Perc: 1 – 72,000 perc </br>-Másodperc: 1 – 9,999,999 másodperc<p>Például a időköz 6, és a gyakoriság "Month", az ismétlődés esetén minden hatodik hónapban. | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*újrapróbálkozási-viselkedés*> | JSON-objektum | Az átmeneti hibákra, amelyek rendelkeznek a 408, a 429-es, és az 5XX állapotkód és a minden csatlakozási kivétel újrapróbálkozási viselkedés testreszabása. További információkért lásd: [újrapróbálkozási szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*lekérdezés-paraméterek*> | JSON-objektum | Hívja meg a lekérdezési paramétereket tartalmazza az API-val. Például a `"queries": { "api-version": "2018-01-01" }` objektumot ad `?api-version=2018-01-01` a hívást. | 
| <*Max-futtatások*> | Egész szám | Alapértelmezés szerint a logikai alkalmazás munkafolyamat-példányok futnak egy időben, vagy a párhuzamos akár a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ez a korlát módosítása egy új beállításával <*száma*> érték, lásd: [módosítása az eseményindító egyidejűségi](#change-trigger-concurrency). | 
| <*várólista-max-futtatások*> | Egész szám | Ha a logikai alkalmazás már fut a példányok maximális száma, amelyet módosíthat alapján a `runtimeConfiguration.concurrency.runs` tulajdonságot használja, minden olyan új futtatások kerüljenek, ennek az üzenetsornak a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Az alapértelmezett korlát módosításához lásd [módosítása várakozási futtatások korlátozza](#change-waiting-runs). | 
| <*splitOn-kifejezés*> | Karakterlánc | Tömbök visszaadó eseményindítók Ez a kifejezés hivatkozik, amelyek akkor létrehozása és futtatása egy munkafolyamat-példány a tömb mindegyik elemén, ahelyett használni egy "mindegyikre" hurkot a tömbben. <p>Például az a kifejezés az eseményindító szövegtörzse belül visszakapott tömbben szereplő elem jelöli: `@triggerbody()?['value']` |
| <*művelet – beállítás*> | Karakterlánc | Az alapértelmezett viselkedés módosításához állítsa a `operationOptions` tulajdonság. További információkért lásd: [műveleti beállítások](#operation-options). | 
||||

*Kimenetek*
 
| Elem | Típus | Leírás |
|---------|------|-------------| 
| A fejlécek | JSON-objektum | A válaszból a fejlécek | 
| törzs | JSON-objektum | A szervezetnek a válaszból | 
| Állapotkód | Egész szám | A válaszból állapotkód | 
|||| 

*Példa*

Az eseményindító definíciójában e-mailek ellenőrzi a Beérkezett fájlok mappa belül minden nap egy olyan Office 365 Outlook-fiók: 

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

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook eseményindító

Ez az eseményindító előfizetési kérelmet küld a végpont használatával egy [a Microsoft által felügyelt API](../connectors/apis-list.md), biztosít egy *visszahívási URL-Címének* , ahol a végpont küldhet a választ, és megvárja a a végpontot kell. További információkért lásd: [végpont előfizetések](#subscribe-unsubscribe).

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*kapcsolat neve*> | Karakterlánc | A felügyelt API-t a munkafolyamat által használt kapcsolat neve | 
| <*törzs – tartalom*> | JSON-objektum | Bármely hasznos a felügyelt API-hoz elküldendő üzenet | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*újrapróbálkozási-viselkedés*> | JSON-objektum | Az átmeneti hibákra, amelyek rendelkeznek a 408, a 429-es, és az 5XX állapotkód és a minden csatlakozási kivétel újrapróbálkozási viselkedés testreszabása. További információkért lásd: [újrapróbálkozási szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*lekérdezés-paraméterek*> | JSON-objektum | A lekérdezési paramétereket tartalmazza az API-hívással <p>Például a `"queries": { "api-version": "2018-01-01" }` objektumot ad `?api-version=2018-01-01` a hívást. | 
| <*Max-futtatások*> | Egész szám | Alapértelmezés szerint a logikai alkalmazás munkafolyamat-példányok futnak egy időben, vagy a párhuzamos akár a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ez a korlát módosítása egy új beállításával <*száma*> érték, lásd: [módosítása az eseményindító egyidejűségi](#change-trigger-concurrency). | 
| <*várólista-max-futtatások*> | Egész szám | Ha a logikai alkalmazás már fut a példányok maximális száma, amelyet módosíthat alapján a `runtimeConfiguration.concurrency.runs` tulajdonságot használja, minden olyan új futtatások kerüljenek, ennek az üzenetsornak a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Az alapértelmezett korlát módosításához lásd [módosítása várakozási futtatások korlátozza](#change-waiting-runs). | 
| <*splitOn-kifejezés*> | Karakterlánc | Tömbök visszaadó eseményindítók Ez a kifejezés hivatkozik, amelyek akkor létrehozása és futtatása egy munkafolyamat-példány a tömb mindegyik elemén, ahelyett használni egy "mindegyikre" hurkot a tömbben. <p>Például az a kifejezés az eseményindító szövegtörzse belül visszakapott tömbben szereplő elem jelöli: `@triggerbody()?['value']` |
| <*művelet – beállítás*> | Karakterlánc | Az alapértelmezett viselkedés módosításához állítsa a `operationOptions` tulajdonság. További információkért lásd: [műveleti beállítások](#operation-options). | 
|||| 

*Példa*

Az eseményindító definíciójában előfizet az Office 365 Outlook API-t, egy visszahívási URL-címe az API-végpont biztosít, és megvárja, amíg a végpontot kell egy új e-mail érkezésekor.

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

### <a name="http-trigger"></a>HTTP eseményindító

A trigger ellenőrzi vagy lekérdezi a megadott végponton, a megadott ismétlődési ütemezés alapján. Koncového bodu válasz határozza meg, hogy fut-e a munkafolyamatot.

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*eljárástípus*> | Karakterlánc | A lekérdezés a megadott végpont használata HTTP-metódus: "GET", "PUT", "POST", "JAVÍTÁS", "DELETE" | 
| <*végpont-URL-címe*> | Karakterlánc | A HTTP vagy HTTPS URL-címet a végpontjához, lekérdezéséhez <p>Karakterlánc maximális mérete: 2 KB | 
| <*időegység*> | Karakterlánc | Az időegység, amely leírja, hogy milyen gyakran az eseményindító aktiválódik: "A második", "Minute", "Hour", "Day", "Week", "Month" | 
| <*szám, idő egységek*> | Egész szám | A gyakoriságát, és várja meg, amíg az eseményindítót újra mértékegységét száma alapján, amely meghatározza, hogy milyen gyakran az eseményindítót hodnotu <p>Az alábbiakban a minimális és maximális időközönként: <p>– Hónap: 1 – 16 hónap </br>– Nap: 1-500 nap </br>– Óra: 1 – 12 000 óra </br>– Perc: 1 – 72,000 perc </br>-Másodperc: 1 – 9,999,999 másodperc<p>Például a időköz 6, és a gyakoriság "Month", az ismétlődés esetén minden hatodik hónapban. | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*fejléc-tartalom*> | JSON-objektum | A kérés küldése a fejlécek <p>Ha például a nyelvi és a egy kérelem típusa: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*törzs – tartalom*> | Karakterlánc | A kérelem-adattartalom elküldeni az üzenet tartalma | 
| <*hitelesítés – módszer*> | JSON-objektum | A metódus a kérést, használ. További információkért lásd: [Kimenő hitelesítés a Schedulerben](../scheduler/scheduler-outbound-authentication.md). Scheduler túl a `authority` vlastnost podporována. Ha nincs megadva, az alapértelmezett értéke `https://login.windows.net`, de használhat például egy másik értéket`https://login.windows\-ppe.net`. |
| <*újrapróbálkozási-viselkedés*> | JSON-objektum | Az átmeneti hibákra, amelyek rendelkeznek a 408, a 429-es, és az 5XX állapotkód és a minden csatlakozási kivétel újrapróbálkozási viselkedés testreszabása. További információkért lásd: [újrapróbálkozási szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies). |  
 <*lekérdezés-paraméterek*> | JSON-objektum | Minden együtt a kérelem lekérdezési paraméterek <p>Ha például a `"queries": { "api-version": "2018-01-01" }` objektumot ad `?api-version=2018-01-01` a kérelemre. | 
| <*Max-futtatások*> | Egész szám | Alapértelmezés szerint a logikai alkalmazás munkafolyamat-példányok futnak egy időben, vagy a párhuzamos akár a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ez a korlát módosítása egy új beállításával <*száma*> érték, lásd: [módosítása az eseményindító egyidejűségi](#change-trigger-concurrency). | 
| <*várólista-max-futtatások*> | Egész szám | Ha a logikai alkalmazás már fut a példányok maximális száma, amelyet módosíthat alapján a `runtimeConfiguration.concurrency.runs` tulajdonságot használja, minden olyan új futtatások kerüljenek, ennek az üzenetsornak a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Az alapértelmezett korlát módosításához lásd [módosítása várakozási futtatások korlátozza](#change-waiting-runs). | 
| <*művelet – beállítás*> | Karakterlánc | Az alapértelmezett viselkedés módosításához állítsa a `operationOptions` tulajdonság. További információkért lásd: [műveleti beállítások](#operation-options). | 
|||| 

*Kimenetek*

| Elem | Típus | Leírás |
|---------|------|-------------| 
| A fejlécek | JSON-objektum | A válaszból a fejlécek | 
| törzs | JSON-objektum | A szervezetnek a válaszból | 
| Állapotkód | Egész szám | A válaszból állapotkód | 
|||| 

*A bejövő kéréseket követelmények*

Jól működnek a logikai alkalmazást, a végpontot kell egy adott eseményindító minta vagy a szerződés felel meg, és ismeri fel ezeket a tulajdonságokat:  
  
| Válasz | Szükséges | Leírás | 
|----------|----------|-------------| 
| Állapotkód | Igen | A "200 OK" állapotkód: elindít egy Futtatás. Minden más állapotkód nem indul el egy Futtatás. | 
| Retry-after fejléccel | Nem | Mindaddig, amíg a logikai alkalmazás lekérdezi a végpont újra másodpercben | 
| Location fejlécet | Nem | A következő lekérdezési időköz hívják meg az URL-címe. Ha nincs megadva, az eredeti URL-címet használja. | 
|||| 

*A különböző kérésekhez tartozó példa viselkedések*

| Állapotkód | Újrapróbálkozás | Viselkedés | 
|-------------|-------------|----------|
| 200 | {nincs} | A munkafolyamat futtatása, majd a megadott ismétlődési után újra további adatok kereséséhez. | 
| 200 | 10 másodperc | A munkafolyamat futtatása, majd 10 másodperc után újra további adatok kereséséhez. |  
| 202 | 60 másodperc | A munkafolyamat nem elindítására. A következő kísérlet történik egy percen belül, a megadott ismétlődési vonatkoznak. Ha a megadott ismétlődési kevesebb mint egy perc, a retry-after fejléccel élvez elsőbbséget. Ellenkező esetben a megadott ismétlődési szolgál. | 
| 400 | {nincs} | Hibás kérés, a munkafolyamat nem futnak. Ha nincs `retryPolicy` van megadva, majd az alapértelmezett házirend szolgál. Miután a rendszer elérte az újrapróbálkozások számát, a trigger ellenőriz újra adatok után a megadott ismétlődési. | 
| 500 | {nincs}| Kiszolgálóhiba, a munkafolyamat nem futnak. Ha nincs `retryPolicy` van megadva, majd az alapértelmezett házirend szolgál. Miután a rendszer elérte az újrapróbálkozások számát, a trigger ellenőriz újra adatok után a megadott ismétlődési. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook trigger  

Ez az eseményindító lehetővé teszi a logikai alkalmazás használata hívható egy végpontot, amely a megadott végpont URL-címe meghívásával regisztrálhatja az előfizetés létrehozásával. Ez az eseményindító a munkafolyamat létrehozásakor egy kimenő kérelem hívást az az előfizetés regisztrálásához. Ezzel a módszerrel a az eseményindító elindíthat a eseményeket figyeli. Ha egy művelet érvénytelen hajt végre erre az eseményindítóra, egy kimenő kérelem automatikusan hívást a szüntetheti meg előfizetését. További információkért lásd: [végpont előfizetések](#subscribe-unsubscribe).

Azt is megadhatja [aszinkron korlátok](#asynchronous-limits) a egy **HTTPWebhook** eseményindító.
Az eseményindító viselkedése attól függ, hogy a következő szakaszok használja, vagy hagyja ki. 

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

Egyes értékek, mint például <*-eljárástípus*>, is a `"subscribe"` és `"unsubscribe"` objektumokat.

*Kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*eljárástípus*> | Karakterlánc | Az előfizetési kérést használandó HTTP-metódus: "GET", "PUT", "Közzététel", "Javítás" vagy "DELETE" | 
| <*végpontot feliratkozási URL*> | Karakterlánc | A végpont URL-címe, hova küldhetők az előfizetési kérés | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*eljárástípus*> | Karakterlánc | A megszakítási kérelemre váró használandó HTTP-metódus: "GET", "PUT", "Közzététel", "Javítás" vagy "DELETE" | 
| <*végpont-előfizetés lemondása – URL-címe*> | Karakterlánc | A végpont URL-címe, hova küldhetők a megszakítási kérelemre váró | 
| <*törzs – tartalom*> | Karakterlánc | Bármilyen tartalom küldése az előfizetés vagy megszakítási kérelmet kap | 
| <*hitelesítés – módszer*> | JSON-objektum | A metódus a kérést, használ. További információkért lásd: [Kimenő hitelesítés a Schedulerben](../scheduler/scheduler-outbound-authentication.md). |
| <*újrapróbálkozási-viselkedés*> | JSON-objektum | Az átmeneti hibákra, amelyek rendelkeznek a 408, a 429-es, és az 5XX állapotkód és a minden csatlakozási kivétel újrapróbálkozási viselkedés testreszabása. További információkért lásd: [újrapróbálkozási szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*Max-futtatások*> | Egész szám | Alapértelmezés szerint a logikai alkalmazás munkafolyamat-példányok futnak egy időben, vagy a párhuzamos akár a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ez a korlát módosítása egy új beállításával <*száma*> érték, lásd: [módosítása az eseményindító egyidejűségi](#change-trigger-concurrency). | 
| <*várólista-max-futtatások*> | Egész szám | Ha a logikai alkalmazás már fut a példányok maximális száma, amelyet módosíthat alapján a `runtimeConfiguration.concurrency.runs` tulajdonságot használja, minden olyan új futtatások kerüljenek, ennek az üzenetsornak a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Az alapértelmezett korlát módosításához lásd [módosítása várakozási futtatások korlátozza](#change-waiting-runs). | 
| <*művelet – beállítás*> | Karakterlánc | Az alapértelmezett viselkedés módosításához állítsa a `operationOptions` tulajdonság. További információkért lásd: [műveleti beállítások](#operation-options). | 
|||| 

*Kimenetek* 

| Elem | Típus | Leírás |
|---------|------|-------------| 
| A fejlécek | JSON-objektum | A válaszból a fejlécek | 
| törzs | JSON-objektum | A szervezetnek a válaszból | 
| Állapotkód | Egész szám | A válaszból állapotkód | 
|||| 

*Példa*

Ez az eseményindító létrehoz egy előfizetést a megadott végponton, egy egyedi visszahívási URL-címet biztosít, és megvárja, amíg a technológia újonnan közzétett cikkek.

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

A megadott ismétlődési ütemezés alapján fut. erre az eseményindítóra, és egyszerű megoldást kínál egy rendszeresen futó munkafolyamat létrehozásához. 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*időegység*> | Karakterlánc | Az időegység, amely leírja, hogy milyen gyakran az eseményindító aktiválódik: "A második", "Minute", "Hour", "Day", "Week", "Month" | 
| <*szám, idő egységek*> | Egész szám | A gyakoriságát, és várja meg, amíg az eseményindítót újra mértékegységét száma alapján, amely meghatározza, hogy milyen gyakran az eseményindítót hodnotu <p>Az alábbiakban a minimális és maximális időközönként: <p>– Hónap: 1 – 16 hónap </br>– Nap: 1-500 nap </br>– Óra: 1 – 12 000 óra </br>– Perc: 1 – 72,000 perc </br>-Másodperc: 1 – 9,999,999 másodperc<p>Például a időköz 6, és a gyakoriság "Month", az ismétlődés esetén minden hatodik hónapban. | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | Karakterlánc | A kezdő dátum és idő a következő formátumban: <p>ÉÉÉÉ-hh-nnTóó: pp: Ha megad egy időzóna <p>– vagy – <p>ÉÉÉÉ-hh-DDThh:mm:ssZ, ha nem határoz meg időzónát <p>Így például, ha azt szeretné, 2017. szeptember 18., 2:00-kor, majd adja meg "2017-09-18T14:00:00", és adja meg például a "Csendes-óceáni téli idő" időzónát, vagy adja meg "2017-09-18T14:00:00Z" időzóna nélkül. <p>**Megjegyzés:** Hajtsa végre a kezdő időpont a [ISO 8601 dátum-idő specifikáció](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) a [UTC idő dátumformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de egy [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Ha nem határoz meg időzónát, hozzá kell adnia a levél "Z" végén szóközök nélkül. A "Z" hivatkozik az azzal egyenértékű [hajózási idő](https://en.wikipedia.org/wiki/Nautical_time). <p>Egyszerű ütemezések esetében a kezdési időpont az első előfordulás a összetett ütemezéseknél az eseményindító nem indul el minden korábban, a kezdési időpontnál. További információ a kezdő dátum és idő: [létrehozása és rendszeresen futó feladatok ütemezését](../connectors/connectors-native-recurrence.md). | 
| <*Időzóna*> | Karakterlánc | Csak amikor Ön megadja a kezdési időt, mert ez az eseményindító nem fogadja el érvényes [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Adja meg az időzónát, amely a alkalmazni szeretné. | 
| <*egy-vagy-információ – óra – jelek*> | Egész szám vagy az egész számok tömbje | Ha adja meg a "Day" vagy "Week" `frequency`, megadhat egy vagy több egész számok 0 és 23, vesszővel elválasztva, mint a nap, amikor a munkafolyamatot futtatni szeretné az óra. <p>Például ha megadja a "10", "12" és "14", kap, az óra együtt 2 óra, 10 Órakor, 12 PM. | 
| <*egy-vagy – több-perc-jelek*> | Egész szám vagy az egész számok tömbje | Ha adja meg a "Day" vagy "Week" `frequency`, megadhat egy vagy több egész számok 0 és 59 közötti, vesszővel elválasztva, a percek, az óra, ha a munkafolyamatot futtatni szeretné. <p>Például "30" megadhatja a percenkénti be van jelölve, és megjelenik az előző példa a nap, óra, 10:30-kor, 12:30 = 1997031213, és 2:30-kor. | 
| weekDays | Karakterláncot vagy karakterlánc-tömbben | Ha adja meg a "Week" `frequency`, megadhat egy vagy több napot, vesszővel elválasztva, ha a munkafolyamatot futtatni szeretné: "Hétfő", "Tuesday", "Szerda", "Thursday", "Péntek", "Saturday" és "Sunday értékkel" | 
| <*Max-futtatások*> | Egész szám | Alapértelmezés szerint a logikai alkalmazás munkafolyamat-példányok futnak egy időben, vagy a párhuzamos akár a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ez a korlát módosítása egy új beállításával <*száma*> érték, lásd: [módosítása az eseményindító egyidejűségi](#change-trigger-concurrency). | 
| <*várólista-max-futtatások*> | Egész szám | Ha a logikai alkalmazás már fut a példányok maximális száma, amelyet módosíthat alapján a `runtimeConfiguration.concurrency.runs` tulajdonságot használja, minden olyan új futtatások kerüljenek, ennek az üzenetsornak a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Az alapértelmezett korlát módosításához lásd [módosítása várakozási futtatások korlátozza](#change-waiting-runs). | 
| <*művelet – beállítás*> | Karakterlánc | Az alapértelmezett viselkedés módosításához állítsa a `operationOptions` tulajdonság. További információkért lásd: [műveleti beállítások](#operation-options). | 
|||| 

*1. példa*

Ez a alapvető ismétlődési trigger naponta fut:

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

Megadhatja, hogy a kezdő dátum és idő aktiválja az eseményindítót. Az ismétlődési eseményindító a megadott napon kezdődik, és ezután következik be a napi:

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

Az ismétlődési eseményindító 2017. szeptember 9-es 2:00-kor kezdődik, és akkor aktiválódik, hetente minden hétfőn, 10:30-kor, 12:30 = 1997031213, és 2:30 = 1997031213 csendes-óceáni téli idő:

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

További információ és példák erre az eseményindítóra, lásd: [létrehozása és rendszeresen futó feladatok ütemezését](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Kérelem típusú trigger

Ez az eseményindító lehetővé teszi a logikai alkalmazás használata hívható hozzon létre egy végpontot, amely a bejövő kérelmeket fogadhat. Erre az eseményindítóra adja meg egy JSON-sémát, amely ismerteti, és érvényesíti a vagy az, amely a bejövő kérelem megkapja a trigger bemenetei között. A séma is megkönnyíti az eseményindító tulajdonságait referenciáját a későbbi műveletek a munkafolyamatban. 

Szeretne hívásokat indítani erre az eseményindítóra, kell használnia a `listCallbackUrl` API-t, amelyet a a [munkafolyamat szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/logic/workflows). Ez az eseményindító használata HTTP-végpontként kapcsolatban lásd: [hívása, eseményindító, vagy a HTTP-végpontokat munkafolyamatok beágyazása](../logic-apps/logic-apps-http-endpoint.md).

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*tulajdonság neve*> | Karakterlánc | A JSON-sémáját, amely leírja az adattartalomban szereplő tulajdonság neve | 
| <*typ vlastnosti*> | Karakterlánc | A tulajdonság típusa | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*eljárástípus*> | Karakterlánc | A bejövő kéréseket kell meghívni a logikai alkalmazás használó módszer: "GET", "PUT", "POST", "JAVÍTÁS", "DELETE" |
| <*relatív-elérési út-az-elfogadott-paraméter*> | Karakterlánc | A paramétert, amely elfogadja a végponti URL-cím relatív elérési útja | 
| <*kötelező tulajdonságai*> | Tömb | Értékekre van szükség, egy vagy több tulajdonságok | 
| <*Max-futtatások*> | Egész szám | Alapértelmezés szerint a logikai alkalmazás munkafolyamat-példányok futnak egy időben, vagy a párhuzamos akár a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ez a korlát módosítása egy új beállításával <*száma*> érték, lásd: [módosítása az eseményindító egyidejűségi](#change-trigger-concurrency). | 
| <*várólista-max-futtatások*> | Egész szám | Ha a logikai alkalmazás már fut a példányok maximális száma, amelyet módosíthat alapján a `runtimeConfiguration.concurrency.runs` tulajdonságot használja, minden olyan új futtatások kerüljenek, ennek az üzenetsornak a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Az alapértelmezett korlát módosításához lásd [módosítása várakozási futtatások korlátozza](#change-waiting-runs). | 
| <*művelet – beállítás*> | Karakterlánc | Az alapértelmezett viselkedés módosításához állítsa a `operationOptions` tulajdonság. További információkért lásd: [műveleti beállítások](#operation-options). | 
|||| 

*Példa*

Ez az eseményindító Megadja, hogy egy bejövő kérésnek kell használnia a HTTP POST-metódus hívása az eseményindító, és a egy sémát, amely érvényesíti a bemenetet a bejövő kérelem tartalmazza: 

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

## <a name="trigger-conditions"></a>Indítófeltételek

Minden trigger és eseményindítók csak egy tömb, amely tartalmaz egy vagy több feltételek, amelyek meghatározzák, hogy futtasson-e a munkafolyamat a kifejezéseket is megadhat. Hozzáadása a `conditions` tulajdonság a-trigger a logikai alkalmazást, nyissa meg a logikai alkalmazás megtekintése a Kódszerkesztő.

Például megadhatja, hogy egy eseményindítót csak amikor egy webhely adja vissza egy belső kiszolgálóhiba az eseményindító-állapotkódú lére a `conditions` tulajdonság:

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

Alapértelmezés szerint egy eseményindítót csak az első után a "200 OK" választ. Egy kifejezés hivatkozik egy eseményindító állapotkódot, amikor az eseményindító alapértelmezett viselkedés váltja fel. Tehát ha azt szeretné, hogy az eseményindító, egynél több állapotkód, például a "200", "201" állapotkódot el, meg kell adnia a feltételként a kifejezést: 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Több Futtatás aktiválásához

Ha az eseményindító feldolgozni a logikai alkalmazás tömböt ad vissza, néha egy "mindegyikre" hurkot túl hosszú a tömb mindegyik elemén feldolgozáshoz tarthat. Ehelyett használhatja a **SplitOn** tulajdonságot az eseményindító *debatch* a tömbben. Kibontás bontja fel a tömbelemek, és elindul egy új logikaialkalmazás-példányt, amely a tömb mindegyik elemén futtatja. Ez a megközelítés hasznos, ha például szeretne lekérdezni egy végpontot, amely több új elemmel lekérdezési időközökben vissza.
A tömb maximális számát, amely elemek **SplitOn** is egy logikai alkalmazás futtatása feldolgozni, lásd: [korlátozások és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Nem használhat **SplitOn** szinkron válaszra mintával. Minden munkafolyamat által használt **SplitOn** és a válasz tartalmazza művelet aszinkron módon fut, és azonnal küld egy `202 ACCEPTED` választ.

Ha az eseményindító Swagger-fájl egy hasznos, amelyek egy tömb, ismerteti a **SplitOn** tulajdonság automatikusan hozzáadódik az eseményindító. Ellenkező esetben adja hozzá ezt a tulajdonságot, amely rendelkezik a tömb debatch szeretné a válasz-adattartalomra belül. 

*Példa*

Tegyük fel, API, amely ezt a választ adja vissza: 
  
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
 
A logikai alkalmazás csak szüksége van a tartalom a tömbből `Rows`, így hozhat létre egy eseményindítót, mint ebben a példában:

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
> Ha használja a `SplitOn` parancsot, a tulajdonságokat, amelyeket kívül esik a tömb nem kap. Így az ebben a példában nem kap a `status` tulajdonság a válaszban visszaadott az API-ból.
> 
> Ha a hiba elkerülése érdekében a `Rows` tulajdonság nem létezik, ez a példa a `?` operátor.

Most már használhatja a munkafolyamat-definíció `@triggerBody().name` beolvasni a `name` értékeket, amelyek `"customer-name-one"` az első és `"customer-name-two"` , a második Futtatás. Tehát az eseményindító megjeleníti-e meg, például ezekben a példákban:

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

Az Azure Logic Apps biztosít különböző művelettípusok - rendelkező különböző adatbevitelek megadni egy művelet egyedi működését. 

Műveletek a fenti magas szintű elemek rendelkezik, bár egyes nem kötelező:

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

| Érték | Típus | Leírás | 
|-------|------|-------------|
| <*művelet neve*> | Karakterlánc | A művelet neve | 
| <*Művelettípus*> | Karakterlánc | A művelet típusa, például a "Http" vagy az "ApiConnection"| 
| <*bemenet-név*> | Karakterlánc | A nevét, amely meghatározza a művelet viselkedése bemenete | 
| <*bemenet-érték*> | Különböző | A bemeneti érték, amely egy karakterlánc, egész szám, JSON-objektum és így tovább lehet | 
| <*előző-eseményindító-vagy-művelet-állapota*> | JSON-objektum | A név és az eseményindítót vagy műveletet, amely közvetlenül a jelenlegi művelet futtatása előtt kell futtatni az eredményül kapott állapotának | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------|
| <*újrapróbálkozási-viselkedés*> | JSON-objektum | Az átmeneti hibákra, amelyek rendelkeznek a 408, a 429-es, és az 5XX állapotkód és a minden csatlakozási kivétel újrapróbálkozási viselkedés testreszabása. További információkért lásd: [újrapróbálkozási szabályzatok](#retry-policies). | 
| <*futásidejű-config-beállítások*> | JSON-objektum | Egyes műveletekhez, módosíthatja a művelet viselkedése futási időben beállításával `runtimeConfiguration` tulajdonságait. További információkért lásd: [modul konfigurációs beállítások](#runtime-config-options). | 
| <*művelet – beállítás*> | Karakterlánc | Egyes műveletekhez, módosíthatja az alapértelmezett viselkedés beállítása a `operationOptions` tulajdonság. További információkért lásd: [műveleti beállítások](#operation-options). | 
|||| 

## <a name="action-types-list"></a>A művelet típusainak listája

Íme néhány gyakran használt művelet típusa: 

* [Beépített művelettípusok](#built-in-actions) például ezek a példák és további: 

  * [**HTTP** ](#http-action) a HTTP vagy HTTPS-végpontok hívása

  * [**Válasz** ](#response-action) az válaszol a kérelmekre

  * [**Függvény** ](#function-action) az Azure Functions hívása

  * Adatok művelet műveletek, például [ **csatlakozzon**](#join-action), [ **összeállítás**](#compose-action), [ **tábla** ](#table-action), [ **Kiválasztása**](#select-action), és a többi, hozzon létre vagy a különböző bemeneti adatok átalakításához

  * [**A munkafolyamat** ](#workflow-action) egy másik logikai alkalmazás munkafolyamatának meghívására szolgáló

* [Felügyelt API-művelet típusok](#managed-api-actions) például [ **ApiConnection** ](#apiconnection-action) és [ **ApiConnectionWebHook** ](#apiconnectionwebhook-action) , amelyek különböző hívása az összekötők és API-k a Microsoft felügyeli, például Azure Service Bus, az Office 365 Outlook, a Power BI, az Azure Blob Storage, onedrive vállalati verzió, a GitHub és további

* [Szabályozhatja a munkafolyamat művelet](#control-workflow-actions) például [ **Ha**](#if-action), [ **Foreach**](#foreach-action), [ **kapcsoló**  ](#switch-action), [ **Hatókör**](#scope-action), és [ **mindaddig, amíg**](#until-action), amely olyan más műveleteket tartalmaz, és segítséget a munkafolyamat-végrehajtási rendszerezése

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Beépített műveletek

| Művelettípus | Leírás | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Hoz létre egy egyetlen kimeneti bemenetei között, amelyekhez különböző típusainak használatát. | 
| [**Függvény**](#function-action) | Meghív egy Azure-függvényt. | 
| [**HTTP**](#http-action) | Egy HTTP-végpontot hív meg. | 
| [**Csatlakozás**](#join-action) | Egy karakterláncot számmá egy tömb összes eleme, és azok az elemek elkülöníti megadott elválasztó karakterrel. | 
| [**JSON elemzése**](#parse-json-action) | Hoz létre felhasználóbarát jogkivonatok tulajdonságait JSON-tartalom. Azokat a tulajdonságokat a logikai alkalmazásban többek között a jogkivonatok majd hivatkozhat. | 
| [**Lekérdezés**](#query-action) | Létrehoz egy tömböt alapján egy feltétel, vagy a szűrő egy másik tömbben szereplő elemek közül. | 
| [**Válasz**](#response-action) | Bejövő hívás vagy kérés-válasz hoz létre. | 
| [**Válassza ki**](#select-action) | Létrehoz egy tömböt JSON-objektumok átalakításával keletkező elemek a megadott leképezés alapján egy másik tömbből. | 
| [**Tábla**](#table-action) | CSV- vagy HTML-táblázatként egy tömböt hoz létre. | 
| [**Leállítása**](#terminate-action) | Egy folyamatosan futó munkafolyamat leáll. | 
| [**várj**](#wait-action) | A munkafolyamat felfüggesztése, egy adott időtartamot vagy a megadott dátumig és időpontig. | 
| [**A munkafolyamat**](#workflow-action) | Egy munkafolyamatot belül egy másik munkafolyamat nests. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Felügyelt API-műveletek

| Művelettípus | Leírás | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Egy HTTP-végpontot hív használatával egy [API a Microsoft által felügyelt](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | HTTP-Webhook hasonlóan működik, de használja egy [API a Microsoft által felügyelt](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Vezérlési munkafolyamat-műveletek

Ezek a műveletek segítséget nyújt a munkafolyamat futtatásának szabályozása és egyéb műveleteket tartalmazza. A vezérlési munkafolyamat-művelet kívül közvetlenül hivatkozhat műveletek belüli adott vezérlő munkafolyamat-művelet. Például, ha rendelkezik egy `Http` hivatkozhat egy hatókör művelet, a `@body('Http')` bárhonnan kifejezés a munkafolyamatban. Azonban belül vezérlési munkafolyamat-művelet létező műveletek csak "futtatható" más műveletek, amelyek a vezérlő munkafolyamat ugyanazzal a struktúrával.

| Művelettípus | Leírás | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Ugyanazokat a műveleteket futtatni egy hurokba, és a egy tömb összes elemét. | 
| [**Ha**](#if-action) | Futtatási műveleteket, hogy a megadott feltétel értéke true vagy FALSE (hamis). | 
| [**Hatókör**](#scope-action) | A műveletek egy csoportját, csoport állapota alapján műveletek futtatása. | 
| [**Kapcsoló**](#switch-action) | Amikor kifejezések, objektumok vagy jogkivonatok értékek megegyeznek minden esetben által meghatározott esetekben rendszerezve műveletek futtatása. | 
| [**Amíg**](#until-action) | Műveletek futtatása ciklusosan addig, amíg a megadott feltétel teljesül. | 
|||  

## <a name="actions---detailed-reference"></a>Műveletek – részletes útmutató

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection művelet

Ez a művelet egy HTTP-kérést küld egy [API a Microsoft által felügyelt](../connectors/apis-list.md) és a szükséges információt az API-t és a paraméterek és a egy érvényes hivatkozást. 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*művelet neve*> | Karakterlánc | Az összekötő által biztosított művelet neve | 
| <*API-név*> | Karakterlánc | A Microsoft által felügyelt API-t, amely a kapcsolathoz használt neve | 
| <*eljárástípus*> | Karakterlánc | Az API meghívására szolgáló HTTP-metódus: "GET", "PUT", "Közzététel", "Javítás" vagy "DELETE" | 
| <*API-művelet*> | Karakterlánc | Az API-művelet meghívásához | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*egyéb-művelet-specifikus-input-properties*> | JSON-objektum | Ez a művelet vonatkozó bármely más bemeneti tulajdonságok | 
| <*újrapróbálkozási-viselkedés*> | JSON-objektum | Az átmeneti hibákra, amelyek rendelkeznek a 408, a 429-es, és az 5XX állapotkód és a minden csatlakozási kivétel újrapróbálkozási viselkedés testreszabása. További információkért lásd: [újrapróbálkozási szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*lekérdezés-paraméterek*> | JSON-objektum | Hívja meg a lekérdezési paramétereket tartalmazza az API-val. <p>Például a `"queries": { "api-version": "2018-01-01" }` objektumot ad `?api-version=2018-01-01` a hívást. | 
| <*egyéb-művelet-specifikus-properties*> | JSON-objektum | Ez a művelet a alkalmazni egyéb tulajdonságok | 
|||| 

*Példa*

Ez a definíció ismerteti a **e-mail küldése** az Office 365 Outlook-összekötőt, amely egy Microsoft által felügyelt API művelet: 

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

Ez a művelet egy előfizetés kérést küld HTTP-kapcsolaton keresztül a végpont használatával egy [API a Microsoft által felügyelt](../connectors/apis-list.md), biztosít egy *visszahívási URL-Címének* , ahol a végpont küldhet a választ, és megvárja a végpontot, hogy a válaszol. További információkért lásd: [végpont előfizetések](#subscribe-unsubscribe).

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

Egyes értékek, mint például <*-eljárástípus*>, is a `"subscribe"` és `"unsubscribe"` objektumokat.

*Kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*művelet neve*> | Karakterlánc | Az összekötő által biztosított művelet neve | 
| <*eljárástípus*> | Karakterlánc | Az előfizetés vagy iratkoznak le a végpont HTTP-metódus: "GET", "PUT", "Közzététel", "Javítás" vagy "DELETE" | 
| <*API-előfizetés-URL-címe*> | Karakterlánc | Az URI-t az előfizetés az API használata | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*API-előfizetés lemondása – URL-címe*> | Karakterlánc | Az URI-t iratkoznak le az API használata | 
| <*fejléc-tartalom*> | JSON-objektum | A kérés küldése fejlécek <p>Például állítsa a nyelvet, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*törzs – tartalom*> | JSON-objektum | A kérés küldése minden üzenet tartalma | 
| <*hitelesítés – módszer*> | JSON-objektum | A metódus a kérést, használ. További információkért lásd: [Kimenő hitelesítés a Schedulerben](../scheduler/scheduler-outbound-authentication.md). |
| <*újrapróbálkozási-viselkedés*> | JSON-objektum | Az átmeneti hibákra, amelyek rendelkeznek a 408, a 429-es, és az 5XX állapotkód és a minden csatlakozási kivétel újrapróbálkozási viselkedés testreszabása. További információkért lásd: [újrapróbálkozási szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*lekérdezés-paraméterek*> | JSON-objektum | A lekérdezési paramétereket tartalmazza az API-hívással <p>Például a `"queries": { "api-version": "2018-01-01" }` objektumot ad `?api-version=2018-01-01` a hívást. | 
| <*egyéb-művelet-specifikus-input-properties*> | JSON-objektum | Ez a művelet vonatkozó bármely más bemeneti tulajdonságok | 
| <*egyéb-művelet-specifikus-properties*> | JSON-objektum | Ez a művelet a alkalmazni egyéb tulajdonságok | 
|||| 

Korlátok adja meg a egy **ApiConnectionWebhook** ugyanúgy, mint a művelet [HTTP aszinkron korlátok](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Összeállítás műveletet

Ez a művelet létrehoz egy kimeneti származó több bemenet, beleértve a kifejezéseket. A kimenet és a bemenetek Azure Logic Apps natívan támogatja, mint például a tárolótömbök, JSON-objektumok, XML és bináris típussal rendelkezhet.
Egyéb műveletek ezután használhatja a műveleti kimenet. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Kötelező* 

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*bemenet-összeállítás*> | Bármelyik | A bemenet egyetlen kimeneti létrehozásához | 
|||| 

*1. példa*

Ez a művelet a definíció egyesíti `abcdefg ` értékét és a egy záró szóköz `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Ez a művelet létrehoz, a kimenet itt látható:

`abcdefg 1234`

*2. példa*

Ez a művelet a definíció egyesíti tartalmazó karakterlánc-változót `abcdefg` és a egy egész számot tartalmazó változót `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Ez a művelet létrehoz, a kimenet itt látható:

`"abcdefg1234"`

<a name="function-action"></a>

### <a name="function-action"></a>Függvény-művelet

Ez a művelet meghív egy korábban létrehozott [Azure-függvény](../azure-functions/functions-create-first-azure-function.md).

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

| Érték | Típus | Leírás | 
|-------|------|-------------|  
| <*Azure-függvény-azonosító*> | Karakterlánc | Az Azure-függvényhez tartozó erőforrás-azonosító szeretné meghívni. Ez az érték formátuma a következő:<p>"/subscriptions/ <*azure-előfizetés-azonosító*> /resourceGroups/ <*Azure-resource-group*> /providers/Microsoft.Web/sites/ <*Azure-függvény-alkalmazás-neve*> /Functions/ <*azure-függvény-name*> " | 
| <*eljárástípus*> | Karakterlánc | A függvény hívása használandó HTTP-metódus: "GET", "PUT", "Közzététel", "Javítás" vagy "DELETE" <p>Ha nincs megadva, az alapértelmezett érték a "POST" metódust. | 
||||

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------|  
| <*fejléc-tartalom*> | JSON-objektum | Fejlécek küldése a hívással <p>Például állítsa a nyelvet, és írja be a kérelem: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*törzs – tartalom*> | JSON-objektum | A kérés küldése minden üzenet tartalma | 
| <*lekérdezés-paraméterek*> | JSON-objektum | A lekérdezési paramétereket tartalmazza az API-hívással <p>Például a `"queries": { "api-version": "2018-01-01" }` objektumot ad `?api-version=2018-01-01` a hívást. | 
| <*egyéb-művelet-specifikus-input-properties*> | JSON-objektum | Ez a művelet vonatkozó bármely más bemeneti tulajdonságok | 
| <*egyéb-művelet-specifikus-properties*> | JSON-objektum | Ez a művelet a alkalmazni egyéb tulajdonságok | 
||||

A logikai alkalmazás mentéséhez, a Logic Apps-motor hajtja végre a hivatkozott függvény ezen ellenőrzések:

* A munkafolyamat a függvény hozzáféréssel kell rendelkeznie.

* A munkafolyamat csak egy normál HTTP-eseményindító vagy egy általános JSON-webhook eseményindító használhatja. 

  A Logic Apps-motor beolvassa és gyorsítótárazza az aktiváló URL-címe, amely futásidőben használatos. Azonban, ha bármilyen műveletet érvényteleníti a gyorsítótárazott URL-CÍMÉT, a **függvény** művelet sikertelen lesz, futásidőben. A probléma megoldásához, mentse a logikai alkalmazást újra, hogy a logikai alkalmazás lekérdezi, és újra gyorsítótárazza az aktiváló URL-címe.

* A függvény nem rendelkezhet meghatározott útvonalakat.

* Csak a "függvény" és "névtelen" hitelesítési szintjeinek engedélyezettek. 

*Példa*

Ez a művelet a definíció a korábban létrehozott "GetProductID" függvényt hívja meg:

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

Ez a művelet egy kérést küld a megadott végponton, és ellenőrzi a válasz határozza meg, hogy a munkafolyamat futtatásának időpontját. 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*eljárástípus*> | Karakterlánc | A kérelem küldéséhez használt módszer: "GET", "PUT", "Közzététel", "Javítás" vagy "DELETE" | 
| <*HTTP-vagy-HTTPS-végpont-URL-címe*> | Karakterlánc | A HTTP vagy HTTPS-végpont meghívására. Karakterlánc maximális mérete: 2 KB | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*fejléc-tartalom*> | JSON-objektum | A kérés küldése fejlécek <p>Ha például nyelvi és típusa: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*törzs – tartalom*> | JSON-objektum | A kérés küldése minden üzenet tartalma | 
| <*újrapróbálkozási-viselkedés*> | JSON-objektum | Az átmeneti hibákra, amelyek rendelkeznek a 408, a 429-es, és az 5XX állapotkód és a minden csatlakozási kivétel újrapróbálkozási viselkedés testreszabása. További információkért lásd: [újrapróbálkozási szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*lekérdezés-paraméterek*> | JSON-objektum | Minden együtt a kérelem lekérdezési paraméterek <p>Például a `"queries": { "api-version": "2018-01-01" }` objektumot ad `?api-version=2018-01-01` a hívást. | 
| <*egyéb-művelet-specifikus-input-properties*> | JSON-objektum | Ez a művelet vonatkozó bármely más bemeneti tulajdonságok | 
| <*egyéb-művelet-specifikus-properties*> | JSON-objektum | Ez a művelet a alkalmazni egyéb tulajdonságok | 
|||| 

*Példa*

Ez a művelet a definíció lekéri a legfrissebb hírek egy kérést küld a megadott végpont:

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

### <a name="join-action"></a>JOIN művelet

Ezzel a művelettel egy tömb összes eleme egy karakterláncot számmá, és azok az elemek elkülöníti a megadott elválasztó karakterrel. 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Pole*> | Tömb | A tömb vagy kifejezés, amely kiszámítja a forrás-elemeket. Ha megad egy kifejezés, tegye idézőjelek kifejezésből. | 
| <*elválasztó karakter*> | Egyetlen karakterlánc | A karakter, amely elválasztja az egyes elemek a karakterláncban | 
|||| 

*Példa*

Tegyük fel, hogy rendelkezik egy korábban létrehozott "myIntegerArray" változó, amely tartalmazza az egész számok tömbje: 

`[1,2,3,4]` 

Ez a művelet a definíció az értékek lekérdezi a változó segítségével a `variables()` a kifejezések működnek, és ezt a karakterláncot hoz ezeket az értékeket, amelyeket egy vesszővel elválasztott: `"1,2,3,4"`

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

### <a name="parse-json-action"></a>A művelet JSON elemzése

Ez a művelet létrehoz felhasználóbarát mezők vagy *jogkivonatok* , a tulajdonságokat a JSON-tartalmak. Ehelyett a jogkivonatok segítségével a logikai alkalmazásban ezután hozzáférhetnek azokat a tulajdonságokat. Például ha JSON-kimenet szolgáltatásokból, például az Azure Service Bus és az Azure Cosmos DB-hez használni kívánt, is felvehet Ez a művelet a logikai alkalmazás, hogy a kimeneti adatait egyszerűbben hivatkozhat. 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*JSON-forrás*> | JSON-objektum | Az elemezni kívánt JSON-tartalmak | 
| <*JSON-sémája*> | JSON-objektum | A JSON-séma, amely leírja, hogy az alapul szolgáló a JSON-tartalom, amely a művelet használja a forrás JSON-tartalmak elemzéséhez. <p>**Tipp**: A Logic Apps Designerben adja meg a séma, vagy adjon meg egy hasznosadat-minta, hogy a művelet a sémát hozhat létre. | 
|||| 

*Példa*

Ez a művelet a definíció hoz létre, ezek a jogkivonatok, hogy a logikai alkalmazás munkafolyamatának, de csak a műveletek adott Futtatás következő használható a **JSON elemzése** művelet: 

`FirstName`, `LastName`, és `Email`

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

Ebben a példában a "content" tulajdonságát adja meg a JSON-tartalom elemzése a művelethez. : A séma létrehozása hasznosadat-minta szerint is megadhatja a JSON-tartalmak.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

A "schema" tulajdonság határozza meg, hogy a JSON-tartalmak leíró használt JSON-sémája:

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

Ez a művelet létrehoz egy tömböt alapján egy megadott feltétel, vagy a szűrő egy másik tömbben szereplő elemek közül.

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Pole*> | Tömb | A tömb vagy kifejezés, amely kiszámítja a forrás-elemeket. Ha megad egy kifejezés, tegye idézőjelek kifejezésből. |
| <*az állapot vagy a szűrő*> | Karakterlánc | A forrás tömbben szereplő elemek szűrési feltétel <p>**Megjegyzés**: Nem teljesítik a feltételt, ha a művelet létrehoz egy üres tömb. |
|||| 

*Példa*

Ez a művelet a definíció hoz létre egy tömb, amely nagyobb, mint a megadott érték, amely két értéke van:

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

### <a name="response-action"></a>Válaszművelet  

Ez a művelet létrehoz az a választ egy HTTP-kérelem hasznos adatai. 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*válasz állapotkódja*> | Egész szám | A HTTP-állapotkódot, a bejövő kérést küldött. Az alapértelmezett kód "200 OK", de a kód bármilyen állapot érvényes kódot, amely elindítja a 2xx, 4xx vagy 5xx, de nem 3xxx lehet. | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*válaszfejlécek*> | JSON-objektum | A válasz szerepeltetni kívánt egy vagy több fejlécek | 
| <*választörzs*> | Különböző | A válasz törzse, amely egy karakterlánc, JSON-objektum vagy egy korábbi művelet még bináris tartalom | 
|||| 

*Példa*

Ez a művelet a definíció egy válasz HTTP-kérést hoz létre a megadott állapotkód, üzenet szövege és üzenetfejlécekben:

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

Ellentétben más műveletek a **válasz** műveletnek speciális korlátozások: 

* A munkafolyamat használhatja a **válasz** művelet csak akkor, ha a munkafolyamat elindul a HTTP-kérelem trigger, azaz a munkafolyamat kell elindítható a HTTP-kérést.

* A munkafolyamat használhatja a **válasz** művelet bárhol *kivételével* belül **Foreach** hurkok, **mindaddig, amíg** hurkok, beleértve a szekvenciális hurok és a párhuzamos ágak. 

* Az eredeti HTTP-kérelem beolvassa a munkafolyamat válasz csak akkor, ha a szükséges összes műveletet a **válasz** művelet befejezte volna a belül a [HTTP-kérelem időkorlátja](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  Azonban ha a munkafolyamatot egy másik logikai alkalmazás egy beágyazott munkafolyamattal, a szülő-munkafolyamat megvárja, amíg a beágyazott munkafolyamat befejeződött, függetlenül attól, hogy mennyi idő alatt továbbítja a beágyazott munkafolyamat befejezése előtt.

* Amikor a munkafolyamat használja a **válasz** művelet és a egy szinkron válaszra mintát, a munkafolyamat is használható a **splitOn** parancs az eseményindító definíciójában, mivel ez a parancs létrehoz több futtatás. Ebben az esetben ellenőrizze a PUT metódust használja, és ha az értéke igaz, a "Hibás kérés" választ adja vissza.

  Egyéb esetben, ha a munkafolyamat a **splitOn** parancsot és a egy **válasz** műveletet, a munkafolyamat aszinkron módon fut, és azonnal "202-es ELFOGADVA" választ.

* Ha a munkafolyamat-végrehajtási művelet elér a **válasz** művelet, de a bejövő kérelem már kapott választ, a **válasz** művelet "Sikertelen" állapotúnak van jelölve, az ütközés miatt. Ennek eredményeképpen a logikai alkalmazás futtatását, ezért is "Sikertelen" állapotú.

<a name="select-action"></a>

### <a name="select-action"></a>Művelet kiválasztása

Ez a művelet létrehoz egy tömböt JSON-objektumok átalakításával keletkező elemek a megadott leképezés alapján egy másik tömbből. A kimeneti tömbben és forrástömb mindig van azonos számú elemet. Bár a kimeneti tömbben található objektumok száma nem módosítható, adja hozzá, vagy távolítsa el a tulajdonságok és azok értékei között azokat az objektumokat. A `select` tulajdonsága azt adja meg legalább egy olyan kulcs-érték pár, amelyek meghatározzák a térkép alkalmazásán át a forrás tömbben szereplő elemek. Egy kulcs-érték pár a kimeneti tömbben található összes objektum egy tulajdonságot, és annak értékét jelöli. 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Pole*> | Tömb | A tömb vagy kifejezés, amely kiszámítja a forrás-elemeket. Ellenőrizze, hogy egy kifejezés, az idézőjelekkel együtt idézőjelek közé kell tenni. <p>**Megjegyzés**: Ha a forrás tömb üres, a művelet létrehoz egy üres tömb. | 
| <*kulcs neve*> | Karakterlánc | Annak az eredménye a tulajdonság neve <*kifejezés*> <p>A kimeneti tömbben található összes objektum hozzáadása az új tulajdonság, adja meg egy <*-kulcsnév*> tulajdonság és a egy <*kifejezés*> a tulajdonság értékeként. <p>Tulajdonság eltávolítása a tömbben lévő összes objektumra, hagyja ki ezt a <*-kulcsnév*> az adott tulajdonságnál. | 
| <*kifejezés*> | Karakterlánc | A kifejezés, amely a cikk a forrástömb alakítja át, és hozzárendeli az eredményt a <*kulcs neve*> | 
|||| 

A **kiválasztása** műveleti kimeneteként, létrehoz egy tömböt, amely szeretnének használni, ez a kimenet egy tömb vagy el kell fogadnia, vagy át kell alakítania a tömb típus, amely a felhasználói műveletet. Például a kimeneti tömbben átalakítása egy karakterlánc, továbbíthatja a tömböt a **összeállítás** műveletet, majd hivatkozhat a kimenetét, és a **összeállítás** művelet az egyéb műveletek.

*Példa*

Ez a művelet a definíció hoz létre egy JSON-objektum tömb egész számok tömbje. Használatával a forrás tömb végighalad a művelet lekérdezi az egyes egész szám a `@item()` kifejezést, és rendel minden egyes érték a "`number`" tulajdonság található minden JSON-objektum: 

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

Itt látható a tömbben, hogy ez a művelet létrehoz:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Használja ezt a tömböt a kimenetet más műveletek, át kell adnia a kimenete egy **összeállítás** művelet:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Ezután használhatja a kimenete a **összeállítás** a más műveletek, például a művelet a **Office 365 Outlook – e-mail küldése** művelet:

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

### <a name="table-action"></a>Táblázat művelet

Ez a művelet létrehoz egy CSV- vagy HTML-táblázat egy tömbből. Ezzel a művelettel a JSON-objektumok tömbök, automatikusan számmá az objektumok a tulajdonságnevek az oszlopfejléceket. Más típusú adatokat tartalmazó tömb meg kell adnia az oszlopfejléceket és értékeket. Például ezt a tömböt tartalmaz, ez a művelet használhat a fejléc oszlopnevek "ID" és "Terméknév" tulajdonságait:

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| < CSV *vagy* HTML >| Karakterlánc | A formátum a létrehozni kívánt tábla | 
| <*Pole*> | Tömb | A tömb vagy kifejezés, amely biztosítja a tábla a forrás-elemek <p>**Megjegyzés**: Ha a forrás tömb üres, a művelet létrehoz egy üres táblát. | 
|||| 

*Nem kötelező*

Adja meg, vagy testre szabhatja oszlopfejlécek és értékeket használja a `columns` tömb. Amikor `header-value` párok azonos legyen a fejléc neve, ugyanazt az oszlopot a fejléc neve alatt jelennek meg azok értékeit. Ellenkező esetben minden egyedi fejléc határozza meg egy egyedi oszlopot.

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*oszlopnév*> | Karakterlánc | Egy oszlopot az állomásfejléc-nevet | 
| <*oszlop-érték*> | Bármelyik | Az érték az adott oszlopban | 
|||| 

*1. példa*

Tegyük fel, hogy rendelkezik egy korábban létrehozott "myItemArray" változó jelenleg ezt a tömböt tartalmazó: 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Ez a művelet a definíció egy CSV-táblázat "myItemArray" változó hoz létre. A kifejezés által használt a `from` tulajdonság olvas be a tömb "myItemArray" használatával az `variables()` függvény: 

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

A CSV-táblázat, amely a művelet létrehozza a következő: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*2. példa*

Ez a művelet a definíció egy HTML-táblázat "myItemArray" változó hoz létre. A kifejezés által használt a `from` tulajdonság olvas be a tömb "myItemArray" használatával az `variables()` függvény: 

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

A HTML-táblázat, amely a művelet létrehozza a következő: 

<table><thead><tr><th>ID (Azonosító)</th><th>Terméknév</th></tr></thead><tbody><tr><td>0</td><td>Alma</td></tr><tr><td>1</td><td>Narancs</td></tr></tbody></table>

*3. példa*

Ez a művelet a definíció egy HTML-táblázat "myItemArray" változó hoz létre. Ebben a példában azonban felülbírálja az alapértelmezett fejléc oszlopnevek "Stock_ID" és "Description", és a "Leírás" oszlopban szereplő értékeket ad hozzá a "Szerves" szó.

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

A HTML-táblázat, amely a művelet létrehozza a következő: 

<table><thead><tr><th>Stock_ID</th><th>Leírás</th></tr></thead><tbody><tr><td>0</td><td>Organikus alma</td></tr><tr><td>1</td><td>Organikus narancs</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Leállítási művelet

Ez a művelet leállítja a logikai alkalmazás munkafolyamat-példány futtatása, megszakítja a folyamatban lévő műveletek, kihagyja a hátralévő műveletekkel és a megadott állapotát adja vissza. Használhatja például a **Leállítás** műveletet, amikor a logikai alkalmazás teljes mértékben kell lépnie a hibás állapotú. Ez a művelet nem érinti a már elvégzett műveletek, és nem szerepelhet belül **Foreach** és **mindaddig, amíg** hurkok, beleértve a szekvenciális hurok. 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*status*> | Karakterlánc | A Futtatás esetében visszaadandó állapota: "Sikertelen", "Megszakítva" vagy "Sikeres" |
|||| 

*Nem kötelező*

A "runStatus" objektum tulajdonságainak a alkalmazni: csak akkor, ha a "runStatus" tulajdonsága "Sikertelen" állapotú.

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Hiba-kód-vagy a-name*> | Karakterlánc | A kód vagy a hiba nevét |
| <*hibaüzenetek*> | Karakterlánc | Az üzenet vagy a szöveg, amely leírja a hibát, és azokat a műveleteket az alkalmazás felhasználói is eltarthat | 
|||| 

*Példa*

Ez a művelet a definíció egy munkafolyamat-Futtatás leállítása, beállítja a futási állapotát a "Sikertelen", és állapotát, egy hibakódot és egy hibaüzenetet adja vissza:

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

### <a name="wait-action"></a>Várjon a művelet  

Ez a művelet felfüggeszti a munkafolyamat-végrehajtási számára a megadott időszak legalább a megadott időtartam alatt, de nem mindkettőt. 

*Megadott időköz*

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*szám – a-egység*> | Egész szám | Az a **késleltetés** művelet, várjon egységek száma | 
| <*időköz*> | Karakterlánc | Az a **késleltetés** művelet, a várakozási időköz: "A második", "Minute", "Hour", "Day", "Week", "Month" | 
| <*dátum-időbélyeg*> | Karakterlánc | Az a **késleltetés amíg** művelet, a dátumot és időpontot, végrehajtása folytatódik. Ezt az értéket kell használnia a [UTC idő dátumformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*1. példa*

Ez a művelet a definíció 15 percig a munkafolyamat felfüggesztése:

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

Ez a művelet a definíció a munkafolyamat a megadott időpontig felfüggeszti:

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

Ez a művelet egy másik korábban létrehozott logikai alkalmazást, amely azt jelenti, hogy tartalmazza, és újra felhasználhatja más logikai alkalmazások munkafolyamataiba meghívja. A gyermek kimenetei is használhatja, vagy *beágyazott* műveleteket, amelyeket a beágyazott logikai alkalmazást, kövesse, feltéve, hogy a gyermek logikai alkalmazás választ ad a logikai alkalmazást.

A Logic Apps-motor az eseményindító hívja, ezért ügyeljen arra, hogy az eseményindító érheti el szeretné a hozzáférést ellenőrzi. A logikai alkalmazások beágyazott logikai Ezenkívül ezeknek a feltételeknek kell megfelelnie:

* Eseményindító lehetővé teszi a logikai alkalmazások beágyazott logikai használata hívható, mint például egy [kérelem](#request-trigger) vagy [HTTP](#http-trigger) eseményindító

* Az Azure-előfizetéshez, a szülő logikai alkalmazás

* A szülő logikai alkalmazásban a logikai alkalmazások beágyazott logikai kimenetei használatához rendelkeznie kell a beágyazott logikai alkalmazást egy [válasz](#response-action) művelet 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*a beágyazott-logic-alkalmazás-neve*> | Karakterlánc | A logikai alkalmazás nevét szeretné meghívni | 
| <*eseményindító-neve*> | Karakterlánc | A beágyazott logikai alkalmazás szeretné meghívni az eseményindító neve | 
| <*Az Azure-előfizetés-azonosítója*> | Karakterlánc | A beágyazott logikai alkalmazás az Azure-előfizetés azonosítója |
| <*Az Azure-resource-group*> | Karakterlánc | A beágyazott logikai alkalmazást az Azure erőforráscsoport-neve |
| <*a beágyazott-logic-alkalmazás-neve*> | Karakterlánc | A logikai alkalmazás nevét szeretné meghívni |
||||

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------|  
| <*fejléc-tartalom*> | JSON-objektum | Fejlécek küldése a hívással | 
| <*törzs – tartalom*> | JSON-objektum | A hívás küldhet bármely üzenet tartalma | 
||||

*Kimenetek*

Ez a művelet kimenetek a beágyazott logikaialkalmazás-válaszművelet függ. Ha a beágyazott logikai alkalmazást egy válaszművelet nem tartalmaz, a kimenetek nincsenek megadva.

*Példa*

A "Start_search" művelet sikeres befejezését követően a munkafolyamat-művelet definíciója hívások nevű, "Get_product_information", amely áthalad a megadott bemenetei között meg egy másik logikai alkalmazás: 

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

## <a name="control-workflow-action-details"></a>A vezérlő munkafolyamat-művelet részletei

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach művelet

Ismétlési művelet végighalad a tömböt, és a tömb mindegyik elemén végrehajtja a műveleteket. Alapértelmezés szerint a "mindegyikre" hurkot a hurkok maximális számú párhuzamosan futtatja. Ez a maximális érték, lásd: [határértékek és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ismerje meg, [létrehozása "mindegyikre" hurok](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*művelet – 1.. n*> | Karakterlánc | A végrehajtandó műveleteket futtatni a tömb mindegyik elemén nevei | 
| <*művelet-definition-1.. n*> | JSON-objektum | A definíciók futtatott művelet | 
| <*for-each-kifejezés*> | Karakterlánc | A kifejezés, amely hivatkozik a megadott tömbben lévő egyes elemek | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Száma*> | Egész szám | Az ismétlések futtathat egy időben, illetve párhuzamosan akár "for each" iteráció alapértelmezés szerint a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ez a korlát módosítása egy új beállításával <*száma*> érték, lásd: [módosítása "mindegyikre" hurkot egyidejűségi](#change-for-each-concurrency). | 
| <*művelet – beállítás*> | Karakterlánc | Egy "mindegyikre" hurkot egymás után, hanem párhuzamosan, állítsuk be vagy <*műveletbeállítás*> való `Sequential` vagy <*száma*> való `1`, egyszerre azonban nem. További információkért lásd: [futtassa a "for each" egymás után hurkokat](#sequential-for-each). | 
|||| 

*Példa*

A "mindegyikre" hurkot minden elemről e-mailt küld a tömbben, amely tartalmazza a bejövő e-mailt a mellékleteket. A hurok küld egy e-mailt, többek között a mellékleteket, a személy, aki ellenőrzi a melléklet.

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

Annak megadásához, csak a trigger által átadott kimenete egy tömb, ez a kifejezés lekérdezi a <*tömbnév*> tömb az eseményindító törzsében. Hiba elkerülése érdekében, ha a tömb nem létezik, a kifejezést használ a `?` operátor:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Ha a művelet

Ez a művelet, amely egy *feltételes utasítás*, Kiértékel egy kifejezést, amely feltétel jelöli, és a egy másik ág alapján, hogy a feltétel teljesül futtatja vagy a False (hamis). Ha a feltétel teljesül, a feltétel "Succeeded" állapottal van megjelölve. Ismerje meg, [hogyan hozhat létre feltételes utasításokat](../logic-apps/logic-apps-control-flow-conditional-statement.md).

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*a feltétel*> | JSON-objektum | A feltétel, amely lehet egy kifejezés kiértékelése | 
| <*művelet – 1*> | JSON-objektum | A műveletet úgy, hogy futtatható, ha <*feltétel*> igaz értéket ad vissza | 
| <*a művelet-definíció*> | JSON-objektum | A művelet definíciója | 
| <*művelet – 2*> | JSON-objektum | A műveletet úgy, hogy futtatható, ha <*feltétel*> kifejezés hamis | 
|||| 

A műveletek a `actions` vagy `else` objektumok lekérése a fenti állapotok megjelenése:

* "Sikeres" futtassa, és a sikeres
* "Sikertelen" futtassa, és a sikertelen
* "A rendszer kihagyja" a megfelelő fiókirodai nem fut

*Példa*

Ez az állapot Itt adhatja meg, hogy az egész szám típusú változó csak nullánál nagyobb érték van, ha a munkafolyamat egy webhely ellenőrzi. Ha a változó nulla vagy annál kisebb, a munkafolyamat ellenőrzi egy másik webhelyre.

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

#### <a name="how-conditions-use-expressions"></a>Hogyan feltételek kifejezések használata

Íme néhány példa azt mutatják be, hogyan használhat kifejezéseket feltételek esetében alkalmazhatja:
  
| JSON | Eredmény | 
|------|--------| 
| "kifejezés": "@parameters(a(z) <*hasSpecialAction*>") " | A csak logikai kifejezésen, a feltétel teljesül, amely kiértékeli a megadott értéket Igaz értékre. <p>Más típusú logikai típusra konvertál, használja ezeket a funkciókat: `empty()` vagy `equals()`. | 
| "kifejezés": "@greater(actions('<*action*>').output.value, paraméterek (" <*küszöbérték*> "))" | Összehasonlító függvények, a művelet csak akkor, ha futtatja a kimenetét <*művelet*> van több, mint a <*küszöbérték*> érték. | 
| "kifejezés": "@or(nagyobb (actions('<*action*>').output.value, paraméterek (" <*küszöbérték*> ")), kevesebb (műveleteket (a(z) <*ugyanazon művelet*>').Output.Value, 100)) " | Az funkciók logic és létrehozása a beágyazott logikai kifejezésen, a művelet a futás mikor kimenete <*művelet*> van több, mint a <*küszöbérték*> érték, vagy a 100. | 
| "kifejezés": "@equals(hossza (actions('<*action*>').outputs.errors), 0))" | Tömb funkciók használhatja annak ellenőrzéséhez, hogy a tömb rendelkezik-e az elemeket. A művelet ekkor fut a `errors` Pole je üres. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Hatókör művelet

Ez a művelet logikailag csoportosítja az műveletek *hatókörök*, amelyek a saját állapotának beolvasása után a műveletek abban, hogy a hatókör futása befejeződik. Ezután használhatja a hatókör állapot meghatározásához, hogy más műveletek futtatása. Ismerje meg, [hatókörök létrehozása](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

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

| Érték | Típus | Leírás | 
|-------|------|-------------|  
| <*belső-művelet-1.. n*> | JSON-objektum | Egy vagy több műveletet, amely hatókörében |
| <*művelet – bemenetek*> | JSON-objektum | A bemenet minden egyes művelethez |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Kapcsolóműveletből

Ez a művelet, más néven egy *utasítás váltson*, rendezi azokat más műveletek *esetek*, és a egy értéket rendel minden esetben az alapértelmezett eset kivételével, ha van ilyen. Amikor a munkafolyamat fut, a **kapcsoló** művelet összehasonlítja az értéket egy kifejezés, az objektumot, vagy a jogkivonat minden esetben megadott értékekkel. Ha a **kapcsoló** a művelet megkeresi a megfelelő eset, a munkafolyamat futtatása csak adott esetben a műveleteket. Minden alkalommal, amikor a **kapcsoló** lefut, vagy csak egy egyező eset létezik, vagy nincs egyezés található. Ha nincs egyezés, a **kapcsoló** művelet futtatja, az alapértelmezett műveletek. Ismerje meg, [switch-utasítások létrehozása](../logic-apps/logic-apps-control-flow-switch-statement.md).

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*kifejezés objektum-vagy-jogkivonat*> | Változó | A kifejezés, a JSON-objektum vagy a token kiértékelése | 
| <*művelet neve*> | Karakterlánc | A megfelelő esethez futtatni kívánt műveletet neve | 
| <*a művelet-definíció*> | JSON-objektum | A megfelelő esethez futtatni kívánt műveletet definíciója | 
| <*egyező értéket*> | Változó | A kiértékelt eredménnyel összehasonlítandó érték | 
|||| 

*Nem kötelező*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*alapértelmezett műveletnév*> | Karakterlánc | Az alapértelmezett művelet futtatását, amikor nem egyező eset létezik neve | 
| <*alapértelmezett – művelet-definíció*> | JSON-objektum | A művelet futtatását, amikor nem egyező eset létezik definíciója | 
|||| 

*Példa*

Ez a művelet a definíció kiértékeli-e a személyt válaszol a jóváhagyást kérő e-mailben a kiválasztott a "Jóváhagyás" vagy az "Elutasítás" lehetőséget. Ez a választás alapján a **kapcsoló** művelet futtatja a műveleteket, hogy egy másik e-mail küldése a válaszadó, de minden esetben máshogyan egyező esetekhez. 

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

### <a name="until-action"></a>Amíg a művelet

A hurok művelet futni, amíg a megadott feltétel teljesül műveleteket tartalmaz. A hurok utolsó lépéseként ellenőrzi a feltételt, minden más műveletek futtatása után. Az egynél több művelet is felvehet a `"actions"` objektumot, és a műveletet meg kell adnia legalább egy határértéket. Ismerje meg, [létrehozása "until" hurkok](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

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

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*művelet neve*> | Karakterlánc | A hurok belül futó kívánt művelet neve | 
| <*Művelettípus*> | Karakterlánc | A futtatni kívánt művelet típusa | 
| <*művelet – bemenetek*> | Különböző | A bemenetek a futtatni kívánt műveletet | 
| <*a feltétel*> | Karakterlánc | A feltétel, vagy ha minden kiértékelendő kifejezés a műveletek a hurok futása befejeződik | 
| <*hurok-száma*> | Egész szám | A hurkok, amelyek futtathatók a művelet a legtöbb számára vonatkozó határértéket. Az alapértelmezett `count` értéke 60. | 
| <*hurok-időtúllépés*> | Karakterlánc | A leghosszabb idő, a hurok futtatható a határértéket. Az alapértelmezett `timeout` érték `PT1H`, azaz a szükséges [ISO 8601 formátumú](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Példa*

A hurok művelet definíció HTTP-kérelmet küld a megadott URL-cím addig, amíg ezek a feltételek valamelyike teljesül: 

* A kérelem válaszként megkapja a "200 OK" állapotkódot.
* A hurok 60 alkalommal futott.
* A hurok egy óráig futott.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200])",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhookok és -előfizetések

Webhookokon alapuló triggereket és műveleteket nem rendszeresen ellenőrzi a végpontokat, de konkrét eseményeket vagy adatokat, ezekre a végpontokra inkább várja. Ezek triggereket és műveleteket *előfizetés* azáltal, hogy a végpontokhoz egy *visszahívási URL-Címének* ahol a végpont válaszokat is küldhet.

A `subscribe` hívása történik, ha a munkafolyamat változik semmilyen módon, például amikor történik meg a hitelesítő adatokat, vagy amikor egy trigger vagy művelet esetén módosítsa a bemeneti paraméterek. Ez a hívás ugyanazokat a paramétereket használja a szabványos HTTP-műveleteket. 

A `unsubscribe` hívás automatikusan történik, ha egy műveletet hajt végre az eseményindítót vagy műveletet érvénytelen, például:

* Törlése, vagy az eseményindító letiltása. 
* Törlése, vagy a munkafolyamat letiltása. 
* Törlése, vagy az előfizetés letiltása. 

Ezeket a hívásokat támogatásához a `@listCallbackUrl()` kifejezést ad vissza egy egyedi "visszahívási URL-címe" az eseményindítót vagy műveletet. Az URL-címet a végpontok a szolgáltatás REST API-t használó egyedi azonosítóját jelöli. Ez a függvény paramétereit ugyanazok, mint a webhook eseményindítót vagy műveletet.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Aszinkron időtartam módosítása

Eseményindítók és műveletek, korlátozhatja az aszinkron minta egy adott időtartam alatt az időtartam hozzáadásával a `limit.timeout` tulajdonság. Így ha a művelet nem fejeződött be, amikor a időköze le nem telik, a művelet állapota van megjelölve, `Cancelled` együtt a `ActionTimedOut` kódot. A `timeout` tulajdonságot használ [ISO 8601 formátumú](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). 

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

## <a name="runtime-configuration-settings"></a>Modul konfigurációs beállítások

Módosíthatja a triggereket és műveleteket ezekkel az alapértelmezett működését `runtimeConfiguration` tulajdonságok az eseményindítót vagy műveletet-definícióban.

| Tulajdonság | Típus | Leírás | Eseményindítót vagy műveletet | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Egész szám | Módosítsa a [ *alapértelmezett korlát* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) egyszerre, vagy a párhuzamosan futtatható logic app-példányok számát. Ez az érték segítségével, amelyek megkapják a háttérrendszerek kérések számának korlátozásához. <p>Beállítás a `runs` tulajdonságot `1` beállításként ugyanúgy működik a `operationOptions` tulajdonságot `SingleInstance`. Beállíthatja, vagy tulajdonságot, de nem mindkettőt. <p>Az alapértelmezett korlát módosításához lásd [módosítása az eseményindító egyidejűségi](#change-trigger-concurrency) vagy [példányok egymás után aktiválása](#sequential-trigger). | Minden trigger | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Egész szám | Módosítsa a [ *alapértelmezett korlát* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) logikai alkalmazás futtatását, amikor a logikai alkalmazás már fut a legnagyobb párhuzamos alkalmazáspéldányok várhat példányok száma. Módosíthatja az egyidejűségi korlát a `concurrency.runs` tulajdonság. <p>Az alapértelmezett korlát módosításához lásd [módosítása várakozási futtatások korlátozza](#change-waiting-runs). | Minden trigger | 
| `runtimeConfiguration.concurrency.repetitions` | Egész szám | Módosítsa a [ *alapértelmezett korlát* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) "for each" száma ciklus ismétléseinek egyszerre, vagy a párhuzamosan futtatható. <p>Beállítás a `repetitions` tulajdonságot `1` beállításként ugyanúgy működik a `operationOptions` tulajdonságot `SingleInstance`. Beállíthatja, vagy tulajdonságot, de nem mindkettőt. <p>Az alapértelmezett korlát módosításához lásd [módosítása "for each" egyidejűségi](#change-for-each-concurrency) vagy [futtassa a "for each" egymás után hurkokat](#sequential-for-each). | Művelet: <p>[Foreach](#foreach-action) | 
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Művelet beállításai

Módosíthatja az eseményindítók és műveletek az alapértelmezett viselkedése a `operationOptions` tulajdonság eseményindítót vagy műveletet-definícióban.

| A művelet lehetőség | Típus | Leírás | Eseményindítót vagy műveletet | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Karakterlánc | HTTP-alapú műveletek szinkron módon történik, nem pedig aszinkron módon fut. <p><p>Ezzel a beállítással lásd [szinkron műveletek futtatása](#asynchronous-patterns). | Műveletek: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Válasz](#response-action) | 
| `OptimizedForHighThroughput` | Karakterlánc | Módosítsa a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) száma 5 percenként, műveletvégrehajtások a [maximálisan](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Ezzel a beállítással lásd [magas átviteli módban fusson](#run-high-throughput-mode). | Minden művelet | 
| `Sequential` | Karakterlánc | Futtassa a "for each" hurok az ismétlések egy egyszerre, nem pedig minden a párhuzamosan egyszerre. <p>Ez a beállítás szerint ugyanúgy működik a `runtimeConfiguration.concurrency.repetitions` tulajdonságot `1`. Beállíthatja, vagy tulajdonságot, de nem mindkettőt. <p><p>Ezzel a beállítással lásd [futtassa a "for each" egymás után hurkokat](#sequential-for-each).| Művelet: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Karakterlánc | Az eseményindító minden egyes logikaialkalmazás-példányt az egymás után futnak, és várja meg a korábban aktív Futtatás befejeződik, mielőtt elindítaná a következő logikaialkalmazás-példányt. <p><p>Ez a beállítás szerint ugyanúgy működik a `runtimeConfiguration.concurrency.runs` tulajdonságot `1`. Beállíthatja, vagy tulajdonságot, de nem mindkettőt. <p>Ezzel a beállítással lásd [példányok egymás után aktiválása](#sequential-trigger). | Minden trigger | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Az eseményindító egyidejűségi módosítása

Alapértelmezés szerint a logic app-példányok futnak egyszerre, egy időben, vagy a párhuzamos akár a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Minden trigger példány tehát akkor aktiválódik, előtt a fenti logikaialkalmazás-példányt a lejáratot követően újrainduljon. Ez a korlátozás segít határozzák meg, amelyek megkapják a háttérrendszerek kérelmek. 

Ha módosítani szeretné az alapértelmezett korlát, használhatja a Kódszerkesztő nézet vagy a Logic Apps Designer, mert a Tervező keresztül a feldolgozási beállítás módosítása hozzáadása vagy frissítése a `runtimeConfiguration.concurrency.runs` tulajdonságának az alapul szolgáló eseményindító definíciójában, és ez fordítva is igaz. Ez a tulajdonság azt szabályozza, párhuzamosan futó logic app-példányok maximális száma. 

> [!NOTE] 
> Az eseményindítót egymás után, a Tervező vagy a segítségével a Kódszerkesztő nézet állít be, ha nem állít be az eseményindító `operationOptions` tulajdonságot `SingleInstance` a kódszerkesztőben megtekintése. Ellenkező esetben ellenőrzési hibát kap. További információkért lásd: [példányok egymás után aktiválása](#sequential-trigger).

#### <a name="edit-in-code-view"></a>A kód nézet szerkesztése 

Az alapul szolgáló eseményindító definícióját, hozzáadása vagy frissítése a `runtimeConfiguration.concurrency.runs` közötti értékre tulajdonság `1` és `50` szélsőértékeket is beleértve.

Íme egy példa, amely korlátozza a 10 példányra egyidejű Futtatás:

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

#### <a name="edit-in-logic-apps-designer"></a>A Logic Apps Designerben szerkesztése

1. Az eseményindító jobb felső sarokban a három pontra (…) gombra, és válassza **beállítások**.

2. A **egyidejűség-vezérlés**állítsa be **korlát** való **a**. 

3. Húzza a **mértékét a párhuzamosság** csúszkát a kívánt értéket. Egymás után futnak a logikai alkalmazás, húzza a csúszkát érték **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>"For each" egyidejűségi módosítása

"For each" ciklus ismétléseinek futtathat egy időben, illetve párhuzamosan, akár alapértelmezés szerint a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha módosítani szeretné az alapértelmezett korlát, használhatja a Kódszerkesztő nézet vagy a Logic Apps Designer, mert a Tervező keresztül a feldolgozási beállítás módosítása hozzáadása vagy frissítése a `runtimeConfiguration.concurrency.repetitions` tulajdonságának az alapul szolgáló "for each" művelet definíciója, és fordítva. Ez a tulajdonság azt szabályozza, hogy párhuzamosan futtatható az ismétlések maximális számát.

> [!NOTE] 
> Beállította a "for each" futtatására vonatkozó műveletet egymás után, a Tervező vagy a segítségével a Kódszerkesztő nézet, ha nem állít be a művelet `operationOptions` tulajdonságot `Sequential` a kódszerkesztőben megtekintése. Ellenkező esetben ellenőrzési hibát kap. További információkért lásd: [futtassa a "for each" egymás után hurkokat](#sequential-for-each).

#### <a name="edit-in-code-view"></a>A kód nézet szerkesztése 

Az alapul szolgáló "for each" definíciója, hozzáadása vagy frissítése a `runtimeConfiguration.concurrency.repetitions` közötti értékre tulajdonság `1` és `50` szélsőértékeket is beleértve. 

Íme egy példa, amely korlátozza, az ismétlések 10 egyidejű Futtatás:

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

#### <a name="edit-in-logic-apps-designer"></a>A Logic Apps Designerben szerkesztése

1. Az a **minden** műveletet, a jobb felső sarokban, válassza a három pontra (...) gombot, és válassza **beállítások**.

2. A **egyidejűség-vezérlés**állítsa be **egyidejűség-vezérlés** való **a**. 

3. Húzza a **mértékét a párhuzamosság** csúszkát a kívánt értéket. Egymás után futnak a logikai alkalmazás, húzza a csúszkát érték **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Várakozás a futtatások korlát módosítása

Alapértelmezés szerint a logic app-példányok futnak egyszerre, egy időben, vagy a párhuzamos akár a [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Minden trigger példány akkor következik be, mielőtt a korábban aktív logikaialkalmazás-példányt a lejáratot követően újrainduljon. Bár [módosítsa az alapértelmezett korlát](#change-trigger-concurrency), amikor a logic app-példányok száma eléri a új egyidejűségi korlát bármilyen egyéb új példányok futtatásához meg kell várnia. 

Is rendelkezik, amely várhat a futtatások száma egy [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits), amelyet módosíthat. Miután a logikai alkalmazás eléri a korlátot, a várakozási fut, az a Logic Apps-motor már nem fogad új futtatások. Kérés- és webhook eseményindítók 429 hibákat ad vissza, és ismétlődő triggereket indítsa el a rendszer kihagyja a lekérdezési kísérletek.

Ha módosítani szeretné az alapértelmezett korlát a várakozási fut, az alapul szolgáló eseményindító definícióját, és adja hozzá a `runtimeConfiguration.concurency.maximumWaitingRuns` vlastnost s közötti értéket `0` és `100`. 

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

### <a name="trigger-instances-sequentially"></a>Példányok egymás után aktiválása

Minden egyes logikai példányt csak az előző példány befejeződése után fut, állítsuk be az eseményindító, egymás után futnak. Használhatja a Kódszerkesztő nézet vagy a Logic Apps Designerben mert designer keresztül a feldolgozási beállítás módosítása is hozzáadása vagy frissítése a `runtimeConfiguration.concurrency.runs` tulajdonságának az alapul szolgáló eseményindító definíciójában, és ez fordítva is igaz. 

> [!NOTE] 
> Amikor beállít egy olyan eseményindítót egymás után, a Tervező vagy a segítségével a Kódszerkesztő nézet, nem állít be az eseményindító `operationOptions` tulajdonságot `Sequential` a kódszerkesztőben megtekintése. Ellenkező esetben ellenőrzési hibát kap. 

#### <a name="edit-in-code-view"></a>A kód nézet szerkesztése

Az eseményindító definíciójában, állítsa be, vagy ezeket a tulajdonságokat, de nem mindkettőt. 

Állítsa be a `runtimeConfiguration.concurrency.runs` tulajdonságot `1`:

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

*– vagy –*

Állítsa be a `operationOptions` tulajdonságot `SingleInstance`:

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

#### <a name="edit-in-logic-apps-designer"></a>A Logic Apps Designerben szerkesztése

1. Az eseményindító jobb felső sarokban a három pontra (…) gombra, és válassza **beállítások**.

2. A **egyidejűség-vezérlés**állítsa be **korlát** való **a**. 

3. Húzza a **mértékét a párhuzamosság** csúszkát a szám a `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Futtassa a "for each" hurkokat egymás után

Egy "mindegyikre" hurkot iteráció csak az előző iteráció befejeződése után fut, állítsuk be a "for each" műveletet az egymás után futnak. Használhatja a Kódszerkesztő nézet vagy a Logic Apps Designer, mert a művelet egyidejűséget designer módosítása is hozzáadása vagy frissítése a `runtimeConfiguration.concurrency.repetitions` tulajdonságának az alapul szolgáló művelet-definícióban, és ez fordítva is igaz. 

> [!NOTE] 
> Amikor beállít egy "for each" futtatására vonatkozó műveletet egymás után, a Tervező vagy segítségével Kódszerkesztő megtekintése, a művelet nem beállítása `operationOptions` tulajdonságot `Sequential` a kódszerkesztőben megtekintése. Ellenkező esetben ellenőrzési hibát kap. 

#### <a name="edit-in-code-view"></a>A kód nézet szerkesztése

A művelet-definícióban állítsa be, vagy ezeket a tulajdonságokat, de nem mindkettőt. 

Állítsa be a `runtimeConfiguration.concurrency.repetitions` tulajdonságot `1`:

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

*– vagy –*

Állítsa be a `operationOptions` tulajdonságot `Sequential`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>A Logic Apps Designerben szerkesztése

1. Az a **minden** művelet jobb felső sarokban, válassza a három pontra (...) gombot, és válassza **beállítások**.

2. A **egyidejűség-vezérlés**állítsa be **egyidejűség-vezérlés** való **a**. 

3. Húzza a **mértékét a párhuzamosság** csúszkát a szám a `1`. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Szinkron műveletek futtatása

Alapértelmezés szerint az összes HTTP-alapú műveletet a normál aszinkron művelet mintát követik. Ezt a mintát adja meg, hogy ha egy HTTP-alapú műveletet egy kérést küld a megadott végponton, a távoli kiszolgáló küld vissza "202-es ELFOGADVA" választ. A válasz azt jelenti, hogy a kiszolgáló fogadja a kérelem feldolgozásához. A Logic Apps-motor folyamatosan ellenőrzi az URL-cím feldolgozása leáll, amíg a válasz location fejlécébe által megadott, vagyis nem 202 választ.

Azonban a kérések időtúllépés kell korlátozni, így a hosszú ideig futó műveletek, letilthatja az aszinkron viselkedés hozzáadásával és beállításával a `operationOptions` tulajdonságot `DisableAsyncPattern` a műveletek bemenetei alatt.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Magas átviteli módban fusson

Egyetlen logikai alkalmazás futtatásának, 5 percenként végrehajtott műveletek számát rendelkezik egy [alapértelmezett korlát](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Ez a vonatkozó korlát megemeléséhez a [maximális](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) lehetséges, állítsa be a `operationOptions` tulajdonságot `OptimizedForHighThroughput`. Ez a beállítás a logikai alkalmazás "nagy átviteli sebességű" módba helyezi. 

> [!NOTE]
> Nagy átviteli sebességű módja előzetes verzióban érhető el. Egy számítási feladatot is szét szükség szerint több mint egy logikai alkalmazást.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-triggers-or-actions"></a>Eseményindítók és műveletek hitelesítése

HTTP-végpontokat különböző hitelesítési támogatja. Beállíthatja a hitelesítést ezekhez a HTTP-eseményindítók és műveletek:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)

Az alábbiakban a bármilyen típusú hitelesítés állíthatja be:

* [Alapszintű hitelesítés](#basic-authentication)
* [Ügyféltanúsítvány-alapú hitelesítés](#client-certificate-authentication)
* [Az Azure Active Directory (Azure AD) OAuth-hitelesítés](#azure-active-directory-oauth-authentication)

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Alapszintű hitelesítés

A hitelesítési típus az eseményindítót vagy műveletet definíciót tartalmazhat egy `authentication` ezeket a tulajdonságokat tartalmazó JSON-objektum:

| Tulajdonság | Szükséges | Érték | Leírás | 
|----------|----------|-------|-------------| 
| **type** | Igen | "Alapszintű" | A hitelesítési típus szeretne használni, amely itt az "Alapszintű" | 
| **felhasználónév** | Igen | "@parameters(userNameParam)" | Egy paraméter, amely átadja a felhasználó nevét a célként megadott szolgáltatás végpontjának eléréséhez szükséges hitelesítéséhez |
| **jelszó** | Igen | "@parameters(passwordParam)" | Egy paraméter, amely továbbítja a jelszót a hitelesítéshez a célként megadott szolgáltatás végpontjának eléréséhez |
||||| 

Például a következő formátumát a `authentication` objektum az eseményindítót vagy műveletet definíciójában. Paraméterek védelmével kapcsolatos további információkért lásd: [bizalmas adatok biztonságos](#secure-info). 

```javascript
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Ügyféltanúsítvány-alapú hitelesítés

A hitelesítési típus az eseményindítót vagy műveletet definíciót tartalmazhat egy `authentication` ezeket a tulajdonságokat tartalmazó JSON-objektum:

| Tulajdonság | Szükséges | Érték | Leírás | 
|----------|----------|-------|-------------| 
| **type** | Igen | "ClientCertificate" | A Secure Sockets Layer (SSL) ügyféltanúsítványok használandó hitelesítési típus | 
| **PFX** | Igen | <*Base64-kódolású-pfx-fájl*> | A base64-kódolású tartalmak személyes információcsere (PFX) fájlból |
| **jelszó** | Igen | "@parameters(passwordParam)" | Egy paraméter fér hozzá a PFX-fájl jelszava |
||||| 

Például a következő formátumát a `authentication` objektum az eseményindítót vagy műveletet definíciójában. Paraméterek védelmével kapcsolatos további információkért lásd: [bizalmas adatok biztonságos](#secure-info). 

```javascript
"authentication": {
   "password": "@parameters('passwordParam')",
   "pfx": "aGVsbG8g...d29ybGQ=",
   "type": "ClientCertificate"
}
```

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Az Azure Active Directory (AD) OAuth-hitelesítés

A hitelesítési típus az eseményindítót vagy műveletet definíciót tartalmazhat egy `authentication` ezeket a tulajdonságokat tartalmazó JSON-objektum:

| Tulajdonság | Szükséges | Érték | Leírás | 
|----------|----------|-------|-------------| 
| **type** | Igen | `ActiveDirectoryOAuth` | A hitelesítési típus szeretne használni, amely az Azure AD OAuth "ActiveDirectoryOAuth" | 
| **szolgáltató** | Nem | <*URL-cím-az-szolgáltató-jogkivonat-kibocsátó*> | A szolgáltató által biztosított a hitelesítési jogkivonat URL-címe |  
| **bérlő** | Igen | <*Bérlőazonosító*> | A bérlő Azonosítóját az Azure AD-bérlő számára | 
| **Célközönség** | Igen | <*erőforrás-engedélyezés*> | Az erőforrást, amelyeket szeretne használni, például engedélyezési `https://management.core.windows.net/` | 
| **ClientId** | Igen | <*ügyfél-azonosító*> | Engedély kérése az alkalmazás ügyfél-azonosítója | 
| **credentialType** | Igen | "Secret" vagy "Tanúsítványok" | A hitelesítőadat-típus az ügyfél engedélyezési kérése használ. Ez a tulajdonság és érték nem jelennek meg az alapul szolgáló definíciójának, de a hitelesítőadat-típus szükséges paraméterek határozza meg. | 
| **jelszó** | Igen, csak a "Tanúsítványok" hitelesítő adatok típusa | "@parameters(passwordParam)" | Egy paraméter fér hozzá a PFX-fájl jelszava | 
| **PFX** | Igen, csak a "Tanúsítványok" hitelesítő adatok típusa | <*Base64-kódolású-pfx-fájl*> | A base64-kódolású tartalmak személyes információcsere (PFX) fájlból |
| **Titkos kulcs** | Igen, csak a "Secret" hitelesítőadat-típus esetében | <*titkos kulcs-az-hitelesítés*> | Az ügyfél használja kérő engedélyezési base64-kódolású titkos kulcs |
||||| 

Például a következő formátumát a `authentication` objektumot, amikor az eseményindítót vagy műveletet definíciója használja a "Secret" hitelesítő adatok típusa: Paraméterek védelmével kapcsolatos további információkért lásd: [bizalmas adatok biztonságos](#secure-info). 

```javascript
"authentication": {
   "audience": "https://management.core.windows.net/",
   "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
   "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
   "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
   "type": "ActiveDirectoryOAuth"
}
```

<a name="secure-info"></a>

## <a name="secure-sensitive-information"></a>Bizalmas adatok védelme

Használhat olyan hitelesítést, például felhasználóneveket és jelszavakat, az eseményindító és művelet definíciókat, az érzékeny információk védelmére használható paramétereket, és a `@parameters()` kifejezés úgy, hogy ezt az információt nem látható, a logikai mentése után alkalmazás. 

Tegyük fel például, "Alapszintű" hitelesítési használ az eseményindítót vagy műveletet definíciójában. Íme egy példa `authentication` objektum, amely megadja a felhasználónevet és jelszót:

```javascript
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

Az a `parameters` című szakaszt a logikai alkalmazás definíciójának, akkor az eseményindítót vagy műveletet definíciójában használt paraméterek megadásához:

```javascript
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "HTTP": {
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
},
```

Ha létrehozásakor, és egy Azure Resource Manager üzembe helyezési sablon használatával is be kell felvenni egy külső `parameters` a Sablondefiníció a következő szakaszban. Paraméterek védelmével kapcsolatos további információkért lásd: [biztonságos hozzáférés a logic apps a](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). 

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [munkafolyamat-definíciós nyelv](../logic-apps/logic-apps-workflow-definition-language.md)
