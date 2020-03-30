---
title: Azure Monitor naplók használata az Azure Logic-alkalmazásokkal és a Power Automate-tal
description: Megtudhatja, hogyan használhatja az Azure Logic Apps és a Power Automate segítségével az átaható folyamatok gyors automatizálásához az Azure Monitor-összekötő használatával.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480011"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>Az Azure Monitor naplói nak összekötője a logikai alkalmazásokhoz és a folyamathoz
[Az Azure Logic Apps](/azure/logic-apps/) és [a Power Automate](https://ms.flow.microsoft.com) lehetővé teszi, hogy automatizált munkafolyamatokat hozzon létre több száz művelet használatával a különböző szolgáltatásokhoz. Az Azure Monitor Naplók összekötő lehetővé teszi, hogy olyan munkafolyamatokat hozzon létre, amelyek adatokat kérnek le egy Log Analytics-munkaterületről vagy egy Application Insights-alkalmazásból az Azure Monitorban. Ez a cikk ismerteti az összekötőhöz tartozó műveleteket, és egy forgatókönyvet biztosít egy munkafolyamat létrehozásához ezen adatok használatával.

Létrehozhat például egy logikai alkalmazást az Azure Monitor naplóadatainak használatához az Office 365-ből küldött e-mail-értesítésben, létrehozhat egy hibát az Azure DevOps-ban, vagy közzétehet egy Lazaság-üzenetet.  A munkafolyamatot egyszerű ütemezéssel vagy egy csatlakoztatott szolgáltatás valamilyen műveletével indíthatja el, például amikor e-mail vagy tweet érkezik. 

## <a name="actions"></a>Műveletek
Az alábbi táblázat ismerteti az Azure Monitor Naplók összekötőtartalmazza a műveleteket. Mindkettő lehetővé teszi, hogy egy log-lekérdezést egy Log Analytics-munkaterület vagy az Application Insights-alkalmazás futtasson. A különbség az adatok visszaadásának módjában van.

> [!NOTE]
> Az Azure Monitor Naplók összekötő felváltja az [Azure Log Analytics-összekötő](https://docs.microsoft.com/connectors/azureloganalytics/) és az [Azure Application Insights-összekötő.](https://docs.microsoft.com/connectors/applicationinsights/) Ez az összekötő ugyanazokat a funkciókat biztosítja, mint a többiek, és az előnyben részesített módszer egy lekérdezés futtatásához egy Log Analytics-munkaterület vagy egy Application Insights-alkalmazás.


| Műveletek | Leírás |
|:---|:---|
| [Lekérdezés és listaeredmények futtatása](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | Minden sort saját objektumként ad eredményül. Akkor használja ezt a műveletet, ha a munkafolyamat többi részében minden sorral külön szeretne dolgozni. A műveletet általában egy [minden tevékenységhez követi.](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop) |
| [Lekérdezés futtatása és eredmények megjelenítése](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Az eredményhalmaz összes sorát egyetlen formázott objektumként adja vissza. Akkor használja ezt a műveletet, ha a munkafolyamat többi részében együtt szeretné használni az eredményhalmazt, például az eredményeket e-mailben szeretné elküldeni.  |

## <a name="walkthroughs"></a>Útmutatók
Az alábbi oktatóanyagok bemutatják az Azure Monitor-összekötők használatát az Azure Logic Apps-ben. Ugyanezt a példát a Power Automate segítségével is elvégezheti, az egyetlen különbség az, hogy hogyan hozza létre a kezdeti munkafolyamatot, és hogyan futtathatja azt, amikor befejeződött. A munkafolyamat és a műveletek konfigurálása megegyezik a kettő között. Első lépésekhez olvassa el A [folyamat létrehozása sablonból a Power Automate-ben](https://docs.microsoft.com/power-automate/get-started-logic-template) című témakört.


### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Nyissa meg a **Logikai alkalmazások at** the Azure Portal and click **Add.** Válasszon ki egy **Előfizetés**, **Erőforrás csoportot**és **régiót** az új logikai alkalmazás tárolásához, majd adjon neki egyedi nevet. A Log **Analytics** beállítás bekapcsolva gyűjtheti a futásidejű adatokat és eseményeket az Azure Monitor naplóinak beállítása című részben leírtak szerint, [és diagnosztikai adatokat gyűjthet az Azure Logic Apps alkalmazáshoz.](../../logic-apps/monitor-logic-apps-log-analytics.md) Ez a beállítás nem szükséges az Azure Monitor Naplók összekötő használatához.

![Logikai alkalmazás létrehozása](media/logicapp-flow-connector/create-logic-app.png)


Kattintson **a Véleményezés + létrehozás,** majd **a Létrehozás gombra.** Amikor a központi telepítés befejeződött, kattintson az Ugrás az **erőforrásra** gombra a **Logic Apps Designer**megnyitásához.

### <a name="create-a-trigger-for-the-logic-app"></a>Eseményindító létrehozása a logikai alkalmazáshoz
A **Kezdés egy közös eseményindítóval**csoportban válassza az **Ismétlődés**lehetőséget. Ez létrehoz egy logikai alkalmazást, amely automatikusan fut rendszeres időközönként. A művelet **Gyakoriság** mezőjében válassza a **Nap** lehetőséget, és az **Intervallum** mezőbe írja be az **1** értéket a munkafolyamat napi egyszeri futtatásához.

![Ismétlődési művelet](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Forgatókönyv: A posta által vizualizált eredmények
Az alábbi oktatóanyag bemutatja, hogyan hozhat létre egy logikai alkalmazást, amely e-mailben elküldi az Azure Monitor naplólekérdezésének eredményeit. 

### <a name="add-azure-monitor-logs-action"></a>Azure Figyelőnaplók hozzáadása művelet
Kattintson **a + Új lépés** gombra az ismétlődési művelet után futó művelet hozzáadásához. A **Művelet kiválasztása csoportban**írja be az **Azure-figyelőt,** és válassza az **Azure Figyelőnaplók lehetőséget.**

![Az Azure Monitor naplók művelet](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Kattintson **az Azure Log Analytics – Lekérdezés futtatása és az eredmények megjelenítése parancsra.**

![Lekérdezés futtatása és az eredményművelet megjelenítése](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Figyelőnaplók hozzáadása művelet

Válassza ki az **előfizetési** és **erőforráscsoportot** a Log Analytics-munkaterülethez. Válassza a *Log Analytics Workspace* lehetőséget az **erőforrástípushoz,** majd az **Erőforrás neve**területen válassza ki a munkaterület nevét.

Adja hozzá a következő naplólekérdezést a **Lekérdezés** ablakhoz.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Válassza *a Beállítás a lekérdezésben* lehetőséget a diagramtípus **időtartományához** és **HTML-táblázatához.** **Chart Type**
   
![Lekérdezés futtatása és az eredményművelet megjelenítése](media/logicapp-flow-connector/run-query-visualize-action.png)

Az e-mailt az aktuális kapcsolathoz társított fiók küldi el. Másik fiókot a Kapcsolat **módosítása**gombra kattintva adhat meg.

### <a name="add-email-action"></a>E-mail hozzáadása művelet

Kattintson **a + Új lépés**gombra , majd a + Művelet hozzáadása **gombra**. A **Művelet kiválasztása csoportban**írja be az **outlook** parancsot, és válassza az **Office 365 Outlook**lehetőséget.

![Outlook-összekötő kiválasztása](media/logicapp-flow-connector/select-outlook-connector.png)

Válassza **az E-mail küldése (V2) lehetőséget.**

![Az Office 365 Outlook kijelölési ablaka](media/logicapp-flow-connector/select-mail-action.png)

Kattintson bárhová a **Törzs** mezőben egy **dinamikus tartalomablak** megnyitásához, amely a logikai alkalmazás előző műveleteinek értékeit tartalmaz. Válassza **a Tovább,** majd a **Törzs,** amely a lekérdezés eredménye a Log Analytics műveletben.

![Törzs kijelölése](media/logicapp-flow-connector/select-body.png)

Adja meg a címzett e-mail címét a **Címzett** ablakban, és az e-mail tárgyát a **Tárgy ablakban.** 

![Postai művelet](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>A logikai alkalmazás mentése és tesztelése
Kattintson a **Mentés,** majd a **Futtatás** gombra a logikai alkalmazás tesztfuttatásának végrehajtásához.

![Mentés és futtatás](media/logicapp-flow-connector/save-run.png)


Amikor a logikai alkalmazás befejeződik, ellenőrizze a megadott címzett e-mailjeit.  A következőhöz hasonló törzsű e-mailt kellett volna kapnia:

![Minta e-mail](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>További lépések

- További információ a [naplólekérdezésekről az Azure Monitorban.](../log-query/log-query-overview.md)
- További információ a [logikai alkalmazásokról](/azure/logic-apps/)
- További információ a [Microsoft Flow szolgáltatásról.](https://ms.flow.microsoft.com)

