---
title: Hiba és kezelése az Azure Logic Apps a kivétel |} Microsoft Docs
description: Hiba és a Logic Apps kivételkezelés kombinációját.
services: logic-apps
documentationcenter: ''
author: dereklee
manager: jeconnoc
editor: ''
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: ee2c4f1408dcb6527220cd3870ab00d83987f471
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300062"
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Hibákat és kivételeket a Logic Apps alkalmazásokat kezeléséhez

Megfelelően kezelési állásidő vagy függő rendszerek kapcsolatos problémákat jelenthet a kihívás az összes integrációs architektúra. A Logic Apps a szeretne létrehozni, amelyek rugalmasan kiküszöbölik problémák és hibák robusztus integrációja, a hibákat és kivételeket kezelésére egy első osztályú módszert is kínál. 

## <a name="retry-policies"></a>Ismételje meg a házirendek

A legalapvetőbb kivétel és hibakezelési használhatja az újrapróbálkozási házirendet. Ha egy kezdeti kérelem túllépte az időkorlátot, vagy sikertelen volt, ez utóbbi érték, hogy egy 429 vagy 5XX típusú válasz érkezett, ezzel a házirend-eredmények határozza meg, hogyan a művelettel újrapróbálkozik a kérelem kérelmet. 

Az újrapróbálkozási házirendek négy típusa van: alapértelmezett, nincs időköz és az exponenciális időköz. Ha a munkafolyamat-definíciót újrapróbálkozási házirendje nem rendelkezik, az alapértelmezett házirendet, a szolgáltatás által meghatározott módon használja.

Újrapróbálkozási házirendek beállítása, ha van ilyen, nyissa meg a Logic App tervezőt a Logic Apps alkalmazást, és válassza **beállítások** a Logic Apps alkalmazást az adott művelethez. Vagy az újrapróbálkozási házirendeket határozhatja meg a **bemenetek** szakasz egy bizonyos művelet vagy eseményindító, ha Újrapróbálkozást lehetővé tevő, a munkafolyamat-definícióban. Az általános szintaxisa a következő:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Szintaxisra vonatkozó további információkért és a **bemenetek** szakaszban, tekintse meg a [újrapróbálkozási-házirend a szakasz a munkafolyamat-műveleteket és eseményindítók][retryPolicyMSDN]. Újrapróbálkozási házirend korlátozásaival kapcsolatos további információkért lásd: [Logic Apps korlátozásai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md). 

### <a name="default"></a>Alapértelmezett

Az újrapróbálkozási házirendje nem definiált a **retryPolicy** szakaszban, a Logic Apps alkalmazást használja az alapértelmezett házirendet, amely egy [exponenciális időköz házirend](#exponential-interval) exponenciálisan küld, amely legfeljebb négy újrapróbálkozási lehetőségbe: 7.5 másodperccel méretezése időszakok növelését. Az időköz 5 és 45 másodperc között tárfiókonként. Ezzel a házirend-megegyezik az ebben a példában HTTP munkafolyamat-definíciót házirend:

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT1H"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>None

Ha **retryPolicy** való **nincs**, ez a házirend nem próbálja meg újra a sikertelen kérelmek.

| Elem neve | Szükséges | Típus | Leírás | 
| ------------ | -------- | ---- | ----------- | 
| type | Igen | Sztring | **none** | 
||||| 

### <a name="fixed-interval"></a>Rögzített távolság

Ha **retryPolicy** való **rögzített**, ez a házirend egy sikertelen kérelmek újrapróbálja a által megadott időtartam, a következő kérelem elküldése előtt vár.

| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| type | Igen | Sztring | **Rögzített** |
| darab | Igen | Egész szám | 1 és 90 közé kell esnie újrapróbálkozások száma | 
| interval | Igen | Sztring | Az újrapróbálkozási időköz [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), amely PT5S és PT1D között kell lennie | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Az exponenciális időköz

Ha **retryPolicy** való **exponenciális**, ez a házirend újrapróbálkozik a sikertelen kérelmek exponenciálisan növekvő tartományból véletlenszerű időtartam után. A házirend is biztosítja, hogy az egyes újrapróbálkozások, véletlenszerű időközönként, amely nagyobb, mint küldendő **minimumInterval** és kisebb, mint **maximumInterval**. Exponenciális házirendje előírja **száma** és **időköz**, miközben értékeinek **minimumInterval** és **maximumInterval** megadása nem kötelező. Ha azt szeretné, illetve a PT5S és PT1D alapértelmezett érték felülírására, ezeket az értékeket is hozzáadhat.

| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| type | Igen | Sztring | **Az exponenciális** |
| darab | Igen | Egész szám | 1 és 90 közé kell esnie újrapróbálkozások száma  |
| interval | Igen | Sztring | Az újrapróbálkozási időköz [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), amely PT5S és PT1D közé kell esnie. |
| minimumInterval | Nem | Sztring | A minimális újrapróbálkozási időközének [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), amely PT5S között kell lennie, és **időköz** |
| maximumInterval | Nem | Sztring | A minimális újrapróbálkozási időközének [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), amelyek közé kell esnie **időköz** és PT1D | 
||||| 

Az alábbi táblázatban a megadott tartomány egységes véletlen változó előállítási módszerének minden újrapróbálkozásra bezárólag **száma**:

**Véletlen változó tartomány**

| Ismételje meg a számot | Minimális időköze | Maximális időköze |
| ------------ | ---------------- | ---------------- |
| 1 | Maximális (0, **minimumInterval**) | Minimális (időköz, **maximumInterval**) |
| 2 | Max (időköz, **minimumInterval**) | Minimális (2 * időköz, **maximumInterval**) |
| 3 | Max (2 * időköz, **minimumInterval**) | Minimális (4 * időköz, **maximumInterval**) |
| 4 | Max (4 * időköz, **minimumInterval**) | Minimális (8 * időköz, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>A tényleges és kijavíthassa a hibákat, ha az RunAfter tulajdonság

Minden logikai alkalmazás művelet csak befejezése után a művelet elindul, hogyan adhatja meg a lépések sorrendjét a munkafolyamat hasonló műveletek deklarálja. Egy művelet-definícióban a **runAfter** tulajdonság határozza meg, a rendezés, és olyan objektum, amely leírja, milyen műveleteket és a művelet állapotok hajtja végre a műveletet.

Alapértelmezés szerint a Logic App Designer hozzáadott összes műveletek beállítás van megadva az előző lépés után fut, ha az előző lépésben eredmény **sikeres**. Azonban testre szabhatja a **runAfter** értékét úgy, hogy a műveletek érvényesítést, ha a korábbi műveletek szerint eredménye **sikertelen**, **kimaradnak**, vagy a következő értékek bizonyos kombinációinál. Ahhoz például, hogy egy adott követően vegyen fel egy elemet egy adott Service Bus-témakörbe **Insert_Row** művelet meghiúsul, használhat ez a példa **runAfter** definíciója:

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

A **runAfter** tulajdonsága futtatható, ha a **Insert_Row** művelet állapota: **sikertelen**. A művelet futtatásához, ha a művelet állapota **sikeres**, **sikertelen**, vagy **kimaradnak**, a következő szintaxist használja:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Műveletek futtatásához, és fejeződik be sikeresen, miután a fenti művelet sikertelen volt, fel van tüntetve **sikeres**. Ez azt jelenti, hogy ha Ön egy munkafolyamatban, maga a Futtatás sikeresen általános hibák van megjelölve **sikeres**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>A hatókörök és azok eredményeit műveletek kiértékelése

Hasonló lépéseket fut az egyes műveletek után a **runAfter** tulajdonság, csoportosíthatja műveletek belül egy [hatókör](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Hatókörök is használhatja, ha meg szeretné logikailag műveletek csoportba foglalásához, mérje fel a hatókör összesített állapotát és a műveletek adott állapota alapján. A hatókör összes művelet után a saját magát a hatókör lekéri a saját állapotát. 

A hatókör állapotának ellenőrzéséhez használja a ugyanazok a feltételek, például ellenőrizze a logikai alkalmazás futási állapotát, amelyekkel **sikeres**, **sikertelen**, és így tovább. 

Alapértelmezés szerint a hatókör összes művelet sikeres, a hatókör állapotát megjelölve **sikeres**. Ha az utolsó művelet hatókörben **sikertelen** vagy **Aborted**, a hatókör állapotát van megjelölve **sikertelen**. 

A kivételeket a egy **sikertelen** hatókör és futtatási műveleteket, amelyek kezelik azokat a hibákat, használhatja a **runAfter** tulajdonság az adott **sikertelen** hatókör. Így ha *bármely* hatókörében művelet sikertelen, és használja a **runAfter** tulajdonság az adott hatókörnél, létrehozhat egyetlen művelettel van szüksége a hibák.

Hatókörök használati korlátait, lásd: [korlátozásai és konfigurációs](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>És az eredmények lekérése sikertelen

Bár a hatókörből hibák elfogja akkor hasznos, érdemes segít megérteni, hogy pontosan mely műveletek nem sikerült, és az esetleges hibákat vagy visszaadott eredményobjektumokban tárolt állapotkódok környezetben is. A  **@result()** munkafolyamat-funkció biztosít a hatókör összes művelet eredménye kapcsolatos környezetben.

A  **@result()** függvény (a hatókör neve) egyetlen paramétert fogad, és minden művelet eredményeinek hatókörön belüli tömbjét adja vissza. E művelet objektumok például őket a  **@actions()** objektumot, például a művelet kezdési idő, befejezési időpontja, állapota, bemeneti adatokat, korrelációs azonosító és kimenetek. Egy hatókörön belüli bármely művelet, amelyet nem sikerült a környezet küldéséhez, könnyen összepárosíthassa egy  **@result()** működik egy **runAfter** tulajdonság.

Egy művelet *minden* egy hatókör, amely rendelkezik a művelet egy **sikertelen** eredményt, és a tömb le a sikertelen műveletek eredmények szűréséhez párosítható  **@result()** az egy **[szűrő tömb](../connectors/connectors-native-query.md)** műveletet és egy **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)** hurok. A szűrt eredmény tömb igénybe vehet, és egy műveletet minden egyes hiba használatára vonatkozó a **ForEach** hurok. 

Íme egy példa, majd részletesen ismerteti, amelyet az adott válasz törzsének bármely művelet, amelyet nem sikerült a HTTP POST kérést küld "My_Scope" hatókörén belül:

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Részletes útmutató, amely leírja, mi történik, ebben a példában a következő:

1. Az eredmény "My_Scope" belül minden műveletek a **szűrő tömb** művelet szűrők  **@result(My_Scope)**.

2. A feltétel **szűrő tömb** tetszőleges  **@result()** egyenlő állapotú elem **sikertelen**. Ez a feltétel az összes művelet eredményeinek "My_Scope" le egy tömb csak a sikertelen művelet eredményekkel tömbje szűrők.

3. Hajtsa végre a **minden** művelet a hurok a *szűrt tömb* kimenete. Ezt a lépést végrehajt egy műveletet *minden* korábban szűrt művelet eredménye nem sikerült.

   Ha a hatókörében egyetlen művelet sikertelen volt, a műveletek a **foreach** csak egyszer futnak le. 
   Több sikertelen műveletek miatt egy művelet hibája /.

4. Küldjön egy HTTP POST a **foreach** elemet adott válasz törzsének, amely  **@item() ["kimenetek"] [a "törzs"]**. A  **@result()** elem alakzat megegyezik a  **@actions()** alakul és értelmezni tudja a megszokott módon.

5. Közé tartoznak a sikertelen művelet nevű két egyéni fejlécek  **@item() [name]** és a sikertelen Futtatás ügyfél nyomkövetési azonosító  **@item() [clientTrackingId]**.

Referenciaként Íme egy példa  **@result()** elem, amely a **neve**, **törzs**, és **clientTrackingId** az előző példában szereplő elemzésének tulajdonságokhoz. Kívüli egy **foreach** művelet,  **@result()** ezek az objektumok tömbjét adja vissza.

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

Hajtsa végre a különböző kivételkezelő minták, a jelen cikkben korábban ismertetett kifejezések is használhat. Előfordulhat, hogy egyetlen kivételkezelő, amely fogadja a teljes szűrt tömb hibák hatókörén kívüli művelet végrehajtásához válassza ki, és távolítsa el a **foreach** művelet. Egyéb hasznos tulajdonságait is használható a  **@result()** előzőekben leírt válasz.

## <a name="azure-diagnostics-and-telemetry"></a>Az Azure Diagnostics- és telemetriabevitelt

Az előző mintától nagy a módon a hibákat és kivételeket futtató belül, de is azonosíthatja és hibáknak független Futtatás magát. 
[Az Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) üzenetküldési összes munkafolyamat, beleértve az összes futtató és a művelet állapotának, egy Azure Storage-fiókot vagy egy eseményközpontot, az Azure Event Hubs létrehozott egyszerű módszert kínál. 

Futási állapotának értékeléséhez, a naplók és a metrikák figyelése, vagy közzéteheti bármely előnyben részesített felügyeleti eszközt. Egy lehetséges lehetőség az Event Hubs keresztül az események adatfolyamként történő [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). A Stream Analytics írhat élő lekérdezést, bármely rendellenességek észlelését, átlagok vagy sikertelen a diagnosztikai naplókat. A Stream Analytics segítségével adatokat küld más adatforrások, például várólisták, témakörök, SQL, Azure Cosmos DB vagy Power bi-ban.

## <a name="next-steps"></a>További lépések

* [Tekintse meg, hogyan ügyfél buildek hiba történt az Azure Logic Apps kezelése](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [További Logic Apps példák és forgatókönyvek keresése](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9