---
title: Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor
description: Hozzon létre műveletnapló-riasztásokat a Azure Portal, egy Azure Resource Manager sablon és a Azure PowerShell használatával.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 06/25/2019
ms.openlocfilehash: 54122d784467bc090d82f4126e03ab688883ea7d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364798"
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


## <a name="azure-portal"></a>Azure portál

A Azure Portal a műveletnapló riasztási szabályainak létrehozására és módosítására használható. A felhasználói élmény az Azure-tevékenység naplójában van integrálva, így biztosítva a zökkenőmentes riasztások létrehozását az adott események esetében.

### <a name="create-with-the-azure-portal"></a>Létrehozás a Azure Portal

Használja az alábbi eljárást.

1. A Azure Portal válassza a **figyelő** > **riasztások**lehetőséget.
2. A **riasztások** ablak bal felső sarkában válassza az **új riasztási szabály** lehetőséget.

     ![Új riasztási szabály](media/alerts-activity-log/AlertsPreviewOption.png)

     Megjelenik a **szabály létrehozása** ablak.

      ![Új riasztási szabály beállításai](media/alerts-activity-log/create-new-alert-rule-options.png)

3. A **riasztási feltétel meghatározása**területen adja meg a következő információkat, majd válassza a **kész**lehetőséget:

   - **Riasztási cél:** Az új riasztás céljának megtekintéséhez és kiválasztásához használja az **előfizetés szerinti szűrés** / **szűrés erőforrás-típus alapján**lehetőséget. Válassza ki az erőforrást vagy erőforráscsoportot a megjelenő listából.

     > [!NOTE]
     > 
     > Csak [Azure Resource Manager](../../azure-resource-manager/management/overview.md) követett erőforrást, erőforráscsoportot vagy teljes előfizetést választhat a tevékenység naplójának jelzéséhez. 

     **Riasztási cél minta nézete**

     ![Cél kiválasztása](media/alerts-activity-log/select-target.png)

   - A **célként megadott feltételek**területen válassza a **feltétel hozzáadása**lehetőséget. Megjelenik a cél összes elérhető jele, amely magában foglalja a különböző kategóriájú **tevékenységek naplóit**. A rendszer hozzáfűzi a kategória nevét a **figyelő szolgáltatás** nevéhez.

   - Válassza ki a típus **tevékenység naplója**számára lehetséges különböző műveletekből megjelenített jelet.

     A naplózási előzmények idővonalát és a hozzá tartozó riasztási logikát kiválaszthatja a cél jelzéshez:

     **Feltételek hozzáadása képernyő**

     ![Feltételek hozzáadása](media/alerts-activity-log/add-criteria.png)

     - **Előzmények időpontja**: a kiválasztott művelethez rendelkezésre álló események az elmúlt 6, 12 vagy 24 órában, vagy az elmúlt héten láthatók.

     - **Riasztási logika**:

       - **Esemény szintje**: az esemény súlyossági szintje: _részletes_, _tájékoztató_, _Figyelmeztetés_, _hiba_vagy _kritikus_.
       - **Állapot**: az esemény állapota: _elindítva_, _sikertelen_, vagy _sikeres_.
       - A (z) **által kezdeményezett esemény**, más néven a hívó. A műveletet végrehajtó felhasználó e-mail-címe vagy Azure Active Directory azonosítója.

       Ez a mintavételi gráf a riasztási logikát alkalmazza:

       ![Kijelölt feltételek](media/alerts-activity-log/criteria-selected.png)

4. A **riasztás részleteinek megadása**területen adja meg a következő adatokat:

    - **Riasztási szabály neve**: az új riasztási szabály neve.
    - **Leírás**: az új riasztási szabály leírása.
    - **Riasztás mentése az erőforráscsoporthoz**: válassza ki azt az erőforráscsoportot, amelybe menteni szeretné ezt az új szabályt.

5. A **Művelettípus**alatt, a legördülő menüből válassza ki azt a műveleti csoportot, amelyet hozzá szeretne rendelni ehhez az új riasztási szabályhoz. Vagy [hozzon létre egy új műveleti csoportot](../../azure-monitor/platform/action-groups.md) , és rendelje hozzá az új szabályhoz. Új csoport létrehozásához válassza az **+ új csoport**lehetőséget.

6. Ha a létrehozás után engedélyezni szeretné a szabályokat, válassza az **Igen** lehetőséget a **szabály engedélyezése a létrehozáskor** beállításnál.
7. Válassza a **Riasztási szabály létrehozása** lehetőséget.

    A rendszer létrehozza a műveletnapló új riasztási szabályát, és egy megerősítő üzenet jelenik meg az ablak jobb felső sarkában.

    Engedélyezheti, letilthatja, szerkesztheti vagy törölheti a szabályokat. További információ a műveletnapló szabályainak kezeléséről.


A tevékenység naplójában a riasztási szabályok létrehozására szolgáló feltételek értelmezése egyszerű analóg, ha az eseményeket a [Azure Portal tevékenységi naplójából](activity-log-view.md#azure-portal)vizsgálja vagy szűri. A **Azure monitor-Activity napló** képernyőn szűrheti vagy megkeresheti a szükséges eseményt, majd létrehozhatja a riasztást a **műveletnapló riasztás hozzáadása** gomb használatával. Ezután kövesse a 4 – 7. lépést a korábban bemutatott módon.
    
 ![Riasztás hozzáadása a tevékenység naplójából](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Megtekintés és kezelés a Azure Portal

1. A Azure Portal válassza a **figyelő** > **riasztások**lehetőséget. Az ablak bal felső sarkában válassza a **riasztási szabályok kezelése** lehetőséget.

    ![Riasztási szabályok kezelése](media/alerts-activity-log/manage-alert-rules.png)

    Megjelenik az elérhető szabályok listája.

2. Keresse meg a módosítandó tevékenység-naplózási szabályt.

    ![Keresési tevékenység naplójának riasztási szabályai](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    A szerkeszteni kívánt tevékenységi szabály megkereséséhez használhatja az elérhető szűrőket, _előfizetést_, _erőforráscsoportot_, _erőforrást_, _jel típusát_vagy _állapotát_.

   > [!NOTE]
   > 
   > Csak a **Leírás**, a **célként megadott feltételek**és a **műveleti csoportok**szerkeszthetők.

3. Válassza ki a szabályt, majd kattintson duplán a szabály beállításainak szerkesztéséhez. Végezze el a szükséges módosításokat, majd kattintson a **Mentés**gombra.

   ![Riasztási szabályok kezelése](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Engedélyezheti, letilthatja vagy törölheti a szabályokat. Válassza ki a megfelelő lehetőséget az ablak tetején, miután kiválasztotta a szabályt a 2. lépésben leírtak szerint.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Ha Azure Resource Manager sablonnal szeretne létrehozni egy műveletnapló-riasztást, hozzon létre egy `microsoft.insights/activityLogAlerts`típusú erőforrást. Ezután töltse ki az összes kapcsolódó tulajdonságot. A következő sablon egy műveletnapló-riasztást hoz létre:

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

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): új műveletnapló riasztást hoz létre, vagy egy meglévő műveletnapló riasztását frissíti.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): egy vagy több műveletnapló riasztási erőforrásának beolvasása.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): lehetővé teszi egy meglévő műveletnapló riasztását, és beállítja annak címkéit.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): letiltja a meglévő műveletnapló riasztását, és beállítja annak címkéit.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): eltávolítja a tevékenység naplójának riasztását.

## <a name="azure-cli"></a>Azure parancssori felület (CLI)

A set az [monitor Activity-log riasztás](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) alatt DEDIKÁLT Azure CLI-parancsok érhetők el a műveletnapló riasztási szabályainak kezeléséhez.

Új műveletnapló riasztási szabály létrehozásához használja a következő parancsokat ebben a sorrendben:

1. [az monitor Activity-log riasztás létrehozása](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): hozzon létre egy új műveletnapló riasztási szabály erőforrást.
1. az [monitor Activity-log riasztás hatóköre](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): a létrehozott műveletnapló riasztási szabály hatókörének hozzáadása.
1. az [monitor Activity-log riasztás műveleti-csoport](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): műveleti csoport hozzáadása a műveletnapló riasztási szabályához.

Az egyik műveletnapló riasztási szabály erőforrásának lekéréséhez használja az Azure CLI-parancsot az [monitor Activity-log Alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)paranccsal. Ha meg szeretné tekinteni az összes műveletnapló riasztási szabályának erőforrását egy erőforráscsoporthoz, használja az [az monitor Activity-log riasztások listáját](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
A műveletnapló riasztási szabályának erőforrásai eltávolíthatók az Azure CLI-paranccsal az [monitor Activity-log riasztás törlése](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)lehetőséggel.

## <a name="next-steps"></a>Következő lépések

- További információ a [tevékenységi naplók webhook-sémájáról](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Olvassa el [a Tevékenységnaplók áttekintését](../../azure-monitor/platform/activity-log-alerts.md).
- További információ a [műveleti csoportokról](../../azure-monitor/platform/action-groups.md).  
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](../../azure-monitor/platform/service-notifications.md).
