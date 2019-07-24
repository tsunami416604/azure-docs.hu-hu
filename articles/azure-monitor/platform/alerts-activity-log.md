---
title: Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor
description: Hozzon létre műveletnapló-riasztásokat a Azure Portal, egy Azure Resource Manager sablon és a Azure PowerShell használatával.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: a7f80698791831b3d4404ea0f687a75c660c2222
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67852748"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával  

## <a name="overview"></a>Áttekintés
A műveletnapló riasztásai olyan riasztások, amelyek akkor jelentkeznek be, amikor egy új műveletnapló esemény következik be, amely megfelel a riasztásban megadott feltételeknek.

Ezek a riasztások az Azure-erőforrásokhoz használhatók, és Azure Resource Manager sablon használatával hozhatók létre. Emellett a Azure Portal is létrehozhatók, frissíthetők és törölhetők. Az Azure-előfizetésben lévő erőforrásokra vonatkozó bizonyos változások esetén a rendszer általában a tevékenység naplójának riasztásait hozza létre az értesítések fogadásához. A riasztásokat gyakran meghatározott erőforráscsoportok vagy erőforrások hatóköre képezi. Előfordulhat például, hogy értesítést szeretne kapni, ha a minta erőforráscsoport **myProductionResourceGroup** található bármely virtuális gép törlődik. Vagy előfordulhat, hogy értesítést szeretne kapni, ha minden új szerepkör hozzá van rendelve egy felhasználóhoz az előfizetésében.

> [!IMPORTANT]
> A szolgáltatás állapotáról szóló értesítés nem hozható létre az illesztőfelületen keresztül a tevékenység naplójának riasztásának létrehozásához. Ha többet szeretne megtudni a szolgáltatás állapotával kapcsolatos értesítések létrehozásáról és használatáról, tekintse meg a következő témakört: [fogadási tevékenység naplójának riasztásai a szolgáltatás állapotáról](alerts-activity-log-service-notifications.md)

Riasztási szabályok létrehozásakor ügyeljen a következőkre:

- A hatókörbeli előfizetés nem különbözik a riasztást létrehozó előfizetéstől.
- A feltételnek az a szint, állapot, hívó, Erőforráscsoport, erőforrás-azonosító vagy erőforrástípus típusú eseménynek kell lennie, amelyen a riasztás konfigurálva van.
- Nincs "anyOf" feltétel vagy beágyazott feltétel a riasztási konfiguráció JSON-fájljában. Alapvetően csak egy "allOf" feltétel engedélyezett a további "allOf" vagy "anyOf" feltételek nélkül.
- Ha a kategória "adminisztratív", akkor a riasztásban meg kell adnia legalább az előző feltétel egyikét. Előfordulhat, hogy nem hoz létre olyan riasztást, amely minden alkalommal aktiválódik, amikor létrejön egy esemény a tevékenység naplóiban.


## <a name="azure-portal"></a>Azure Portal

A Azure Portal a műveletnapló riasztási szabályainak létrehozására és módosítására használható. A felhasználói élmény az Azure-tevékenység naplójában van integrálva, így biztosítva a zökkenőmentes riasztások létrehozását az adott események esetében.

### <a name="create-with-the-azure-portal"></a>Létrehozás a Azure Portal

Használja az alábbi eljárást.

1. A Azure Portal válassza a > **riasztások** **figyelése**lehetőséget.
2. A **riasztások** ablak bal felső sarkában válassza az **új riasztási szabály** lehetőséget.

     ![Új riasztási szabály](media/alerts-activity-log/AlertsPreviewOption.png)

     Megjelenik a **szabály létrehozása** ablak.

      ![Új riasztási szabály beállításai](media/alerts-activity-log/create-new-alert-rule-options.png)

3. A **riasztási feltétel meghatározása**területen adja meg a következő információkat, majd válassza a **kész**lehetőséget:

   - **Riasztási cél:** Az új riasztás céljának megtekintéséhez és kiválasztásához használja az **előfizetés** / szerinti szűrés az**erőforrástípus alapján értéket**. Válassza ki az erőforrást vagy erőforráscsoportot a megjelenő listából.

     > [!NOTE]
     > 
     > Csak [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) követett erőforrást, erőforráscsoportot vagy teljes előfizetést választhat a tevékenység naplójának jelzéséhez. 

     **Riasztási cél minta nézete**

     ![Cél kiválasztása](media/alerts-activity-log/select-target.png)

   - A **célként megadott feltételek**területen válassza a **feltétel hozzáadása**lehetőséget. Megjelenik a cél összes elérhető jele, amely magában foglalja a különböző kategóriájú **tevékenységek naplóit**. A rendszer hozzáfűzi a kategória nevét a **figyelő szolgáltatás** nevéhez.

   - Válassza ki a típus **tevékenység naplója**számára lehetséges különböző műveletekből megjelenített jelet.

     A naplózási előzmények idővonalát és a hozzá tartozó riasztási logikát kiválaszthatja a cél jelzéshez:

     **Feltételek hozzáadása képernyő**

     ![Feltételek hozzáadása](media/alerts-activity-log/add-criteria.png)

     - **Előzmények időpontja**: A kiválasztott művelethez elérhető események az elmúlt 6, 12 vagy 24 órában, vagy az elmúlt héten láthatók.

     - **Riasztási logika**:

       - **Esemény szintje**: Az esemény súlyossági szintje: _Részletes_, _tájékoztató_, _Figyelmeztetés_, _hiba_vagy _kritikus_.
       - **Állapot**: Az esemény állapota: Elindítva, _sikertelen_, vagy _sikeres_.
       - **Esemény kezdeményezője**: Más néven a hívó. A műveletet végrehajtó felhasználó e-mail-címe vagy Azure Active Directory azonosítója.

       Ez a mintavételi gráf a riasztási logikát alkalmazza:

       ![Kijelölt feltételek](media/alerts-activity-log/criteria-selected.png)

4. A **riasztás részleteinek megadása**területen adja meg a következő adatokat:

    - **Riasztási szabály neve**: Az új riasztási szabály neve.
    - **Leírás**: Az új riasztási szabály leírása.
    - **Riasztás mentése az erőforráscsoporthoz**: Válassza ki azt az erőforráscsoportot, amelybe menteni szeretné az új szabályt.

5. A **Művelettípus**alatt, a legördülő menüből válassza ki azt a műveleti csoportot, amelyet hozzá szeretne rendelni ehhez az új riasztási szabályhoz. Vagy [hozzon létre egy új műveleti csoportot](../../azure-monitor/platform/action-groups.md) , és rendelje hozzá az új szabályhoz. Új csoport létrehozásához válassza az **+ új csoport**lehetőséget.

6. Ha a létrehozás után engedélyezni szeretné a szabályokat, válassza az **Igen** lehetőséget a **szabály engedélyezése** a létrehozáskor beállításnál.
7. Válassza a **riasztási szabály létrehozása**lehetőséget.

    A rendszer létrehozza a műveletnapló új riasztási szabályát, és egy megerősítő üzenet jelenik meg az ablak jobb felső sarkában.

    Engedélyezheti, letilthatja, szerkesztheti vagy törölheti a szabályokat. További információ a műveletnapló szabályainak kezeléséről.


A tevékenység naplójában a riasztási szabályok létrehozására szolgáló feltételek értelmezése egyszerű analóg, ha az eseményeket a [Azure Portal tevékenységi naplójából](activity-log-view.md#azure-portal)vizsgálja vagy szűri. A **Azure monitor-Activity napló** képernyőn szűrheti vagy megkeresheti a szükséges eseményt, majd létrehozhatja a riasztást a **műveletnapló riasztás hozzáadása** gomb használatával. Ezután kövesse a 4 – 7. lépést a korábban bemutatott módon.
    
 ![Riasztás hozzáadása a tevékenység naplójából](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Megtekintés és kezelés a Azure Portal

1. A Azure Portal válassza a > **riasztások** **figyelése**lehetőséget. Az ablak bal felső sarkában válassza a **riasztási szabályok kezelése** lehetőséget.

    ![Riasztási szabályok kezelése](media/alerts-activity-log/manage-alert-rules.png)

    Megjelenik az elérhető szabályok listája.

2. Keresse meg a módosítandó tevékenység-naplózási szabályt.

    ![Keresési tevékenység naplójának riasztási szabályai](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    A szerkeszteni kívánt tevékenységi szabály megkereséséhez használhatja az elérhető szűrőket, előfizetést, _erőforráscsoportot_, _erőforrást_, _jel típusát_vagy _állapotát_.

   > [!NOTE]
   > 
   > Csak a **Leírás**, a **célként megadott feltételek**és a **műveleti csoportok**szerkeszthetők.

3. Válassza ki a szabályt, majd kattintson duplán a szabály beállításainak szerkesztéséhez. Végezze el a szükséges módosításokat, majd kattintson a **Mentés**gombra.

   ![Riasztási szabályok kezelése](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Engedélyezheti, letilthatja vagy törölheti a szabályokat. Válassza ki a megfelelő lehetőséget az ablak tetején, miután kiválasztotta a szabályt a 2. lépésben leírtak szerint.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Ha Azure Resource Manager sablonnal szeretne létrehozni egy műveletnapló-riasztást, hozzon létre egy típusú `microsoft.insights/activityLogAlerts`erőforrást. Ezután töltse ki az összes kapcsolódó tulajdonságot. A következő sablon egy műveletnapló-riasztást hoz létre:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Az előző minta JSON például a sampleActivityLogAlert. JSON fájlba menthető, és a Azure Resource Manager használatával telepíthető [a Azure Portalban](../../azure-resource-manager/resource-group-template-deploy-portal.md), és üzembe helyezhető.

> [!NOTE]
> Akár 5 percet is igénybe vehet, amíg az új tevékenység naplójának riasztási szabálya aktívvá válik.

## <a name="rest-api"></a>REST API 
A [Azure monitor Activity log-riasztások API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) egy REST API. A Azure Resource Manager REST API teljes mértékben kompatibilis. A PowerShell használatával a Resource Manager-parancsmaggal vagy az Azure CLI-vel is használható.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>A Resource Manager-sablon üzembe helyezése a PowerShell-lel
Ha a PowerShell használatával szeretné telepíteni az előző [Azure Resource Manager sablonban](#azure-resource-manager-template) látható Resource Manager-sablont, használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

ahol a sampleActivityLogAlert. Parameters. JSON tartalmazza a riasztási szabály létrehozásához szükséges paraméterekhez megadott értékeket.

### <a name="use-activity-log-powershell-cmdlets"></a>A log PowerShell-parancsmagok használata

A műveletnapló riasztásai dedikált PowerShell-parancsmagokat is elérhetők:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Új műveletnapló-riasztást hoz létre, vagy egy meglévő műveletnapló riasztását frissíti.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Egy vagy több tevékenység naplójának riasztási erőforrásának beolvasása.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Engedélyezi egy meglévő műveletnapló riasztását, és beállítja annak címkéit.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Letiltja egy meglévő műveletnapló-riasztást, és beállítja annak címkéit.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Eltávolítja a tevékenység naplójának riasztását.

## <a name="azure-cli"></a>Azure CLI

A set az [monitor Activity-log riasztás](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) alatt DEDIKÁLT Azure CLI-parancsok érhetők el a műveletnapló riasztási szabályainak kezeléséhez.

Új műveletnapló riasztási szabály létrehozásához használja a következő parancsokat ebben a sorrendben:

1. [az figyelő tevékenység-napló riasztás létrehozása](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Hozzon létre egy új műveletnapló riasztási szabályának erőforrását.
1. [az monitor Activity-log riasztás hatóköre](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Hatókör hozzáadása a létrehozott tevékenység naplójának riasztási szabályához.
1. [az monitor Activity-log riasztás műveleti-csoport](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Adjon hozzá egy műveleti csoportot a tevékenység naplójának riasztási szabályához.

Az egyik műveletnapló riasztási szabály erőforrásának lekéréséhez használja az Azure [CLI-parancsot az monitor Activity-](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)log Alert show paranccsal. Ha meg szeretné tekinteni az összes műveletnapló riasztási szabályának erőforrását egy erőforráscsoporthoz, használja az [az monitor Activity-log riasztások listáját](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
A műveletnapló riasztási szabályának erőforrásai eltávolíthatók az Azure CLI-paranccsal az [monitor Activity-log riasztás törlése](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)lehetőséggel.

## <a name="next-steps"></a>További lépések

- További információ a [tevékenységi naplók webhook](../../azure-monitor/platform/activity-log-alerts-webhook.md)-sémájáról.
- Olvassa el [a Tevékenységnaplók áttekintését](../../azure-monitor/platform/activity-log-alerts.md).
- További információ a [műveleti csoportokról](../../azure-monitor/platform/action-groups.md).  
- Tudnivalók a [szolgáltatás állapotával](../../azure-monitor/platform/service-notifications.md)kapcsolatos értesítésekről.
