---
title: "Hiba és kezelése az Azure Logic Apps a kivétel |} Microsoft Docs"
description: "Hiba és a Logic Apps kivételkezelés kombinációját."
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Hibákat és kivételeket a Logic Apps alkalmazásokat kezeléséhez

A Logic Apps az Azure-ban hatékony eszközöket és minták segítségével győződjön meg arról, hogy a Integrációk hatékony és rugalmas-hibákkal szemben biztosít. Minden integrációs architektúra a dokumentációkat az megfelelően kezeli az állásidőt jelent, vagy a függő rendszerekről kapcsolatos problémákat. A Logic Apps segítségével kezelési hibák a kiváló felhasználói élményt nyújtja. Ez biztosítja az eszközöket kivételeket és a munkafolyamatok hibáinak intézkedjen kell.

## <a name="retry-policies"></a>Ismételje meg a házirendek

Újrapróbálkozási házirendje a legalapvetőbb típusú kivétel és hibakezelés. Ha az eredeti kérést átlépi az időkorlátot vagy meghiúsul (összes kérelmet, amely egy 429 eredményez vagy 5XX típusú válasz érkezett), egy újrapróbálkozási házirend határozza meg, ha, és hogyan műveletet meg kell ismételni. 

Az újrapróbálkozási házirendek négy típusa van: alapértelmezett, nincs időköz és az exponenciális időköz. Újrapróbálkozási házirendje nem találhatók munkafolyamat-definíciót, ha az alapértelmezett házirendet, a szolgáltatás által definiált szolgál. 

Az újrapróbálkozási házirendeket konfigurálhat a *bemenetek* egy adott művelet vagy eseményindító Újrapróbálkozást lehetővé tevő esetén. Ehhez hasonlóan beállíthatja újrapróbálkozási (ha van ilyen) Logic App tervezőben. Újrapróbálkozási házirendje, Logic App Designer beállítása Ugrás **beállítások** egy adott művelet végrehajtásához.

Újrapróbálkozási házirend korlátozásaival kapcsolatos további információkért lásd: [Logic Apps korlátozásai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md). Támogatott szintaxissal kapcsolatos további információkért lásd: a [ismételje meg a munkafolyamat-műveleteket és eseményindítók házirend című][retryPolicyMSDN].

### <a name="default"></a>Alapértelmezett

Ha nem definiál újrapróbálkozási házirendje (**retryPolicy** nincs megadva), az alapértelmezett házirend. Az alapértelmezett házirendet az exponenciális időköz házirend, amely legfeljebb négy újrapróbálkozások exponenciálisan gyarapítása 7.5 másodperccel méretezhető intervallumok. Az időköz 5 és 45 másodperc között tárfiókonként maximum. Ebben a példában HTTP munkafolyamat-definíció a házirend az alapértelmezett szabályzat megegyezik:

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

### <a name="none"></a>Nincs

Ha **retryPolicy** értéke **nincs**, a sikertelen kérelmek nem a rendszer ismét megkísérli.

| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| type | Igen | Karakterlánc | **egyik sem** |

### <a name="fixed-interval"></a>Rögzített távolság

Ha **retryPolicy** értéke **rögzített**, a házirend a sikertelen kérelmek újrapróbálja a által megadott időtartam, a következő kérelem elküldése előtt vár.

| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| type | Igen | Karakterlánc | **rögzített** |
| darab | Igen | Egész szám | Újrapróbálkozások száma. 1 és 90 között kell lennie. |
| interval | Igen | Karakterlánc | Újrapróbálkozás a [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). PT5S és PT1D között kell lennie. |

### <a name="exponential-interval"></a>Az exponenciális időköz

Ha **retryPolicy** értéke **exponenciális**, a házirend újrapróbálkozik a sikertelen kérelmek exponenciálisan növekvő tartományból véletlenszerű időtartam után. Egyes újrapróbálkozások garantáltan nagyobb véletlenszerű időközzel küldendő **minimumInterval** és kisebb, mint **maximumInterval**. Egy egységes véletlen változó a következő táblázat a következő tartományban generál a rendszer minden egyes újrapróbálkozási bezárólag **száma**:

**Véletlen változó tartomány**

| Ismételje meg a számot | Minimális időköze | Maximális időköze |
| ------------ |  ------------ |  ------------ |
| 1 | Maximális (0, **minimumInterval**) | Minimális (időköz, **maximumInterval**) |
| 2 | Max (időköz, **minimumInterval**) | Minimális (2 * időköz, **maximumInterval**) |
| 3 | Max (2 * időköz, **minimumInterval**) | Minimális (4 * időköz, **maximumInterval**) |
| 4 | Max (4 * időköz, **minimumInterval**) | Minimális (8 * időköz, **maximumInterval**) |
| ... |

Az exponenciális típus házirendek **száma** és **időköz** szükség. Értékei **minimumInterval** és **maximumInterval** megadása nem kötelező. Később is hozzáadhatja az alapértelmezett érték PT5S és PT1D, illetve felülírására.

| Elem neve | Szükséges | Típus | Leírás |
| ------------ | -------- | ---- | ----------- |
| type | Igen | Karakterlánc | **Az exponenciális** |
| darab | Igen | Egész szám | Újrapróbálkozások száma. 1 és 90 között kell lennie.  |
| interval | Igen | Karakterlánc | Újrapróbálkozás a [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). PT5S és PT1D között kell lennie. |
| minimumInterval | Nem | Karakterlánc | A minimális újrapróbálkozás [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). PT5S között kell lennie, és **időköz**. |
| maximumInterval | Nem | Karakterlánc | A minimális újrapróbálkozás [ISO 8601 formátum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Közé kell esnie **időköz** és PT1D. |

## <a name="catch-failures-with-the-runafter-property"></a>A tényleges runAfter tulajdonság hibák

Minden logikai alkalmazás művelet kijelenti, hogy milyen műveleteket csak befejezése a művelet megkezdése előtt. Ez hasonlít rendezést a lépéseket a munkafolyamatban. A művelet definícióban ebben a rendezése is ismert, a **runAfter** tulajdonság. 

A **runAfter** tulajdonsága olyan objektum, amely leírja, milyen műveleteket és a művelet állapotok hajtja végre a műveletet. Alapértelmezés szerint a Logic App Designer által hozzáadott összes műveletek beállítás van megadva futtatása után az előző lépést, ha az előző lépésben eredmény **sikeres**. 

Azonban testre szabhatja a **runAfter** érték az érvényesítést a műveleteket, ha a fenti műveletek eredménye **sikertelen**, **kimaradnak**, vagy egy lehetséges ezeket az értékeket. Ha egy adott művelet után vegyen fel egy elemet egy kijelölt Azure Service Bus-témakörbe **Insert_Row** nem sikerül, használhatja a következő **runAfter** konfiguráció:

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

Vegye figyelembe, hogy **runAfter** van beállítva az érvényesítést, ha a **Insert_Row** művelet eredménye **sikertelen**. A művelet futtatásához, ha a művelet állapota **sikeres**, **sikertelen**, vagy **kimaradnak**, a következő szintaxist használja:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Műveletek futtatásához, és fejeződik be sikeresen, miután az előző művelet nem sikerült fel van tüntetve **sikeres**. Ez azt jelenti, hogy ha Ön egy munkafolyamatban, maga a Futtatás sikeresen általános hibák jelölésű **sikeres**.

## <a name="scopes-and-results-to-evaluate-actions"></a>Hatókörök és az eredmények műveletek kiértékelése

Csoportosíthatja műveletek belül egy [hatókör](../logic-apps/logic-apps-loops-and-scopes.md), hasonlóan ahhoz, ahogy az egyes műveletek után futtatja. A hatókör úgy működik, műveletek logikai csoportosítása. 

Hatókörök lehetnek hasznosak, a logikai alkalmazás műveletek sorolására, mind a hatókör állapotát összesítő értékelések végrehajtásához. A hatókör maga állapot kap, a hatókör összes művelet után. A hatókör határozza meg a Futtatás mint feltételeknek. Ha a végső egy végrehajtási fiókirodai művelete **sikertelen** vagy **Aborted**, az állapot **sikertelen**.

Az érvényesítést a konkrét műveletek az összes hiba történt a hatókörön belül, használhatja a **runAfter** van megjelölve hatókörrel rendelkező **sikertelen**. Ha *bármely* hatókörében művelet sikertelen, ha **runAfter** adott hatókörben van szüksége a hibák egyetlen művelettel hozhat létre.

### <a name="get-the-context-of-failures-with-results"></a>A környezet sikertelen eredménnyel lekérdezése

Bár a hatókörből hibák elfogja akkor hasznos, érdemes segít megérteni, hogy pontosan mely művelet végrehajtása sikertelen volt, és megérteni az esetleges hibákat vagy visszaadott eredményobjektumokban tárolt állapotkódok környezetben is. A  **@result()** munkafolyamat-funkció biztosít a hatókör összes művelet eredménye kapcsolatos környezetben.

A  **@result()** egyetlen paramétert (hatókör neve) és az összes művelet eredményeinek hatókörön belüli tömbjét adja vissza. E művelet objektumok például őket a  **@actions()** objektum, például művelet kezdési ideje, a művelet befejezése, a művelet állapota, a művelet bemeneti, a művelet közötti összefüggést szemléltető azonosítók és a művelet kimenetében. 

Egy hatókörön belüli bármely művelet, amelyet nem sikerült kontextusában küldéséhez, könnyen összepárosíthassa egy  **@result()** működik egy **runAfter** tulajdonság.

Művelet végrehajtása *minden* egy hatókör, amely rendelkezik a művelet egy **sikertelen** eredményt, és a sikertelen műveletekre eredmények tömb szűréséhez párosítható  **@result()** az egy [Filter_array](../connectors/connectors-native-query.md) műveletet és egy [foreach](../logic-apps/logic-apps-loops-and-scopes.md) hurok. A szűrt eredmény tömbbel rendelkező végezheti el egy műveletet minden egyes hibához tartozó használatával a **foreach** hurok. 

Az alábbiakban egy példa, amely elküld egy HTTP POST kérelem bármely művelet, amelyet nem sikerült a hatókörében My_Scope válasz törzsű:

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

Részletes útmutató bemutatja, mi történik, az előző példában az itt található:

1. Minden műveletek belül My_Scope, az eredmény a **Filter_array** művelet szűrők  **@result(My_Scope)**.

2. A feltétel **Filter_array** tetszőleges  **@result()** egyenlő állapotú elem **sikertelen**. Ez a feltétel az összes művelet eredményeinek My_Scope, csak a sikertelen művelet eredményekkel tömbhöz tömbben szűrők.

3. Hajtsa végre a **foreach** műveletet alkalmazza a *szűrt tömb* kimenete. Ezt a lépést végrehajt egy műveletet *minden* korábban szűrt művelet eredménye nem sikerült.

    Ha a hatókörében egyetlen művelet sikertelen volt, a műveletek a **foreach** csak egyszer futnak le. Több sikertelen műveletek miatt egy művelet hibája /.

4. Küldjön egy HTTP POST a **foreach** adott válasz törzsének, elem vagy  **@item() ["kimenetek"] [a "törzs"]**. A  **@result()** elem alakzat megegyezik a  **@actions()** alakú. Akkor értelmezhető azonos módon.

5. Közé tartoznak a sikertelen művelet nevű két egyéni fejlécek  **@item() [name]** és a sikertelen Futtatás ügyfél nyomkövetési azonosító  **@item() [clientTrackingId]**.

Referenciaként Íme egy példa  **@result()** elemet. Azt illusztrálja a **neve**, **törzs**, és **clientTrackingId** tulajdonságokról az előző példában elemzésének. Külső egy **foreach** művelet,  **@result()** ezek az objektumok tömbjét adja vissza.

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

A különböző kivételkezelő minták a kifejezések a cikk korábbi részében ismertetett használhatják. Előfordulhat, hogy egyetlen kivételkezelő, amely fogadja a teljes szűrt tömb hibák hatókörén kívüli művelet végrehajtásához válassza ki, és távolítsa el a **foreach**. Egyéb hasznos tulajdonságait is használható a  **@result()** választ, a fentebb leírt módon.

## <a name="azure-diagnostics-and-telemetry"></a>Az Azure Diagnostics- és telemetriabevitelt

A jelen cikkben ismertetett minták hibákat és kivételeket futtató belüli kezelésére kiváló módszereket biztosítanak, de is azonosíthatja és hibáknak független Futtatás magát. [Az Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) (beleértve az összes futtató és a művelet állapotának) összes munkafolyamat események küldésére, egy Azure storage-fiókot, vagy egy eseményközpontot, az Azure Event Hubs egyszerű módszert kínál. 

Futási állapotának értékeléséhez, naplók és a metrikák figyelése, vagy közzététele bármely előnyben részesített felügyeleti eszközt. A lehetséges lehetőség egy adatfolyam-Eseményközpontok csatlakoztatásával keresztül az események [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). A Stream Analytics írhat élő lekérdezést, bármely rendellenességek észlelését, átlagok vagy sikertelen a diagnosztikai naplókat. A Stream Analytics segítségével adatokat küld a más adatforrásokhoz, például várólisták, témakörök, SQL, Azure Cosmos DB vagy Power bi-ban.

## <a name="next-steps"></a>További lépések

* Lásd: hogyan ügyfél [alkot a Logic Apps az Azure-ban hibakezelési](../logic-apps/logic-apps-scenario-error-and-exception-handling.md).
* További [Logic Apps példák és forgatókönyvek](../logic-apps/logic-apps-examples-and-scenarios.md).
* Megtudhatja, hogyan hozzon létre [logic Apps alkalmazások központi telepítése automatikus](../logic-apps/logic-apps-create-deploy-template.md).
* Megtudhatja, hogyan [hozza létre és logikai alkalmazások telepítése a Visual Studio](logic-apps-deploy-from-vs.md).

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
