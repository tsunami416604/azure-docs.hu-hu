---
title: Sémahivatkozás eseményindító- és művelettípusokhoz
description: Séma-referenciaútmutató munkafolyamat-definíciós nyelv eseményindítójához és művelettípusaihoz az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 18e9c9d330ffb8cc4e284fc649cff0840ec2c82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270367"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Séma-útmutató az Azure Logic Apps eseményindító- és művelettípusaihoz

Ez a hivatkozás a logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójában az eseményindítók és műveletek azonosítására használt általános típusokat ismerteti, amelyet a [munkafolyamat-definíciónyelve](../logic-apps/logic-apps-workflow-definition-language.md)ír le és érvényesít. A logikai alkalmazásokban használható konkrét összekötő-eseményindítók és műveletek megkereséséhez tekintse meg az [Összekötők áttekintése](https://docs.microsoft.com/connectors/)című listát.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Eseményindítók – áttekintés

Minden munkafolyamat tartalmaz egy eseményindítót, amely meghatározza a hívásokat, amelyek példányosítják és elindítják a munkafolyamatot. Itt vannak az általános trigger kategóriák:

* *Lekérdezési* eseményindító, amely rendszeres időközönként ellenőrzi a szolgáltatás végpontját

* Egy *leküldéses* eseményindító, amely létrehoz egy előfizetést egy végponthoz, és egy *visszahívási URL-címet* biztosít, így a végpont értesítheti az eseményindítót, ha a megadott esemény történik, vagy adatok érhetők el. Az eseményindító ezután megvárja a végpont válaszát a tüzelés előtt.

Az eseményindítók a legfelső szintű elemeket tartalmazják, bár néhány nem kötelező:  
  
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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*eseményindító neve*> | Sztring | Az eseményindító neve | 
| <*eseményindító típusa*> | Sztring | Az eseményindító típusa, például "Http" vagy "ApiConnection" | 
| <*trigger-bemenetek*> | JSON-objektum | Az eseményindító viselkedését meghatározó bemenetek | 
| <*időegység*> | Sztring | Az időegység, amely leírja, hogy milyen gyakran a ravaszt tüzek: "Második", "Perc", "Óra", "Nap", "Hét", "Hónap" | 
| <*időegységek száma*> | Egész szám | Egy érték, amely megadja, hogy az eseményindító milyen gyakran aktiválódjon a gyakoriság alapján, amely az időegységek száma, amelyet meg kell várni, amíg az eseményindító újra aktiválódik <p>A minimális és maximális intervallumok a következők: <p>- Hónap: 1-16 hónap </br>- Nap: 1-500 nap </br>- Óra: 1-12.000 óra </br>- Perc: 1-72.000 perc </br>- Második: 1-9,999,999 másodperc<p>Ha például az intervallum 6, és a gyakorisága "Hónap", az ismétlődés 6 havonta történik. | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*tömb-with-feltételek*> | Tömb | Egy tömb, amely egy vagy több [feltételt](#trigger-conditions) tartalmaz, amelyek meghatározzák, hogy futtassa-e a munkafolyamatot. Csak az eseményindítók számára érhető el. | 
| <*runtime-config-options*> | JSON-objektum | Az eseményindítók futásidejű `runtimeConfiguration` viselkedését a tulajdonságok beállításával módosíthatja. További információt a [Futásidejű konfigurációs beállítások című](#runtime-config-options)témakörben talál. | 
| <*splitOn-kifejezés*> | Sztring | Tömböt visszaadó eseményindítók hoz meg egy olyan kifejezést, amely a tömbelemeket feldolgozásra több munkafolyamat-példányra [osztja vagy *bontja* ](#split-on-debatch) fel. | 
| <*művelet-opció*> | Sztring | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információt a Műveleti beállítások című témakörben [talál.](#operation-options) | 
|||| 

## <a name="trigger-types-list"></a>Eseményindítók típusainak listája

Minden eseményindítótípus más-más felülettel és bemenetekkel rendelkezik, amelyek meghatározzák az eseményindító viselkedését. 

### <a name="built-in-triggers"></a>Beépített eseményindítók

| Trigger típusa | Leírás | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Bármely végpontot ellenőriz vagy *lekérdezi.* Ennek a végpontnak meg kell felelnie egy adott eseményindító-szerződésnek egy "202" aszinkron minta használatával vagy egy tömb visszaadásával. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Létrehoz egy hívható végpontot a logikai alkalmazáshoz, de meghívja a megadott URL-címet a regisztrációhoz vagy a regisztráció megszüntetéséhez. |
| [**Ismétlődés**](#recurrence-trigger) | Tüzek egy meghatározott ütemezés alapján. Beállíthatja az eseményindító kilövési dátumát és időpontját. A gyakoriság alapján megadhatja a munkafolyamat futtatásának idejét és napjait is. | 
| [**Kérés**](#request-trigger)  | Létrehoz egy hívható végpontot a logikai alkalmazáshoz, és "manuális" eseményindítóként is ismert. Lásd például: [Hívás, eseményindító vagy munkafolyamatok beágyazása HTTP-végpontokkal.](../logic-apps/logic-apps-http-endpoint.md) | 
||| 

### <a name="managed-api-triggers"></a>Felügyelt API-eseményindítók

| Trigger típusa | Leírás | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | A végpontot a [Microsoft által felügyelt API-k](../connectors/apis-list.md)használatával ellenőrzi vagy *lekérdezi.* | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Létrehoz egy hívható végpontot a logikai alkalmazáshoz a [Microsoft által felügyelt API-k](../connectors/apis-list.md) felhívásával az előfizetéshez és a leiratkozáshoz. | 
||| 

## <a name="triggers---detailed-reference"></a>Triggerek – részletes referencia

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection eseményindító  

Ez az eseményindító a [Microsoft által felügyelt API-k](../connectors/apis-list.md) használatával ellenőrzi vagy *lekérdezi* a végpontot, így az eseményindító paraméterei a végponttól függően eltérőek lehetnek. Az eseményindító definíciójának számos szakasza nem kötelező. Az eseményindító viselkedése attól függ, hogy a szakaszok szerepelnek-e.

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

*Szükséges*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Sztring | Az eseményindító neve |
| <*kapcsolat neve*> | Sztring | A munkafolyamat által használt felügyelt API-val való kapcsolat neve |
| <*metódustípus*> | Sztring | A felügyelt API-val való kommunikáció HTTP-módszere: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*api-művelet*> | Sztring | A hívandó API-művelet |
| <*időegység*> | Sztring | Az időegység, amely leírja, hogy milyen gyakran a ravaszt tüzek: "Második", "Perc", "Óra", "Nap", "Hét", "Hónap" |
| <*időegységek száma*> | Egész szám | Egy érték, amely megadja, hogy az eseményindító milyen gyakran aktiválódjon a gyakoriság alapján, amely az időegységek száma, amelyet meg kell várni, amíg az eseményindító újra aktiválódik <p>A minimális és maximális intervallumok a következők: <p>- Hónap: 1-16 hónap </br>- Nap: 1-500 nap </br>- Óra: 1-12.000 óra </br>- Perc: 1-72.000 perc </br>- Második: 1-9,999,999 másodperc<p>Ha például az intervallum 6, és a gyakorisága "Hónap", az ismétlődés 6 havonta történik. |
||||

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testre szabja az újrapróbálkozási viselkedést a 408-as, 429-es és 5XX-es állapotkóddal rendelkező időszakos hibák, valamint a kapcsolódási kivételek újrapróbálkozási viselkedése miatt. További információt az [Újraházirendek](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*lekérdezés-paraméterek*> | JSON-objektum | Az API-híváshoz foglalandó lekérdezési paraméterek. Az `"queries": { "api-version": "2018-01-01" }` objektum például `?api-version=2018-01-01` hozzáadódik a híváshoz. | 
| <*max-fut*> | Egész szám | Alapértelmezés szerint a munkafolyamat-példányok egyidejűleg (egyidejűleg vagy párhuzamosan) futnak az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Ha új <*számláló*> érték beállításával szeretné módosítani ezt a korlátot, olvassa el az [Eseményindító kondiviáteriszor-változásának módosítása](#change-trigger-concurrency). | 
| <*max-runs-várólista*> | Egész szám | Ha a munkafolyamat már futtatja a példányok maximális számát, `runtimeConfiguration.concurrency.runs` amely a tulajdonság alapján módosítható, minden új futtatás ebbe a várólistába kerül az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Az alapértelmezett korlát módosításáról a [Várakozási futtatások módosítása korlát című témakörben](#change-waiting-runs)olvashat. | 
| <*splitOn-kifejezés*> | Sztring | A tömböket visszaadó eseményindítók esetében ez a kifejezés a használni kívánt tömbre hivatkozik, így minden tömbelemhez létrehozhat és futtathat munkafolyamat-példányt, nem pedig "minden hez" ciklust. <p>Ez a kifejezés például az eseményindító törzsének tartalmán belül visszaadott tömb egy elemét jelöli:`@triggerbody()?['value']` |
| <*művelet-opció*> | Sztring | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információt a Műveleti beállítások című témakörben [talál.](#operation-options) |
||||

*Kimenetek*
 
| Elem | Típus | Leírás |
|---------|------|-------------|
| Fejlécek | JSON-objektum | A válasz fejlécei |
| body (Törzs) | JSON-objektum | A szervezet a válasz |
| állapotkód | Egész szám | A válasz állapotkódja |
|||| 

*Példa*

Ez az eseményindító-definíció minden nap ellenőrzi az e-maileket egy Office 365 Outlook-fiók beérkezett üzenetek mappájában:

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

Ez az eseményindító egy Microsoft által [felügyelt API használatával](../connectors/apis-list.md)küld egy előfizetési kérelmet egy végpontnak, amely egy *visszahívási URL-címet* biztosít, ahol a végpont választ küldhet, és megvárja, amíg a végpont válaszol. További információ: [Endpoint-előfizetések](#subscribe-unsubscribe).

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*kapcsolat neve*> | Sztring | A munkafolyamat által használt felügyelt API-val való kapcsolat neve | 
| <*testtartalom*> | JSON-objektum | Bármilyen üzenettartalom, amelyet hasznos adatként küld a felügyelt API-ba | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testre szabja az újrapróbálkozási viselkedést a 408-as, 429-es és 5XX-es állapotkóddal rendelkező időszakos hibák, valamint a kapcsolódási kivételek újrapróbálkozási viselkedése miatt. További információt az [Újraházirendek](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*lekérdezés-paraméterek*> | JSON-objektum | Az API-hívásba bevonandó lekérdezési paraméterek <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például `?api-version=2018-01-01` hozzáadódik a híváshoz. | 
| <*max-fut*> | Egész szám | Alapértelmezés szerint a munkafolyamat-példányok egyidejűleg (egyidejűleg vagy párhuzamosan) futnak az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Ha új <*számláló*> érték beállításával szeretné módosítani ezt a korlátot, olvassa el az [Eseményindító kondiviáteriszor-változásának módosítása](#change-trigger-concurrency). | 
| <*max-runs-várólista*> | Egész szám | Ha a munkafolyamat már futtatja a példányok maximális számát, `runtimeConfiguration.concurrency.runs` amely a tulajdonság alapján módosítható, minden új futtatás ebbe a várólistába kerül az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Az alapértelmezett korlát módosításáról a [Várakozási futtatások módosítása korlát című témakörben](#change-waiting-runs)olvashat. | 
| <*splitOn-kifejezés*> | Sztring | A tömböket visszaadó eseményindítók esetében ez a kifejezés a használni kívánt tömbre hivatkozik, így minden tömbelemhez létrehozhat és futtathat munkafolyamat-példányt, nem pedig "minden hez" ciklust. <p>Ez a kifejezés például az eseményindító törzsének tartalmán belül visszaadott tömb egy elemét jelöli:`@triggerbody()?['value']` |
| <*művelet-opció*> | Sztring | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információt a Műveleti beállítások című témakörben [talál.](#operation-options) | 
|||| 

*Példa*

Ez az eseményindító-definíció előfizet az Office 365 Outlook API-ra, megadja az API-végpont visszahívási URL-címét, és megvárja, amíg a végpont válaszol, amikor új e-mail érkezik.

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

Ez az eseményindító kérést küld a megadott HTTP- vagy HTTPS-végpontnak a megadott ismétlődési ütemezés alapján. Az eseményindító ezután ellenőrzi a választ, hogy meghatározza, hogy a munkafolyamat fut.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
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

*Szükséges*

| Tulajdonság | Érték | Típus | Leírás |
|----------|-------|------|-------------|
| `method` | <*metódustípus*> | Sztring | A kimenő kérelem küldésére használt módszer: "GET", "PUT", "POST", "PATCH", vagy "DELETE" |
| `uri` | <*HTTP-or-HTTPS-endpoint-URL*> | Sztring | A HTTP- vagy HTTPS-végpont URL-címe, ahová a kimenő kérelmet el szeretné küldeni. Maximális karakterláncméret: 2 KB <p>Egy Azure-szolgáltatás vagy -erőforrás esetén ez az URI-szintaxis tartalmazza az erőforrás-azonosítót és az elérni kívánt erőforrás elérési útját. |
| `frequency` | <*időegység*> | Sztring | Az időegység, amely leírja, hogy milyen gyakran a ravaszt tüzek: "Második", "Perc", "Óra", "Nap", "Hét", "Hónap" |
| `interval` | <*időegységek száma*> | Egész szám | Egy érték, amely megadja, hogy az eseményindító milyen gyakran aktiválódjon a gyakoriság alapján, amely az időegységek száma, amelyet meg kell várni, amíg az eseményindító újra aktiválódik <p>A minimális és maximális intervallumok a következők: <p>- Hónap: 1-16 hónap </br>- Nap: 1-500 nap </br>- Óra: 1-12.000 óra </br>- Perc: 1-72.000 perc </br>- Második: 1-9,999,999 másodperc<p>Ha például az intervallum 6, és a gyakorisága "Hónap", az ismétlődés 6 havonta történik. |
|||||

*Választható*

| Tulajdonság | Érték | Típus | Leírás |
|----------|-------|------|-------------|
| `headers` | <*fejléc-tartalom*> | JSON-objektum | A kérelemhez mellékeléshez szükséges fejlécek <p>Például a nyelv és a típus beállításához: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*lekérdezés-paraméterek*> | JSON-objektum | A kérelemben használandó lekérdezési paraméterek <p>Például az `"queries": { "api-version": "2018-01-01" }` objektum `?api-version=2018-01-01` hozzáadódik a kéréshez. |
| `body` | <*testtartalom*> | JSON-objektum | A kérelemmel együtt hasznos adatként küldő üzenet tartalma |
| `authentication` | <*hitelesítés-típus-és-tulajdonság-értékek*> | JSON-objektum | A hitelesítési modell, amelyet a kérelem a kimenő kérelmek hitelesítéséhez használ. További információt a [Hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)című témakörben talál. Az Ütemezőn túl a `authority` tulajdonság támogatott. Ha nincs megadva, az `https://management.azure.com/`alapértelmezett érték a , de használhat másik értéket. |
| `retryPolicy` > `type` | <*újrapróbálkozási viselkedés*> | JSON-objektum | Testre szabja az újrapróbálkozási viselkedést a 408-as, 429-es és 5XX-es állapotkóddal rendelkező időszakos hibák, valamint a kapcsolódási kivételek újrapróbálkozási viselkedése miatt. További információt az [Újraházirendek](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. |
| `runs` | <*max-fut*> | Egész szám | Alapértelmezés szerint a munkafolyamat-példányok egyidejűleg (egyidejűleg vagy párhuzamosan) futnak az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Ha új <*számláló*> érték beállításával szeretné módosítani ezt a korlátot, olvassa el az [Eseményindító kondiviáteriszor-változásának módosítása](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*max-runs-várólista*> | Egész szám | Ha a munkafolyamat már futtatja a példányok maximális számát, `runtimeConfiguration.concurrency.runs` amely a tulajdonság alapján módosítható, minden új futtatás ebbe a várólistába kerül az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Az alapértelmezett korlát módosításáról a [Várakozási futtatások módosítása korlát című témakörben](#change-waiting-runs)olvashat. |
| `operationOptions` | <*művelet-opció*> | Sztring | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információt a Műveleti beállítások című témakörben [talál.](#operation-options) |
|||||

*Kimenetek*

| Elem | Típus | Leírás |
|---------|------|-------------| 
| Fejlécek | JSON-objektum | A válasz fejlécei | 
| body (Törzs) | JSON-objektum | A szervezet a válasz | 
| állapotkód | Egész szám | A válasz állapotkódja | 
|||| 

*A bejövő kérelmekre vonatkozó követelmények*

A logikai alkalmazással való megfelelő munka érdekében a végpontnak meg kell felelnie egy adott eseményindító mintának vagy szerződésnek, és fel kell ismernie ezeket a tulajdonságokat:  
  
| Válasz | Kötelező | Leírás | 
|----------|----------|-------------| 
| Állapotkód | Igen | A "200 OK" állapotkód elindul. A többi állapotkód nem indít futtatást. | 
| Újrapróbálkozás utáni fejléc | Nem | A logikai alkalmazás végpontjának ismételt leszavazásáig eltelt másodpercek száma | 
| Hely fejléce | Nem | A következő lekérdezési időközönként hívandó URL-cím. Ha nincs megadva, a program az eredeti URL-címet használja. | 
|||| 

*Példa viselkedések a különböző kérelmek*

| Állapotkód | Próbálkozzon újra a | Viselkedés | 
|-------------|-------------|----------|
| 200 | {nincs} | Futtassa a munkafolyamatot, majd ellenőrizze újra a további adatokat a megadott ismétlődés után. | 
| 200 | 10 másodperc | Futtassa a munkafolyamatot, majd 10 másodperc elteltével ellenőrizze újra a további adatokat. |  
| 202 | 60 másodperc | Ne indítsa el a munkafolyamatot. A következő kísérlet egy percen belül megtörténik, a meghatározott ismétlődés függvényében. Ha a megadott ismétlődés kevesebb, mint egy perc, az újrapróbálkozásutáni fejléc élvez elsőbbséget. Ellenkező esetben a program a megadott ismétlődést használja. | 
| 400 | {nincs} | Rossz kérés, ne futtassa a munkafolyamatot. Ha `retryPolicy` nincs megadva, akkor az alapértelmezett házirend lesz használva. Az újrapróbálkozások számának elérése után az eseményindító újra ellenőrzi az adatokat a megadott ismétlődés után. | 
| 500 | {nincs}| Kiszolgálóhiba, ne futtassa a munkafolyamatot. Ha `retryPolicy` nincs megadva, akkor az alapértelmezett házirend lesz használva. Az újrapróbálkozások számának elérése után az eseményindító újra ellenőrzi az adatokat a megadott ismétlődés után. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook eseményindító  

Ez az eseményindító a logikai alkalmazást hívhatóvá teszi egy olyan végpont létrehozásával, amely a megadott végpont URL-címének felhívásával regisztrálhat egy előfizetést. Amikor létrehozza ezt az eseményindítót a munkafolyamatban, egy kimenő kérelem kezdeményezi az előfizetés regisztrálásának hívását. Így az eseményindító megkezdheti az események figyelését. Ha egy művelet érvénytelenné teszi ezt az eseményindítót, egy kimenő kérelem automatikusan elindítja az előfizetés megszüntetésére irányuló hívást. További információ: [Endpoint-előfizetések](#subscribe-unsubscribe).

AHTTPWebhook-eseményindítókon [aszinkron korlátokat](#asynchronous-limits) is megadhat. **HTTPWebhook** Az eseményindító viselkedése a használt vagy kihagyni kívánt szakaszoktól függ.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
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

Egyes értékek, például <*módszer-típusú*>, `"subscribe"` `"unsubscribe"` mind a, mind az objektumokhoz elérhetők.

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*metódustípus*> | Sztring | Az előfizetési kérelemhez használandó HTTP-módszer: "GET", "PUT", "POST", "PATCH", vagy "DELETE" | 
| <*végpont-feliratkozás-URL*> | Sztring | A végpont URL-címe, hogy hová küldje az előfizetési kérelmet | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*metódustípus*> | Sztring | A lemondási kérelemhez használandó HTTP-módszer: "GET", "PUT", "POST", "PATCH", vagy "DELETE" | 
| <*végpont-leiratkozás-URL*> | Sztring | A végpont URL-címe, ahová a törlési kérelmet el kell küldeni | 
| <*testtartalom*> | Sztring | Az előfizetési vagy lemondási kérelemben elküldandó üzenetek tartalma | 
| <*hitelesítés típusa*> | JSON-objektum | A hitelesítési modell, amelyet a kérelem a kimenő kérelmek hitelesítéséhez használ. További információt a [Hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)című témakörben talál. |
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testre szabja az újrapróbálkozási viselkedést a 408-as, 429-es és 5XX-es állapotkóddal rendelkező időszakos hibák, valamint a kapcsolódási kivételek újrapróbálkozási viselkedése miatt. További információt az [Újraházirendek](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*max-fut*> | Egész szám | Alapértelmezés szerint a munkafolyamat-példányok mindegyike egyszerre (egyidejűleg vagy párhuzamosan) fut az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Ha új <*számláló*> érték beállításával szeretné módosítani ezt a korlátot, olvassa el az [Eseményindító kondiviáteriszor-változásának módosítása](#change-trigger-concurrency). | 
| <*max-runs-várólista*> | Egész szám | Ha a munkafolyamat már futtatja a példányok maximális számát, `runtimeConfiguration.concurrency.runs` amely a tulajdonság alapján módosítható, minden új futtatás ebbe a várólistába kerül az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Az alapértelmezett korlát módosításáról a [Várakozási futtatások módosítása korlát című témakörben](#change-waiting-runs)olvashat. | 
| <*művelet-opció*> | Sztring | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információt a Műveleti beállítások című témakörben [talál.](#operation-options) | 
|||| 

*Kimenetek* 

| Elem | Típus | Leírás |
|---------|------|-------------| 
| Fejlécek | JSON-objektum | A válasz fejlécei | 
| body (Törzs) | JSON-objektum | A szervezet a válasz | 
| állapotkód | Egész szám | A válasz állapotkódja | 
|||| 

*Példa*

Ez az eseményindító létrehoz egy előfizetést a megadott végponthoz, egyedi visszahívási URL-címet biztosít, és megvárja az újonnan közzétett technológiai cikkeket.

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

### <a name="recurrence-trigger"></a>Ismétlődés iszaktiválása  

Ez az eseményindító a megadott ismétlődési ütemezés alapján fut, és egyszerű módot biztosít a rendszeresen futó munkafolyamat létrehozására.

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*időegység*> | Sztring | Az időegység, amely leírja, hogy milyen gyakran a ravaszt tüzek: "Második", "Perc", "Óra", "Nap", "Hét", "Hónap" | 
| <*időegységek száma*> | Egész szám | Egy érték, amely megadja, hogy az eseményindító milyen gyakran aktiválódjon a gyakoriság alapján, amely az időegységek száma, amelyet meg kell várni, amíg az eseményindító újra aktiválódik <p>A minimális és maximális intervallumok a következők: <p>- Hónap: 1-16 hónap </br>- Nap: 1-500 nap </br>- Óra: 1-12.000 óra </br>- Perc: 1-72.000 perc </br>- Második: 1-9,999,999 másodperc<p>Ha például az intervallum 6, és a gyakorisága "Hónap", az ismétlődés 6 havonta történik. | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*kezdő-dátum-idő-formátum-YYYY-MM-DDThh:mm:ss*> | Sztring | A kezdő dátum és idő ebben a formátumban: <p>YYYÉ-HH-DDThh:mm:ss, ha időzónát ad meg <p>– vagy – <p>YÉÉÉ-HH-DDThh:mm:ssZ, ha nem ad meg időzónát <p>Ha például 2017. <p>**Megjegyzés:** Ennek a kezdési időpontnak a jövőben legfeljebb 49 éve van, és [utc dátumidő-formátumban](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)kell követnie az [ISO 8601 dátumidő-meghatározási](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) szintet , de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül . Ha nem ad meg időzónát, a végén szóközök nélkül kell hozzáadnia a "Z" betűt. Ez a "Z" az egyenértékű [hajózási időre](https://en.wikipedia.org/wiki/Nautical_time)vonatkozik . <p>Egyszerű ütemezések esetén a kezdési időpont az első előfordulás, míg az összetett ütemezések esetén az eseményindító nem aktiválódik hamarabb, mint a kezdési időpont. A kezdési dátumokról és időpontokról a [Rendszeresen futó feladatok létrehozása és ütemezése](../connectors/connectors-native-recurrence.md)című témakörben talál további információt. | 
| <*időzóna*> | Sztring | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az eseményindító nem fogadja el [az UTC eltolást.](https://en.wikipedia.org/wiki/UTC_offset) Adja meg az alkalmazni kívánt időzónát. | 
| <*egy vagy több órás jelek*> | Egész vagy egész tömb | Ha a "Nap" vagy a `frequency`"Hét" értéket adja meg a számára, akkor egy vagy több, vesszővel elválasztott egész szám adható meg a munkafolyamat futtatásának nappali órájaként. <p>Ha például a "10", a "12" és a "14" értéket adja meg, akkor 10:00, 12:00 és 14:00 órajelként kapja meg. | 
| <*egy vagy több perces jelek*> | Egész vagy egész tömb | Ha a "Nap" vagy a `frequency`"Hét" értéket adja meg a számára, akkor egy vagy több, vesszővel elválasztott egész szám adható meg az óra azon perceként, amikor futtatni szeretné a munkafolyamatot. <p>Megadhatja például a "30" szót percjelként, és az előző példát használva a nap óráiban 10:30, 12:30 és 14:30 óra kor között. | 
| weekDays | Karakterlánc- vagy karakterlánctömb | Ha a " Hét" értéket adja meg a számára, `frequency`akkor egy vagy több napot is megadhat vesszővel elválasztva, amikor a munkafolyamatot futtatni szeretné: "Hétfő", "Kedd", "Szerda", "Csütörtök", "Péntek", "Szombat" és "Vasárnap" | 
| <*max-fut*> | Egész szám | Alapértelmezés szerint a munkafolyamat-példányok mindegyike egyszerre (egyidejűleg vagy párhuzamosan) fut az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Ha új <*számláló*> érték beállításával szeretné módosítani ezt a korlátot, olvassa el az [Eseményindító kondiviáteriszor-változásának módosítása](#change-trigger-concurrency). | 
| <*max-runs-várólista*> | Egész szám | Ha a munkafolyamat már futtatja a példányok maximális számát, `runtimeConfiguration.concurrency.runs` amely a tulajdonság alapján módosítható, minden új futtatás ebbe a várólistába kerül az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Az alapértelmezett korlát módosításáról a [Várakozási futtatások módosítása korlát című témakörben](#change-waiting-runs)olvashat. | 
| <*művelet-opció*> | Sztring | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információt a Műveleti beállítások című témakörben [talál.](#operation-options) | 
|||| 

*1. példa*

Ez az alapvető ismétlődési eseményindító naponta fut:

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

Megadhatja az eseményindító indításának kezdő dátumát és időpontját. Ez az ismétlődési eseményindító a megadott napon kezdődik, majd naponta aktiválódik:

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

2017. szeptember 9-én 14:00 órakor kezdődik, és minden hétfőn 10:30-kor, 12:30-kor és csendes-óceáni téli idő szerint 12:30-kor indul:

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

További információ, valamint az eseményindítóhoz való példák a [Rendszeresen futó feladatok létrehozása és ütemezése](../connectors/connectors-native-recurrence.md)című témakörben található.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Kérelem eseményindító

Ez az eseményindító a logikai alkalmazást hívhatóvá teszi egy olyan végpont létrehozásával, amely képes fogadni a bejövő kérelmeket. Ehhez az eseményindítóhoz adjon meg egy JSON-sémát, amely leírja és ellenőrzi a bejövő kérelemből az eseményindító által fogadott hasznos vagy bemeneteket. A séma is teszi trigger tulajdonságok könnyebb hivatkozni a későbbi műveletek a munkafolyamatban.

Az eseményindító hívásához az `listCallbackUrl` API-t kell használnia, amely et a [Workflow Service REST API ismerteti.](https://docs.microsoft.com/rest/api/logic/workflows) Az eseményindító HTTP-végpontként való használatáról a [Http-végpontokkal rendelkező munkafolyamatok hívása, eseményindító vagy egymásba ágyazása](../logic-apps/logic-apps-http-endpoint.md)című témakörben olvashat.

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*tulajdonságnév*> | Sztring | Egy tulajdonság neve a JSON-sémában, amely a hasznos terhet írja le | 
| <*tulajdonságtípus*> | Sztring | A szálláshely típusa | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*metódustípus*> | Sztring | Az a módszer, amelyet a bejövő kérelmeknek a logikai alkalmazás hívásához kell használniuk: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*relatív elérési út elfogadott paraméterhez*> | Sztring | Annak a paraméternek a relatív elérési útja, amelyet a végpont URL-címe el tud fogadni | 
| <*szükséges tulajdonságok*> | Tömb | Egy vagy több értéket igénylő tulajdonság | 
| <*max-fut*> | Egész szám | Alapértelmezés szerint a munkafolyamat-példányok mindegyike egyszerre (egyidejűleg vagy párhuzamosan) fut az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Ha új <*számláló*> érték beállításával szeretné módosítani ezt a korlátot, olvassa el az [Eseményindító kondiviáteriszor-változásának módosítása](#change-trigger-concurrency). | 
| <*max-runs-várólista*> | Egész szám | Ha a munkafolyamat már futtatja a példányok maximális számát, `runtimeConfiguration.concurrency.runs` amely a tulajdonság alapján módosítható, minden új futtatás ebbe a várólistába kerül az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Az alapértelmezett korlát módosításáról a [Várakozási futtatások módosítása korlát című témakörben](#change-waiting-runs)olvashat. | 
| <*művelet-opció*> | Sztring | Az alapértelmezett viselkedést a `operationOptions` tulajdonság beállításával módosíthatja. További információt a Műveleti beállítások című témakörben [talál.](#operation-options) | 
|||| 

*Példa*

Ez az eseményindító azt adja meg, hogy a bejövő kérelemnek a HTTP POST metódust kell használnia az eseményindító hívásához, és tartalmaz egy sémát, amely ellenőrzi a bejövő kérelem bemenetét:

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

## <a name="trigger-conditions"></a>Aktiválási feltételek

Bármely eseményindító, és csak az eseményindítók, akkor tartalmazhat egy tömb, amely egy vagy több kifejezéseket feltételek, amelyek meghatározzák, hogy a munkafolyamat futtatásához. Ha hozzá `conditions` szeretné adni a tulajdonságot a munkafolyamat egyik eseményindítójához, nyissa meg a logikai alkalmazást a kódnézet-szerkesztőben.

Megadhatja például, hogy az eseményindító csak akkor aktiválódik, ha egy webhely belső kiszolgálói hibát ad vissza, hivatkozva az eseményindító állapotkódjára a `conditions` tulajdonságban:

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

Alapértelmezés szerint az eseményindító csak a "200 OK" válasz beszerzése után aktiválódik. Amikor egy kifejezés egy eseményindító állapotkódjára hivatkozik, az eseményindító alapértelmezett viselkedése lecserélődik. Ha azt szeretné, hogy az eseményindító egynél több állapotkódhoz , például a "200" és a "201" állapotkódhoz aktiválódjon, ezt a kifejezést kell megadnia feltételként:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Többszörös futtatás indítása

Ha az eseményindító egy tömböt ad vissza a logikai alkalmazás feldolgozásához, néha egy "minden" ciklus túl sokáig tarthat az egyes tömbelemek feldolgozása. Ehelyett használhatja a **SplitOn** tulajdonság ot az eseményindítóban a tömb *debatch.* A kötegelés felosztja a tömbelemeket, és új munkafolyamat-példányt indít el, amely minden tömbelemhez fut. Ez a megközelítés például akkor hasznos, ha olyan végpontot szeretne lekérni, amely több új elemet adhat vissza a lekérdezési időközök között. A **SplitOn** által egyetlen logikai alkalmazásban feldolgozható tömbelemek maximális számát a Korlátok és konfiguráció című témakörben [tésszet.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) 

> [!NOTE]
> A **SplitOn** nem használható szinkron válaszmintával. Minden olyan munkafolyamat, amely **splitont** használ, és tartalmaz egy válaszműveletet, aszinkron módon fut, és azonnal küld választ. `202 ACCEPTED`
>
> Ha az eseményindító egyidejűség engedélyezve van, a [SplitOn korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) jelentősen csökken. Ha az elemek száma meghaladja ezt a korlátot, a SplitOn funkció le van tiltva.
 
Ha az eseményindító Swagger-fájl javunkra egy tömb, amely egy tömb, a **SplitOn** tulajdonság automatikusan hozzáadódik az eseményindítóhoz. Ellenkező esetben adja hozzá ezt a tulajdonságot a válasz hasznos adatához, amely rendelkezik a debatcholni kívánt tömböt.

*Példa*

Tegyük fel, hogy van egy API-ja, amely ezt a választ adja vissza: 
  
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

A logikai alkalmazásnak csak a `Rows`tömb ből származó tartalomra van szüksége, így létrehozhat egy ilyen eseményindítót, mint például:

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
> Ha a `SplitOn` parancsot használja, nem kaphatja meg a tömbön kívüli tulajdonságokat. Így ebben a példában nem `status` tudja beszerezni a tulajdonságot az API-ból visszaadott válaszban.
> 
> A hiba elkerülése `Rows` érdekében, ha a tulajdonság `?` nem létezik, ez a példa az operátort használja.

A munkafolyamat-definíció `@triggerBody().name` most már `name` használhatja `"customer-name-one"` az értékeket, `"customer-name-two"` amelyek az első futtatás és a második futtatás. Így az eseményindító kimenetek a következő példákhoz hasonlóan néznek ki:

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

## <a name="actions-overview"></a>A Műveletek áttekintése

Az Azure Logic Apps különböző művelettípusokat biztosít – mindegyik különböző bemenetekkel rendelkezik, amelyek meghatározzák a művelet egyedi viselkedését. A műveletek a következő magas szintű elemekkel rendelkeznek, bár némelyik nem kötelező:

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------|
| <*műveletneve*> | Sztring | A művelet neve | 
| <*művelet-típusú*> | Sztring | A művelettípusa, például "Http" vagy "ApiConnection"| 
| <*bemeneti név*> | Sztring | A művelet viselkedését meghatározó bemenet neve | 
| <*input-érték*> | Különböző | A bemeneti érték, amely lehet karakterlánc, egész, JSON objektum és így tovább | 
| <*előző-trigger-or-action-status*> | JSON-objektum | Az eseményindító vagy művelet neve és az eredményül kapott állapota, amelynek közvetlenül az aktuális művelet futtatása előtt meg kell futnia. | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------|
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testre szabja az újrapróbálkozási viselkedést a 408-as, 429-es és 5XX-es állapotkóddal rendelkező időszakos hibák, valamint a kapcsolódási kivételek újrapróbálkozási viselkedése miatt. További információ: Újra házirendek. | 
| <*runtime-config-options*> | JSON-objektum | Egyes műveletek esetén a műveletek viselkedését futásidőben módosíthatja `runtimeConfiguration` a tulajdonságok beállításával. További információt a [Futásidejű konfigurációs beállítások című](#runtime-config-options)témakörben talál. | 
| <*művelet-opció*> | Sztring | Egyes műveletek nél a tulajdonság beállításával `operationOptions` módosíthatja az alapértelmezett viselkedést. További információt a Műveleti beállítások című témakörben [talál.](#operation-options) | 
|||| 

## <a name="action-types-list"></a>Művelettípusok listája

Íme néhány általánosan használt művelettípus: 

* [Beépített művelettípusok,](#built-in-actions) például az alábbi példák és még sok más: 

  * [**HTTP**](#http-action) a végpontok HTTP-n vagy HTTPS-en keresztüli hívásához

  * [**Válasz**](#response-action) a megkeresésekre adott válaszokra

  * [**JavaScript-kód végrehajtása**](#run-javascript-code) JavaScript-kódrészletek futtatásához

  * Az Azure Functions hívásának [**függvénye**](#function-action)

  * Adatműveletek, például [**Illesztés**](#join-action), [**Összeállítás**](#compose-action), [**Táblázat**](#table-action), [**Kijelölés**](#select-action)és mások, amelyek különböző bemenetekből hoznak létre vagy alakítanak át adatokat

  * [**Munkafolyamat**](#workflow-action) egy másik logikai alkalmazás munkafolyamatának hívásához

* [Felügyelt API-művelettípusok,](#managed-api-actions) például ApiConnection és [**ApiConnectionWebHook,**](#apiconnectionwebhook-action) amelyek a Microsoft által kezelt különböző összekötőket és API-kat hívnak meg, például az Azure Service Bus, az Office 365 Outlook, a Power BI, az Azure Blob Storage, a OneDrive, a GitHub stb. [**ApiConnection**](#apiconnection-action)

* Az Olyan [munkafolyamat-művelettípusok vezérlése,](#control-workflow-actions) mint az [**If**](#if-action), [**Foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action)és [**Until**](#until-action), amelyek más műveleteket tartalmaznak, és segítenek a munkafolyamat-végrehajtás rendszerezésének megszervezésében

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Beépített műveletek

| Művelettípus | Leírás | 
|-------------|-------------| 
| [**Össze**](#compose-action) | Egyetlen kimenetet hoz létre a bemenetekből, amelyek különböző típusúak lehetnek. | 
| [**JavaScript-kód végrehajtása**](#run-javascript-code) | Futtasson bizonyos feltételeknek megfelelő JavaScript-kódrészleteket. A kódkövetelményekről és további információkról a [Kódrészletek hozzáadása és futtatása szövegközi kódokkal](../logic-apps/logic-apps-add-run-inline-code.md)című témakörben található. |
| [**Függvény**](#function-action) | Egy Azure-függvényt hív meg. | 
| [**HTTP**](#http-action) | HTTP-végpontot hív meg. | 
| [**Csatlakozzon**](#join-action) | Karakterláncot hoz létre egy tömb összes eleméből, és elválasztja azokat egy megadott határoló karakterrel. | 
| [**JSON elemzés**](#parse-json-action) | Felhasználóbarát jogkivonatokat hoz létre a JSON-tartalom tulajdonságaiból. Ezután hivatkozhat ezekre a tulajdonságokra a jogkivonatok logikai alkalmazásban való belefoglalásával. | 
| [**Lekérdezés**](#query-action) | Egy feltétel vagy szűrő alapján tömböt hoz létre egy másik tömb elemeiből. | 
| [**Válasz**](#response-action) | Bejövő hívásra vagy kérésre választ hoz létre. | 
| [**Válassza ki**](#select-action) | JSON-objektumokkal rendelkező tömböt hoz létre egy másik tömb elemeinek átalakításával a megadott térkép alapján. | 
| [**Tábla**](#table-action) | CSV- vagy HTML-táblázatot hoz létre tömbből. | 
| [**Megszünteti**](#terminate-action) | Leállít egy aktívan futó munkafolyamatot. | 
| [**Várakozás**](#wait-action) | A munkafolyamat szüneteltetése egy megadott időtartamra vagy a megadott dátumig és időpontig. | 
| [**Munkafolyamat**](#workflow-action) | Munkafolyamat ot ágyaz egy másik munkafolyamatba. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Felügyelt API-műveletek

| Művelettípus | Leírás | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | HTTP-végpontot hív meg egy [Microsoft által felügyelt API használatával.](../connectors/apis-list.md) | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Úgy működik, mint a HTTP Webhook, de [Microsoft által felügyelt API-t](../connectors/apis-list.md)használ. | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Munkafolyamat-műveletek szabályozása

Ezek a műveletek segítenek a munkafolyamat-végrehajtás szabályozásában és más műveletek ben való feltüntetésben. A vezérlőmunkafolyamat-műveleten kívülről közvetlenül hivatkozhat az adott munkafolyamat-műveleten belüli műveletekre. Ha például egy `Http` hatókörön belül van egy `@body('Http')` művelet, a munkafolyamat bármely pontjáról hivatkozhat a kifejezésre. A vezérlőmunkafolyamat-műveleten belül létező műveletek azonban csak "futtatásután" futtathatnak más műveleteket, amelyek ugyanabban a vezérlőmunkafolyamat-struktúrában vannak.

| Művelettípus | Leírás | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Futtassa ugyanazokat a műveleteket egy ciklusban a tömb minden eleméhez. | 
| [**Ha a(z)**](#if-action) | A megadott feltétel igaz vagy hamis alapján futtatja a műveleteket. | 
| [**Hatókör**](#scope-action) | Műveletek futtatása a csoport állapota alapján egy műveletkészletből. | 
| [**Kapcsoló**](#switch-action) | Esetekbe rendezett műveleteket futtat, amikor a kifejezések, objektumok vagy tokenek értékei megegyeznek az egyes esetek által megadott értékekkel. | 
| [**Amíg**](#until-action) | Addig futtassa a műveleteket egy ciklusban, amíg a megadott feltétel teljesül. | 
|||  

## <a name="actions---detailed-reference"></a>Műveletek - Részletes hivatkozás

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection művelet

Ez a művelet HTTP-kérelmet küld egy [Microsoft által felügyelt API-nak,](../connectors/apis-list.md) és információkat kér az API-ról és a paraméterekről, valamint egy érvényes kapcsolatra mutató hivatkozást. 

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*műveletneve*> | Sztring | Az összekötő által biztosított művelet neve | 
| <*api-név*> | Sztring | A kapcsolathoz használt Microsoft által felügyelt API neve | 
| <*metódustípus*> | Sztring | Az API hívásának HTTP-metódusa: "GET", "PUT", "POST", "PATCH", vagy "DELETE" | 
| <*api-művelet*> | Sztring | A hívandó API-művelet | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*egyéb-cselekvés-specifikus-input-tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó bármely egyéb bemeneti tulajdonság | 
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testre szabja az újrapróbálkozási viselkedést a 408-as, 429-es és 5XX-es állapotkóddal rendelkező időszakos hibák, valamint a kapcsolódási kivételek újrapróbálkozási viselkedése miatt. További információt az [Újraházirendek](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*lekérdezés-paraméterek*> | JSON-objektum | Az API-híváshoz foglalandó lekérdezési paraméterek. <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például `?api-version=2018-01-01` hozzáadódik a híváshoz. | 
| <*egyéb-cselekvés-specifikus tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb tulajdonságok | 
|||| 

*Példa*

Ez a definíció az Office 365 Outlook-összekötőhöz készült **E-mail küldése** műveletet ismerteti, amely a Microsoft által felügyelt API: 

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

Ez a művelet [http-n](../connectors/apis-list.md)keresztüli előfizetési kérelmet küld egy végpontnak egy Microsoft által felügyelt API használatával, amely egy *visszahívási URL-címet* biztosít, ahol a végpont választ küldhet, és megvárja, amíg a végpont válaszol. További információ: [Endpoint-előfizetések](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Egyes értékek, például <*módszer-típusú*>, `"subscribe"` `"unsubscribe"` mind a, mind az objektumokhoz elérhetők.

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*műveletneve*> | Sztring | Az összekötő által biztosított művelet neve | 
| <*metódustípus*> | Sztring | A végpontra való feliratkozáshoz vagy leiratkozáshoz használandó HTTP-módszer: "GET", "PUT", "POST", "PATCH" vagy "DELETE" | 
| <*api-subscribe-URL*> | Sztring | Az API-ra való feliratkozáshoz használandó URI | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | Sztring | Az API-ból való leiratkozáshoz használandó URI | 
| <*fejléc-tartalom*> | JSON-objektum | A kérelem elküldéséhez küldő fejlécek <p>Például a nyelv és a beírás beállítása a kérelemben: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*testtartalom*> | JSON-objektum | A kérelemben elküldandó üzenetek tartalma | 
| <*hitelesítés típusa*> | JSON-objektum | A hitelesítési modell, amelyet a kérelem a kimenő kérelmek hitelesítéséhez használ. További információt a [Hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)című témakörben talál. |
| <*újrapróbálkozási viselkedés*> | JSON-objektum | Testre szabja az újrapróbálkozási viselkedést a 408-as, 429-es és 5XX-es állapotkóddal rendelkező időszakos hibák, valamint a kapcsolódási kivételek újrapróbálkozási viselkedése miatt. További információt az [Újraházirendek](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. | 
| <*lekérdezés-paraméterek*> | JSON-objektum | Az API-hívásba bevonandó lekérdezési paraméterek <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például `?api-version=2018-01-01` hozzáadódik a híváshoz. | 
| <*egyéb-cselekvés-specifikus-input-tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó bármely egyéb bemeneti tulajdonság | 
| <*egyéb-cselekvés-specifikus tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb tulajdonságok | 
|||| 

**Az ApiConnectionWebhook-művelethez** ugyanúgy is megadhat korlátozásokat, mint a [HTTP aszinkron korlátokat.](#asynchronous-limits)

<a name="compose-action"></a>

### <a name="compose-action"></a>Művelet összeállítása

Ez a művelet egyetlen kimenetet hoz létre több bemenetből, beleértve a kifejezéseket is. A kimeneti és a bemeneti lehet bármilyen típusú, amely az Azure Logic Apps natívan támogatja, például tömbök, JSON-objektumok, XML és bináris. Ezután használhatja a művelet kimenetét más műveletekben. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Szükséges* 

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*bemenetek –összeállítás*> | Bármelyik | Egyetlen kimenet létrehozásához használható bemenetek | 
|||| 

*1. példa*

<!-- markdownlint-disable MD038 -->
Ez a műveletdefiníció egy záró `abcdefg ` térrel `1234`és az értékkel egyesül:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Itt van a kimenet, amelyet ez a művelet létrehoz:

`abcdefg 1234`

*2. példa*

Ez a műveletdefiníció egyesít egy `abcdefg` karakterlánc-változót, amely `1234`tartalmaz egy egész változót, amely a következőket tartalmazza:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Itt van a kimenet, amelyet ez a művelet létrehoz:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>JavaScript-kód művelet végrehajtása

Ez a művelet egy JavaScript-kódrészletet futtat, és az eredményeket egy `Result` olyan jogkivonaton keresztül adja vissza, amely későbbi műveletekre hivatkozhat.

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

*Szükséges*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*JavaScript-kódrészlet*> | Változó | A futtatni kívánt JavaScript-kód. A kódkövetelményekről és további információkról a [Kódrészletek hozzáadása és futtatása szövegközi kódokkal](../logic-apps/logic-apps-add-run-inline-code.md)című témakörben található. <p>Az `code` attribútumban a kódrészlet használhatja az írásvédett `workflowContext` objektumot bemenetként. Ez az objektum olyan altulajdonságokkal rendelkezik, amelyek hozzáférést biztosítanak a kódnak az eseményindító és a munkafolyamat korábbi műveletei eredményeihez. Az objektumról `workflowContext` további információt a [Kód eseményindítóés műveleteredményeihivatkozás című témakörben talál.](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext) |
||||

*Bizonyos esetekben szükséges*

Az `explicitDependencies` attribútum azt adja meg, hogy az eseményindítóból, a korábbi műveletekből vagy mindkettőből származó eredményeket kifejezetten a kódkódrészlet függőségeiként kívánja feltüntetni. A függőségek hozzáadásáról a [Szövegközi kód paramétereinek hozzáadása](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters)című témakörben talál további információt. 

Az `includeTrigger` attribútumhoz megadhat `true` vagy `false` értékeket adhat meg.

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*korábbi műveletek*> | Sztringtömb | Tömb a megadott műveletnevekkel. Használja a munkafolyamat-definícióban megjelenő műveletneveket, ahol a műveletnevek aláhúzásjeleket (_), nem szóközöket (" ) használnak. |
||||

*1. példa*

Ez a művelet olyan kódot futtat, amely leadja a \<logikai alkalmazás nevét, és ennek eredményeképpen a "Hello world from logic-app-name>" szöveget adja vissza. Ebben a példában a kód a munkafolyamat nevére `workflowContext.workflow.name` hivatkozik, és `workflowContext` a tulajdonságot az írásvédett objektumon keresztül éri el. Az objektum használatáról `workflowContext` a [Kód eseményindító és műveleteredményeihivatkozás című témakörben talál](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext)további információt.

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

Ez a művelet egy logikai alkalmazásban futtatja a kódot, amely akkor aktiválódik, amikor új e-mail érkezik egy Office 365 Outlook-fiókba. A logikai alkalmazás is használ egy küldési jóváhagyási e-mail művelet, amely továbbítja a tartalmat a fogadott e-mail ben együtt jóváhagyásiránti kérelem.

A kód kinyeri az e-mail `Body` címeket az eseményindító `SelectedOption` tulajdonságából, és visszaadja a címeket a tulajdonság értékével együtt a jóváhagyási műveletből. A művelet kifejezetten tartalmazza a küldési jóváhagyási `explicitDependencies`  >  `actions` e-mail művelet, mint a függőség az attribútumban.

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

### <a name="function-action"></a>Függvényművelet

Ez a művelet egy korábban létrehozott [Azure-függvényt](../azure-functions/functions-create-first-azure-function.md)hív meg.

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------|  
| <*Azure-function-ID*> | Sztring | A hívni kívánt Azure-függvény erőforrásazonosítója. Itt van a formátum az érték:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*metódustípus*> | Sztring | A függvény hívásához használt HTTP-módszer: "GET", "PUT", "POST", "PATCH", vagy "DELETE" <p>Ha nincs megadva, az alapértelmezett a "POST" metódus. | 
||||

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------|  
| <*fejléc-tartalom*> | JSON-objektum | A hívással küldő fejlécek <p>Például a nyelv és a beírás beállítása a kérelemben: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*testtartalom*> | JSON-objektum | A kérelemben elküldandó üzenetek tartalma | 
| <*lekérdezés-paraméterek*> | JSON-objektum | Az API-hívásba bevonandó lekérdezési paraméterek <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például `?api-version=2018-01-01` hozzáadódik a híváshoz. | 
| <*egyéb-cselekvés-specifikus-input-tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó bármely egyéb bemeneti tulajdonság | 
| <*egyéb-cselekvés-specifikus tulajdonságok*> | JSON-objektum | Az adott műveletre vonatkozó egyéb tulajdonságok | 
||||

A logikai alkalmazás mentésekor a Logic Apps motor elvégzi ezeket az ellenőrzéseket a hivatkozott függvényen:

* A munkafolyamatnak hozzáféréssel kell rendelkeznie a függvényhez.

* A munkafolyamat csak egy szabványos HTTP-eseményindítót vagy általános JSON webhook-eseményindítót használhat. 

  A Logic Apps motor leváltja, és gyorsítótárazza az eseményindító URL-címét, amely futásidőben használatos. Ha azonban bármely művelet érvényteleníti a gyorsítótárazott URL-címet, a **Függvény** művelet futásidőben sikertelen lesz. A probléma megoldásához mentse újra a logikai alkalmazást, hogy a logikai alkalmazás megkapja, és újra gyorsítótárazza az eseményindító URL-címét.

* A függvényhez nem lehet definiált útvonal.

* Csak a "függvény" és a "névtelen" engedélyezési szintek engedélyezettek. 

*Példa*

Ez a műveletdefiníció meghívja a korábban létrehozott "GetProductID" függvényt:

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

Ez a művelet kérést küld a megadott HTTP vagy HTTPS-végpontnak, és ellenőrzi a választ annak meghatározásához, hogy a munkafolyamat fut-e.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Szükséges*

| Tulajdonság | Érték | Típus | Leírás |
|----------|-------|------|-------------|
| `method` | <*metódustípus*> | Sztring | A kimenő kérelem küldésére használt módszer: "GET", "PUT", "POST", "PATCH", vagy "DELETE" |
| `uri` | <*HTTP-or-HTTPS-endpoint-URL*> | Sztring | A HTTP- vagy HTTPS-végpont URL-címe, ahová a kimenő kérelmet el szeretné küldeni. Maximális karakterláncméret: 2 KB <p>Egy Azure-szolgáltatás vagy -erőforrás esetén ez az URI-szintaxis tartalmazza az erőforrás-azonosítót és az elérni kívánt erőforrás elérési útját. |
|||||

*Választható*

| Tulajdonság | Érték | Típus | Leírás |
|----------|-------|------|-------------|
| `headers` | <*fejléc-tartalom*> | JSON-objektum | A kérelemhez mellékeléshez szükséges fejlécek <p>Például a nyelv és a típus beállításához: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*lekérdezés-paraméterek*> | JSON-objektum | A kérelemben használandó lekérdezési paraméterek <p>Az `"queries": { "api-version": "2018-01-01" }` objektum például `?api-version=2018-01-01` hozzáadódik a híváshoz. |
| `body` | <*testtartalom*> | JSON-objektum | A kérelemmel együtt hasznos adatként küldő üzenet tartalma |
| `authentication` | <*hitelesítés-típus-és-tulajdonság-értékek*> | JSON-objektum | A hitelesítési modell, amelyet a kérelem a kimenő kérelmek hitelesítéséhez használ. További információt a [Hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)című témakörben talál. Az Ütemezőn túl a `authority` tulajdonság támogatott. Ha nincs megadva, az `https://management.azure.com/`alapértelmezett érték a , de használhat másik értéket. |
| `retryPolicy` > `type` | <*újrapróbálkozási viselkedés*> | JSON-objektum | Testre szabja az újrapróbálkozási viselkedést a 408-as, 429-es és 5XX-es állapotkóddal rendelkező időszakos hibák, valamint a kapcsolódási kivételek újrapróbálkozási viselkedése miatt. További információt az [Újraházirendek](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál. |
| <*egyéb-cselekvés-specifikus-input-tulajdonságok*> | <*input-tulajdonság*> | JSON-objektum | Az adott műveletre vonatkozó bármely egyéb bemeneti tulajdonság |
| <*egyéb-cselekvés-specifikus tulajdonságok*> | <*tulajdonság értéke*> | JSON-objektum | Az adott műveletre vonatkozó egyéb tulajdonságok |
|||||

*Példa*

Ez a műveletdefiníció a legfrissebb híreket a megadott végpontra küldött kéréssel kapja meg:

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

### <a name="join-action"></a>Csatlakozás művelet

Ez a művelet egy karakterláncot hoz létre egy tömb összes eleméből, és elválasztja azokat a megadott határoló karakterrel. 

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Tömb*> | Tömb | A forráselemeket tartalmazó tömb vagy kifejezés. Ha megad egy kifejezést, csatolja a kifejezést idézőjelek közé. | 
| <*Határoló*> | Egykarakteres karakterlánc | A karakterlánc minden elemét elválasztó karakter | 
|||| 

*Példa*

Tegyük fel, hogy van egy korábban létrehozott "myIntegerArray" változója, amely ezt az egész tömböt tartalmazza: 

`[1,2,3,4]` 

Ez a műveletdefiníció leveszi az `variables()` értékeket a változóból a függvény használatával egy kifejezésben, és létrehozza ezt a karakterláncot azokkal az értékekkel, amelyeket vessző választ el egymástól:`"1,2,3,4"`

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

Ez a művelet felhasználóbarát mezőket vagy *jogkivonatokat* hoz létre a JSON-tartalom tulajdonságaiból. Ezután elérheti ezeket a tulajdonságokat a logikai alkalmazásban a jogkivonatok használatával. Ha például json-kimenetet szeretne használni olyan szolgáltatásokból, mint az Azure Service Bus és az Azure Cosmos DB, ezt a műveletet a logikai alkalmazásban is felveheti, így könnyebben hivatkozhat az adott kimenetben lévő adatokra.

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*JSON-forrás*> | JSON-objektum | Az elemezni kívánt JSON-tartalom | 
| <*JSON-séma*> | JSON-objektum | A JSON-séma, amely leírja a JSON-tartalom alapjául szolgáló, amely a művelet a forrás JSON-tartalom elemzéséhez használ. <p>**Tipp**: A Logic Apps Designerben megadhatja a sémát, vagy mintahasznos térhet el, hogy a művelet létrehozhassa a sémát. | 
|||| 

*Példa*

Ez a műveletdefiníció létrehozza ezeket a jogkivonatokat, amelyeket a munkafolyamatban használhat, de csak a **Parse JSON** műveletet követően futó műveletekben:

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

Ebben a példában a "content" tulajdonság határozza meg a JSON-tartalmat a művelet elemzéséhez. Ezt a JSON-tartalmat is megadhatja a séma létrehozásához.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

A "séma" tulajdonság a JSON-tartalom leírásához használt JSON-sémát adja meg:

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

Ez a művelet egy megadott feltétel vagy szűrő alapján egy másik tömb elemeiből hoz létre tömböt.

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Tömb*> | Tömb | A forráselemeket tartalmazó tömb vagy kifejezés. Ha megad egy kifejezést, csatolja a kifejezést idézőjelek közé. |
| <*állapot-vagy-szűrő*> | Sztring | A forrástömb elemeinek szűrésére használt feltétel <p>**Megjegyzés:** Ha egyetlen érték sem felel meg a feltételnek, akkor a művelet üres tömböt hoz létre. |
|||| 

*Példa*

Ez a műveletdefiníció olyan tömböt hoz létre, amelynek értéke nagyobb a megadott értéknél, amely kettő:

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

Ez a művelet létrehozza a http-kérelemre adott válasz hasznos adatát. 

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*válasz-állapotkód*> | Egész szám | A bejövő kérelemnek küldött HTTP-állapotkód. Az alapértelmezett kód "200 OK", de a kód bármilyen érvényes állapotkód lehet, amely 2xx, 4xx vagy 5xx kezdetű, de nem 3xxx-tel. | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*válaszfejlécek*> | JSON-objektum | Egy vagy több fejléc, amelyet a választartalmaz | 
| <*válasz-test*> | Különböző | A választörzs, amely lehet karakterlánc, JSON-objektum vagy akár bináris tartalom egy korábbi műveletből | 
|||| 

*Példa*

Ez a műveletdefiníció választ hoz létre egy HTTP-kérelemre a megadott állapotkóddal, üzenettörzskel és üzenetfejlécekkel:

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

Más műveletektől eltérően a **Válasz** művelet speciális korlátozásokkal rendelkezik: 

* A munkafolyamat csak akkor **használhatja** a Válasz műveletet, ha a munkafolyamat HTTP-kérelem-eseményindítóval kezdődik, ami azt jelenti, hogy a munkafolyamatot HTTP-kérelemnek kell aktiválnia.

* A munkafolyamat **használhatja** a Válasz művelet *bárhol, kivéve* a **Foreach** hurkok, **Amíg** hurkok, beleértve a szekvenciális hurkok, és párhuzamos ágak. 

* Az eredeti HTTP-kérelem csak akkor kapja meg a munkafolyamat válaszát, ha a **Válasz** művelet hez szükséges összes művelet befejeződött a [HTTP-kérelem időkorlátján](../logic-apps/logic-apps-limits-and-config.md#request-limits)belül.

  Ha azonban a munkafolyamat egy másik logikai alkalmazást hív meg beágyazott munkafolyamatként, a szülőmunkafolyamat megvárja, amíg a beágyazott munkafolyamat befejeződik, függetlenül attól, hogy mennyi idő telik el a beágyazott munkafolyamat befejezése előtt.

* Ha a munkafolyamat a **Válasz** műveletet és egy szinkron válaszmintát használja, a munkafolyamat nem használhatja a **splitOn** parancsot az eseményindító-definícióban, mert ez a parancs több futtatást hoz létre. Ellenőrizze ezt az esetet, ha a PUT metódust használja, és ha igaz, "rossz kérés" választ ad vissza.

  Ellenkező esetben, ha a munkafolyamat a **splitOn** parancsot és egy **Válasz** műveletet használja, a munkafolyamat aszinkron módon fut, és azonnal "202 ELFOGADVA" választ ad vissza.

* Amikor a munkafolyamat végrehajtása eléri a **Válasz** műveletet, de a bejövő kérelem már kapott választ, a **Válasz** művelet "Sikertelen" jelöléssel van ellátva az ütközés miatt. Ennek eredményeképpen a logikai alkalmazás futtatása is "Sikertelen" állapotú is van megjelölve.

<a name="select-action"></a>

### <a name="select-action"></a>Művelet kijelölése

Ez a művelet json-objektumokkal rendelkező tömböt hoz létre egy másik tömb elemeinek átalakításával a megadott térkép alapján. A kimeneti tömb és a forrástömb mindig ugyanannyi elemből áll. Bár a kimeneti tömbben lévő objektumok száma nem módosítható, hozzáadhat vagy eltávolíthat tulajdonságokat és azok értékeit ezeken az objektumokon. A `select` tulajdonság legalább egy kulcs-érték párt határoz meg, amely meghatározza a forrástömb elemeinek átalakítására szolgáló leképezést. A kulcs-érték pár egy tulajdonságot és annak értékét jelöli a kimeneti tömb összes objektumában.

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

*Szükséges* 

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Tömb*> | Tömb | A forráselemeket tartalmazó tömb vagy kifejezés. Ügyeljen arra, hogy egy kifejezést idézőjelek közé zárjon. <p>**Megjegyzés:** Ha a forrástömb üres, a művelet üres tömböt hoz létre. | 
| <*kulcsnév*> | Sztring | Az eredményhez rendelt tulajdonságnév <*kifejezésből*> <p>Ha új tulajdonságot szeretne hozzáadni a kimeneti tömb összes objektumához, adjon meg egy <*kulcsnév->* a tulajdonsághoz, és adjon meg egy <*kifejezést,*> a tulajdonság értékéhez. <p>Ha el szeretne távolítani egy tulajdonságot a tömb összes objektumából, hagyja ki a tulajdonság <*kulcsnév->.* | 
| <*Kifejezés*> | Sztring | Az a kifejezés, amely átalakítja az elemet a forrástömbben, és az eredményt <*kulcsnévhez* rendeli> | 
|||| 

A **Kijelölés** művelet kimenetként létrehoz egy tömböt, így minden olyan műveletnek, amely ezt a kimenetet szeretné használni, el kell fogadnia egy tömböt, vagy a tömböt olyan típussá kell alakítania, amelyet a fogyasztói művelet elfogad. Ha például a kimeneti tömböt karakterláncmá szeretné alakítani, átadhatja a tömböt a **Közösségműveletnek,** majd hivatkozhat a Többi művelet **írása** műveletkimenetére.

*Példa*

Ez a műveletdefiníció JSON-objektumtömböt hoz létre egész tömbből. A művelet végighalad a forrástömbön, leveszi az `@item()` egyes egész értékeket a kifejezés`number`használatával, és minden értéket hozzárendel az egyes JSON-objektumok " " " tulajdonságához:

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

A művelet által létrehozott tömb a következőket hozza létre:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Ha ezt a tömbkimenetet más műveletekben szeretné használni, adja át ezt a kimenetet egy **Összeállítás** műveletnek:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Ezután **használhatja** a Közösség művelet kimenetét a többi műveletben, például az **Office 365 Outlook – E-mail küldése** műveletben:

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

Ez a művelet egy TÖMBBŐL hoz létre CSV- vagy HTML-táblát. A JSON-objektumokkal rendelkező tömbök esetében ez a művelet automatikusan létrehozza az oszlopfejléceket az objektumok tulajdonságneveiből. Más adattípusú tömbök esetén meg kell adniaz oszlopfejléceket és -értékeket. Ez a tömb például tartalmazza az "ID" és a "Product_Name" tulajdonságokat, amelyeket ez a művelet az oszlopfejlécek nevéhez használhat:

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

*Szükséges* 

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| \<CSV *vagy* HTML>| Sztring | A létrehozni kívánt táblázat formátuma | 
| <*Tömb*> | Tömb | A tábla forráselemeit tartalmazó tömb vagy kifejezés <p>**Megjegyzés:** Ha a forrástömb üres, a művelet üres táblát hoz létre. | 
|||| 

*Választható*

Az oszlopfejlécek és -értékek megadásához vagy testreszabásához használja a `columns` tömböt. Ha `header-value` a párok fejlécneve megegyezik, az értékeik ugyanabban az oszlopban jelennek meg a fejléc neve alatt. Ellenkező esetben minden egyedi fejléc egyedi oszlopot határoz meg.

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*oszlopnév*> | Sztring | Egy oszlop fejlécneve | 
| <*oszlop-érték*> | Bármelyik | Az oszlop értéke | 
|||| 

*1. példa*

Tegyük fel, hogy van egy korábban létrehozott "myItemArray" változója, amely jelenleg ezt a tömböt tartalmazza:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Ez a műveletdefiníció egy CSV-táblát hoz létre a "myItemArray" változóból. A tulajdonság által `from` használt kifejezés a tömböt a "myItemArray" `variables()` függvényből szerzi be a következő függvénnyel:

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

A művelet által létrehozott CSV-tábla a következőket hozza létre: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*2. példa*

Ez a műveletdefiníció html-táblázatot hoz létre a "myItemArray" változóból. A tulajdonság által `from` használt kifejezés a tömböt a "myItemArray" `variables()` függvényből szerzi be a következő függvénnyel:

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

A művelet által létrehozott HTML-táblázat a következőket hozza létre: 

<table><thead><tr><th>ID (Azonosító)</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Alma</td></tr><tr><td>1</td><td>Narancs</td></tr></tbody></table>

*3. példa*

Ez a műveletdefiníció html-táblázatot hoz létre a "myItemArray" változóból. Ez a példa azonban felülbírálja az alapértelmezett oszlopfejlécneveket a "Stock_ID" és a "Leírás" szöveggel, és hozzáadja az "Organikus" szót a "Leírás" oszlop értékeihez.

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

A művelet által létrehozott HTML-táblázat a következőket hozza létre: 

<table><thead><tr><th>Stock_ID</th><th>Leírás</th></tr></thead><tbody><tr><td>0</td><td>Szerves alma</td></tr><tr><td>1</td><td>Szerves narancs</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Művelet leállítása

Ez a művelet leállítja egy munkafolyamat-példány futását, megszakítja a folyamatban lévő műveleteket, kihagyja a fennmaradó műveleteket, és visszaadja a megadott állapotot. Használhatja például a **Leállítja** a műveletet, ha a logikai alkalmazás nak teljesen ki kell lépnie egy hibaállapotból. Ez a művelet nincs hatással a már befejezett műveletekre, és nem jelenhet meg a **Foreach** és **a Until** hurkokban, beleértve a szekvenciális hurkokat is.

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Állapot*> | Sztring | A futtatáshoz visszaadandó állapot: "Sikertelen", "Megszakítva" vagy "Sikerült" |
|||| 

*Választható*

A "runStatus" objektum tulajdonságai csak akkor érvényesek, ha a "runStatus" tulajdonság "Sikertelen" állapotra van állítva.

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*hibakód vagy-név*> | Sztring | A hiba kódja vagy neve |
| <*hibaüzenet*> | Sztring | A hibát leíró üzenet vagy szöveg, valamint az alkalmazás felhasználója által végrehajtható műveletek | 
|||| 

*Példa*

Ez a műveletdefiníció leállítja a munkafolyamat futtatását, a futtatási állapotot "Sikertelen" értékre állítja, és az állapotot, egy hibakódot és egy hibaüzenetet adja vissza:

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

Ez a művelet szünetelteti a munkafolyamat végrehajtását a megadott időközre vagy a megadott időpontig, de mindkettőre nem.

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*egységek száma*> | Egész szám | A **Késleltetés** művelet esetén a várakozó egységek száma | 
| <*Intervallum*> | Sztring | A **Késleltetés** műveletnél a várakozási idő: "Második", "Perc", "Óra", "Nap", "Hét", "Hónap" | 
| <*dátum-időbélyegző*> | Sztring | A **Késleltetés a műveletig** esetében a végrehajtás folytatásának dátuma és időpontja. Ennek az értéknek [utc dátumidő-formátumot kell használnia.](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) | 
|||| 

*1. példa*

Ez a műveletdefiníció 15 percre szünetelteti a munkafolyamatot:

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

Ez a műveletdefiníció a megadott időpontig szünetelteti a munkafolyamatot:

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

Ez a művelet meghívja egy másik korábban létrehozott logikai alkalmazás, ami azt jelenti, hogy más logikai alkalmazás-munkafolyamatok is felveheti, és újra felhasználhatja. A gyermek *vagy* beágyazott logikai alkalmazás kimeneteit is használhatja a beágyazott logikai alkalmazást követő műveletekben, feltéve, hogy a gyermek logikai alkalmazás választ ad vissza.

A Logic Apps motor ellenőrzi a hozzáférést a hívni kívánt eseményindítóhoz, ezért győződjön meg arról, hogy hozzáférhet az eseményindítóhoz. Emellett a beágyazott logikai alkalmazásnak meg kell felelnie ezeknek a feltételeknek:

* Az eseményindító kérhetővé teszi a beágyazott logikai alkalmazást, például [egy kérést](#request-trigger) vagy [EGY HTTP-eseményindítót](#http-trigger)

* Ugyanaz az Azure-előfizetés, mint a szülőlogikai alkalmazás

* A szülőlogikai alkalmazásban a beágyazott logikai alkalmazás kimeneteinek használatához a beágyazott logikai alkalmazásnak válaszművelettel kell [rendelkeznie](#response-action)

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*beágyazott-logika-alkalmazás-név*> | Sztring | A hívni kívánt logikai alkalmazás neve | 
| <*eseményindító neve*> | Sztring | Az eseményindító neve a hívni kívánt beágyazott logikai alkalmazásban | 
| <*Azure-előfizetés-azonosító*> | Sztring | Az Azure-előfizetés azonosítója a beágyazott logikai alkalmazáshoz |
| <*Azure-erőforrás-csoport*> | Sztring | A beágyazott logikai alkalmazás Azure-erőforráscsoport-neve |
| <*beágyazott-logika-alkalmazás-név*> | Sztring | A hívni kívánt logikai alkalmazás neve |
||||

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------|  
| <*fejléc-tartalom*> | JSON-objektum | A hívással küldő fejlécek | 
| <*testtartalom*> | JSON-objektum | A hívással küldő üzenetek tartalma | 
||||

*Kimenetek*

Ez a művelet kimenetek a beágyazott logikai alkalmazás válaszművelettől függően változnak. Ha a beágyazott logikai alkalmazás nem tartalmaz válaszműveletet, a kimenetek üresek.

*Példa*

Miután a "Start_search" művelet sikeresen befejeződött, ez a munkafolyamat-műveletdefiníció egy másik "Get_product_information" nevű logikai alkalmazást hív meg, amely a megadott bemeneteken halad át:

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

## <a name="control-workflow-action-details"></a>Munkafolyamat-művelet részleteinek szabályozása

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach művelet

Ez a hurkolási művelet egy tömbön keresztül halad, és minden tömbelemen műveleteket hajt végre. Alapértelmezés szerint a "minden" ciklus párhuzamosan fut, legfeljebb a hurkok maximális számáig. Ehhez a maximális, lásd: [Korlátok és config](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ismerje [meg, hogyan hozhat létre "minden" hurkot.](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)

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

*Szükséges* 

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*akció-1... N*> | Sztring | Az egyes tömbelemeken futó műveletek nevei | 
| <*akció-meghatározás-1... N*> | JSON-objektum | A futó műveletek definíciói | 
| <*minden kifejezéshez*> | Sztring | A megadott tömb egyes elemére hivatkozó kifejezés | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*Számít*> | Egész szám | Alapértelmezés szerint a "minden" ciklus ismétlése egyidejűleg (egyidejűleg vagy párhuzamosan) fut az [alapértelmezett korlátig.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Ha új <*számláló*> érték beállításával szeretné módosítani ezt a korlátot, olvassa el [a "minden hez" ciklus egyidejűsítésének módosítása című témakört.](#change-for-each-concurrency) | 
| <*művelet-opció*> | Sztring | Ha a "for each" ciklust nem párhuzamosan, hanem párhuzamosan `Sequential` szeretné futtatni, állítsa be <*műveletbeállítási*>, vagy <*számolja* össze>, de mindkettőt `1`ne. További információ: ["Minden egyes" hurkok futtatása egymás után.](#sequential-for-each) | 
|||| 

*Példa*

Ez a "minden" hurok e-mailt küld a tömb minden eleméhez, amely egy bejövő e-mail mellékleteit tartalmazza. A hurok e-mailt küld, beleértve a mellékletet is, egy olyan személynek, aki áttekinti a mellékletet.

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

Ha csak egy tömböt szeretne megadni kimenetként az eseményindítóból, ez a kifejezés leváltja a <*tömbnév>* tömböt az eseményindító törzséből. A hiba elkerülése érdekében, ha a tömb `?` nem létezik, a kifejezés az operátort használja:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Ha a művelet

Ez a művelet, amely egy *feltételes utasítás,* kiértékel egy kifejezést, amely egy feltételt jelöl, és egy másik ágat futtat attól függően, hogy a feltétel igaz vagy hamis. Ha a feltétel igaz, a feltétel "Sikeres" állapottal van megjelölve. További információ [a feltételes utasítások létrehozásáról.](../logic-apps/logic-apps-control-flow-conditional-statement.md)

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
| <*Feltétel*> | JSON-objektum | A feltétel, amely lehet egy kifejezés, hogy értékelje | 
| <*művelet-1*> | JSON-objektum | A <állapot> kiértékelt *művelet* igaz | 
| <*művelet-meghatározás*> | JSON-objektum | A cselekvés meghatározása | 
| <*művelet-2*> | JSON-objektum | Az <*állapot*> esetén futtatandó művelet hamis | 
|||| 

A műveletek `actions` a `else` vagy objektumok kap ezeket az állapotokat:

* "Sikeres", ha futnak, és sikerül
* "Nem sikerült", amikor futnak, és nem
* "Kihagyva", ha az adott ág nem fut

*Példa*

Ez a feltétel azt határozza meg, hogy ha az egész változó értéke nulla-nál nagyobb, a munkafolyamat ellenőrzi a webhelyet. Ha a változó nulla vagy kevesebb, a munkafolyamat egy másik webhelyet ellenőriz.

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

#### <a name="how-conditions-use-expressions"></a>A feltételek használatának módjáról szóló kifejezések

Íme néhány példa arra, hogyan használhatja a kifejezéseket feltételek ben:
  
| JSON | Eredmény | 
|------|--------| 
| "kifejezés":@parameters" ("<specialaction>')" *hasSpecialAction* | Csak logikai kifejezések esetén a feltétel minden olyan értéket átad, amely igaz értéket ad ki. <p>Ha más típusokat logikai értékké `empty()` `equals()`szeretne alakítani, használja a következő függvényeket: vagy . | 
| "kifejezés":@greater" ("<*művelet*>').output.value, parameters('<*threshold*>')" | Az összehasonlító függvények esetében a művelet csak akkor fut, ha *a*> <kimenete meghaladja a <*küszöbértéket*> érték. | 
| "kifejezés":@or" (nagyobb(<*művelet*>').output.value, parameters('<*threshold*>')), less(actions("<*same-action*>').output.value, 100))" | Logikai függvények és beágyazott logikai kifejezések létrehozása esetén a művelet akkor fut, ha a <*művelet*> kimenete több, mint a <*küszöbérték*> érték vagy 100 alatt van. | 
| "kifejezés":@equals" (hossz(műveletek(<*művelet*>').outputs.errors), 0))" | Tömbfüggvények segítségével ellenőrizheti, hogy a tömbrendelkezik-e elemekkel. A művelet akkor `errors` fut, ha a tömb üres. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Hatókör-művelet

Ez a művelet logikailag *hatókörökbe csoportosítja*a műveleteket, amelyek saját állapotukat kapják, miután az adott hatókörben lévő műveletek befejeződnek. Ezután a hatókör állapotával meghatározhatja, hogy futnak-e más műveletek. További információ [a hatókörök létrehozásáról.](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------|  
| <*belső-akció-1... N*> | JSON-objektum | Egy vagy több művelet, amely a hatókörön belül fut |
| <*művelet-bemenetek*> | JSON-objektum | Az egyes műveletek bemenetei |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Switch művelet

Ez a művelet, más néven *kapcsolóutasítás,* más műveleteket *is esetcsoportokba*rendez, és minden esethez értéket rendel, kivéve az alapértelmezett esetet, ha van ilyen. A munkafolyamat futtatásakor a **Switch** művelet összehasonlítja egy kifejezés, objektum vagy token értékét az egyes esethez megadott értékekkel. Ha a **Switch** művelet egy ezz és egyező esetet talál, a munkafolyamat csak az adott esethez tartozó műveleteket futtatja. Minden alkalommal, amikor a **Switch** művelet fut, vagy csak egy egyező eset létezik, vagy nincs egyezés. Ha nincs egyezés, a **Switch** művelet futtatja az alapértelmezett műveleteket. További információ [a kapcsolóutasítások létrehozásáról.](../logic-apps/logic-apps-control-flow-switch-statement.md)

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

*Szükséges*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*kifejezés-objektum-vagy-token*> | Változó | A kiértékelendő Kifejezés, JSON-objektum vagy token | 
| <*műveletneve*> | Sztring | Az egyező esethez futtatandó művelet neve | 
| <*művelet-meghatározás*> | JSON-objektum | Az egyező esethez futtatandó művelet definíciója | 
| <*egyezési érték*> | Változó | Az összehasonlított eredménnyel összehasonlítandó érték | 
|||| 

*Választható*

| Érték | Típus | Leírás | 
|-------|------|-------------| 
| <*alapértelmezett művelet-név*> | Sztring | A futtatandó alapértelmezett művelet neve, ha nem létezik egyező eset | 
| <*alapértelmezett-művelet-definíció*> | JSON-objektum | A végrehajtandó művelet definíciója, ha nincs egyező eset | 
|||| 

*Példa*

Ez a műveletdefiníció kiértékeli, hogy a jóváhagyási kérelemre válaszoló e-mailre válaszoló személy a "Jóváhagyás" vagy az "Elutasítás" lehetőséget választotta-e. Ez alapján a **választás,** a Switch művelet futtatja a műveleteket az egyező eset, amely egy másik e-mailt küldeni a válaszadó, de különböző megfogalmazásminden esetben. 

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

### <a name="until-action"></a>Amíg a cselekvés

Ez a ciklusművelet olyan műveleteket tartalmaz, amelyek addig futnak, amíg a megadott feltétel teljesül. A ciklus az összes többi művelet futtatása után utolsó lépésként ellenőrzi a feltételt. Az `"actions"` objektumba több műveletet is beilleszthet, és a műveletnek legalább egy korlátot meg kell határoznia. Ismerje [meg, hogyan hozhat létre "amíg" hurkok](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

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
| <*műveletneve*> | Sztring | A cikluson belül futtatni kívánt művelet neve | 
| <*művelet-típusú*> | Sztring | A futtatni kívánt művelettípus | 
| <*művelet-bemenetek*> | Különböző | A művelet futtatásához bevitt adatok | 
| <*Feltétel*> | Sztring | A ciklus összes művelete után kiértékelandó feltétel vagy kifejezés futtatása | 
| <*hurok-szám*> | Egész szám | A művelet futtatásához használható legtöbb hurkok száma korlátozva. Az alapértelmezett korlátról és a maximális korlátról további információt az Azure Logic Apps korlátai és konfigurációja című [témakörben talál.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) | 
| <*ciklus-időkitöltés*> | Sztring | A ciklus futtatásának leghosszabb idejének korlátozása. Az `timeout` alapértelmezett `PT1H`érték a szükséges [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Példa*

Ez a ciklusművelet-definíció HTTP-kérelmet küld a megadott URL-címre, amíg a feltételek valamelyike meg nem teljesül:

* A kérés a "200 OK" állapotkóddal érkezik.
* A hurok már 60-szor futott.
* A hurok már egy órája fut.

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

A webhook-alapú eseményindítók és műveletek nem ellenőrzik rendszeresen a végpontokat, de inkább bizonyos eseményekre vagy adatokra várnak. Ezek az eseményindítók és műveletek *előfizetnek* a végpontokra egy *visszahívási URL-cím* megadásával, ahol a végpont válaszokat küldhet.

A `subscribe` hívás akkor történik, amikor a munkafolyamat bármilyen módon megváltozik, például a hitelesítő adatok megújításakor, vagy amikor a bemeneti paraméterek megváltoznak egy eseményindítóhoz vagy művelethez. Ez a hívás ugyanazokat a paramétereket használja, mint a szabványos HTTP-műveletek. 

A `unsubscribe` hívás automatikusan megtörténik, ha egy művelet érvénytelenné teszi az eseményindítót vagy műveletet, például:

* Az eseményindító törlése vagy letiltása. 
* A munkafolyamat törlése vagy letiltása. 
* Az előfizetés törlése vagy letiltása. 

Ezek nek a `@listCallbackUrl()` hívásoknak a támogatásához a kifejezés egy egyedi "visszahívási URL-t" ad vissza az eseményindítóhoz vagy művelethez. Ez az URL-cím a szolgáltatás REST API-ját használó végpontok egyedi azonosítóját jelöli. A függvény paraméterei megegyeznek a webhook-eseményindítóval vagy művelettel.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Aszinkron időtartam módosítása

Az eseményindítók és a műveletek esetében a `limit.timeout` tulajdonság hozzáadásával korlátozhatja az aszinkron minta időtartamát egy adott időintervallumra. Így, ha a művelet nem fejeződött be, amikor az intervallum lejár, `ActionTimedOut` a művelet állapota a kóddal együtt `Cancelled` lesz megjelölve. A `timeout` tulajdonság [ISO 8601 formátumú.](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)

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

Módosíthatja az eseményindítók és műveletek alapértelmezett futásidejű viselkedését, ha ezeket `runtimeConfiguration` a tulajdonságokat hozzáadja az eseményindítóhoz vagy a műveletdefinícióhoz.

| Tulajdonság | Típus | Leírás | Eseményindító vagy művelet | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Egész szám | Módosítsa az egyszerre (egyidejűleg vagy párhuzamosan) futtatható munkafolyamat-példányok számának [*alapértelmezett korlátját.*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Ennek az értéknek a módosításával korlátozhatja a háttérrendszerek által fogadott kérelmek számát. <p>A `runs` tulajdonság `1` beállítása ugyanúgy működik, `operationOptions` mint `SingleInstance`a tulajdonság beállítása . Bármelyik tulajdonságot beállíthatja, de mindkettőt nem. <p>Az alapértelmezett korlát módosításáról az [Eseményindító-egyidejűség módosítása](#change-trigger-concurrency) vagy [a Példányok sorrendben való módosítása című témakörben](#sequential-trigger)olvashat. | Minden eseményindító | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Egész szám | Módosítsa az [*alapértelmezett korlátot*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) azon munkafolyamat-példányok számában, amelyeknek meg kell várniuk a futtatást, ha a logikai alkalmazás már futtatja a maximális egyidejű példányokat. <p>Az alapértelmezett korlát módosításáról a [Várakozási futtatások módosítása korlát című témakörben](#change-waiting-runs)olvashat. | Minden eseményindító | 
| `runtimeConfiguration.concurrency.repetitions` | Egész szám | Módosítsa az [*egyszerre*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) (egyidejűleg vagy párhuzamosan) futtatható "minden" ciklusismétlések alapértelmezett korlátját. <p>A `repetitions` tulajdonság `1` beállítása ugyanúgy működik, `operationOptions` mint `SingleInstance`a tulajdonság beállítása . Bármelyik tulajdonságot beállíthatja, de mindkettőt nem. <p>Az alapértelmezett korlát módosításáról a ["Minden egyes" egyidejűség módosítása](#change-for-each-concurrency) vagy [a "minden" hurkok futtatása című témakörben olvashat.](#sequential-for-each) | Művelet: <p>[Foreach (Foreach)](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Egész szám | A tördelést támogató és bekapcsolt műveletek esetén ez az érték adja meg a beolvasandó eredmények *minimális* számát. <p>A tördelés bekapcsolását lásd: [Tömeges adatok, elemek vagy eredmények beszedése tördelés használatával](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Akció: Változatos |
| `runtimeConfiguration.secureData.properties` | Tömb | Számos eseményindítók és műveletek, ezek a beállítások elrejtik a bemenetek, kimenetek, vagy mindkettő a logikai alkalmazás futtatási előzmények. <p>Ha többet szeretne tudni az adatok védelméről, olvassa el a [Bemenetek és kimenetek elrejtése a futtatási előzményekből című témakört.](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view) | A legtöbb eseményindító és művelet |
| `runtimeConfiguration.staticResult` | JSON-objektum | A [statikus eredménybeállítást](../logic-apps/test-logic-apps-mock-data-static-results.md) támogató és bekapcsolt `staticResult` műveletek esetén az objektum a következő attribútumokkal rendelkezik: <p>- `name`, amely az aktuális művelet statikus eredménydefiníciójának nevére hivatkozik, amely a `definition` logikai alkalmazás munkafolyamatának attribútumában jelenik meg az `staticResults` attribútumban. További információ: [Statikus eredmények – sémahivatkozás a munkafolyamat-definíciós nyelvhez.](../logic-apps/logic-apps-workflow-definition-language.md#static-results) <p> - `staticResultOptions`, amely meghatározza, hogy `Enabled` statikus eredmények vannak-e az aktuális művelethez. <p>A statikus eredmények bekapcsolása a [Logikai alkalmazások tesztelése modelladatokkal statikus eredmények beállításával](../logic-apps/test-logic-apps-mock-data-static-results.md) | Akció: Változatos |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Működési beállítások

Módosíthatja az eseményindítók és műveletek `operationOptions` alapértelmezett viselkedését a tulajdonsággal az eseményindítóban vagy a műveletdefinícióban.

| Művelet beállítás | Típus | Leírás | Eseményindító vagy művelet | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Sztring | A HTTP-alapú műveleteket szinkron módon, nem pedig aszinkron módon futtassa. <p><p>A beállítás beállításához olvassa el A [műveletek szinkron futtatása (Műveletek szinkronban) (Műveletek futtatása szinkronban) (Műveletek futtatása szinkronban) (Műveletek futtatása szinkronban) (Műveletek futtatása szinkronban)](#asynchronous-patterns) | Műveletek: <p>[ApiConnection](#apiconnection-action), <br>[HTTP,](#http-action) <br>[Válasz](#response-action) | 
| `OptimizedForHighThroughput` | Sztring | Módosítsa a műveletvégrehajtás 5 percenkénti számának [alapértelmezett korlátját](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) a [maximális korlátra.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) <p><p>A beállítás beállításához olvassa el A [nagy átviteli sebességű futtatás a témakört.](#run-high-throughput-mode) | Minden művelet | 
| `Sequential` | Sztring | Futtassa a "minden" ciklus ismétlések egy olyan időpontban, nem pedig az összes egy időben párhuzamosan. <p>Ez a beállítás ugyanúgy `runtimeConfiguration.concurrency.repetitions` működik, `1`minta tulajdonság beállítása a . Bármelyik tulajdonságot beállíthatja, de mindkettőt nem. <p><p>A beállítás beállításához olvassa el a ["minden hez" hurkok futtatása című témakört.](#sequential-for-each)| Művelet: <p>[Foreach (Foreach)](#foreach-action) | 
| `SingleInstance` | Sztring | Futtassa az eseményindítót az egyes logikai alkalmazáspéldányok egymás után, és várja meg, amíg a korábban aktív futtatás befejeződik, mielőtt a következő logikai alkalmazáspéldány. <p><p>Ez a beállítás ugyanúgy `runtimeConfiguration.concurrency.runs` működik, `1`minta tulajdonság beállítása a . Bármelyik tulajdonságot beállíthatja, de mindkettőt nem. <p>A beállítás beállításához olvassa el a [Példányok sorrendben való aktiválása (Trigger) (Példányok sorrendben) (Példányok sorrendben) ( Eseményindító ) témakört.](#sequential-trigger) | Minden eseményindító | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Trigger egyidejűségének módosítása

Alapértelmezés szerint a logikai alkalmazás munkafolyamat-példányai egyszerre futnak (egyidejűleg vagy párhuzamosan). Ez a viselkedés azt jelenti, hogy minden eseményindító példány elindul, mielőtt a korábban aktív munkafolyamat-példány futása befejeződik. Az egyidejűleg futó példányok száma azonban [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)rendelkezik. Ha az egyidejűleg futó munkafolyamat-példányok száma eléri ezt a korlátot, minden más új példánynak várnia kell a futtatásra. Ez a korlát segít szabályozhatja, hogy a háttérrendszer-rendszerek száma hány kérelmet fogad.

Az alapértelmezett korlát módosításához használhatja a kódnézet-szerkesztőt vagy a Logic Apps Designert, `runtimeConfiguration.concurrency.runs` mivel az egyidejűségi beállítás módosítása a tervezőn keresztül hozzáadja vagy frissíti a tulajdonságot az alapul szolgáló eseményindító-definícióban, és fordítva. Ez a tulajdonság szabályozza a párhuzamosan futtatható munkafolyamat-példányok maximális számát. Az alábbiakban néhány szempontot ismer ünk be, ha engedélyezni szeretné az egyidejűség-vezérlést:

* Ha az egyidejűség engedélyezve van, a [SplitOn korlát](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) jelentősen csökken a [tömbök kötegelése](#split-on-debatch)esetén. Ha az elemek száma meghaladja ezt a korlátot, a SplitOn funkció le van tiltva.

* Amíg az egyidejűség engedélyezve van, egy hosszú ideig futó logikai alkalmazáspéldány új logikai alkalmazáspéldányok várakozási állapotba léphet. Ez az állapot megakadályozza, hogy az Azure Logic Apps új példányokat hozzon létre, és akkor is megtörténik, ha az egyidejű futtatások száma kisebb, mint az egyidejű futtatások megadott maximális száma.

  * Az állapot megszakításához szakítsa meg a még *futó*legkorábbi példányokat.

    1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget.

    1. A **Futtatások előzményei szakaszban** válassza ki a legkorábbi példányt, amely még mindig fut, például:

       ![A legkorábbi futó példány kijelölése](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Ha csak a még futó példányokat szeretné megtekinteni, nyissa meg a **Mind** listát, és válassza a **Futás**lehetőséget.

    1. A **Logikai alkalmazás futtatása**csoportban válassza **a Mégse futtatás lehetőséget.**

       ![Legkorábbi futó példány megkeresése](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * A lehetőség kerüléséhez adjon hozzá időtúllépést minden olyan művelethez, amely feltartóztathatja ezeket a futtatásokat. Ha a kódszerkesztőben dolgozik, olvassa el az Aszinkron időtartam módosítása című [témakört.](#asynchronous-limits) Ellenkező esetben, ha a tervezőt használja, kövesse az alábbi lépéseket:

    1. A logikai alkalmazásban az időtúllépést a jobb felső sarokban kattintson a három pontra (**...**) gombra, majd a **Beállítások parancsra.**

       ![Műveletbeállítások megnyitása](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. Az **Időtúlidőpont**csoportban adja meg az időtúltöltés időtartamát [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Időleg-idő időtartamának megadása](./media/logic-apps-workflow-actions-triggers/timeout.png)

* A logikai alkalmazás egymás utáni futtatásához állítsa `1` be az eseményindító egyidejűségét a kódnézet-szerkesztő vagy a tervező használatával. Győződjön meg arról, hogy nem `operationOptions` állítja `SingleInstance` be az eseményindító tulajdonságát a kódnézet-szerkesztőben. Ellenkező esetben érvényesítési hiba jelenik meg. További információ: [Példányok sorrendben.](#sequential-trigger)

#### <a name="edit-in-code-view"></a>Szerkesztés kódnézetben 

Az alapul szolgáló eseményindító-definícióban adja hozzá a `runtimeConfiguration.concurrency.runs` tulajdonságot, amelynek értéke a és a `1` `50`között mozog.

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

További információt a [Futásidejű konfigurációs beállítások című](#runtime-config-options)témakörben talál.

#### <a name="edit-in-logic-apps-designer"></a>Szerkesztés a Logic Apps Designer alkalmazásban

1. Az eseményindító jobb felső sarkában jelölje ki a három pont (**...**) gombot, majd a **Beállítások lehetőséget.**

1. Az **Egyidejűség-ellenőrzés csoportban**állítsa a **Korlátot** **Be beállításra.** 

1. Húzza a **Párhuzamosság mértéke** csúszkát a kívánt értékre. A logikai alkalmazás egymás utáni futtatásához húzza a csúszka értékét **az 1-re.**

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Módosítsa a "minden" egyidejűség módosítása

Alapértelmezés szerint a "minden" ciklus ismétlések minden fut egy időben (egyidejűleg vagy párhuzamosan). Ez a viselkedés azt jelenti, hogy minden iteráció elindul, mielőtt az előző iteráció befejeződik. Az egyidejűleg futó ismétlések száma azonban [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)rendelkezik. Ha az egyidejűleg futó ismétlések száma eléri ezt a korlátot, minden más iterációnak várnia kell a futtatásra.

Az alapértelmezett korlát módosításához használhatja a kódnézet-szerkesztőt vagy a Logic Apps Designert, `runtimeConfiguration.concurrency.repetitions` mivel az egyidejűségi beállítás módosítása a tervezőn keresztül hozzáadja vagy frissíti a tulajdonságot az alapul szolgáló "minden" műveletdefinícióban, és fordítva. Ez a tulajdonság szabályozza a párhuzamosan futtatható ismétlések maximális számát.

> [!NOTE] 
> Ha úgy állítja be, hogy a "minden" művelet egymás után fusson a tervező vagy `operationOptions` a `Sequential` kódnézet-szerkesztő használatával, ne állítsa a művelet tulajdonságát a kódnézet-szerkesztőben. Ellenkező esetben érvényesítési hiba jelenik meg. További információ: ["Minden egyes" hurkok futtatása egymás után.](#sequential-for-each)

#### <a name="edit-in-code-view"></a>Szerkesztés kódnézetben 

Az alapul szolgáló "minden" definícióban `runtimeConfiguration.concurrency.repetitions` adja hozzá vagy frissítse a tulajdonságot, amelynek értéke a és `1` `50`a tartománya lehet.

Íme egy példa, amely az egyidejű futtatásokat 10 ismétlésre korlátozza:

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

További információt a [Futásidejű konfigurációs beállítások című](#runtime-config-options)témakörben talál.

#### <a name="edit-in-logic-apps-designer"></a>Szerkesztés a Logic Apps Designer alkalmazásban

1. A **Minden** művelethez a jobb felső sarokban jelölje ki a három pont (**...**) gombot, majd válassza a **Beállítások lehetőséget**.

1. Az **Egyidejűség-vezérlés csoportban**állítsa az **Egyidejűség-vezérlést** **Be**beállításra.

1. Húzza a **Párhuzamosság mértéke** csúszkát a kívánt értékre. A logikai alkalmazás egymás utáni futtatásához húzza a csúszka értékét **az 1-re.**

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>A várakozási futtatások módosítása korlátja

Alapértelmezés szerint a logikai alkalmazás munkafolyamat-példányai egyszerre futnak (egyidejűleg vagy párhuzamosan). Ez a viselkedés azt jelenti, hogy minden eseményindító példány elindul, mielőtt a korábban aktív munkafolyamat-példány futása befejeződik. Az egyidejűleg futó példányok száma azonban [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)rendelkezik. Ha az egyidejűleg futó munkafolyamat-példányok száma eléri ezt a korlátot, minden más új példánynak várnia kell a futtatásra.

A várakozási futtatások száma alapértelmezett [korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)is rendelkezik. Amikor a várakozási futtatások száma eléri ezt a korlátot, a Logic Apps motor már nem fogadja el az új futtatásokat. A kérelem és a webhook-eseményindítók 429 hibát adnak vissza, és az ismétlődő eseményindítók kihagyják a lekérdezési kísérleteket.

Nem csak [az eseményindító-egyidejűség alapértelmezett korlátját módosíthatja,](#change-trigger-concurrency)hanem a várakozási futtatások alapértelmezett korlátját is. Az alapul szolgáló eseményindító-definícióban adja hozzá a `runtimeConfiguration.concurrency.maximumWaitingRuns` tulajdonságot, amelynek értéke a és a `1` `100`között mozog.

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

További információt a [Futásidejű konfigurációs beállítások című](#runtime-config-options)témakörben talál.

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Példányok sorrendben

Ha minden logikai alkalmazás munkafolyamat-példányát csak az előző példány futása után szeretné futtatni, állítsa be az eseményindítót egymás után. Használhatja a kódnézet-szerkesztőt vagy a Logic Apps Designert, mert `runtimeConfiguration.concurrency.runs` az egyidejűségi beállítás tervezőn keresztüli módosítása az alapul szolgáló eseményindító-definícióban is hozzáadja vagy frissíti a tulajdonságot, és fordítva.

> [!NOTE] 
> Ha úgy állítja be az eseményindítót, hogy a tervező vagy a kódnézet-szerkesztő használatával egymás után fusson, ne állítsa az eseményindító tulajdonságát `operationOptions` a `Sequential` kódnézet-szerkesztőben. Ellenkező esetben érvényesítési hiba jelenik meg. 

#### <a name="edit-in-code-view"></a>Szerkesztés kódnézetben

Az eseményindító definíciójában állítsa be ezeket a tulajdonságokat, de mindkettőt ne. 

Állítsa `runtimeConfiguration.concurrency.runs` a `1`tulajdonságot a következőre:

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

*-vagy-*

Állítsa `operationOptions` a `SingleInstance`tulajdonságot a következőre:

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

További információt a [Futásidejű konfigurációs beállítások](#runtime-config-options) és a Művelet beállításai című témakörben [talál.](#operation-options)

#### <a name="edit-in-logic-apps-designer"></a>Szerkesztés a Logic Apps Designer alkalmazásban

1. Az eseményindító jobb felső sarkában jelölje ki a három pont (**...**) gombot, majd a **Beállítások lehetőséget.**

1. Az **Egyidejűség-ellenőrzés csoportban**állítsa a **Korlátot** **Be beállításra.** 

1. Húzza a **párhuzamosság mértékét** csúszkát `1`a számra. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Futtassa a "minden" hurkot egymás után

Ha csak az előző iteráció futtatása után szeretné futtatni a "minden" ciklusismétlést, állítsa be a "minden" műveletet egymás után. Használhatja a kódnézet-szerkesztőt vagy a Logic Apps Designert, mert a `runtimeConfiguration.concurrency.repetitions` művelet egyidejűsítésének tervezőn keresztüli módosítása az alapul szolgáló műveletdefinícióban is hozzáadja vagy frissíti a tulajdonságot, és fordítva.

> [!NOTE] 
> Ha úgy állítja be, hogy a "minden" művelet egymás után fusson a tervező `operationOptions` vagy `Sequential` a kódnézet-szerkesztő használatával, ne állítsa a művelet tulajdonságát a kódnézet-szerkesztőben. Ellenkező esetben érvényesítési hiba jelenik meg. 

#### <a name="edit-in-code-view"></a>Szerkesztés kódnézetben

A műveletdefinícióban állítsa be ezeket a tulajdonságokat, de mindkettőt ne. 

Állítsa `runtimeConfiguration.concurrency.repetitions` a `1`tulajdonságot a következőre:

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

*-vagy-*

Állítsa `operationOptions` a `Sequential`tulajdonságot a következőre:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

További információt a [Futásidejű konfigurációs beállítások](#runtime-config-options) és a Művelet beállításai című témakörben [talál.](#operation-options)

#### <a name="edit-in-logic-apps-designer"></a>Szerkesztés a Logic Apps Designer alkalmazásban

1. Az **Egyes** műveletek jobb felső sarkában jelölje ki a három pont (**...**) gombot, majd a **Beállítások lehetőséget.**

1. Az **Egyidejűség-vezérlés csoportban**állítsa az **Egyidejűség-vezérlést** **Be**beállításra.

1. Húzza a **párhuzamosság mértékét** csúszkát `1`a számra.

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Műveletek szinkron futtatása

Alapértelmezés szerint az összes HTTP-alapú művelet a szabványos aszinkron műveletmintát követi. Ez a minta azt adja meg, hogy amikor egy HTTP-alapú művelet kérést küld a megadott végpontnak, a távoli kiszolgáló "202 ACCEPTED" választ küld vissza. Ez a válasz azt jelenti, hogy a kiszolgáló elfogadta a feldolgozási kérelmet. A Logic Apps motor folyamatosan ellenőrzi a válasz helyfejléce által megadott URL-címet, amíg a feldolgozás le nem áll, ami bármely nem 202-es válasz.

A kérelmek azonban időkorláttal rendelkeznek, így a hosszú ideig futó műveletek esetében letilthatja `operationOptions` az `DisableAsyncPattern` aszinkron viselkedést, ha hozzáadja és beállítja a tulajdonságot a művelet bemenetei alatt.

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

További információt a Műveleti beállítások című témakörben [talál.](#operation-options)

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Nagy átviteli sebességű üzemmódban való futtatás

Egyetlen logikai alkalmazásdefiníció esetén az 5 percenként végrehajtott műveletek száma [alapértelmezett korláttal](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)rendelkezik. Ha ezt a korlátot a `operationOptions` lehető `OptimizedForHighThroughput` [legnagyobb ra](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) szeretné emelni, állítsa a tulajdonságot a értékre. Ez a beállítás a logikai alkalmazást "nagy átviteli sebességű" módba helyezi.

> [!NOTE]
> A nagy átviteli sebességű mód előzetes verzióban érhető el. A számítási feladatok között is eloszthatja a számítási feladatok at több logikai alkalmazás szükség szerint.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Eseményindítók és műveletek hitelesítése

A HTTP- és HTTPS-végpontok különböző típusú hitelesítéseket támogatnak. A kimenő hívások vagy a végpontok elérésére irányuló kérelmek eléréséhez használt eseményindító vagy művelet alapján a hitelesítési típusok különböző tartományai közül választhat. További információt a [Hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)című témakörben talál.

## <a name="next-steps"></a>További lépések

* További információ a [munkafolyamat-definíció nyelvéről](../logic-apps/logic-apps-workflow-definition-language.md)
