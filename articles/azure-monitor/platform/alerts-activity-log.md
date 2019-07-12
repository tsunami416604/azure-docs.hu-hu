---
title: Létrehozása, megtekintése és kezelése az Azure Monitor tevékenységnapló-riasztások
description: Tevékenységnapló-riasztások létrehozása az Azure Portalon, az Azure Resource Manager-sablon és az Azure PowerShell használatával.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705296"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Létrehozása, megtekintése és a tevékenységnapló-riasztások kezelése az Azure Monitor használatával  

## <a name="overview"></a>Áttekintés
A tevékenységnapló-riasztások a riasztásokat, amelyek aktiválni, ha egy új tevékenységnapló eseményéhez történik, amely megfelel a feltételeknek, a riasztásban feltüntetett.

Ezek a riasztások az Azure-erőforrásokhoz, és az Azure Resource Manager-sablon segítségével hozható létre. Akkor is képes lehet létrehozott, frissített vagy törölt az Azure Portalon. Általában akkor tevékenységnapló-riasztások létrehozása, hogy értesítést kapjon, amikor adott változás következik be az Azure-előfizetés erőforrásainak. Riasztások gyakran hatóköre az adott erőforrás-csoportok vagy erőforrásokat. Például érdemes értesíti, ha bármelyik virtuális gépet a mintául szolgáló erőforráscsoportban **myProductionResourceGroup** törlődik. Vagy előfordulhat, hogy szeretne kaphat, ha egyetlen új szerepkör hozzá van rendelve egy felhasználó az előfizetésében.

> [!IMPORTANT]
> A szolgáltatás állapotával kapcsolatos értesítés figyelmeztetések nem hozhatók létre, a felületen, a tevékenység log riasztás létrehozásához. Hogyan hozhat létre és használhat a szolgáltatás állapotával kapcsolatos értesítésekre kapcsolatos további információkért lásd: [tevékenységnapló-riasztások a szolgáltatás állapotára vonatkozó értesítések fogadásához](alerts-activity-log-service-notifications.md).

Riasztási szabályok létrehozásakor ügyeljen arra, a következőket:

- Az előfizetést, a hatókör nem különbözik az előfizetés ahol a riasztás létrejött.
- A feltételek a szint, állapotot, hívó, erőforráscsoport, erőforrás-azonosító vagy erőforrás típusa az eseménykategóriát, amelyen a riasztás úgy van konfigurálva kell lennie.
- Nincs "anyOf" feltétel vagy beágyazott feltételeknek a riasztás konfigurálásakor JSON. Alapvetően csak egy "allOf" feltétel nem további "allOf" vagy "anyOf" feltételekkel használata engedélyezett.
- Ha "rendszergazda" kategória, adjon meg legalább egy, a fenti feltételek közül a riasztásban. Nem hozható létre egy riasztást, amely minden alkalommal, amikor létrejön egy esemény a Tevékenységnaplókban aktiválja.


## <a name="azure-portal"></a>Azure Portal

Az Azure portal segítségével létrehozása és módosítása tevékenység naplóriasztási szabály. A felhasználói élményt integrálva van egy Azure-tevékenységnapló zökkenőmentes riasztás létrehozása adott események a lényeges biztosítása érdekében.

### <a name="create-with-the-azure-portal"></a>Hozzon létre az Azure portal használatával

Az alábbi eljárással.

1. Az Azure Portalon válassza ki a **figyelő** > **riasztások**.
2. Válassza ki **Új riasztási szabály** bal felső sarkában a **riasztások** ablak.

     ![Új riasztási szabály](media/alerts-activity-log/AlertsPreviewOption.png)

     A **létrehozás szabály** ablak jelenik meg.

      ![Új riasztási szabály-beállítások](media/alerts-activity-log/create-new-alert-rule-options.png)

3. A **riasztási feltétel megadása**, adja meg a következő adatokat, és válassza ki **kész**:

   - **Riasztási cél:** Megtekintheti, és jelölje ki a cél az új riasztás, használja a **előfizetés-szűrő** / **szűrés erőforrástípus szerint**. Az erőforrás vagy erőforráscsoport kijelölhet a listában jelenik meg.

     > [!NOTE]
     > 
     > Kiválaszthatja, hogy egy erőforrás, erőforráscsoport vagy egy tevékenységnaplóbeli egy teljes előfizetés.

     **Riasztási cél minta nézet**

     ![Cél kiválasztása](media/alerts-activity-log/select-target.png)

   - Alatt **feltételek cél**válassza **adja meg a feltételeket**. A cél az összes rendelkezésre álló jelek jelennek meg, amely tartalmazza azokat a különböző kategóriák **tevékenységnapló**. A kategória nevét a rendszer hozzáfűzi a **Monitor Service** nevét.

   - A jel kijelölhet a listában megjelenik a különböző típusú lehetséges műveletek **tevékenységnapló**.

     A naplózási előzmények ütemterv és a cél jel a megfelelő riasztási logika választhatja ki:

     **Feltételek képernyő hozzáadása**

     ![feltételek hozzáadása](media/alerts-activity-log/add-criteria.png)

     - **Előzmények idő**: A kiválasztott művelet eseményeket az utolsó 6, 12 vagy 24 órában, vagy a múlt héten algoritmus.

     - **Riasztási logika**:

       - **Eseményszint**: Az esemény súlyossági szintje: _Részletes_, _tájékoztató_, _figyelmeztetés_, _hiba_, vagy _kritikus_.
       - **Állapot**: Az esemény állapota: _Lépések_, _sikertelen_, vagy _sikeres_.
       - **Esemény kezdeményezője**: A hívó néven is ismert. Az e-mail címet vagy az Azure Active Directory a műveletet végrehajtó felhasználó azonosítója.

       Ez a minta jel diagram a alkalmazni riasztási logika rendelkezik:

       ![kijelölt feltétel](media/alerts-activity-log/criteria-selected.png)

4. A **riasztás részleteinek megadása**, adja meg a következő adatokat:

    - **Riasztási szabály neve**: Az Új riasztási szabály neve.
    - **Leírás**: Az Új riasztási szabály leírását.
    - **A riasztás mentése erőforráscsoportba**: Válassza ki az erőforráscsoport, amelyben az új szabály menteni szeretné.

5. A **műveletcsoport**, a legördülő menüben adja meg a műveletcsoport, amelyet szeretne hozzárendelni az Új riasztási szabály. Vagy, [hozzon létre egy új műveletcsoportot](../../azure-monitor/platform/action-groups.md) és rendelje hozzá az új szabályt. Hozzon létre egy új csoportot, válassza a **+ új csoport**.

6. Létrehozásuk után a szabályok engedélyezéséhez jelölje be **Igen** számára a **engedélyezése a szabály létrehozásakor** lehetőséget.
7. Válassza ki **riasztási szabály létrehozása**.

    A tevékenységnapló Új riasztási szabály jön létre, és az ablak jobb felső sarokban megjelenik egy megerősítő üzenetet.

    Engedélyezi, tiltsa le, szerkesztése vagy egy szabály törlése. További információ a tevékenység szabály kezelése.


Egy egyszerű analógia ismertetése feltételeket, amelyen a tevékenységnapló-riasztási szabályok hozhatók és felfedezni vagy szűrheti az eseményeket a rendszer a [tevékenységnapló az Azure Portalon](activity-log-view.md#azure-portal). Az a **Azure figyelés – tevékenységnapló** képernyő, szűrheti vagy keresse meg a szükséges események és majd a használatával hozzon létre egy riasztást a **tevékenységnapló-riasztás hozzáadása** gombra. Ezután kövesse a bemutatott lépések 4-7, mint korábban.
    
 ![riasztás hozzáadása a tevékenységnaplóról](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Megtekintése és kezelése az Azure Portalon

1. Az Azure Portalon válassza ki a **figyelő** > **riasztások**. Válassza ki **riasztási szabályok kezelése** a az ablak bal felső sarkában.

    ![Riasztási szabályok kezelése](media/alerts-activity-log/manage-alert-rules.png)

    Megjelenik a rendelkezésre álló szabályok listája.

2. Keresse meg a tevékenység log szabály módosítása.

    ![keresési tevékenység naplóriasztási szabály](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    A rendelkezésre álló szűrők _előfizetés_, _erőforráscsoport_, _erőforrás_, _jel típusa_, vagy _állapota_ , és keresse meg a szerkeszteni kívánt tevékenység szabályt.

   > [!NOTE]
   > 
   > Csak szerkesztheti **leírás**, **feltételek cél**, és **Műveletcsoportok**.

3. Válassza ki a szabályt, és kattintson duplán a szabály beállítások szerkesztéséhez. Végezze el a szükséges módosításokat, és válassza ki **mentése**.

   ![Riasztási szabályok kezelése](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Engedélyezze, tiltsa le, vagy egy szabály törlése. Válassza ki a megfelelő lehetőséget az ablak tetején, miután kiválasztotta a szabály a 2. lépésben leírtak szerint.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Tevékenységnapló-riasztás létrehozása Azure Resource Manager-sablon használatával, a típusú erőforrás létrehozása `microsoft.insights/activityLogAlerts`. Ezután, töltse ki az összes kapcsolódó tulajdonságok. Íme egy sablont, amely létrehoz egy tevékenységnapló-riasztás:

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
A fenti példa JSON-t, például sampleActivityLogAlert.json ebben az útmutatóban céljából menthetők, és használatával központilag telepítheti [Azure Resource Manager az Azure Portalon](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Az új tevékenység log riasztási szabály aktiválása akár 5 percet igénybe vehet.

## <a name="rest-api"></a>REST API 
A [Azure figyelése tevékenység Log riasztások API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) egy olyan REST API. Célszerű a teljes mértékben kompatibilisek az Azure Resource Manager REST API-val. Használat PowerShell használatával a Resource Manager parancsmag vagy az Azure CLI használatával.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>A PowerShell használatával a Resource Manager-sablon üzembe helyezése
A minta az előző látható Resource Manager-sablon üzembe helyezése a PowerShell használatával történő [Azure Resource Manager-sablon](#azure-resource-manager-template) területén az alábbi paranccsal:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

ahol a sampleActivityLogAlert.parameters.json a riasztási szabály létrehozásához szükséges paraméterek számára megadott értékeket tartalmazza.

### <a name="use-activity-log-powershell-cmdlets"></a>Tevékenységnapló PowerShell-parancsmagok használata

A tevékenységnapló-riasztások rendelkezhetnek dedikált elérhető PowerShell-parancsmagokkal:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Egy új tevékenységnapló-riasztás létrehozása vagy frissítése egy meglévő tevékenységnapló-riasztás.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Lekérdezi egy vagy több tevékenység log riasztás erőforrásai.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Lehetővé teszi a meglévő tevékenységnapló-riasztás, és beállítja a címkéket.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Letiltja a meglévő tevékenységnapló-riasztás, és beállítja a címkéket.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Eltávolítja a tevékenységnapló-riasztás.

## <a name="azure-cli"></a>Azure CLI

Azure CLI-parancsok mellett a csoport dedikált [az monitor tevékenységnapló riasztás](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) érhetők el tevékenység napló a riasztási szabályok kezelése.

Egy új tevékenység riasztási szabály létrehozásához használja az alábbi parancsokat az itt látható sorrendben:

1. [az monitor tevékenységnapló riasztás létrehozása](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Hozzon létre egy új tevékenység log riasztási szabály erőforrást.
1. [az monitor tevékenységnapló riasztás hatókör](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Adja hozzá a létrehozott tevékenység riasztási szabály hatóköre.
1. [az monitor tevékenységnapló riasztás – műveletcsoport](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Műveletcsoport hozzáadása a tevékenység riasztási szabály.

Riasztási szabály erőforrás egy tevékenység napló lekéréséhez használja az Azure CLI-paranccsal [az monitor tevékenységnapló riasztás megjelenítése](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Egy erőforráscsoportba tartozó összes tevékenység log riasztási szabály erőforrás megtekintéséhez használja [az monitor tevékenységnapló riasztás lista](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Tevékenység log riasztási szabály erőforrásokat az Azure CLI-paranccsal távolíthatók el [az monitor tevékenységnapló riasztás törlése](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>További lépések

- Ismerje meg [tevékenységi naplóit a webhook sémáról](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Olvassa el az [Tevékenységnaplók áttekintése](../../azure-monitor/platform/activity-log-alerts.md).
- Tudjon meg többet [Műveletcsoportok](../../azure-monitor/platform/action-groups.md).  
- Ismerje meg [szolgáltatás állapotára vonatkozó értesítések](../../azure-monitor/platform/service-notifications.md).
