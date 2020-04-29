---
title: Azure-beli tevékenységek naplójának összegyűjtése és elemzése Azure Monitor
description: Gyűjtse össze az Azure-beli tevékenység naplóját Azure Monitor naplókban, és a figyelési megoldás használatával elemezze és keresse meg az Azure-tevékenység naplóját az összes Azure-előfizetésében.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382870"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Azure-beli tevékenységek naplójának összegyűjtése és elemzése Azure Monitor
Az [Azure-tevékenység naplója](platform-logs-overview.md) egy olyan [platform-napló](platform-logs-overview.md) , amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. Míg a Azure Portalban megtekintheti a tevékenység naplóját, úgy kell beállítania, hogy a Azure Monitor további funkcióinak engedélyezéséhez egy Log Analytics munkaterületre küldje el. Ez a cikk bemutatja, hogyan hajthatja végre ezt a konfigurációt, és hogyan küldheti el a műveletnapló az Azure Storage-ba és az Event hubokba.

A Log Analytics munkaterületen a tevékenység naplójának összegyűjtése a következő előnyöket nyújtja:

- Nincs adatfeldolgozási vagy adatmegőrzési díj a Log Analytics munkaterületen tárolt műveletnapló-adatokra vonatkozóan.
- A tevékenység-naplózási adatok korrelációja Azure Monitor által gyűjtött egyéb megfigyelési adatokkal.
- A naplók használatával összetett elemzéseket végezhet, és részletes elemzéseket készíthet a tevékenységi naplók bejegyzéseiről.
- A naplózási riasztásokat olyan tevékenységi bejegyzésekkel használhatja, amelyek összetettebb riasztási logikát tesznek lehetővé.
- 90 napnál hosszabb ideig tárolhatja a tevékenység naplójának bejegyzéseit.
- Több Azure-előfizetésből és-bérlőből származó naplóbejegyzések konszolidálása egyetlen helyre az elemzéshez.

> [!IMPORTANT]
> A naplók a bérlők közötti gyűjtéséhez az [Azure Lighthouse](/azure/lighthouse)szükséges.

## <a name="collecting-activity-log"></a>Tevékenység naplójának gyűjtése
A rendszer automatikusan összegyűjti a tevékenység naplóját a [Azure Portal való megtekintéshez](activity-log-view.md). Ha Log Analytics munkaterületen szeretné összegyűjteni, vagy az Azure Storage vagy az Event hub küldését szeretné elküldeni, hozzon létre egy [diagnosztikai beállítást](diagnostic-settings.md). Ez ugyanaz a módszer, amelyet az erőforrás-naplók az összes platform- [napló](platform-logs-overview.md)esetében konzisztensként használnak.  

Ha diagnosztikai beállítást szeretne létrehozni a tevékenység naplójában, válassza a **diagnosztikai beállítások** elemet a Azure monitor **tevékenység napló** menüjében. A beállítás létrehozásával kapcsolatos részletekért tekintse meg a [diagnosztikai beállítás létrehozása a platform naplófájljainak és metrikáinak gyűjtéséhez az Azure-ban](diagnostic-settings.md) című témakört. A szűrni kívánt kategóriák leírását a [tevékenység naplójának kategóriák](activity-log-view.md#categories-in-the-activity-log) részében tekintheti meg. Ha örökölt beállítások vannak, akkor a diagnosztikai beállítások létrehozása előtt tiltsa le őket. Ha mindkettő engedélyezve van, duplikált adatértéket eredményezhet.

![Diagnosztikai beállítások](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Jelenleg csak az Azure Portal és egy Resource Manager-sablon használatával hozhat létre előfizetési szintű diagnosztikai beállítást. 


## <a name="legacy-settings"></a>Örökölt beállítások 
Míg a diagnosztikai beállítások a tevékenység naplójának különböző célhelyekre való küldésének előnyben részesített módszere, az örökölt metódusok továbbra is működni fognak, ha nem választ egy diagnosztikai beállítással. A diagnosztikai beállítások az örökölt módszereknél az alábbi előnyökkel járnak, és azt javasoljuk, hogy frissítse a konfigurációt:

- Konzisztens módszer az összes platform naplójának összegyűjtéséhez.
- Gyűjtsön több előfizetésre és bérlőre kiterjedő tevékenység naplóját.
- A szűrő gyűjtemény csak bizonyos kategóriákhoz tartozó naplókat gyűjt.
- Az összes műveletnapló-kategória összegyűjtése. Egyes kategóriák gyűjtése nem a régi metódussal történik.
- Gyorsabb késés a naplók betöltéséhez. Az előző metódus körülbelül 15 percet vesz igénybe, míg a diagnosztikai beállítások csak 1 percet vesznek fel.



### <a name="log-profiles"></a>Log-profilok
A log profilok a tevékenység naplójának az Azure Storage-ba vagy az Event hubokba való küldésére szolgáló örökölt módszer. A következő eljárással folytathatja a munkát a napló profiljával, vagy letilthatja azt a diagnosztikai beállításokra való Migrálás előkészítése során.

1. A Azure Portal **Azure monitor** menüjében válassza a **műveletnapló**elemet.
3. Kattintson a **Diagnosztikai beállítások** elemre.

   ![Diagnosztikai beállítások](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. A régi élményhez kattintson a lila szalagcímre.

    ![Korábbi élmény](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
A tevékenység naplójának Log Analytics munkaterületre való gyűjtésének örökölt módszere a munkaterület konfigurációjában található napló csatlakoztatása. 

1. A Azure Portal **log Analytics munkaterületek** menüjében válassza ki a munkaterületet a tevékenység naplójának összegyűjtéséhez.
1. A munkaterület menü **munkaterület adatforrásai** területén válassza az **Azure-tevékenység napló**elemet.
1. Kattintson arra az előfizetésre, amelyhez csatlakozni szeretne.

    ![Munkaterületek](media/activity-log-collect/workspaces.png)

1. Kattintson a **Kapcsolódás** lehetőségre a tevékenység naplójának a kiválasztott munkaterülethez való összekapcsolásához. Ha az előfizetés már egy másik munkaterülethez van csatlakoztatva, kattintson az első **Leválasztás** elemre a leválasztáshoz.

    ![Munkaterületek összekapcsolása](media/activity-log-collect/connect-workspace.png)


A beállítás letiltásához hajtsa végre ugyanezt az eljárást, és kattintson a **Leválasztás** elemre az előfizetés munkaterületről való eltávolításához.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>A tevékenység naplójának elemzése Log Analytics munkaterületen
Amikor egy Log Analytics munkaterülethez kapcsolódik a tevékenység naplójában, a bejegyzéseket a *AzureActivity* nevű táblába írja a rendszer, amelyet egy [napló lekérdezéssel](../log-query/log-query-overview.md)kérhet le. A tábla szerkezete a [naplóbejegyzés kategóriájára](activity-log-view.md#categories-in-the-activity-log)függően változik. Az egyes kategóriák leírását az [Azure Activity napló esemény sémája](activity-log-schema.md) tartalmazza.


### <a name="data-structure-changes"></a>Adatstruktúra-változások
A diagnosztikai beállítások ugyanazokat az adatokat gyűjtik, mint a *AzureActivity* tábla struktúrájának módosításaival a tevékenység naplójának összegyűjtéséhez használt örökölt módszer.

A következő táblázat oszlopai elavultak a frissített sémában. Továbbra is léteznek a *AzureActivity* -ben, de nem lesznek elérhetők. Ezeknek az oszlopoknak a pótlása nem új, de ugyanazokat az adattípusokat tartalmazzák, mint az elavult oszlop. Ezek eltérő formátumúak, ezért előfordulhat, hogy módosítania kell azokat a naplózási lekérdezéseket, amelyek használják azokat. 

| Elavult oszlop | Helyettesítő oszlop |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> Bizonyos esetekben az oszlopok értékei az összes nagybetűvel rendelkezhetnek. Ha olyan lekérdezéssel rendelkezik, amely tartalmazza ezeket az oszlopokat, a [= ~ operátort](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) kell használnia a kis-és nagybetűk megkülönböztetésének összehasonlításához.

A következő oszlop lett hozzáadva a *AzureActivity* -hez a frissített sémában:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Activity logs Analytics monitorozási megoldás
Az Azure Log Analytics-figyelési megoldás hamarosan elavult, és a Log Analytics munkaterület frissített sémájával helyettesíti a munkafüzetet. Továbbra is használhatja a megoldást, ha már engedélyezve van, de csak akkor használható, ha örökölt beállítások használatával gyűjti a tevékenység naplóját. 



### <a name="use-the-solution"></a>A megoldás használata
A figyelési megoldások a Azure Portal **figyelő** menüjében érhetők el. Válassza a **továbbiak** lehetőséget a **betekintési** szakaszban, hogy megnyissa az **Áttekintés** lapot a megoldás csempével. Az **Azure-tevékenység naplói** csempén a munkaterületen található **AzureActivity** -rekordok száma látható.

![Az Azure-tevékenység naplói csempéje](media/collect-activity-logs/azure-activity-logs-tile.png)


Kattintson az **Azure-tevékenységek naplói** csempére az **Azure-tevékenység naplói** nézetének megnyitásához. A nézet a következő táblázatban szereplő vizualizációs részeket tartalmazza. Mindegyik rész legfeljebb 10 olyan elemet sorol fel, amelyek megfelelnek a megadott időtartományra vonatkozó feltételeknek. Futtathat egy olyan naplózási lekérdezést, amely az összes egyező rekordot visszaadja, ha az **összes megtekintése** elemre kattint a rész alján.

![Azure Activity-naplók irányítópultja](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>A megoldás engedélyezése új előfizetésekhez
Hamarosan többé nem fogja tudni felvenni a tevékenység naplóinak elemzési megoldását az előfizetésbe a Azure Portal használatával. A következő eljárással adhat hozzá egy Resource Manager-sablon használatával. 

1. Másolja a következő JSON-t egy *ActivityLogTemplate*. JSON nevű fájlba.

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

2. Telepítse a sablont a következő PowerShell-parancsok használatával:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>További lépések

- További információ a [tevékenység naplóról](platform-logs-overview.md).
- További információ a [Azure monitor adatplatformról](data-platform.md).
- A [napló lekérdezései](../log-query/log-query-overview.md) segítségével megtekintheti a tevékenység naplójának részletes adatait.
