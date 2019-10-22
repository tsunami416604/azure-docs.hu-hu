---
title: Állapot ellenõrzése, naplózás beállítása és riasztások beolvasása – Azure Logic Apps
description: Az állapot figyelése, a naplózási diagnosztika adatait és a riasztások beállítása Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 07/21/2017
ms.openlocfilehash: 336e2ac93a954c35b7afc8dbb98dd1fca1838985
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680301"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Figyelő állapota, diagnosztikai naplózás beállítása és riasztások bekapcsolása Azure Logic Apps

[Egy logikai alkalmazás létrehozása és futtatása](../logic-apps/quickstart-create-first-logic-app-workflow.md) után megtekintheti annak futtatási előzményeit, triggerelőzményeit, állapotát és teljesítményét. A valós idejű események figyelése és a szélesebb körű hibakeresés érdekében állítson be [diagnosztikai naplózást](#azure-diagnostics) a logikai alkalmazáshoz. Így [megkeresheti és megtekintheti az eseményeket](#find-events), például a triggereseményeket, a futtatási eseményeket és a műveleteseményeket. Ezeket a [diagnosztikai adatokat más szolgáltatásokkal](#extend-diagnostic-data) is használhatja, például az Azure Storage és az Azure Event Hubs szolgáltatásokkal. 

Ha értesítéseket szeretne kapni a hibákról vagy egyéb lehetséges problémákról, állítson be [riasztásokat](#add-azure-alerts). Létrehozhat például egy olyan riasztást, amely észleli a következőt: "Ha ötnél több futtatása meghiúsul egy órán belül." A figyelést, a nyomon követést és a naplózást programozott módon is beállíthatja [Azure Diagnostics esemény-beállítások és tulajdonságok](#diagnostic-event-properties)használatával.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>A logikai alkalmazás futtatási és kiváltó előzményeinek megtekintése

1. Ha szeretné megkeresni a logikai alkalmazást a [Azure Portalban](https://portal.azure.com), az Azure fő menüjében válassza a **minden szolgáltatás**lehetőséget. A keresőmezőbe írja be a "Logic apps" kifejezést, majd válassza a **Logic apps**lehetőséget.

   ![Logikai alkalmazás megkeresése](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   A Azure Portal megjeleníti az Azure-előfizetéshez társított összes logikai alkalmazást. 

2. Válassza ki a logikai alkalmazást, majd válassza az **Áttekintés**lehetőséget.

   A Azure Portal megjeleníti a logikai alkalmazás futtatási előzményeit és a kiváltó előzményeket. Példa:

   ![A logikai alkalmazás előzményeket és trigger-előzményeket futtat](media/logic-apps-monitor-your-logic-apps/overview.png)

   * A **Run History (futtatási előzmények** ) megjeleníti a logikai alkalmazás összes futtatását. 
   * Az **aktiválási előzmények** a logikai alkalmazás összes kiváltó tevékenységét megjelenítik.

   Az állapot leírása: [a logikai alkalmazás hibáinak megoldása](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Ha nem találja a várt adatértékeket, akkor az eszköztáron válassza a **frissítés**lehetőséget.

3. Ha egy adott Futtatás lépéseit szeretné megtekinteni, a futtatási **Előzmények**szakaszban válassza ki a Futtatás elemet. 

   A figyelő nézet a Futtatás minden lépését megjeleníti. Példa:

   ![Adott Futtatás műveletei](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. A Futtatás részleteinek megtekintéséhez válassza a **Futtatás részletek**lehetőséget. Ez az információ összefoglalja a Futtatás lépéseit, állapotát, bemeneteit és kimeneteit. 

   ![Válassza a "Futtatás részletei" lehetőséget.](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Lekérheti például a Futtatás **korrelációs azonosítóját**, amelyre szükség lehet, ha a [Logic apps REST API](https://docs.microsoft.com/rest/api/logic)használja.

5. Egy adott lépés részleteinek megszerzéséhez válassza ezt a lépést. Most már áttekintheti a bemeneteket, a kimeneteket és az adott lépéshez kapcsolódó hibákat. Példa:

   ![Lépés részletei](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Az összes futásidejű adat és esemény titkosítva van a Logic Apps szolgáltatáson belül. Csak akkor lesznek visszafejtve, ha egy felhasználó az adott adatmegjelenítést kéri. Az eseményekhez való hozzáférést az [Azure szerepköralapú Access Control (RBAC)](../role-based-access-control/overview.md)használatával is szabályozhatja.

6. Egy adott eseményindító esemény részleteinek megtekintéséhez térjen vissza az **Áttekintés** panelre. Az **eseményindító-előzmények**területen válassza ki az eseményindító eseményt. Most már áttekintheti a bemeneteket és kimeneteket, például a következő adatokat:

   ![Eseményindító-esemény kimenetének részletei](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Diagnosztikai naplózás bekapcsolása a logikai alkalmazáshoz

A futásidejű adatokkal és eseményekkel való gazdagabb hibakereséshez [Azure monitor naplók](../log-analytics/log-analytics-overview.md)segítségével állíthatja be a diagnosztikai naplózást. A Azure Monitor egy Azure-beli szolgáltatás, amely figyeli a Felhőbeli és a helyszíni környezeteket, hogy segítsen megőrizni a rendelkezésre állást és a teljesítményt. 

A Kezdés előtt rendelkeznie kell egy Log Analytics munkaterülettel. Megtudhatja [, hogyan hozhat létre log Analytics munkaterületet](../azure-monitor/learn/quick-create-workspace.md).

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a logikai alkalmazást. 

2. A logikai alkalmazás panel menüjének **figyelés**területén válassza a **diagnosztika** > **diagnosztikai beállítások**elemet.

   ![Ugrás a figyelés, diagnosztika, diagnosztikai beállítások menüpontra](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. A **diagnosztika beállításai**területen válassza **a be**lehetőséget.

   ![diagnosztikai naplók bekapcsolása](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Most válassza ki a Log Analytics munkaterületet és az esemény kategóriáját a naplózáshoz, ahogy az látható:

   1. Válassza **a küldés log Analytics**lehetőséget. 
   2. A **log Analytics**alatt válassza a **Konfigurálás**lehetőséget. 
   3. A **OMS-munkaterületek**területen válassza ki a naplózáshoz használni kívánt munkaterületet.
      > [!NOTE]
      > Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.
   4. A **napló**területen válassza ki a **WorkflowRuntime** kategóriát.
   5. Válassza ki a mérőszám intervallumát.
   6. Ha elkészült, kattintson a **Mentés** gombra.

   ![Válassza ki Log Analytics munkaterületet és az adataikat a naplózáshoz](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Most megtalálhatja az eseményeket és egyéb adatműveleteket az aktiválási eseményekhez, az események futtatásához és a műveleti eseményekhez.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>A logikai alkalmazás eseményeinek és adatainak megkeresése

Az alábbi lépéseket követve megkeresheti és megtekintheti a logikai alkalmazás eseményeit, például az események aktiválását, az események futtatását és a műveleti eseményeket.

1. A [Azure Portal](https://portal.azure.com)válassza a **minden szolgáltatás**lehetőséget. Keressen rá a "log Analytics" kifejezésre, majd válassza a **log Analytics** az itt látható módon:

   ![Válassza a "Log Analytics" lehetőséget](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. A **log Analytics**alatt keresse meg és válassza ki log Analytics munkaterületét. 

   ![Log Analytics munkaterület kiválasztása](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. A **kezelés**területen válassza a **naplóbeli keresés**lehetőséget.

   ![A "keresés naplózása" lehetőség kiválasztása](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. A keresőmezőbe írjon be egy keresni kívánt mezőt, majd nyomja le az **ENTER**billentyűt. A gépelés megkezdése után láthatja a lehetséges egyezéseket és a használható műveleteket. 

   Ha például az első 10 eseményt szeretné megkeresni, írja be és válassza ki ezt a keresési lekérdezést: **keresési kategória = = "WorkflowRuntime" | 10. korlát**

   ![Adja meg a keresési karakterláncot](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   További információ az [Azure monitor-naplókban található adatkeresésről](../log-analytics/log-analytics-log-searches.md).

5. Az eredmények lap bal oldali sávján válassza ki a megtekinteni kívánt időkeretet.
Ha egy szűrő hozzáadásával szeretné pontosítani a lekérdezést, válassza a **+ Hozzáadás**lehetőséget.

   ![Lekérdezési eredmények időkeretének kiválasztása](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. A **szűrők hozzáadása**lehetőségnél adja meg a szűrő nevét, hogy megtalálja a kívánt szűrőt. Válassza ki a szűrőt, és válassza a **+ Hozzáadás**lehetőséget.

   Ez a példa a "status" szót használja a **AzureDiagnostics**alatti sikertelen események kereséséhez.
   Itt már ki van választva a **status_s** szűrője.

   ![Szűrő kiválasztása](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. A bal oldali sávban válassza ki a használni kívánt szűrő értékét, és kattintson az **alkalmaz**gombra.

   ![Válassza ki a szűrő értékét, és válassza az alkalmaz lehetőséget.](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Most térjen vissza a felépített lekérdezéshez. A lekérdezés a kiválasztott szűrővel és értékkel frissül. Az előző eredmények szűrése már megtörtént.

   ![Visszatérés a lekérdezésbe szűrt eredményekkel](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Ha későbbi használatra szeretné menteni a lekérdezést, válassza a **Mentés**lehetőséget. Megtudhatja [, hogyan mentheti a lekérdezést](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>A diagnosztikai és más szolgáltatásokkal való használatának kiterjesztése

A Azure Monitor naplókkal együtt kiterjesztheti a logikai alkalmazás diagnosztikai adatait más Azure-szolgáltatásokkal, például a következő módon: 

* [Azure Diagnostics naplók archiválása az Azure Storage-ban](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Stream Azure Diagnostics naplók az Azure-ba Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Ezt követően valós idejű monitorozást érhet el a telemetria és az Analytics használatával más szolgáltatásokból, például a [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) és az [Power bi](../azure-monitor/platform/powerbi.md). Példa:

* [Stream-adatok Event Hubsról Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Adatfolyamok elemzése Stream Analytics és valós idejű elemzési irányítópult létrehozása Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

A beállítani kívánt beállítások alapján győződjön meg arról, hogy először [létrehoz egy Azure Storage-fiókot](../storage/common/storage-create-storage-account.md) , vagy [létrehoz egy Azure Event hub](../event-hubs/event-hubs-create.md)-t. Ezután válassza ki azokat a beállításokat, amelyekhez diagnosztikai adatküldést szeretne küldeni:

![Adatküldés az Azure Storage-fiókba vagy az Event hub-ba](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> A megőrzési időszakok csak akkor érvényesek, ha a Storage-fiókot választja.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Riasztások beállítása a logikai alkalmazáshoz

A logikai alkalmazáshoz tartozó konkrét metrikák vagy túllépte küszöbértékek figyeléséhez állítsa be a [riasztásokat az Azure-ban](../azure-monitor/platform/alerts-overview.md). Ismerje meg [Az Azure mérőszámait](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

[Azure monitor naplók](../log-analytics/log-analytics-overview.md)nélküli riasztások beállításához kövesse az alábbi lépéseket. Ha további speciális riasztási feltételeket és műveleteket kíván [beállítani, állítsa be Azure monitor naplókat](#azure-diagnostics) is.

1. A logikai alkalmazás panel menüjének **figyelés**területén válassza a **diagnosztika**  > **riasztási szabályok**  >  riasztás**hozzáadása** lehetőséget az itt látható módon:

   ![Riasztás hozzáadása a logikai alkalmazáshoz](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. A **riasztási szabály hozzáadása** panelen hozza létre a riasztást az alábbiak szerint:

   1. Az **erőforrás**területen válassza ki a logikai alkalmazást, ha még nincs kiválasztva. 
   2. Adja meg a riasztás nevét és leírását.
   3. Válasszon ki egy **mérőszámot** vagy eseményt, amelyet nyomon szeretne követni.
   4. Válasszon ki egy **feltételt**, adjon meg egy **küszöbértéket** a metrika számára, és válassza ki a mérőszám figyelésének **időszakát** .
   5. Válassza ki, hogy szeretne-e e-mailt küldeni a riasztásnak. 
   6. Más e-mail-címek megadása a riasztás elküldéséhez. 
   Megadhat egy webhook URL-címét is, ahová el szeretné küldeni a riasztást.

   Ez a szabály például riasztást küld, ha öt vagy több Futtatás meghiúsul egy órán belül:

   ![Metrika riasztási szabály létrehozása](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> A logikai alkalmazások riasztásból való futtatásához belefoglalhatja a [kérelem-triggert](../connectors/connectors-native-reqres.md) a munkafolyamatba, amellyel a következő példákhoz hasonló feladatokat végezhet el:
> 
> * [Közzététel a Slackben](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Szöveg küldése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Üzenet hozzáadása egy várólistához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Azure Diagnostics esemény beállításai és részletei

Minden diagnosztikai esemény a logikai alkalmazással és az eseménysel kapcsolatos részleteket tartalmaz, például az állapotot, a kezdési időt, a befejezési időpontot és így tovább. A figyelés, a nyomon követés és a naplózás programozott beállításához ezeket REST API az adatokat a [Azure Logic Appshoz](https://docs.microsoft.com/rest/api/logic) és a [Azure Diagnostics Rest APIához](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows)használhatja.

Például a `ActionCompleted` esemény rendelkezik a nyomon követéshez és a figyeléshez használható `clientTrackingId` és `trackedProperties` tulajdonságokkal:

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

* `clientTrackingId`: Ha nincs megadva, az Azure automatikusan létrehozza ezt az azonosítót, és korrelálja az eseményeket egy logikai alkalmazás futása során, beleértve a logikai alkalmazásból meghívott beágyazott munkafolyamatokat is. Ezt az azonosítót manuálisan is megadhatja egy triggerből, ha egy `x-ms-client-tracking-id` fejlécet ad meg a trigger-kérelemben szereplő egyéni azonosító értékkel. Használhat kérelem-triggert, HTTP-triggert vagy webhook-triggert.

* `trackedProperties`: a diagnosztikai adatok bemeneteit és kimeneteit nyomon követheti a logikai alkalmazás JSON-definíciójában lévő műveletekhez. A nyomon követett tulajdonságok csak egyetlen művelet bemeneteit és kimeneteit követhetik nyomon, de az események `correlation` tulajdonságainak használatával korrelálhat a futtatások műveletei között.

  Egy vagy több tulajdonság nyomon követéséhez adja hozzá a `trackedProperties` szakaszt és a művelet definíciójában használni kívánt tulajdonságokat. Tegyük fel például, hogy nyomon szeretné követni az olyan adatait, mint például egy "Order ID" a telemetria:

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
  Íme egy másik példa, amely az **inicializálási változó** műveletet használja. A példa a művelet bemenetén lévő nyomon követett tulajdonságokat adja meg, a bemenet pedig tömb, nem pedig rekord.  

  ``` json
  "actions": { 
   "Initialize_variable": { 
      "inputs": { 
         "variables": [{ 
            "name": "ConnectorName", 
            "type": "String", 
            "value": "SFTP-SSH" 
         }]
      },
      "runAfter": {},
      "trackedProperties": { 
         "Track1": "@action().inputs.variables[0].value"
      },
      "type": "InitializeVariable"
   } 
  }
  ```

## <a name="next-steps"></a>Következő lépések

* [A logikai alkalmazások telepítésének automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [B2B-forgatókönyvek Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [B2B üzenetek megfigyelése](../logic-apps/logic-apps-monitor-b2b-message.md)
