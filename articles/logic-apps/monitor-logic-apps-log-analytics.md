---
title: Logikai alkalmazások figyelése az Azure Monitor naplóinak használatával
description: Logikai alkalmazások hibaelhárítása az Azure Monitor-naplók beállításával és az Azure Logic Apps diagnosztikai adatainak gyűjtésével
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270237"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Az Azure Monitor naplóinak beállítása és diagnosztikai adatok gyűjtése az Azure Logic Apps alkalmazásokhoz

A logikai alkalmazások futásidejű hibakeresési adatainak jobb kihasználásához beállíthatja és használhatja az [Azure Monitor naplóit](../azure-monitor/platform/data-platform-logs.md) a futásidejű adatok és események adatainak rögzítésére és tárolására, például eseményindító események, események futtatása és műveletesemények a Log [Analytics-munkaterületen.](../azure-monitor/platform/resource-logs-collect-workspace.md) [Az Azure Monitor](../azure-monitor/overview.md) segítségével figyelheti a felhőbeli és a helyszíni környezeteket, így könnyebben megőrizheti azok rendelkezésre állását és teljesítményét. Az Azure Monitor naplóinak használatával [naplólekérdezéseket](../azure-monitor/log-query/log-query-overview.md) hozhat létre, amelyek segítségével összegyűjtheti és áttekintheti ezeket az adatokat. Ezeket [a diagnosztikai adatokat más Azure-szolgáltatásokkal,](#extend-data)például az Azure Storage-szal és az Azure Event Hubs-szal is használhatja.

A logikai alkalmazás naplózásának beállításához engedélyezheti a [Log Analytics-et a logikai alkalmazás létrehozásakor,](#logging-for-new-logic-apps)vagy [telepítheti a Logic Apps Management megoldást a](#install-management-solution) Log Analytics-munkaterületre a meglévő logikai alkalmazásokhoz. Ez a megoldás összesített információkat biztosít a logikai alkalmazás futtatása, és olyan konkrét részleteket tartalmaz, mint az állapot, a végrehajtási idő, az újraküldési állapot és a korrelációs azonosítók. Ezután az adatok naplózásának és lekérdezések létrehozásának engedélyezéséhez állítsa be az [Azure Monitor naplóit.](#set-up-resource-logs)

Ez a cikk bemutatja, hogyan engedélyezheti a Log Analytics logikai alkalmazások létrehozásakor, hogyan telepítheti és állíthatja be a Logic Apps Management megoldást, és hogyan állíthat be és hozhat létre lekérdezéseket az Azure Monitor-naplókhoz.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, szüksége van egy [Log Analytics-munkaterületre.](../azure-monitor/platform/resource-logs-collect-workspace.md) Ha nem rendelkezik munkaterülettel, ismerje meg, [hogyan hozhat létre Log Analytics-munkaterületet.](../azure-monitor/learn/quick-create-workspace.md)

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Log Analytics engedélyezése új logikai alkalmazásokhoz

A logikai alkalmazás létrehozásakor bekapcsolhatja a Log Analytics szolgáltatást.

1. Az [Azure Portalon](https://portal.azure.com)az ablaktáblán, ahol megadja az adatokat a logikai alkalmazás létrehozásához, kövesse az alábbi lépéseket:

   1. A **Log Analytics csoportban**válassza **a Be**lehetőséget.

   1. A **Log Analytics munkaterület** ialapján válassza ki azt a munkaterületet, amelyen az adatokat a logikai alkalmazásból szeretné elküldeni.

      ![Logikai alkalmazás adatainak megadása](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Miután befejezte ezt a lépést, az Azure létrehozza a logikai alkalmazást, amely most már a Log Analytics-munkaterülethez van társítva. Ez a lépés automatikusan telepíti a Logic Apps Management megoldást a munkaterületen.

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. A logikai alkalmazás futtatása után a logikai alkalmazás futtatásai megtekintéséhez [folytassa az alábbi lépésekkel.](#view-logic-app-runs)

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Logic Apps Management megoldás telepítése

Ha a logikai alkalmazás létrehozásakor bekapcsolta a Log Analytics szolgáltatást, hagyja ki ezt a lépést. Már telepítette a Logic Apps Management megoldást a Log Analytics-munkaterületre.

1. Az [Azure Portal](https://portal.azure.com)keresőmezőjébe `log analytics workspaces`írja be a t, és válassza a **Log Analytics-munkaterületeket.**

   ![Válassza a "Log Analytics-munkaterületek" lehetőséget](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. A **Log Analytics-munkaterületek csoportban**válassza ki a munkaterületet.

   ![A Log Analytics-munkaterület kiválasztása](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. Az **Áttekintés** ablaktáblán az Első lépések a **Log Analytics** > **szolgáltatással figyelési megoldások konfigurálása**csoportban válassza a **Megoldások megtekintése**lehetőséget.

   ![Az áttekintő ablaktáblán válassza a "Megoldások megtekintése" lehetőséget](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Az **Áttekintés csoportban**válassza a **Hozzáadás**lehetőséget.

   ![Az áttekintő ablaktáblán új megoldás hozzáadása](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. A **Piactér** megnyitása után a `logic apps management`keresőmezőbe írja be a be írt , és válassza a **Logic Apps Management**lehetőséget.

   ![A Piactéren válassza a "Logic Apps Management" lehetőséget](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. A megoldás leíró ablaktábláján válassza a **Létrehozás lehetőséget.**

   ![A "Logic Apps Management" megoldás hozzáadásához válassza a "Létrehozás" lehetőséget](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Tekintse át és erősítse meg a Log Analytics munkaterületet, ahol telepíteni szeretné a megoldást, és válassza a **Létrehozás** újra lehetőséget.

   ![Válassza a "Létrehozás" lehetőséget a "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Miután az Azure üzembe helyezte a megoldást a Log Analytics-munkaterületet tartalmazó Azure-erőforráscsoportba, a megoldás megjelenik a munkaterület összefoglaló ablaktábláján.

   ![Munkaterület összefoglalási ablaktáblája](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Az Azure Monitor-naplók beállítása

Ha az [Azure Monitor naplóiban](../azure-monitor/platform/data-platform-logs.md)tárolja a futásidejű eseményekre és adatokra vonatkozó információkat, [naplólekérdezéseket](../azure-monitor/log-query/log-query-overview.md) hozhat létre, amelyek segítségével megkeresheti és áttekintheti ezeket az információkat.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki a logikai alkalmazást.

1. A logikai alkalmazás **menüjének Figyelés**területén válassza a **Diagnosztikai beállítások** > **Diagnosztikai beállítás hozzáadása lehetőséget.**

   ![A "Figyelés" csoportban válassza a "Diagnosztikai beállítások" > a "Diagnosztikai beállítás hozzáadása" lehetőséget.](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. A beállítás létrehozásához hajtsa végre az alábbi lépéseket:

   1. Adja meg a beállítás nevét.

   1. Válassza **a Küldés a Log Analytics szolgáltatásba**lehetőséget.

   1. **Előfizetés esetén**válassza ki a Log Analytics-munkaterülethez társított Azure-előfizetést.

   1. A **Log Analytics-munkaterület területen**válassza ki a használni kívánt munkaterületet.

   1. A **napló csoportban**válassza ki a **WorkflowRuntime** kategóriát, amely megadja a rögzíteni kívánt eseménykategóriát.

   1. Az összes mutató kiválasztásához a **metrika**területen válassza az **Összes mutató lehetőséget.**

   1. Amikor elkészült, válassza a **Mentés** lehetőséget.

   Példa:

   ![Válassza a Log Analytics munkaterületés adatok naplózáshoz lehetőséget](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>A nézetlogikai alkalmazás fut

A logikai alkalmazás futtatása után megtekintheti a Log Analytics-munkaterületen futtatott adatokat.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg a Log Analytics-munkaterületet.

1. A munkaterület menüjében válassza **a Munkaterület összefoglaló** > **logikai alkalmazáskezelése parancsot.**

   ![A logikai alkalmazások futási állapota és száma](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Ha a Logic Apps Management csempe nem jeleníti meg azonnal az eredményeket a futtatás után, próbálja meg a **Frissítés** lehetőséget, vagy várjon egy rövid ideig, mielőtt újra próbálkozna.

   Itt a logikai alkalmazás fut vannak csoportosítva név vagy végrehajtási állapot. Ez a lap is részletesen ismerteti a műveletek vagy a logikai alkalmazás futtatásai sikertelenségrészleteit.

   ![A logikai alkalmazás állapotösszegzése](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Egy adott logikai alkalmazás vagy állapot összes futásának megtekintéséhez jelölje ki az adott logikai alkalmazás vagy állapot sorát.

   Íme egy példa, amely egy adott logikai alkalmazás összes futását mutatja:

   ![Logikai alkalmazások futásának és állapotának megtekintése](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   A nyomon [követett tulajdonságokat tartalmazó](#extend-data)műveletek esetén ezeket a tulajdonságokat a **Követett tulajdonságok** oszlop Nézet oszlopának **kiválasztásával** is megtekintheti. A követett tulajdonságokban való kereséshez használja az oszlopszűrőt.

   ![Logikai alkalmazás nyomon követett tulajdonságainak megtekintése](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > A nyomon követett tulajdonságok vagy befejezett események 10–15 perces késéseket tapasztalhatnak, mielőtt megjelennek a Log Analytics-munkaterületen.
   > Ezen az oldalon az **Újraküldés** funkció is jelenleg nem érhető el.

1. Az eredmények szűréséhez ügyféloldali és kiszolgálóoldali szűrést is végezhet.

   * **Ügyféloldali szűrő:** Minden oszlophoz jelölje ki a kívánt szűrőket, például:

     ![Példa oszlopszűrők](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Kiszolgálóoldali szűrő:** Adott időablak kiválasztásához vagy a megjelenő futtatások számának korlátozásához használja a lap tetején található hatókörvezérlőt. Alapértelmezés szerint egyszerre csak 1000 rekord jelenik meg.

     ![Az időablak módosítása](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Az adott futtatás összes műveletének és részleteinek megtekintéséhez jelölje ki a logikai alkalmazás futtatásához tartozó sort.

   Íme egy példa, amely bemutatja egy adott logikai alkalmazás összes műveletét és eseményindítóját:

   ![Logikai alkalmazás futtatásának műveletei megtekintése](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Diagnosztikai adatok küldése az Azure Storage és az Azure Event Hubs számára

Az Azure Monitor-naplókkal együtt kiterjesztheti a logikai alkalmazás diagnosztikai adatainak más Azure-szolgáltatásokkal való használatának módját, például:

* [Az Azure-erőforrásnaplók archiválása tárfiókba](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Az Azure platformnaplók streamelése az Azure Event Hubs-ba](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Ezután valós idejű figyelést kaphat más szolgáltatások, például az [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) és a Power [BI](../azure-monitor/platform/powerbi.md)telemetriai adatok és elemzések használatával. Példa:

* [Adatok streamelése az Event Hubsból a Stream Analytics szolgáltatásba](../stream-analytics/stream-analytics-define-inputs.md)
* [Streamelési adatok elemzése a Stream Analytics segítségével, és valós idejű elemzési irányítópult létrehozása a Power BI-ban](../stream-analytics/stream-analytics-power-bi-dashboard.md)

A diagnosztikai adatok küldésének helyei alapján győződjön meg arról, hogy először [létrehoz egy Azure-tárfiókot,](../storage/common/storage-create-storage-account.md) vagy [létrehoz egy Azure-eseményközpontot.](../event-hubs/event-hubs-create.md) Ezután kiválaszthatja azokat a célokat, ahová el szeretné küldeni az adatokat. Megőrzési időszakok csak akkor vonatkoznak, ha egy tárfiókot használ.

![Adatok küldése az Azure storage-fiókba vagy eseményközpontba](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Az Azure Monitor diagnosztikai eseményei

Minden diagnosztikai esemény rendelkezik a logikai alkalmazás és az adott esemény részleteivel, például az állapottal, a kezdési idővel, a befejezési idővel és így tovább. A figyelés, a nyomon követés és a naplózás programozott beállításához használhatja ezt az információt az [Azure Logic Apps REST API-jával](https://docs.microsoft.com/rest/api/logic) és az Azure Monitor REST [API-jával.](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) Használhatja a `clientTrackingId` és `trackedProperties` a tulajdonságokat is, amelyek a 

* `clientTrackingId`: Ha nincs megadva, az Azure automatikusan létrehozza ezt az azonosítót, és korrelálja az eseményeket egy logikai alkalmazás futtatása között, beleértve a logikai alkalmazásból meghívott beágyazott munkafolyamatokat is. Ezt az azonosítót manuálisan is megadhatja egy eseményindítóban, ha az `x-ms-client-tracking-id` eseményindító-kérelemben átadja az egyéni azonosítóértékét. Használhatja a kérelem eseményindító, HTTP-eseményindító, vagy webhook-eseményindító.

* `trackedProperties`: A diagnosztikai adatok bemeneteinek vagy kimeneteinek nyomon követéséhez hozzáadhat egy szakaszt egy `trackedProperties` művelethez a Logic App Designer használatával vagy közvetlenül a logikai alkalmazás JSON-definíciójában. A nyomon követett tulajdonságok csak egyetlen művelet bemeneteit és kimeneteit tudják nyomon követni, de az `correlation` események tulajdonságaival korrelálhat a műveletek között egy futtatássorán. Ha egynél több tulajdonságot, egy vagy `trackedProperties` több tulajdonságot szeretne nyomon követni, adja hozzá a szakaszt és a kívánt tulajdonságokat a műveletdefinícióhoz.

  Íme egy példa, amely bemutatja, hogy az **Inicializálás változó** műveletdefiníciója hogyan tartalmazza a művelet bemenetéből származó nyomon követett tulajdonságokat, ahol a bemenet tömb, nem pedig rekord.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  Ez a példa több nyomon követett tulajdonságot mutat be:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Ez a példa `ActionCompleted` bemutatja, `trackedProperties` hogy az esemény hogyan tartalmazza a és az `clientTrackingId` attribútumokat:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
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
         "workflowId": "<logic-app-workflow-ID>",
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
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>További lépések

* [Monitorozási és nyomkövetési lekérdezések létrehozása](../logic-apps/create-monitoring-tracking-queries.md)
* [B2B-üzenetek monitorozása Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)
