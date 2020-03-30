---
title: B2B-üzenetek figyelése az Azure Monitor használatával
description: Az AS2, X12 és EDIFACT üzenetek hibaelhárítása az Azure Monitor-naplók beállításával és az Azure Logic Apps diagnosztikai adatainak gyűjtésével
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907980"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Az Azure Monitor naplóinak beállítása és diagnosztikai adatok gyűjtése a B2B-üzenetekhez az Azure Logic Apps alkalmazásban

Miután beállította a B2B kommunikációt a kereskedelmi partnerek között az integrációs fiókjában, ezek a partnerek üzeneteket válthatnak olyan protokollok használatával, mint az AS2, X12 és EDIFACT. Annak ellenőrzéséhez, hogy ez a kommunikáció a várt módon működik-e, beállíthatja az [Azure Monitor-naplókat](../azure-monitor/platform/data-platform-logs.md) az integrációs fiókhoz. [Az Azure Monitor](../azure-monitor/overview.md) segítségével figyelheti a felhőbeli és a helyszíni környezeteket, így könnyebben megőrizheti azok rendelkezésre állását és teljesítményét. Az Azure Monitor-naplók használatával rögzítheti és tárolhatja a futásidejű adatokra és eseményekre vonatkozó adatokat, például az eseményindító eseményeket, az események futtatását és a műveleteseményeket a [Log Analytics-munkaterületen.](../azure-monitor/platform/resource-logs-collect-workspace.md) Az üzenetek esetében a naplózás olyan információkat is gyűjt, mint például:

* Üzenetek száma és állapota
* Nyugtázás állapota
* Az üzenetek és a nyugták közötti korrelációk
* A hibák részletes hibaleírása

Az Azure Monitor lehetővé teszi, hogy [naplólekérdezéseket](../azure-monitor/log-query/log-query-overview.md) hozzon létre, amelyek segítségével megkeresheti és áttekintheti ezeket az információkat. Ezeket [a diagnosztikai adatokat más Azure-szolgáltatásokkal,](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)például az Azure Storage-szal és az Azure Event Hubs-szal is használhatja.

Az integrációs fiók naplózásának beállításához [telepítse a Logic Apps B2B megoldást](#install-b2b-solution) az Azure Portalon. Ez a megoldás összesített információkat nyújt a B2B üzeneteseményekhez. Ezután az adatok naplózásának és lekérdezések létrehozásának engedélyezéséhez állítsa be az [Azure Monitor naplóit.](#set-up-resource-logs)

Ez a cikk bemutatja, hogyan engedélyezheti az Azure Monitor naplózását az integrációs fiókhoz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy Log Analytics-munkaterület. Ha nem rendelkezik Log Analytics-munkaterülettel, ismerje meg, [hogyan hozhat létre Log Analytics-munkaterületet.](../azure-monitor/learn/quick-create-workspace.md)

* Az Azure Monitor naplózásával beállított logikai alkalmazás, amely elküldi ezeket az adatokat a Log Analytics-munkaterületre. Ismerje [meg, hogyan állíthatja be az Azure Monitor naplóit a logikai alkalmazáshoz.](../logic-apps/monitor-logic-apps.md)

* A logikai alkalmazáshoz kapcsolódó integrációs fiók. [Ismerje meg, hogyan kapcsolhatja össze az integrációs fiókot a logikai alkalmazással.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Logic Apps B2B megoldás telepítése

Mielőtt az Azure Monitor naplók nyomon követheti a B2B üzeneteket a logikai alkalmazás, adja hozzá a **Logic Apps B2B** megoldás a Log Analytics-munkaterülethez.

1. Az [Azure Portal](https://portal.azure.com)keresőmezőjébe `log analytics workspaces`írja be a t, és válassza a **Log Analytics-munkaterületeket.**

   ![Válassza a "Log Analytics-munkaterületek" lehetőséget](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. A **Log Analytics-munkaterületek csoportban**válassza ki a munkaterületet.

   ![A Log Analytics-munkaterület kiválasztása](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. Az Áttekintés ablaktáblán az Első lépések a **Log Analytics** > **szolgáltatással figyelési megoldások konfigurálása**csoportban válassza a **Megoldások megtekintése**lehetőséget.

   ![Az Áttekintő ablaktáblán válassza a "Megoldások megtekintése" lehetőséget](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. Az Áttekintő ablaktáblán válassza a **Hozzáadás**lehetőséget.

   ![Az áttekintő ablaktáblán új megoldás hozzáadása](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. A **Piactér** megnyitása után a `logic apps b2b`keresőmezőbe írja be a be írt , és válassza a **Logic Apps B2B**lehetőséget.

   ![A Piactéren válassza a "Logic Apps Management" lehetőséget](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. A megoldás leíró ablaktábláján válassza a **Létrehozás lehetőséget.**

   ![A "Logic Apps B2B" megoldás hozzáadásához válassza a "Létrehozás" lehetőséget](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Tekintse át és erősítse meg a Log Analytics munkaterületet, ahol telepíteni szeretné a megoldást, és válassza a **Létrehozás** újra lehetőséget.

   ![Válassza a "Create" lehetőséget a "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Miután az Azure üzembe helyezte a megoldást a Log Analytics-munkaterületet tartalmazó Azure-erőforráscsoportba, a megoldás megjelenik a munkaterület összefoglaló ablaktábláján. A B2B-üzenetek feldolgozásakor az ablaktáblán lévő üzenetek száma frissül.

   ![Munkaterület összefoglalási ablaktáblája](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Az Azure Monitor-naplók beállítása

Engedélyezheti az Azure Monitor naplózását közvetlenül az integrációs fiókból.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki az integrációs fiókot.

   ![Az integrációs fiók megkeresése és kiválasztása](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. Az integrációs fiók menüjében válassza a **Figyelés**csoport **Diagnosztikai beállítások lehetőséget.** Válassza **a Diagnosztikai beállítás hozzáadása lehetőséget.**

   ![A "Figyelés" területen válassza a "Diagnosztikai beállítások" lehetőséget.](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. A beállítás létrehozásához hajtsa végre az alábbi lépéseket:

   1. Adja meg a beállítás nevét.

   1. Válassza **a Küldés a Log Analytics szolgáltatásba**lehetőséget.

   1. **Előfizetés esetén**válassza ki a Log Analytics-munkaterülethez társított Azure-előfizetést.

   1. A **Log Analytics-munkaterület területen**válassza ki a használni kívánt munkaterületet.

   1. A **napló csoportban**válassza ki az **IntegrationAccountTrackingEvents** kategóriát, amely megadja a rögzíteni kívánt eseménykategóriát.

   1. Amikor elkészült, válassza a **Mentés** lehetőséget.

   Példa: 

   ![Az Azure Monitor naplóinak beállítása diagnosztikai adatok gyűjtésére](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Üzenet állapotának megtekintése

A logikai alkalmazás futtatása után megtekintheti az állapotot és az adatokat az üzenetekről a Log Analytics-munkaterületen.

1. Az [Azure Portal](https://portal.azure.com) keresőmezőjében keresse meg és nyissa meg a Log Analytics-munkaterületet.

1. A munkaterület menüjében válassza **a Munkaterület összefoglaló** > **Logikai alkalmazások B2B parancsát.**

   ![Munkaterület összefoglalási ablaktáblája](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Ha a Logic Apps B2B csempéje nem jeleníti meg azonnal az eredményeket a futtatás után, próbálja meg a **Frissítés** lehetőséget, vagy várjon egy rövid ideig, mielőtt újra próbálkozna.

   Alapértelmezés szerint a **Logic Apps B2B** csempe egyetlen nap alapján jeleníti meg az adatokat. Ha az adathatókört másik intervallumra szeretné módosítani, jelölje ki a lap tetején található hatókörvezérlőt:

   ![Intervallum módosítása](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Az üzenet állapotirányítópultjának megjelenítése után megtekintheti egy adott üzenettípus további részleteit, amely egyetlen nap alapján jeleníti meg az adatokat. Válassza az **AS2**, **X12**vagy **EDIFACT csempéjét.**

   ![Üzenetek állapotának megtekintése](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Megjelenik az üzenetek listája a kiválasztott csempén. Például a következőképpen nézhet ki egy AS2-üzenetlista:

   ![Az AS2-üzenetek állapotaés részletei](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Ha többet szeretne megtudni az egyes üzenettípusok tulajdonságairól, olvassa el az alábbi üzenettulajdonságok leírását:

   * [AS2 üzenet tulajdonságai](#as2-message-properties)
   * [X12 üzenet tulajdonságai](#x12-message-properties)
   * [EDIFACT üzenet tulajdonságai](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Az AS2, X12 és EDIFACT üzenetek tulajdonságleírásai és névformátumai

Minden üzenettípushoz itt találhatók a letöltött üzenetfájlok tulajdonságleírásai és névformátumai.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 üzenettulajdonság leírása

Itt vannak a tulajdonság leírások minden AS2 üzenetet.

| Tulajdonság | Leírás |
|----------|-------------|
| **Küldő** | A **Fogadási beállítások**ban megadott vendégpartner vagy az AS2-szerződés **küldési beállításai** között megadott gazdapartner |
| **Fogadó** | A **Fogadási beállítások**ban megadott gazdapartner vagy az AS2-szerződés **küldési beállításai** között megadott vendégpartner |
| **Logikai alkalmazás** | Az a logikai alkalmazás, amelyen az AS2-műveletek be vannak állítva |
| **Állapot** | Az AS2 üzenet állapota <br>Success = Érvényes AS2-üzenet érkezett vagy küldött. Nincs beállítva MDN. <br>Success = Érvényes AS2-üzenet érkezett vagy küldött. Az MDN be van állítva és fogadva, vagy az MDN elküldésre kerül. <br>Nem sikerült = Érvénytelen AS2-üzenet érkezett. Nincs beállítva MDN. <br>Függőben = Érvényes AS2-üzenet fogadása vagy elküldése. MDN van beállítva, és MDN várható. |
| **ACK között** | Az MDN-üzenet állapota <br>Elfogadva = Pozitív MDN érkezett vagy küldött. <br>Függőben = Várakozás az MDN fogadására vagy elküldésére. <br>Elutasítva = Negatív MDN érkezett vagy küldött. <br>Nem kötelező = az MDN nincs beállítva a megállapodásban. |
| **Irányba** | Az AS2 üzenet iránya |
| **Nyomon követési azonosító** | Az azonosító, amely korrelálja a logikai alkalmazások összes eseményindítóját és műveletét |
| **Üzenetazonosító** | Az AS2 üzenetfejlécek AS2-üzenetazonosítója |
| **Időbélyeg** | Az az időpont, amikor az AS2 művelet feldolgozta az üzenetet |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 üzenet tulajdonság leírása

Itt vannak az egyes X12-üzenetek tulajdonságleírásai.

| Tulajdonság | Leírás |
|----------|-------------|
| **Küldő** | A **Fogadási beállítások**ban megadott vendégpartner vagy az X12-szerződés **küldési beállításai** között megadott gazdapartner |
| **Fogadó** | A **Fogadási beállítások**ban megadott gazdapartner vagy az X12-szerződés **küldési beállításai** között megadott vendégpartner |
| **Logikai alkalmazás** | Az A logikai alkalmazás, ahol az X12-es műveletek be vannak állítva |
| **Állapot** | Az X12 üzenet állapota <br>Success = Érvényes X12-üzenet érkezett vagy küldött. Nincs beállítva funkcionális ack. <br>Success = Érvényes X12-üzenet érkezett vagy küldött. A funkcionális ack van beállítva és fogadva, vagy egy funkcionális ack küld. <br>Failed = Érvénytelen X12-üzenet érkezett vagy küldött. <br>Függőben = Érvényes X12-üzenet érkezett vagy küldött. Funkcionális ack van beállítva, és egy funkcionális ack várható. |
| **ACK között** | Funkcionális Ack (997) állapot <br>Elfogadott = Pozitív funkcionális ack érkezett vagy küldött. <br>Elutasítva = Negatív funkcionális ack érkezett vagy küldött. <br>Függőben = Funkcionális ack-t vár, de nem érkezett meg. <br>Függőben = Létrehozott egy funkcionális ack, de nem küldheti el a partnernek. <br>Nem kötelező = A funkcionális ack nincs beállítva. |
| **Irányba** | Az X12 üzenet iránya |
| **Nyomon követési azonosító** | Az azonosító, amely korrelálja a logikai alkalmazások összes eseményindítóját és műveletét |
| **Msg típusa** | Az EDI X12 üzenettípusa |
| **Icn** | Az X12 közlemény interchange control száma |
| **TSCN** | Az X12 közlemény tranzakciókészlet-vezérlőszáma |
| **Időbélyeg** | Az az időpont, amikor az X12 művelet feldolgozta az üzenetet |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT üzenet tulajdonságleírásai

Itt vannak az egyes EDIFACT üzenetek tulajdonságleírásai.

| Tulajdonság | Leírás |
|----------|-------------|
| **Küldő** | A **Fogadási beállítások**ban megadott vendégpartner vagy az EDIFACT-szerződés **küldési beállításai** között megadott vendégpartner |
| **Fogadó** | A **Fogadási beállítások**ban megadott gazdapartner vagy az EDIFACT-szerződés **küldési beállításai** között megadott vendégpartner |
| **Logikai alkalmazás** | Az EDIFACT-műveleteket beállító logikai alkalmazás |
| **Állapot** | Az EDIFACT üzenet állapota <br>Success = Érvényes EDIFACT üzenet érkezett vagy küldött. Nincs beállítva funkcionális ack. <br>Success = Érvényes EDIFACT üzenet érkezett vagy küldött. A funkcionális ack van beállítva és fogadva, vagy egy funkcionális ack küld. <br>Failed = Érvénytelen EDIFACT-üzenet fogadása vagy elküldése <br>Függőben = Érvényes EDIFACT üzenet érkezett vagy küldött. Funkcionális ack van beállítva, és egy funkcionális ack várható. |
| **ACK között** | Funkcionális Ack (CONTRL) állapot <br>Elfogadott = Pozitív funkcionális ack érkezett vagy küldött. <br>Elutasítva = Negatív funkcionális ack érkezett vagy küldött. <br>Függőben = Funkcionális ack-t vár, de nem érkezett meg. <br>Függőben = Létrehozott egy funkcionális ack, de nem küldheti el a partnernek. <br>Nem kötelező = A funkcionális Ack nincs beállítva. |
| **Irányba** | Az EDIFACT üzenet iránya |
| **Nyomon követési azonosító** | Az azonosító, amely korrelálja a logikai alkalmazások összes eseményindítóját és műveletét |
| **Msg típusa** | Az EDIFACT üzenet típusa |
| **Icn** | Az EDIFACT közlemény adatcsere-ellenőrzési száma |
| **TSCN** | Az EDIFACT üzenet tranzakciókészlet-vezérlőszáma |
| **Időbélyeg** | Az az időpont, amikor az EDIFACT művelet feldolgozta az üzenetet |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>További lépések

* [Monitorozási és nyomkövetési lekérdezések létrehozása](../logic-apps/create-monitoring-tracking-queries.md)