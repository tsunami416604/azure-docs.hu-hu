---
title: Hibák és kivételek kezelése a munkafolyamatokban
description: Megtudhatja, hogyan kezelheti a Azure Logic Apps használatával létrehozott automatizált feladatokban és munkafolyamatokban előforduló hibákat és kivételeket.
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284030"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hibák és kivételek kezelése Azure Logic Appsban

Az integrációs architektúra megfelelő módon kezeli az állásidőt vagy a függő rendszerek által okozott problémákat. A problémák és hibák zökkenőmentes kezelésére szolgáló robusztus és rugalmas integrációk létrehozásához Logic Apps a hibák és kivételek kezelésének első osztályú élményét nyújtja.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Újrapróbálkozási szabályzatok

A legalapvetőbb kivételek és hibakezelés érdekében az *újrapróbálkozási szabályzatot* bármilyen művelet vagy trigger esetében használhatja, ahol ez támogatott, például: [http-művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Az újrapróbálkozási szabályzat meghatározza, hogy a művelet vagy az indítás hogyan próbálkozik újra egy kéréssel, amikor az eredeti kérelem időtúllépése vagy meghibásodása meghiúsul, ami 408, 429 vagy 5xx választ eredményez. Ha nincs más újrapróbálkozási szabályzat, a rendszer az alapértelmezett házirendet használja.

Az újrapróbálkozási szabályzatok típusai:

| Típus | Leírás |
|------|-------------|
| **Alapértelmezett** | Ez a szabályzat legfeljebb négy újrapróbálkozást küld el *exponenciálisan növekvő* intervallumokban, amelyek mérete 7,5 másodperc, de 5 – 45 másodpercre van korlátozva. |
| **Exponenciális időköz**  | Ez a szabályzat egy exponenciálisan növekvő tartományból érkező véletlenszerű intervallumot vár a következő kérelem elküldése előtt. |
| **Rögzített intervallum**  | Ez a házirend a következő kérelem elküldése előtt megvárja a megadott intervallumot. |
| **NEz egy**  | Ne küldje újra a kérelmet. |
|||

További információ az újrapróbálkozási házirend korlátairól: [Logic apps korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#request-limits).

### <a name="change-retry-policy"></a>Újrapróbálkozási szabályzat módosítása

Másik újrapróbálkozási házirend kiválasztásához kövesse az alábbi lépéseket:

1. Nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Nyisson meg egy művelet vagy trigger **beállításait** .

1. Ha a művelet vagy az aktiválás támogatja az újrapróbálkozási szabályzatokat, az **újrapróbálkozási házirend**területen válassza ki a kívánt típust.

Vagy manuálisan is megadhatja az újrapróbálkozási házirendet az újrapróbálkozási szabályzatokat támogató művelet vagy trigger `inputs` szakaszában. Ha nem ad meg újrapróbálkozási házirendet, a művelet az alapértelmezett házirendet használja.

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
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Szükséges*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*Újrapróbálkozás – házirend típusa*> | Sztring | A használni kívánt újrapróbálkozási házirend típusa: `default`, `none`, `fixed`vagy `exponential` |
| *újrapróbálkozási idő* <> | Sztring | Az újrapróbálkozási időköz, amelyben az értéknek [ISO 8601 formátumot](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)kell használnia. Az alapértelmezett minimális időköz `PT5S`, és a maximális intervallum `PT1D`. Az exponenciális időközi szabályzat használatakor különböző minimális és maximális értékeket adhat meg. |
| *újrapróbálkozási kísérletek* <> | Egész szám | Az újrapróbálkozási kísérletek száma, amelynek 1 és 90 között kell lennie |
||||

*Választható*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*minimális intervallum*> | Sztring | Az exponenciális időközi házirend esetében a véletlenszerűen kiválasztott időköz ( [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ) legkisebb intervalluma |
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

### <a name="none"></a>Nincs

Annak megadásához, hogy a művelet vagy az aktiválás Ne próbálkozzon újra a sikertelen kérelmekkel, állítsa a <*újrapróbálkozási házirend típusú*>t `none`re.

### <a name="fixed-interval"></a>Rögzített intervallum

Annak megadásához, hogy a művelet vagy az indítás a következő kérelem elküldése előtt megvárja-e a megadott intervallumot, állítsa a <*újrapróbálkozási házirend típusú*> a `fixed`re.

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

Annak megadásához, hogy a művelet vagy az indítás a következő kérelem elküldése előtt megvárja-e a véletlenszerű intervallumot, állítsa a <*újrapróbálkozási házirend típusú*> a `exponential`re. A véletlenszerű intervallum egy exponenciálisan növekvő tartományból van kiválasztva. Igény szerint felülbírálhatja az alapértelmezett minimális és maximális intervallumot is a saját minimális és maximális intervallumának megadásával.

**Véletlenszerű változók tartományai**

Ez a táblázat azt mutatja be, hogy a Logic Apps hogyan generál egységes véletlenszerű változót a megadott tartományban az újrapróbálkozások számára, beleértve az újrapróbálkozások számát is:

| Újrapróbálkozás száma | Minimális időköz | Maximális időköz |
|--------------|------------------|------------------|
| 1 | Max (0, <*minimális intervallum*>) | min (intervallum, <*maximális intervallum*>) |
| 2 | Max (intervallum, <*minimális intervallum*>) | min (2 * időköz, <*maximális intervallum*>) |
| 3 | Max (2 * intervallum, <*minimális intervallum*>) | min (4 * intervallum, <*maximális intervallum*>) |
| 4 | Max (4 * intervallum, <*minimális intervallum*>) | min (8 * időköz, <*maximális intervallum*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>A hibák elfogása és kezelése a "Futtatás utáni" viselkedés módosításával

Ha a Logic app Designerben műveleteket ad hozzá, a műveletek futtatásához implicit módon deklarálja a használati sorrendet. Egy művelet futásának befejeződése után a művelet olyan állapottal van megjelölve, mint például a `Succeeded`, a `Failed`, a `Skipped`vagy a `TimedOut`. Az egyes műveletek definíciójában a `runAfter` tulajdonság határozza meg azt a megelőző műveletet, amelynek első befejezését és az előző művelet futtatásához engedélyezett állapotokat kell megadnia. Alapértelmezés szerint a tervezőben megadott művelet csak akkor fut le, ha a megelőző folyamat `Succeeded` állapottal fejeződött be.

Ha egy művelet kezeletlen hibát vagy kivételt jelez, a művelet `Failed`jelöléssel van megjelölve, és minden követő művelet `Skipped`van megjelölve. Ha ez egy párhuzamos ágakat tartalmazó művelet esetében fordul elő, akkor a Logic Apps motor a többi elágazást követi a befejezési állapotuk meghatározásához. Ha például egy ág egy `Skipped` művelettel végződik, az adott ág befejezési állapota a kihagyott művelet megelőző állapotára alapul. A logikai alkalmazás futtatásának befejeződése után a motor meghatározza a teljes Futtatás állapotát az összes ág állapotának kiértékelésével. Ha bármelyik ág meghibásodik, a teljes logikai alkalmazás futtatása `Failed`van megjelölve.

![A futtatási állapotok kiértékelésének módját bemutató példák](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Annak ellenőrzéséhez, hogy egy művelet továbbra is futtatható-e az előd állapota ellenére, [testre szabhatja a művelet "Futtatás utáni" viselkedését](#customize-run-after) , hogy az előd sikertelen állapotait kezelje.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>A "Futtatás utáni" viselkedés testreszabása

Testre szabhatja a művelet "Futtatás utáni" viselkedését, hogy a művelet akkor fusson, amikor az előd állapota vagy `Succeeded`, `Failed`, `Skipped`, `TimedOut`vagy bármelyik állapot. Ha például egy e-mailt szeretne küldeni az Excel online `Add_a_row_into_a_table` megelőző művelet `Failed`, nem pedig `Succeeded`, akkor a következő lépések egyikével módosítsa a "Futtatás után" viselkedést:

* A Tervezés nézetben válassza a három pontot ( **..** .), majd válassza a **Futtatás a konfigurálás után**lehetőséget.

  ![A "Futtatás utáni" viselkedés konfigurálása művelethez](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  A művelet alakzat a megelőző művelethez szükséges alapértelmezett állapotot jeleníti meg, amely **egy sort ad hozzá** a példában szereplő táblázathoz:

  ![Alapértelmezett "Futtatás utáni" viselkedés egy művelethez](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Módosítsa a "Futtatás után" viselkedést a kívánt állapotra, ami a következő példában **nem sikerült** :

  ![A "Futtatás utáni" viselkedés módosítása a következőre: "sikertelen"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Annak megadásához, hogy a művelet fusson-e, hogy a megelőző művelet `Failed`, `Skipped` vagy `TimedOut`ként legyen megjelölve, válassza a többi állapotot:

  ![A "Futtatás utáni" viselkedés módosítása más állapotra](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* A kód nézetben a művelet JSON-definíciójában szerkessze a `runAfter` tulajdonságot, amely a következő szintaxist követi:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  Ehhez a példához módosítsa a `runAfter` tulajdonságot `Succeeded`ról `Failed`ra:

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Annak megadásához, hogy a művelet fusson-e, hogy a megelőző művelet `Failed`ként van-e megjelölve, `Skipped` vagy `TimedOut`, adja hozzá a többi állapotot:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Hatókörökkel és azok eredményeivel kapcsolatos műveletek kiértékelése

A `runAfter` tulajdonsággal végzett egyéni műveletek után a műveletekhez hasonlóan a műveletek egy [hatókörön](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)belül is csoportosíthatók. Hatóköröket akkor használhat, ha logikailag csoportosítja a műveleteket, felméri a hatókör összesített állapotát, és végrehajtja a műveleteket az adott állapot alapján. A hatókör összes műveletének futása után a hatókör maga kapja meg a saját állapotát.

A hatókör állapotának ellenőrzését a logikai alkalmazás futtatási állapotának (például `Succeeded`, `Failed`stb.) ellenőrzési feltételeivel is használhatja.

Alapértelmezés szerint ha a hatókör összes művelete sikeres, a hatókör állapota `Succeeded`jelölésű. Ha a hatókörben lévő végső művelet `Failed` vagy `Aborted`, akkor a hatókör állapota `Failed`van megjelölve.

Ha a kivételeket a `Failed` hatókörében szeretné megfogni, és az ezeket a hibákat kezelő műveleteket is futtat, akkor az adott `Failed` hatókörhöz tartozó `runAfter` tulajdonságot használhatja. Így ha a hatókör *bármelyik* művelete meghiúsul, és a `runAfter` tulajdonságot használja az adott hatókörhöz, létrehozhat egyetlen műveletet a hibák felderítéséhez.

A hatókörök korlátozásait lásd: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>A hibák kontextusának és eredményének beolvasása

Bár a hibák egy hatókörből való kifogása hasznos, előfordulhat, hogy olyan kontextust is szeretne, amely segít megérteni, hogy pontosan milyen műveletek sikertelenek, valamint a visszaadott hibák vagy állapotkódok.

A [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) függvény a hatókör összes műveletének eredményeire vonatkozó kontextust biztosít. A `result()` függvény egyetlen paramétert fogad el, amely a hatókör neve, és egy olyan tömböt ad vissza, amely az adott hatókörből származó összes művelet eredményét tartalmazza. Ezek a műveleti objektumok a `actions()` objektummal megegyező attribútumokat tartalmaznak, például a művelet kezdési idejét, a befejezési időt, az állapotot, a bemeneteket, a korrelációs azonosítókat és a kimeneteket. Ha a hatókörön belül meghiúsult műveletek kontextusát szeretné elküldeni, egyszerűen párosíthat egy `@result()` kifejezést a `runAfter` tulajdonsággal.

Ha műveletet szeretne futtatni egy olyan hatókör minden műveletéhez, amelynek `Failed` eredménye van, és az eredmények tömbjét a sikertelen műveletek alapján szeretné szűrni, egy `@result()` kifejezést egy [**szűrő tömb**](logic-apps-perform-data-operations.md#filter-array-action) művelettel és egy [**minden**](../logic-apps/logic-apps-control-flow-loops.md) hurokhoz párosíthat. Elvégezheti a szűrt eredmény tömböt, és végrehajthat egy műveletet az egyes hibákhoz az `For_each` hurok használatával.

Az alábbi példát követve részletes magyarázatot talál, amely egy HTTP POST-kérést küld a válasz törzsének a "My_Scope" hatókörön belül sikertelen műveletek esetén:

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

1. A "My_Scope" összes műveletének eredményének lekéréséhez a **tömb szűrése** művelet ezt a szűrő kifejezést használja: `@result('My_Scope')`

1. A **szűrő tömb** feltétele bármely olyan `@result()` elem, amely `Failed`értékkel egyenlő állapotú. Ez az állapot szűri azt a tömböt, amely az összes művelet eredményét "My_Scope" értékről egy olyan tömbre szűri, amely csak a sikertelen művelet eredményét eredményezi.

1. Hajtson végre egy `For_each` hurok műveletet a *szűrt tömb* kimenetén. Ez a lépés végrehajt egy műveletet a korábban szűrt összes sikertelen művelet eredményéhez.

   Ha a hatókörben egyetlen művelet meghiúsul, akkor a `For_each` hurok műveletei csak egyszer futnak. Több sikertelen művelet hibát okoz egy művelet végrehajtásakor.

1. Küldjön egy HTTP-BEJEGYZÉST a `For_each` elem válasz törzsén, amely a `@item()['outputs']['body']` kifejezés.

   A `@result()` elem alakzata megegyezik a `@actions()` alakzattal, és ugyanúgy elemezhető.

1. Adjon meg két egyéni fejlécet a meghiúsult művelet nevével (`@item()['name']`) és a sikertelen Futtatás ügyfél-követési AZONOSÍTÓval (`@item()['clientTrackingId']`).

Hivatkozásként Íme egy `@result()` elem, amely az előző példában elemzett `name`, `body`és `clientTrackingId` tulajdonságokat jeleníti meg. Egy `For_each` műveleten kívül `@result()` az objektumok tömbjét adja vissza.

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

A különböző kivételek kezelésére szolgáló mintázatok végrehajtásához használhatja a cikkben korábban ismertetett kifejezéseket. Dönthet úgy is, hogy egyetlen kivétel-kezelési műveletet hajt végre a hatókörön kívül, amely elfogadja a hibák teljes szűrt tömbjét, és eltávolítja a `For_each` műveletet. Más hasznos tulajdonságokat is megadhat a korábban leírt `\@result()` válaszból.

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor naplók beállítása

Az előző minták nagyszerű módon kezelik a hibákat és a kivételeket egy futtatáson belül, de a futtatástól függetlenül is azonosíthatók és reagálnak a hibákra. A [Azure monitor](../azure-monitor/overview.md) egyszerű módot biztosít az összes munkafolyamat-esemény, beleértve a futtatási és a műveleti állapotok elküldését egy [log Analytics-munkaterületre](../azure-monitor/platform/data-platform-logs.md), [Azure Storage-fiókra](../storage/blobs/storage-blobs-overview.md)vagy [Azure-Event Hubsre](../event-hubs/event-hubs-about.md).

A futtatási állapotok kiértékeléséhez nyomon követheti a naplókat és a metrikákat, vagy közzéteheti azokat bármely figyelési eszközön. Az egyik lehetséges lehetőség az, hogy az összes eseményt Event Hubs-ba [Azure stream Analyticsba](https://azure.microsoft.com/services/stream-analytics/)továbbítsa. Stream Analytics élő lekérdezéseket írhat a diagnosztikai naplókból származó rendellenességek, átlagok vagy hibák alapján. A Stream Analytics használatával adatokat küldhet más adatforrásoknak, például várólistákat, témaköröket, SQL-, Azure Cosmos DB-vagy Power BI.

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogy az ügyfél hogyan épít a hibakezelés során Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [További Logic Apps példák és forgatókönyvek](../logic-apps/logic-apps-examples-and-scenarios.md)
