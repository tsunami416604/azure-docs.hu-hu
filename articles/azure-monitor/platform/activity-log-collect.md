---
title: Azure-tevékenységnapló gyűjtése és elemzése az Azure Monitorban
description: Gyűjtse össze az Azure-tevékenységnaplót az Azure Figyelőnaplókban, és használja a figyelési megoldást az Azure-tevékenységnapló elemzéséhez és kereséséhez az összes Azure-előfizetésében.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382870"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Azure-tevékenységnapló gyűjtése és elemzése az Azure Monitorban
Az [Azure-tevékenységnapló](platform-logs-overview.md) egy [platformnapló,](platform-logs-overview.md) amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekbe. Bár megtekintheti a tevékenységnaplót az Azure Portalon, konfigurálnia kell, hogy küldjön egy Log Analytics-munkaterületre az Azure Monitor további funkcióinak engedélyezéséhez. Ez a cikk ismerteti, hogyan hajthatja végre ezt a konfigurációt, és hogyan küldheti el a tevékenységnaplót az Azure storage és eseményközpontok.

A tevékenységnapló gyűjtése a Log Analytics-munkaterületen a következő előnyökkel jár:

- A Log Analytics-munkaterületen tárolt tevékenységnapló-adatok értése nem számít fel adatokat.
- A tevékenységnapló-adatok és az Azure Monitor által gyűjtött egyéb figyelési adatok korrelációja.
- A naplólekérdezések segítségével összetett elemzéseket végezhet, és mélyreható elemzéseket nyerhet a tevékenységnapló-bejegyzésekről.
- Naplóriasztások használata a tevékenységbejegyzések, amelyek összetettebb riasztási logikát lehetővé teszik.
- 90 napnál hosszabb ideig tárolja a tevékenységnapló-bejegyzéseket.
- Konszolidálja a több Azure-előfizetésből és bérlőből származó naplóbejegyzéseket egy helyre elemzésre.

> [!IMPORTANT]
> A bérlők közötti naplók gyűjtése [azure-világítótoronyhoz](/azure/lighthouse)szükséges.

## <a name="collecting-activity-log"></a>Tevékenységnapló gyűjtése
A tevékenységnapló automatikusan frissül [az Azure Portalon való megtekintéshez.](activity-log-view.md) Ha egy Log Analytics-munkaterületen szeretné összegyűjteni, vagy el szeretné küldeni neki az Azure storage- vagy eseményközpontokat, hozzon létre egy [diagnosztikai beállítást.](diagnostic-settings.md) Ez ugyanaz a módszer, amelyet az erőforrásnaplók használnak, így az összes [platformnaplóban](platform-logs-overview.md)konzisztens.  

Diagnosztikai beállítás létrehozásához a tevékenységnapló, válassza **diagnosztikai beállítások** az Azure Monitor **tevékenységnapló** menüjében. A beállítás létrehozásáról a diagnosztikai beállítás létrehozása című témakörben a [platformnaplók és metrikák azure-beli gyűjtéséhez](diagnostic-settings.md) című témakörben talál. A szűrhető kategóriák leírását [lásd a Kategóriák a tevékenységnaplóban.](activity-log-view.md#categories-in-the-activity-log) Ha vannak örökölt beállításai, a diagnosztikai beállítások létrehozása előtt győződjön meg arról, hogy letiltja őket. Ha mindkettő engedélyezve van, az ismétlődő adatokat eredményezhet.

![Diagnosztikai beállítások](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Jelenleg csak az Azure Portal és a Resource Manager-sablon használatával hozhat létre előfizetési szint diagnosztikai beállítást. 


## <a name="legacy-settings"></a>Örökölt beállítások 
Bár a diagnosztikai beállítások az előnyben részesített módszer a tevékenységnapló különböző célokra való elküldéséhez, az örökölt módszerek továbbra is működnek, ha nem választja a diagnosztikai beállítással való lecserélést. A diagnosztikai beállítások a következő előnyökkel járnak az örökölt módszerekkel szemben, ezért ajánlott frissíteni a konfigurációt:

- Konzisztens módszer az összes platformnapló összegyűjtésére.
- Tevékenységnapló gyűjtése több előfizetésés bérlő között.
- Szűrőgyűjtés, hogy csak bizonyos kategóriáknaplóit gyűjtse össze.
- Gyűjtse össze az összes tevékenységnapló-kategóriát. Egyes kategóriák nem gyűjtik örökölt módszerrel.
- Gyorsabb késés a naplóbetöltéshez. Az előző módszer körülbelül 15 perces késés, míg a diagnosztikai beállítások hozzáteszi, csak körülbelül 1 perc.



### <a name="log-profiles"></a>Naplóprofilok
A naplóprofilok a tevékenységnapló Azure-tárolókba vagy eseményközpontokba történő küldésének örökölt módjai. Az alábbi eljárással folytathatja a munkát egy naplóprofillal, vagy letilthatja azt a diagnosztikai beállításba való áttelepítés előkészítéseként.

1. Az **Azure-portál Azure Monitor** menüjében válassza a **Tevékenységnapló**lehetőséget.
3. Kattintson a **Diagnosztikai beállítások** elemre.

   ![Diagnosztikai beállítások](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Kattintson a lila banner az örökölt élmény.

    ![Örökölt élmény](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
A tevékenységnapló naplóba való gyűjtésének örökölt módja a napló a munkaterület konfigurációjában való csatlakoztatása. 

1. Az Azure Portal **Log Analytics-munkaterületeinek** menüjében válassza ki a munkaterületet a tevékenységnapló gyűjtéséhez.
1. A munkaterület menüjének **Munkaterület-adatforrások** szakaszában válassza az **Azure-tevékenységnapló t.**
1. Kattintson a csatlakoztatni kívánt előfizetésre.

    ![Munkaterületek](media/activity-log-collect/workspaces.png)

1. Kattintson a **Csatlakozás** gombra, ha az előfizetésben lévő tevékenységnaplót a kijelölt munkaterülethez szeretné csatlakoztatni. Ha az előfizetés már csatlakoztatva van egy másik munkaterülethez, először a **Kapcsolat bontásához** kattintson a Kapcsolat bontása gombra.

    ![Munkaterületek összekapcsolása](media/activity-log-collect/connect-workspace.png)


A beállítás letiltásához hajtsa végre ugyanezt az eljárást, és kattintson a **Kapcsolat bontása** gombra az előfizetés munkaterületről való eltávolításához.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Tevékenységnapló elemzése a Log Analytics-munkaterületen
Amikor egy tevékenységnaplót egy Log Analytics-munkaterülethez csatlakoztat, a program bejegyzéseket ír a munkaterületre egy *AzureActivity* nevű táblába, amelyet [naplólekérdezéssel](../log-query/log-query-overview.md)lehet beolvasni. A tábla szerkezete [a naplóbejegyzés kategóriájától](activity-log-view.md#categories-in-the-activity-log)függően változik . Az [azure-tevékenységnapló eseménysémája](activity-log-schema.md) az egyes kategóriák leírását tartalmazza.


### <a name="data-structure-changes"></a>Adatszerkezet-változások
Diagnosztikai beállítások ugyanazokat az adatokat gyűjti, mint a korábbi módszer a tevékenységnapló gyűjtésére használt néhány változás az *AzureActivity-tábla* szerkezetét.

Az alábbi táblázat oszlopai elavultak a frissített sémában. Továbbra is léteznek az *AzureActivity,* de nem lesz adat. Ezeknek az oszlopoknak a cseréje nem új, de ugyanazokat az adatokat tartalmazzák, mint az elavult oszlop. Más formátumúak, ezért előfordulhat, hogy módosítania kell az azokat használó naplólekérdezéseket. 

| Elavult oszlop | Csere oszlop |
|:---|:---|
| ActivityStatus (Tevékenységállapota)    | ActivityStatusValue    |
| ActivitySubstatus (Tevékenységsubstatus) | ActivitySubstatusValue érték |
| OperationName     | OperationNameValue érték     |
| ResourceProvider  | ResourceProviderValue érték  |

> [!IMPORTANT]
> Bizonyos esetekben az oszlopokban lévő értékek nagybetűsek lehetnek. Ha olyan lekérdezéssel rendelkezik, amely ezeket az oszlopokat tartalmazza, az [=~ operátorral](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) végezze el a kis- és nagybetűk megkülönböztetését.

A frissített sémában a következő oszlop került be az *AzureActivity szolgáltatásba:*

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Tevékenységnaplók elemzésének figyelési megoldása
Az Azure Log Analytics figyelési megoldás hamarosan elavult, és helyébe egy munkafüzet segítségével a frissített séma a Log Analytics-munkaterületen. Továbbra is használhatja a megoldást, ha már engedélyezve van, de csak akkor használható, ha a tevékenységnaplót örökölt beállításokkal gyűjti. 



### <a name="use-the-solution"></a>Használja a megoldást
A figyelési megoldások az Azure Portal **Monitor** menüjéből érhetők el. Válassza a **Tovább** lehetőséget az **Elemzések** szakaszban az **Áttekintés** lap megoldáscsempékkel való megnyitásához. **Az Azure-tevékenységnaplók** csempe megjeleníti az **AzureActivity-rekordok** számát a munkaterületen.

![Az Azure-tevékenységnaplók csempéje](media/collect-activity-logs/azure-activity-logs-tile.png)


Kattintson az **Azure-tevékenységnaplók** csempére az **Azure-tevékenységnaplók** nézet megnyitásához. A nézet az alábbi táblázatban szereplő képi megjelenítési részeket tartalmazza. Minden alkatrész legfeljebb 10 elemet sorol fel, amelyek megfelelnek az adott alkatrész adott időtartományra vonatkozó feltételeinek. Futtathat egy naplólekérdezést, amely az összes egyező rekordot visszaadja, ha a Rész alján az **Összes** megtekintése gombra kattint.

![Az Azure-tevékenységnaplók irányítópultja](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>A megoldás engedélyezése új előfizetésekhez
Hamarosan már nem tudja hozzáadni a Activity Logs Analytics megoldást az előfizetéséhez az Azure Portalon keresztül. Hozzáadhatja az alábbi eljárással egy erőforrás-kezelő sablonnal. 

1. Másolja a következő jsont egy *ActivityLogTemplate*.json nevű fájlba.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Telepítse a sablont a következő PowerShell-parancsokkal:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>További lépések

- További információ a [tevékenységnaplóról.](platform-logs-overview.md)
- További információ az [Azure Monitor adatplatformjáról.](data-platform.md)
- A [naplólekérdezések](../log-query/log-query-overview.md) segítségével megtekintheti a tevékenységnapló részletes adatait.
