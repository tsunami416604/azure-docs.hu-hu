---
title: Hibák és kivételek kezelésére szolgáló Azure Logic Apps
description: További tudnivalók a hibák és kivételek kezelésére vonatkozó mintákról Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.date: 01/31/2018
ms.topic: article
ms.openlocfilehash: 828bea50a66b90f35843901ae2d7c703ffa58f2d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208175"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hibák és kivételek kezelése Azure Logic Appsban

Az integrációs architektúra megfelelő módon kezeli az állásidőt vagy a függő rendszerek által okozott problémákat. A problémák és hibák zökkenőmentes kezelésére szolgáló robusztus és rugalmas integrációk létrehozásához Logic Apps a hibák és kivételek kezelésének első osztályú élményét nyújtja. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Újrapróbálkozási szabályzatok

A legalapvetőbb kivételek és hibák kezelésére az újrapróbálkozási szabályzatot bármilyen művelet vagy trigger esetében használhatja, ahol a támogatott. Az újrapróbálkozási szabályzat meghatározza, hogy a művelet vagy az indítás hogyan próbálkozik újra egy kéréssel, amikor az eredeti kérelem időtúllépése vagy meghibásodása meghiúsul, ami 408, 429 vagy 5xx választ eredményez. Ha nincs más újrapróbálkozási szabályzat, a rendszer az alapértelmezett házirendet használja. 

Az újrapróbálkozási szabályzatok típusai: 

| Type | Leírás | 
|------|-------------| 
| **Alapértelmezett** | Ez a szabályzat legfeljebb négy újrapróbálkozást küld el *exponenciálisan növekvő* intervallumokban, amelyek mérete 7,5 másodperc, de 5 – 45 másodpercre van korlátozva. | 
| **Exponenciális időköz**  | Ez a szabályzat egy exponenciálisan növekvő tartományból érkező véletlenszerű intervallumot vár a következő kérelem elküldése előtt. | 
| **Rögzített intervallum**  | Ez a házirend a következő kérelem elküldése előtt megvárja a megadott intervallumot. | 
| **Nincsenek**  | Ne küldje újra a kérelmet. | 
||| 

További információ az újrapróbálkozási házirend korlátairól: [Logic apps korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Újrapróbálkozási szabályzat módosítása

Másik újrapróbálkozási házirend kiválasztásához kövesse az alábbi lépéseket: 

1. Nyissa meg a logikai alkalmazást a Logic app Designerben. 

2. Nyisson meg egy művelet vagy trigger **beállításait** .

3. Ha a művelet vagy az aktiválás támogatja az újrapróbálkozási szabályzatokat, az újrapróbálkozási **házirend**területen válassza ki a kívánt típust. 

Vagy manuálisan is megadhatja az újrapróbálkozási házirendet az `inputs` újrapróbálkozási szabályzatokat támogató művelet vagy trigger szakaszban. Ha nem ad meg újrapróbálkozási házirendet, a művelet az alapértelmezett házirendet használja.

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Kötelező*

| Value | Type | Leírás |
|-------|------|-------------|
| <*retry-policy-type*> | String | A használni kívánt újrapróbálkozási szabályzat típusa: `default` `fixed`, `none`,, vagy`exponential` | 
| <*retry-interval*> | String | Az újrapróbálkozási időköz, amelyben az értéknek [ISO 8601 formátumot](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)kell használnia. Az alapértelmezett minimális időköz `PT5S` , a maximális `PT1D`intervallum. Az exponenciális időközi szabályzat használatakor különböző minimális és maximális értékeket adhat meg. | 
| <*retry-attempts*> | Integer | Az újrapróbálkozási kísérletek száma, amelynek 1 és 90 között kell lennie | 
||||

*Választható*

| Value | Type | Leírás |
|-------|------|-------------|
| <*minimum-interval*> | Sztring | Az exponenciális időközi házirend esetében a véletlenszerűen kiválasztott időköz ( [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ) legkisebb intervalluma | 
| <*maximális időköz*> | Sztring | Az exponenciális időközi házirend esetében a véletlenszerűen kiválasztott időköz ( [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ) legnagyobb intervalluma | 
|||| 

További információ a különböző házirend-típusokról.

<a name="default-retry"></a>

### <a name="default"></a>Alapértelmezett

Ha nem ad meg újrapróbálkozási házirendet, a művelet az alapértelmezett házirendet használja, amely tulajdonképpen egy [exponenciális időközi házirend](#exponential-interval) , amely akár négy újrapróbálkozást is küld, exponenciálisan növekvő időközönként, 7,5 másodpercek között. Az intervallum 5 és 45 másodperc közötti értékre van korlátozva. 

Bár a művelet vagy trigger explicit módon nincs definiálva, az alapértelmezett házirend egy példa HTTP-műveletben a következőképpen viselkedik:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Nincsenek

Annak megadásához, hogy a művelet vagy az aktiválás Ne próbálkozzon újra a sikertelen kérelmekkel, állítsa be a `none`< újrapróbálkozási*házirend típusú*> a következőre:.

### <a name="fixed-interval"></a>Rögzített intervallum

Annak megadásához, hogy a művelet vagy az indítás a következő kérelem elküldése előtt megvárja-e a megadott intervallumot, állítsa be `fixed`a < újrapróbálkozási *-házirend típusú*>.

*Példa*

Ez az újrapróbálkozási szabályzat az első sikertelen kérelem után még két alkalommal kísérli meg a legfrissebb híreket, az egyes kísérletek közötti 30 másodperces késleltetéssel:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponenciális időköz

Annak megadásához, hogy a művelet vagy az indítás a következő kérelem elküldése előtt megvárja-e a véletlenszerű intervallumot, állítsa be `exponential`a < újrapróbálkozási*házirend típusú*> a értékre. A véletlenszerű intervallum egy exponenciálisan növekvő tartományból van kiválasztva. Igény szerint felülbírálhatja az alapértelmezett minimális és maximális intervallumot is a saját minimális és maximális intervallumának megadásával.

**Véletlenszerű változók tartományai**

Ez a táblázat azt mutatja be, hogy a Logic Apps hogyan generál egységes véletlenszerű változót a megadott tartományban az újrapróbálkozások számára, beleértve az újrapróbálkozások számát is:

| Újrapróbálkozás száma | Minimális időköz | Maximális időköz |
|--------------|------------------|------------------|
| 1 | max(0, <*minimum-interval*>) | min (intervallum, <*maximális intervallum*>) |
| 2 | max(interval, <*minimum-interval*>) | min (2 * időköz, <*maximális intervallum*>) |
| 3 | Max (2 * intervallum, <*minimális intervallum*>) | min (4 * intervallum, <*maximális intervallum*>) |
| 4 | Max (4 * intervallum, <*minimális intervallum*>) | min (8 * időköz, <*maximális intervallum*>) |
| .... | .... | .... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>A hibák befogása és kezelése a RunAfter tulajdonsággal

Minden logikai alkalmazás művelete deklarálja azokat a műveleteket, amelyeket a művelet elkezdése előtt kell befejezni, hasonlóan ahhoz, ahogy a munkafolyamat lépéseinek sorrendjét megadja. Egy művelet definíciójában a **runAfter** tulajdonság határozza meg ezt a sorrendet, és egy objektum, amely leírja, hogy mely műveletek és tevékenységek futnak a művelet végrehajtásakor.

Alapértelmezés szerint a Logic app Designerben hozzáadott összes művelet úgy van beállítva, hogy az előző lépés **sikeres**végrehajtása után fusson. A **runAfter** értékét azonban testreszabhatja úgy, hogy a műveletek akkor legyenek láthatók, ha az előző műveletek **meghiúsult**, kihagyva vagy az értékek valamilyen kombinációját eredményezik. Ha például egy adott **Insert_Row** művelet végrehajtása után egy adott Service Bus témakörhöz szeretne elemeket hozzáadni, akkor ezt a példát **runAfter** -definíciót használhatja:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

A **runAfter** tulajdonság akkor fut, ha a **Insert_Row** művelet állapota **sikertelen**. A művelet futtatásához, ha a művelet **sikeres**, **sikertelen**vagy kihagyvaállapotú, használja a következő szintaxist:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Az előző művelet meghiúsulása után sikeresen futtatott és befejezési műveletek sikeresként vannakmegjelölve. Ez azt jelenti, hogy ha sikeresen elvégezte az összes hibát a munkafolyamatban, a Futtatás **sikeresként**van megjelölve.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Hatókörökkel és azok eredményeivel kapcsolatos műveletek kiértékelése

Az egyes műveletek a **runAfter** tulajdonsággal való elvégzéséhez hasonlóan a műveletek hatókörön belüli csoportosítására [](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)is lehetőség van. Hatóköröket akkor használhat, ha logikailag csoportosítja a műveleteket, felméri a hatókör összesített állapotát, és végrehajtja a műveleteket az adott állapot alapján. A hatókör összes műveletének futása után a hatókör maga kapja meg a saját állapotát. 

A hatókör állapotának ellenőrzését a logikai alkalmazás futtatási állapotának (például **sikeres**, **sikertelen**stb.) ellenőrzési feltételeit is használhatja. 

Alapértelmezés szerint ha a hatókör összes művelete sikeres, a hatókör állapota **sikeres**lesz. Ha a hatókör végső művelete meghiúsult vagy megszakított eredményt eredményez, a hatókör állapota **nem sikerült**. 

Ha a kivételeket egy **sikertelen** hatókörben szeretné megfogni, és a hibákat kezelő műveleteket futtat, a **runAfter** tulajdonságot használhatja az adott **sikertelen** hatókörhöz. Így ha a hatókör *bármelyik* művelete meghiúsul, és az **runAfter** tulajdonságot használja erre a hatókörre vonatkozóan, létrehozhat egyetlen műveletet a hibák kifogásához.

A hatókörök korlátozásait lásd: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>A hibák kontextusának és eredményének beolvasása

Bár a hibák egy hatókörből való kifogása hasznos, előfordulhat, hogy olyan kontextust is szeretne, amely segít megérteni, hogy pontosan milyen műveletek sikertelenek, valamint a visszaadott hibák vagy állapotkódok.

A [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) függvény kontextust biztosít a hatókör összes műveletének eredményeiről. A `result()` függvény egyetlen paramétert fogad el, amely a hatókör neve, és egy olyan tömböt ad vissza, amely az adott hatókörből származó összes művelet eredményét tartalmazza. Ezek a műveleti objektumok ugyanazokat az attribútumokat `@actions()` tartalmazzák, mint az objektum, például a művelet kezdési ideje, befejezési időpontja, állapota, bemenetei, korrelációs azonosítói és kimenetei. Ha a hatókörön belül meghiúsult műveletek kontextusát szeretné elküldeni, egyszerűen párosíthat egy `@result()` kifejezést a `runAfter` tulajdonsággal.

Ha műveletet szeretne futtatni egy olyan hatókörben lévő művelethez, amely egy **sikertelen** eredménnyel rendelkezik, és az eredmények tömbjét a sikertelen műveletek alapján szűri, `@result()` akkor a kifejezéseket egy szűrési [**tömb**](../connectors/connectors-native-query.md) művelettel és [**minden**](../logic-apps/logic-apps-control-flow-loops.md) hurokhoz társíthatja. Elvégezheti a szűrt eredmény tömböt, és végrehajthat egy műveletet minden egyes hibához a **minden hurok esetében** .

Az alábbi példát követve részletes magyarázatot talál, amely egy HTTP POST-kérelmet küld a válasz törzsének a "My_Scope" hatókörön belül sikertelen műveletekhez:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Íme egy részletes útmutató, amely leírja, hogy mi történik ebben a példában:

1. A "My_Scope" összes műveletének eredményének lekéréséhez a **tömb szűrése** művelet ezt a szűrési kifejezést használja:`@result('My_Scope')`

2. A **szűrő tömb** feltétele bármely `@result()` olyan elem, amelynek állapota **sikertelen**. Ez az állapot szűri azt a tömböt, amely az összes művelet eredményét a "My_Scope" értékről egy olyan tömbre szűri, amely csak a sikertelen művelet eredményeit eredményezi.

3. Hajtson végre **minden** hurok műveletet a *szűrt tömb* kimenetén. Ez a lépés végrehajt egy műveletet a korábban szűrt összes sikertelen művelet eredményéhez.

   Ha a hatókörben egyetlen művelet meghiúsult, akkor az **egyes** ciklusokban végrehajtott műveletek csak egyszer futnak. 
   Több sikertelen művelet hibát okoz egy művelet végrehajtásakor.

4. Küldjön egy http-bejegyzést a **minden** elem válasz törzséhez, amely a `@item()['outputs']['body']` kifejezés. 

   Az `@result()` elem alakzata megegyezik az `@actions()` alakzattal, és ugyanúgy elemezhető.

5. Adjon meg két egyéni fejlécet a sikertelen művelet nevével (`@item()['name']`) és a sikertelen Futtatás ügyfél-követési azonosítóval (`@item()['clientTrackingId']`).

Hivatkozásként Íme egy példa `@result()` egyetlen elemre, amely az előző példában elemzett **nevet**, **törzset**és **clientTrackingId** tulajdonságokat mutatja. **Az egyes** műveleteken `@result()` kívül az objektumok tömbjét adja vissza.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

A különböző kivételek kezelésére szolgáló mintázatok végrehajtásához használhatja a cikkben korábban ismertetett kifejezéseket. Dönthet úgy is, hogy egyetlen kivétel-kezelési műveletet hajt végre a hatókörön kívül, amely elfogadja a hibák teljes szűrt tömbjét, és eltávolítja az **összes** műveletet. Más hasznos tulajdonságokat is hozzáadhat az  **\@eredmény ()** válaszához az előzőekben leírtak szerint.

## <a name="azure-diagnostics-and-metrics"></a>Azure Diagnostics és mérőszámok

Az előző minták nagyszerű módon kezelik a hibákat és a kivételeket egy futtatáson belül, de a futtatástól függetlenül is azonosíthatók és reagálnak a hibákra. 
A [Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) egyszerű módot biztosít az összes munkafolyamat-esemény, beleértve az összes futtatási és művelet állapotát, egy Azure Storage-fiókba vagy egy azure-Event Hubs létrehozott Event hub küldésére. 

A futtatási állapotok kiértékeléséhez nyomon követheti a naplókat és a metrikákat, vagy közzéteheti azokat bármely figyelési eszközön. Az egyik lehetséges lehetőség az, hogy az összes eseményt Event Hubs- [](https://azure.microsoft.com/services/stream-analytics/)ba Azure stream Analyticsba továbbítsa. Stream Analytics élő lekérdezéseket írhat a diagnosztikai naplókból származó rendellenességek, átlagok vagy hibák alapján. A Stream Analytics használatával adatokat küldhet más adatforrásoknak, például várólistákat, témaköröket, SQL-, Azure Cosmos DB-vagy Power BI.

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogy az ügyfél hogyan épít a hibakezelés során Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [További Logic Apps példák és forgatókönyvek](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
