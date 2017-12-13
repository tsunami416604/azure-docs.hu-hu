---
title: "Hiba & kivételkezelést - Azure Logic Apps |} Microsoft Docs"
description: "Hiba és az Azure Logic Apps kivételkezelő minták"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 20f93d5dbcc91a633f040feb454e96ed3f7d561f
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hibákat és kivételeket az Azure Logic Apps alkalmazásokat kezeléséhez

Az Azure Logic Apps hatékony eszközöket biztosít, és minták segítségével győződjön meg arról, hogy a Integrációk hatékony és rugalmas-hibákkal szemben. Minden integrációs architektúra jelent a dokumentációkat az, hogy megfelelően kezelje az állásidő és a függő rendszerek kapcsolatos problémákat. A Logic Apps segítségével hibák kezelése a kiváló felhasználói élmény, felkínálva a szükséges kivételeket és a munkafolyamatok hibáinak intézkedjen eszközöket.

## <a name="retry-policies"></a>Ismételje meg a házirendek

Újrapróbálkozási házirendje a legalapvetőbb típusú kivétel és hibakezelés. Ha az eredeti kérelem túllépte az időkorlátot, vagy sikertelen volt (a kérelmet, amely egy 429 eredményez vagy 5XX típusú válasz érkezett), ez a házirend határozza meg, ha, és hogyan érdemes újra megpróbálnia a művelet. Újrapróbálkozási házirend három típusú `exponential`, `fixed`, és `none`. Ha újrapróbálkozási házirendje nem áll rendelkezésre a munkafolyamat-definíciót, majd az alapértelmezett házirend szolgál. Az újrapróbálkozási házirendeket konfigurálhat a **bemenetek** egy adott művelet vagy eseményindító Újrapróbálkozást lehetővé tevő esetén. Hasonlóképpen, a Logic App Designer újrapróbálkozási a házirendek konfigurálhatók (ha van ilyen) alatt a **beállításait** az egyes blokkok.

Újrapróbálkozási házirendek korlátai információkért lásd: [Logic Apps korlátozásai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md) és a támogatott szintaxis további információkért lásd: a [újrapróbálkozási-házirend a szakasz a munkafolyamat-műveleteket és eseményindítók][retryPolicyMSDN].

### <a name="exponential-interval"></a>Az exponenciális időköz
A `exponential` házirendtípus megpróbálja a sikertelen kérelmek exponenciálisan növekvő tartományból véletlenszerű időtartam után. Egyes újrapróbálkozások garantáltan nagyobb véletlenszerű időközzel küldendő **minimumInterval** és kisebb, mint **maximumInterval**. Az egységes véletlen változó az alábbi tartomány jön létre minden egyes újrapróbálkozási bezárólag **száma**:
<table>
<tr><th> Véletlenszerű változó tartomány </th></tr>
<tr><td>

| Ismételje meg a számot | Minimális időköze | Maximális időköze |
| ------------ |  ------------ |  ------------ |
| 1 | Maximális (0, **minimumInterval**) | Minimális (időköz, **maximumInterval**) |
| 2 | Max (időköz, **minimumInterval**) | Minimális (2 * időköz, **maximumInterval**) |
| 3 | Max (2 * időköz, **minimumInterval**) | Minimális (4 * időköz, **maximumInterval**) |
| 4 | Max (4 * időköz, **minimumInterval**) | Minimális (8 * időköz, **maximumInterval**) |
| ... |

</td></tr></table>

A `exponential` írja be a házirendeket, **száma** és **időköz** során szükséges **minimumInterval** és **maximumInterval** lehet nem kötelezően megadott PT5S és PT1D alapértelmezett értékének felülbírálása kulcsattribútumokkal.

| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| type | Igen | Karakterlánc | `exponential` |
| darab | Igen | Egész szám | Az újrapróbálkozások száma, 1 és 90 között kell lennie  |
| interval | Igen | Karakterlánc | újrapróbálkozás a [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), PT5S és PT1D között kell lennie |
| minimumInterval | Nem| Karakterlánc | a minimális újrapróbálkozás [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), PT5S között kell lennie, és **időköz** |
| maximumInterval | Nem| Karakterlánc | a minimális újrapróbálkozás [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), közé kell esnie **időköz** és PT1D |

### <a name="fixed-interval"></a>Rögzített távolság

A `fixed` házirendtípus megpróbálja a sikertelen kérelmek által megadott időtartam, a következő kérelem elküldése előtt vár.

| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| type | Igen | Karakterlánc | `fixed`|
| darab | Igen | Egész szám | Az újrapróbálkozások száma, 1 és 90 között kell lennie |
| interval | Igen | Karakterlánc | újrapróbálkozás a [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), PT5S és PT1D között kell lennie |

### <a name="none"></a>None
A `none` házirend típusa nem próbálkozik meg a sikertelen kérelmek.

| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| type | Igen | Karakterlánc | `none`|

### <a name="default"></a>Alapértelmezett
Ha nem az újrapróbálkozási házirendje meg van adva, az alapértelmezett házirend szolgál. Az alapértelmezett házirend egy exponenciális időköz házirendet, amely legfeljebb 4 újrapróbálkozások exponenciálisan gyarapítása 7.5 másodperccel méretezhető és 5 és 45 másodperc között tárfiókonként időközönként küldi. Ez az alapértelmezett házirend (Ha **retryPolicy** nincs definiálva) felel meg a házirend ebben a példában HTTP munkafolyamat-definíciót:

```json
"HTTP":
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

## <a name="catch-failures-with-the-runafter-property"></a>A tényleges RunAfter tulajdonság hibák

Minden logikai alkalmazás művelet kijelenti, hogy milyen műveleteket csak befejezése előtt a művelet, például a lépéseket sorrendje a munkafolyamat. A művelet definícióban ebben a rendezése is ismert, a `runAfter` tulajdonság. Ez a tulajdonság olyan objektum, amely leírja, milyen műveleteket és a művelet állapotok hajtja végre a műveletet. Alapértelmezés szerint hozzáadva a Logic App Designer minden művelet beállítás van megadva `runAfter` az előző lépésben Ha az előző lépésben `Succeeded`. Azonban testre szabhatja ezt az értéket az érvényesítést a műveleteket, ha a korábbi műveletek `Failed`, `Skipped`, vagy egy lehetséges ezeket az értékeket. Ha egy adott művelet után vegyen fel egy elemet egy kijelölt Service Bus-témakörbe `Insert_Row` nem sikerül, használhatja a következő `runAfter` konfiguráció:

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

Figyelje meg a `runAfter` tulajdonsága az érvényesítést, ha a `Insert_Row` művelet `Failed`. A művelet futtatásához, ha a művelet állapota `Succeeded`, `Failed`, vagy `Skipped`, a következő szintaxist használja:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Műveletek futtatásához, és sikeresen befejeződik, miután a fenti művelet sikertelen volt, fel van tüntetve `Succeeded`. Ez azt jelenti, hogy ha Ön egy munkafolyamatban, maga a Futtatás sikeresen általános hibák van megjelölve `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Hatókörök és az eredmények műveletek kiértékelése

Hogyan futtathat hasonlít az egyes műveletek után is csoportosíthatja műveletek belül egy [hatókör](../logic-apps/logic-apps-loops-and-scopes.md), amely összekötőként műveletek logikai csoportosítása. Hatókörök hasznosak a logic app műveletek sorolására, mind a hatókör állapotát összesítő értékelések végrehajtásához. A hatókör maga állapot kap, a hatókör összes művelet után. A hatókör határozza meg a Futtatás mint feltételeknek. Ha a végső művelet egy végrehajtás ágában `Failed` vagy `Aborted`, az állapot `Failed`.

Az érvényesítést a konkrét műveletek az esetleges hibákat, és ismételje meg a hatókörön belül, használhatja a `runAfter` van megjelölve hatókörrel rendelkező `Failed`. Ha *bármely* hatókörében művelet sikertelen, a hatókör sikertelen megadható, hogy fut van szüksége a hibák egyetlen művelettel létrehozni.

### <a name="getting-the-context-of-failures-with-results"></a>Hibák az eredményekkel keretében beolvasása

Bár egy hatókörből hibák elfogja akkor hasznos, érdemes lehet a környezetben, pontosan mely műveletek nem sikerült, és az esetleges hibákat vagy a visszaadott eredményobjektumokban tárolt állapotkódok jobb megértése érdekében. A `@result()` munkafolyamat-funkció biztosít a hatókör összes művelet eredménye kapcsolatos környezetben.

`@result()`egy egyetlen paramétert, a hatókör neve időt vesz igénybe, és minden művelet eredményeinek hatókörön belüli tömbjét adja vissza. E művelet objektumok például őket a `@actions()` objektum, például művelet kezdési ideje, a művelet befejezése, a művelet állapota, a művelet bemeneti, a művelet közötti összefüggést szemléltető azonosítók és a művelet kimenetében. Egy hatókörön belüli bármely művelet, amelyet nem sikerült kontextusában küldéséhez, könnyen összepárosíthassa egy `@result()` működik egy `runAfter`.

Egy művelet végrehajtása *minden* művelet hatókörben, amely `Failed`, a tömb sikertelen műveletekre eredmények szűrésére, hogy párosítható `@result()` a egy  **[szűrő tömb](../connectors/connectors-native-query.md)**  művelet és a  **[ForEach](../logic-apps/logic-apps-loops-and-scopes.md)**  hurok. A szűrt eredmény tömb igénybe vehet, és egy műveletet minden egyes hiba használatára vonatkozó a **ForEach** hurok. Íme egy példa, majd részletesen ismerteti, amely az adott válasz törzsének bármely művelet, amelyet nem sikerült a HTTP POST kérést küld a hatókörön belüli `My_Scope`.

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

Részletes útmutató bemutatja, mi történik az itt található:

1. Az eredmény belül minden műveletek `My_Scope`, a **szűrő tömb** művelet szűrők `@result('My_Scope')`.

2. A feltétel **szűrő tömb** tetszőleges `@result()` elem, amelynek állapota egyenlő `Failed`. Ez a feltétel a tömb összes művelet eredményeinek szűrésével `My_Scope` elemből álló tömböt csak sikertelen volt a művelet eredménye.

3. Hajtsa végre egy **minden** műveletet alkalmazza a **szűrt tömb** kimenete. Ezt a lépést végrehajt egy műveletet *minden* korábban szűrt művelet eredménye nem sikerült.

    Ha a hatókörében egyetlen művelettel nem sikerült, a műveletek a `foreach` csak egyszer futnak le. 
    Sok sikertelen műveletek miatt egy művelet hibája /.

4. Küldjön egy HTTP POST a `foreach` adott válasz törzsének, elem vagy `@item()['outputs']['body']`. A `@result()` elem alakzat megegyezik a `@actions()` alakul, és a megszokott módon program értelmezni tudja.

5. Közé tartoznak a sikertelen művelet nevű két egyéni fejlécek `@item()['name']` és a sikertelen Futtatás ügyfél nyomkövetési azonosító `@item()['clientTrackingId']`.

Referenciaként Íme egy példa `@result()` elem, amely a `name`, `body`, és `clientTrackingId` az előző példában szereplő elemzésének tulajdonságokhoz. Kívüli egy `foreach`, `@result()` ezek az objektumok tömbjét adja vissza.

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

Hajtsa végre a különböző kivételkezelő minták, a korábbi műveletek szerint kifejezések is használhat. Előfordulhat, hogy egyetlen kivételkezelő, amely fogadja a teljes szűrt tömb hibák hatókörén kívüli művelet végrehajtásához válassza ki, és távolítsa el a `foreach`. Egyéb hasznos tulajdonságait is használható a `@result()` előzőleg bemutatott válasz.

## <a name="azure-diagnostics-and-telemetry"></a>Az Azure Diagnostics- és telemetriabevitelt

Az előző mintától nagy a módon a hibákat és kivételeket futtató belül, de is azonosíthatja és hibáknak független Futtatás magát. 
[Az Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) (beleértve az összes futtató és a művelet állapotának) összes munkafolyamat eseményt küldeni egy Azure Storage-fiókot vagy egy Azure Eseményközponthoz egyszerű módszert kínál. Futási állapotának értékeléséhez, a naplók és a metrikák figyelése, vagy inkább bármely felügyeleti eszközt közzéteheti. Egy lehetséges lehetőség egy adatfolyam-keresztül Azure Event hubs Eseményközpontot, az összes esemény [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). A Stream Analytics írhat élő lekérdezések ki bármely rendellenességek észlelését, átlagok vagy sikertelen a diagnosztikai naplókat a. A Stream Analytics könnyen kimenetre küldheti például várólisták, témakörök, SQL, Azure Cosmos DB és a Power BI más adatforrásokhoz.

## <a name="next-steps"></a>Következő lépések

* [Tekintse meg, hogyan ügyfél buildek hiba történt az Azure Logic Apps kezelése](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [További Logic Apps példák és forgatókönyvek keresése](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Útmutató a logic apps automatikus központi telepítésének létrehozása](../logic-apps/logic-apps-create-deploy-template.md)
* [Logikai alkalmazások létrehozása és üzembe helyezése a Visual Studióval](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
