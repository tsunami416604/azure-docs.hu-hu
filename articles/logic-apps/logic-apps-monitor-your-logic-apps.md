---
title: Állapot ellenőrzése, naplózás beállítása és lekérése a riasztások – Azure Logic Apps |} A Microsoft Docs
description: Állapot figyelése, diagnosztikai adatok és riasztások beállítása az Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: 0d2b7a5e3239cf46c41db0a086b804c34df5d6e2
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386944"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Állapot figyelése, állítsa be a diagnosztikai naplózás, és kapcsolja be a riasztásokat az Azure Logic Apps

Miután [létrehozni és futtatni egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md), ellenőrizheti a futtatási előzmények, eseményindítási előzményeinek, állapotát és teljesítményét. Valós idejű esemény figyelése és részletesebb hibakeresés, állítsa be a [diagnosztikai célú naplózásának](#azure-diagnostics) a logikai alkalmazás. Ezzel a módszerrel is [keresse meg és események megtekintéséhez](#find-events), mint például a kiváltó események, a futtatási események és a műveleti események. Emellett ezzel [diagnosztikai adatokat más szolgáltatásokkal](#extend-diagnostic-data), mint például az Azure Storage és az Azure Event Hubs. 

Kapcsolatos hibák vagy egyéb lehetséges problémákat értesítéseket kapni, állítsa be [riasztások](#add-azure-alerts). Például létrehozhat egy riasztást, amely észleli a "Ha ötnél több futtatás sikertelen egy órán belül." Is állíthat be a figyelés, nyomon követése és naplózása programozással használatával [Azure Diagnostics esemény beállításait és tulajdonságait](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Nézet futtatási és eseményindítási előzményeinek a logikai alkalmazás

1. A logikai alkalmazás lévő keresése a [az Azure portal](https://portal.azure.com), az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be a "logic apps", és válassza a **Logic apps**.

   ![Keresse meg a logikai alkalmazás](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Az Azure portal megjeleníti az összes Azure-előfizetéséhez társított logikai alkalmazások. 

2. Válassza ki a logikai alkalmazást, majd válassza a **áttekintése**.

   Az Azure Portalon látható, a futtatási előzmények és eseményindítási előzményeinek a logikai alkalmazás. Példa:

   ![Logikai alkalmazás futtatási előzmények és az eseményindító előzmények](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Futtatási előzmények** jeleníti meg a logikai alkalmazás minden futtatás. 
   * **Eseményindítási előzményeinek** jeleníti meg a logikai alkalmazás az eseményindító-tevékenység.

   Állapot leírásáért lásd: [a logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Ha nem találja az adatokat, amelyek várhatóan, az eszköztáron válassza **frissítése**.

3. Az egy adott Futtatás lépéssorral nézetben a **futtatási előzmények**, válassza ki, amelyek futtatni. 

   A figyelő nézet, amely az egyes lépések mutatja. Példa:

   ![Egy adott futtatás esetén végrehajtandó műveletek](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. A Futtatás kapcsolatos további információért válassza **Futtatás részletei**. Ez az információ a lépéseket, állapot, bemenetek és kimenetek a Futtatás foglalja össze. 

   ![Válassza a "Futtatás részletei"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Például megtekintheti a Futtatás **korrelációs azonosító**, amelyre szüksége lehet a használatakor a [REST API a Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Egy adott lépéssel kapcsolatos információért válassza ezt a lépést. Most már más részleteknek, például bemenetek, kimenetek és esetleges hibákat, és ismételje meg ezt a lépést a tekintheti meg. Példa:

   ![Lépés részletei](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Minden modul részleteit és eseményeket a Logic Apps szolgáltatáson belül titkosítottak. Akkor lesznek visszafejtve, csak akkor, amikor egy felhasználó kéri, hogy az adatok megtekintéséhez. Azt is szabályozhatja a hozzáférést az ezeket az eseményeket az [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md).

6. Egy adott eseményindító esemény adatainak lekéréséhez lépjen vissza a **áttekintése** ablaktáblán. A **eseményindítási előzményeinek**, válassza ki a kiváltó esemény. Most már megtekintheti más részleteknek, például bemenetek és kimenetek, például:

   ![A trigger esemény kimenet részletei](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Diagnosztika a logikai alkalmazás naplózás bekapcsolása

Gazdagabb hibakereséshez a modul részleteit és eseményeket, beállíthatja a diagnosztikai naplózás [Azure Log Analytics](../log-analytics/log-analytics-overview.md). A log Analytics szolgáltatása az Azure-ban, amely figyeli a felhőbeli és helyszíni környezeteket a rendelkezésre állás és teljesítmény karbantartásához. 

A Kezdés előtt szüksége van egy Log Analytics-munkaterületet. Ismerje meg, [Log Analytics-munkaterület létrehozása](../azure-monitor/learn/quick-create-workspace.md).

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és válassza ki a logikai alkalmazást. 

2. A logikai alkalmazás panelen menü alatt **figyelés**, válassza a **diagnosztikai** > **diagnosztikai beállítások**.

   ![Ugrás a figyelés, diagnosztika, diagnosztikai beállítások](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. A **diagnosztikai beállítások**, válassza a **a**.

   ![Kapcsolja be a diagnosztikai naplók](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Most válassza ki a Log Analytics munkaterület és az esemény naplózásának kategóriája látható módon:

   1. Válassza ki **Küldés a Log Analyticsnek**. 
   2. A **Log Analytics**, válassza a **konfigurálása**. 
   3. A **OMS-munkaterületek**, válassza ki a használandó naplózási munkaterületet.
   > [!NOTE]
   > Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.
   4. A **Log**, jelölje be a **WorkflowRuntime** kategória.
   5. Válassza ki a metrika időköz.
   6. Ha elkészült, kattintson a **Mentés** gombra.

   ![Válassza ki a Log Analytics-munkaterületet és naplózási adatok](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Most találja események és egyéb adatok kiváltó események, események és műveleti események.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Események és az adatok keresése a logikai alkalmazás

Keresse meg és események megtekintése a logikai alkalmazásban, például az események, események, futtatása és a műveleti események, kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com), válassza a **minden szolgáltatás**. Keresse meg a "log analytics", majd válassza a **Log Analytics** itt látható módon:

   ![Válassza a "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. A **Log Analytics**, keresse meg és válassza ki a Log Analytics-munkaterületre. 

   ![Válassza ki a Log Analytics-munkaterület](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. A **felügyeleti**, válassza a **naplóbeli keresés**.

   ![Válassza a "Log Search"](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. A keresőmezőbe, adjon meg egy mezőt, keresse meg, és nyomja le az kívánt **Enter**. Amikor elkezdi beírni, láthatja lehetséges egyezések és műveleteket, amelyeket használhat. 

   Például az első 10 történt események kereséséhez adja meg, és válassza ki a keresési lekérdezés: **Keresés kategória == "WorkflowRuntime" |} 10 korlátozása**

   ![Adja meg a keresési karakterlánc](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Tudjon meg többet [adatok megkeresése a Log Analytics](../log-analytics/log-analytics-log-searches.md).

5. Az eredmények oldalon a bal oldali sávon válassza ki a megtekinteni kívánt időkeretet.
Pontosítsa a lekérdezést egy szűrő hozzáadásával, válassza ki a **+ Hozzáadás**.

   ![Válassza ki a lekérdezés eredményeinek időkeretre](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. A **szűrők hozzáadása**, adja meg a szűrő nevét, így megtalálhatja a kívánt szűrőt. Válassza ki a szűrőt, és válassza a **+ Hozzáadás**.

   Ebben a példában a "status" szót a sikertelen események megtalálásához **AzureDiagnostics**.
   Itt a szűrő a **status_s** már be van jelölve.

   ![Szűrő kiválasztása](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. A bal oldali sávon válassza ki a szűrő értéket használja, és válassza a kívánt **alkalmaz**.

   ![Válassza ki a szűrő értékét, válassza a "Alkalmaz"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Most már térjen vissza a lekérdezést, amely fejleszt. A lekérdezés frissül a kijelölt szűrő és értékét. Az előző eredmények most túl vannak szűrve.

   ![Térjen vissza a lekérdezést a szűrt eredmények](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. A későbbi használat céljából lekérdezés mentéséhez válassza **mentése**. Ismerje meg, [a lekérdezés mentése](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Hogyan és hol diagnosztikai adatok felhasználhatók más szolgáltatásokkal

Az Azure Log Analytics, valamint bővítheti, hogyan használhatja a logikai alkalmazás diagnosztikai adatok Azure-szolgáltatásokat, például: 

* [Azure Diagnostics-naplók az Azure Storage-archívum](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Stream Azure-beli diagnosztikai naplók az Azure Event hubs szolgáltatásba](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Ezután get valós idejű figyelést végezni a telemetriai adatokat és elemzéseket tesz további szolgáltatásokkal, például [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) és [Power BI](../azure-monitor/platform/powerbi.md). Példa:

* [Stream-adatok az Event Hubsból a Stream Analytics szolgáltatáshoz](../stream-analytics/stream-analytics-define-inputs.md)
* [A Stream Analytics streamelési adatok elemzéséhez, és a egy valós idejű elemzési irányítópult létrehozása a Power bi-ban](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Alapján állítsa be a kívánt beállításokat, győződjön meg arról, hogy Ön első [hozzon létre egy Azure storage-fiók](../storage/common/storage-create-storage-account.md) vagy [az Azure event hub létrehozása](../event-hubs/event-hubs-create.md). Ezután válassza ki a beállításokat, amennyiben meg szeretné-e diagnosztikai adatok küldése:

![Adatok küldése az Azure storage-fiók vagy eseményközpont](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Hosszabb megőrzési időszakok a alkalmazni: csak akkor, ha úgy dönt, hogy a storage-fiókot.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>A logikai alkalmazás riasztások beállítása

Adott mérőszámok vagy túllépte a logikai alkalmazás küszöbértékek monitorozásához beállítása [riasztások az Azure-ban](../azure-monitor/platform/alerts-overview.md). Ismerje meg [mérőszámok az Azure-ban](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Riasztások nélkül beállítása [Azure Log Analytics](../log-analytics/log-analytics-overview.md), kövesse az alábbi lépéseket. Speciális riasztások feltételek és műveletek [Log Analytics beállítása](#azure-diagnostics) túl.

1. A logikai alkalmazás panelen menü alatt **figyelés**, válassza a **diagnosztikai** > **riasztási szabályok** > **riasztáshozzáadása**itt látható módon:

   ![A logikai alkalmazás riasztás hozzáadása](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Az a **riasztási szabály hozzáadása** panelen hozza létre a riasztás látható módon:

   1. A **erőforrás**, válassza ki a logikai alkalmazást, ha nincs bejelölve. 
   2. Adjon egy nevet és a riasztás leírását.
   3. Válassza ki a **metrika** vagy eseményt kívánja nyomon követni.
   4. Válassza ki egy **feltétel**, adjon meg egy **küszöbérték** a metrika, és válassza a a **időszak** esetében ez a metrika figyelése.
   5. Válassza ki, hogy a riasztás e-mailek küldésére. 
   6. Adja meg a riasztást küld minden olyan egyéb e-mail címek. 
   Megadhat egy webhook URL-címet, ahol szeretné a riasztás küldése is.

   Például ez a szabály küld riasztást, ha öt vagy több futtatás sikertelen egy órán belül:

   ![A metrikaalapú riasztási szabály létrehozása](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Logikai alkalmazás futtatása riasztásból, hozzáadhatja a [kérelem típusú trigger](../connectors/connectors-native-reqres.md) a munkafolyamatban, amely feladatok elvégzését teszi a példákat:
> 
> * [A közzététel Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Egy szöveges üzenet elküldéséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Adjon meg egy üzenetet egy üzenetsorba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Az Azure Diagnostics esemény beállításai és részletei

Egyes diagnosztikai események adatait a logikai alkalmazás és a kapcsolódó eseményre, például az állapot, a start idő, befejezési időpontja, és így tovább. Programozott módon állítsa be a figyelés, a nyomon követés és a naplózás, használhatja az alábbi adatokat a [REST API-t az Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) és a [REST API-t az Azure Diagnostics](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

Például a `ActionCompleted` esemény a `clientTrackingId` és `trackedProperties` tulajdonságok, amelyek nyomon követése és figyelése is használhatja:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: Ha nincs megadva, az Azure automatikusan hoz létre ezt az Azonosítót eszközazonosítóként, és események között a logikai alkalmazás futtatását, beleértve a beágyazott munkafolyamatokat, amely lehet meghívni a a logikai alkalmazás. Ez az azonosító egy eseményindítóból átadásával manuálisan megadhatja egy `x-ms-client-tracking-id` a saját egyéni azonosító értékét a trigger kérelem fejléce. Egy kérelem típusú trigger, HTTP-eseményindító vagy egy webhook-eseményindító is használhatja.

* `trackedProperties`: Bemeneti vagy kimeneti a diagnosztikai adatok nyomon követéséhez a nyomon követett tulajdonságokat adhat hozzá műveleteket hajthat végre a logikai alkalmazás JSON-definíciójában. A nyomon követett tulajdonságok menüpontban követheti nyomon, csak egyetlen művelet bemeneteit és kimeneteit, de használhatja a `correlation` eseményeinek korrelációját, ha különböző műveletek futtatása tulajdonságait.

  Egy vagy több tulajdonságának nyomon követéséhez adja hozzá a `trackedProperties` szakaszban, és a művelet definíciójának kívánt tulajdonságokat. Tegyük fel, hogy adatokat – például egy "order ID" a telemetria a követni kívánt:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>További lépések

* [Logikai alkalmazás üzemelő példányának sablonok létrehozásához és a kiadáskezelés](../logic-apps/logic-apps-create-deploy-template.md)
* [Az Enterprise Integration Pack csomag B2B-forgatókönyvekhez](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [B2B üzenetek megfigyelése](../logic-apps/logic-apps-monitor-b2b-message.md)
