---
title: Állapotának, a naplózás beállítása és értesítéskérés - Azure Logic Apps |} Microsoft Docs
description: A figyelő állapotát és teljesítményét a logic apps diagnosztikai adatok naplózása, és értesítések beállítása
author: jeffhollan
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: fdc986fb32225f94e5a257c46a7b943abc0edf8f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Állapotának figyelésére, diagnosztikai naplózás beállítása és az Azure Logic Apps riasztás bekapcsolása

Miután [létrehozása és futtatása a logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md), ellenőrizheti a futtatása előzmények, eseményindító előzmények, állapotát és teljesítményét. A valós idejű esemény figyelése és részletesebb hibakeresés, állítson be [diagnosztikai naplózás](#azure-diagnostics) a logikai alkalmazásnak. Ily módon is [található, és tekintse meg az eseményeket](#find-events), például az eseményindító események, futtatási események és műveleti események. Emellett ezzel [diagnosztikai adatok más szolgáltatásokkal](#extend-diagnostic-data), például az Azure Storage és az Azure Event Hubs. 

Hibák vagy egyéb lehetséges problémákat kapcsolatos értesítéseket kapni, állítsa be a [riasztások](#add-azure-alerts). Például létrehozhat egy riasztást, amelyek észlelik a ""több mint öt nem egy óra alatt. Is beállíthat figyelését, figyelemmel kíséri, és a naplózást programozott módon segítségével [Azure Diagnostics esemény beállításai és a Tulajdonságok](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Nézet futtatása és a Logic Apps alkalmazást eseményindító előzményei

1. A Logic Apps alkalmazást az kereséséhez a [Azure-portálon](https://portal.azure.com), a fő Azure menüben válassza a **minden szolgáltatás**. A keresési mezőbe, írja be a "logikai alkalmazások", és válassza a **a Logic apps**.

   ![A logikai alkalmazás keresése](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Az Azure-portálon az Azure-előfizetéshez társított összes logikai alkalmazást jeleníti meg. 

2. Válassza ki a Logic Apps alkalmazást, majd válassza a **áttekintése**.

   Az Azure-portálon futtatása előzményeinek és a Logic Apps alkalmazást eseményindító előzményeit jeleníti meg. Példa:

   ![Logikai alkalmazás fut előzmények és eseményindító előzményei](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Futtatja az előzmények** jeleníti meg a logikai alkalmazásnak a futtatják. 
   * **Indítás, előzmények** a Logic Apps alkalmazást az eseményindító-tevékenység műveleteit jeleníti meg.

   A leírások, lásd: [hibaelhárítása a Logic Apps alkalmazást](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Ha nem találja az adatokat várt, az eszköztáron válassza **frissítése**.

3. A lépéseket egy adott futtatható megtekintéséhez az **előzmények fut**, válassza ki a futtató. 

   A figyelő nézet futtató egyes lépéseit mutatja. Példa:

   ![A megadott futtató műveletek](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. A Futtatás kapcsolatos további információért kattintson a **futtatása részletek**. Ezt az információt a lépéseket, állapot, bemeneti és kimeneti a Futtatás foglalja össze. 

   ![Válassza a "Futtatás részletei"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Például kaphat a Futtatás **korrelációs azonosító**, amely szükség lehet a használatakor a [REST API-t a Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Ahhoz, hogy egy adott lépésre kapcsolatos adatokat, válassza ki a lépés. Most adatait, például bemenetek, kimenetek és hibáit, és ismételje meg a lépés a tekintheti meg. Példa:

   ![Lépés részletei](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Az összes futásidejű adatokat és eseményeket a Logic Apps szolgáltatáson belül vannak titkosítva. Akkor lesznek visszafejtve, csak akkor, ha egy felhasználó kéri, hogy az adatok megtekintéséhez. Ezeket az eseményeket az elérésére is szabályozhatja [átruházásához hozzáférés-vezérlés (RBAC)](../active-directory/role-based-access-control-what-is.md).

6. Ahhoz, hogy egy adott indítási esemény részleteit, lépjen vissza a **áttekintése** ablaktáblán. A **előzmények indítás**, jelölje be az indítási esemény. Most már megtekintheti adatait, például bemenetekhez és kimenetekhez, például:

   ![Indítási esemény kimeneti részletei](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Diagnosztika a logikai alkalmazás naplózásának engedélyezése

Gazdagabb feltárására futásidejű adatokat és eseményeket, állíthatja be a diagnosztikai naplózás [Azure Naplóelemzés](../log-analytics/log-analytics-overview.md). A Naplóelemzési egy olyan szolgáltatás, amely figyeli a felhőalapú és helyszíni környezetek karbantartásához azok rendelkezésre állását és teljesítményét az Azure-ban. 

Mielőtt elkezdené, szükség van a Naplóelemzési munkaterület. Ismerje meg, [a Naplóelemzési munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md).

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg és jelölje meg a Logic Apps alkalmazást. 

2. A logic app panel menü alatti **figyelés**, válassza a **diagnosztika** > **diagnosztikai beállítások**.

   ![Ugrás a figyelési, diagnosztikai, diagnosztikai beállítások](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. A **diagnosztikai beállítások**, válassza a **a**.

   ![Kapcsolja be a diagnosztikai naplók](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Naplózási Naplóelemzési munkaterületet, és az esemény kategória most kijelölése látható módon:

   1. Válassza ki **küldeni a Naplóelemzési**. 
   2. A **Naplóelemzési**, válassza a **konfigurálása**. 
   3. A **OMS-munkaterület**, válassza ki a Naplóelemzési munkaterület naplózás használatára.
   4. A **napló**, jelölje be a **WorkflowRuntime** kategóriát.
   5. Válassza ki a metrika időközt.
   6. Ha elkészült, kattintson a **Mentés** gombra.

   ![Válassza ki a Naplóelemzési munkaterület és a naplózási adatokat](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Most található események és egyéb adatok eseményindító események, eseményeket és a műveleti események futtatják.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>A Logic Apps alkalmazást talált események és adatok

Található, és tekintse meg az eseményeket a Logic Apps alkalmazást, például Indítás, futtassa az eseményeket, és műveleti események, kövesse az alábbi lépéseket.

1. Az a [Azure-portálon](https://portal.azure.com), válassza a **minden szolgáltatás**. Keresse meg a "naplóelemzési", és válassza a **Naplóelemzési** itt látható módon:

   ![Válassza ki a "Naplóelemzési"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. A **Naplóelemzési**, található, és válassza ki a Naplóelemzési munkaterület. 

   ![A Naplóelemzési munkaterület kiválasztása](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. A **felügyeleti**, válassza a **OMS-portálon**.

   ![Válassza ki a "OMS-portálon"](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. Válassza ki a kezdőlapon **naplófájl-keresési**.

   ![A kezdőlapon válassza a "Naplófájl-keresési"](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   – vagy –

   ![A menüben válassza a "Naplófájl-keresési"](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. A keresési mezőbe, adjon meg egy mező található, és nyomja le az ENTER kívánt **Enter**. Amikor elkezdi beírni, látni lehetséges találatok és a műveletek közül választhat. 

   Például az első 10 eseményeket, amelyek történtek található, adja meg, és válassza a keresési lekérdezés: **kategóriában keresse == "WorkflowRuntime" |} 10 korlátozása**

   ![Adja meg a keresési karakterláncot](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   További információ [adatok megkeresése a Naplóelemzési](../log-analytics/log-analytics-log-searches.md).

6. Az eredmények lapon bal oldali sávon, válassza ki a megtekinteni kívánt időkeretet.
Pontosítsa a lekérdezést egy szűrő hozzáadásával, válassza a **+ Hozzáadás**.

   ![Válassza ki a lekérdezési eredmények időkeretre](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. A **szűrők hozzáadása**, így megtalálja a kívánt szűrőt, adja meg a szűrő nevét. Válassza ki a szűrőt, és válassza a **+ Hozzáadás**.

   A példában a "status" szó a sikertelen események kereséséhez **AzureDiagnostics**.
   Itt a szűrő a **status_s** már be van jelölve.

   ![Válassza ki a szűrő](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. A bal oldali sávon, válassza ki a szűrő értéket használja, és válassza a kívánt **alkalmaz**.

   ![Adja meg a szűrő értéket, válassza az "Alkalmaz"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. Térjen vissza a lekérdezést, amely éppen összeállításakor. A lekérdezés frissül a kijelölt szűrő és az értéke. Az előző eredmények most túl szűrve.

   ![Térjen vissza a lekérdezés szűrt eredményekkel](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. Jövőbeli használatra a lekérdezés mentéséhez válassza **mentése**. Ismerje meg, [a lekérdezés mentése](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Hogyan és hol diagnosztikai adatok használja más szolgáltatásokkal

Azure Naplóelemzés, valamint bővítheti, hogyan használhatja a Logic Apps alkalmazást diagnosztikai adatok más Azure-szolgáltatásokkal, például: 

* [Az Azure diagnosztikai naplók az Azure Storage archív](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Az adatfolyam Azure diagnosztikai naplók az Azure Event hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Ezután a get valós idejű figyelés telemetriai adatok és más szolgáltatások analytics segítségével például [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) és [Power BI](../log-analytics/log-analytics-powerbi.md). Példa:

* [Az adatfolyam Stream Analytics Eseményközpontokból származó adatokat](../stream-analytics/stream-analytics-define-inputs.md)
* [A Stream Analytics adatfolyam-továbbítási adatok elemzése és a Power bi-ban a valós idejű elemzési irányítópult létrehozása](../stream-analytics/stream-analytics-power-bi-dashboard.md)

A beállítások határozzák meg, amely azt szeretné beállítani, győződjön meg arról, hogy Ön első [az Azure storage-fiók létrehozása](../storage/common/storage-create-storage-account.md) vagy [hozzon létre egy Azure event hubs](../event-hubs/event-hubs-create.md). Válassza a beállítások érhetők el, hol szeretné elküldeni a diagnosztikai adatok:

![Adatok küldése az Azure storage-fiók vagy esemény hub](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Megőrzési időtartamú alkalmazása csak akkor, ha úgy dönt, hogy a tárfiókot használja.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>A logikai alkalmazás beállítása

Adott mérőszámok vagy a Logic Apps alkalmazást küszöbértékek túllépése figyeléséhez, állítsa be a [értesítések az Azure-ban](../monitoring-and-diagnostics/monitoring-overview-alerts.md). További tudnivalók [az Azure-ban mérőszámok](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Riasztások nélkül beállítása [Azure Naplóelemzés](../log-analytics/log-analytics-overview.md), kövesse az alábbi lépéseket. A riasztások feltételek és a műveletek, speciális [Naplóelemzési beállítása](#azure-diagnostics) túl.

1. A logic app panel menü alatti **figyelés**, válassza a **diagnosztika** > **riasztási szabályok** > **riasztáshozzáadása**itt látható módon:

   ![A Logic Apps alkalmazást értesítések hozzáadása](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. A a **riasztási szabály felvétele** panelen, a riasztás létrehozása, látható módon:

   1. A **erőforrás**, válassza ki a logikai alkalmazás, ha nincsenek használatban. 
   2. Adjon meg egy nevének és leírásának megadása a riasztás.
   3. Válassza ki a **metrika** vagy nyomon követni kívánt eseményt.
   4. Válassza ki a **feltétel**, adja meg egy **küszöbérték** metrika, és válassza ki a **időszak** Ez a metrika figyelésre.
   5. Válassza ki, hogy a riasztás leveleket is küldhet. 
   6. Adjon meg bármely más e-mail címet az értesítés küldéséhez. 
   A webhook URL-CÍMÉT, amelyen szeretné elküldeni a riasztás is megadható.

   Például ez a szabály küld riasztást, amikor öt vagy több futtatása sikertelen lesz, egy óra alatt:

   ![Metrika riasztási szabály létrehozása](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Logikai alkalmazás futtatása egy riasztást, megadhatja a [kérelem eseményindító](../connectors/connectors-native-reqres.md) a munkafolyamat, amellyel feladatokhoz a példák mint:
> 
> * [A POST Slack-](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [A szöveg küldése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [A várólista üzenet hozzáadása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Az Azure Diagnostics esemény beállításai és részletek

Minden egyes diagnosztikai esemény részleteit a Logic Apps alkalmazást és az esemény, például az állapot, a start idő, befejezési idő, és így tovább. Programozott módon állítsa be a figyelés, nyomon követését és naplózását, szervezeti egység használja ezeket az adatokat a a [REST API-t az Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) és a [REST API-t az Azure Diagnostics](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

Például a `ActionCompleted` eseménynek a `clientTrackingId` és `trackedProperties` nyomon követése és figyelésére használható tulajdonságokról:

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

* `clientTrackingId`: Ha nincs megadott Azure automatikusan létrehozza ezt az Azonosítót, és korrelálja események futtatása logikai alkalmazás között, beleértve a beágyazott munkafolyamatok, amelyek nevezzük a logic App. Ez az azonosító egy úgy, hogy manuálisan megadhatja egy `x-ms-client-tracking-id` fejléc, az egyéni azonosító értéket ad meg a kérésére. A kérelem eseményindító, a HTTP-eseményindítóval, illetve a webhook eseményindító is használhatja.

* `trackedProperties`: A bemeneti vagy kimeneti diagnosztikai adatok követheti nyomon, akkor a nyomon követett tulajdonságokat adhat hozzá műveletek a Logic Apps alkalmazást JSON-definícióban. A nyomon követett tulajdonságok követheti nyomon, csak egyetlen művelettel bemenetekhez és kimenetekhez, de használhatja a `correlation` eseményeket, a Futtatás műveletek között tulajdonságait.

  Egy vagy több tulajdonságának nyomon követésére, vegye fel a `trackedProperties` szakasz és a tulajdonságokat, a művelet definíciójához. Tegyük fel például, például egy "order Azonosítóra" a telemetriai adatok nyomon követni kívánt:

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

* [A logic app központi telepítési sablonok létrehozása és a kiadáskezelés](../logic-apps/logic-apps-create-deploy-template.md)
* [A vállalati integrációs csomag B2B forgatókönyvek](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [B2B üzenetek megfigyelése](../logic-apps/logic-apps-monitor-b2b-message.md)
