---
title: Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése az Azure Monitorban
description: Tevékenységnapló-riasztásokat hozhat létre az Azure Portal, egy Azure Resource Manager-sablon és az Azure PowerShell használatával.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132393"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával  

## <a name="overview"></a>Áttekintés

A tevékenységnapló-riasztások azok a riasztások, amelyek akkor aktiválódnak, ha olyan új tevékenységnapló-esemény következik be, amely megfelel a riasztásban megadott feltételeknek.

Ezek a riasztások az Azure-erőforrások, és egy Azure Resource Manager sablon használatával hozható konklisen. Ők is létrehozható, frissíthető vagy törölhetők az Azure Portalon. Általában tevékenységnapló-riasztásokat hoz létre, hogy értesítéseket kapjon, ha az Azure-előfizetés erőforrásaiban bizonyos módosítások történnek. A riasztások hatóköre gyakran adott erőforráscsoportokra vagy erőforrásokra terjed ki. Például érdemes lehet értesítést kapni, ha a minta erőforráscsoport **ban myProductionResourceGroup** virtuális gép törlődik. Vagy érdemes lehet értesítést kapnia, ha új szerepkörök vannak hozzárendelve egy felhasználóhoz az előfizetésben.

> [!IMPORTANT]
> A szolgáltatás állapotértesítésére vonatkozó riasztások nem hozhatók létre a tevékenységnapló-riasztás létrehozásához. Ha többet szeretne tudni a szolgáltatásállapot-értesítések létrehozásáról és használatáról, olvassa [el a Tevékenységnapló-értesítések fogadása a szolgáltatásállapot-értesítésekről című témakört.](alerts-activity-log-service-notifications.md)

Amikor riasztási szabályokat hoz létre, győződjön meg a következőkről:

- A hatókörben lévő előfizetés nem különbözik attól az előfizetéstől, ahol a riasztás jön létre.
- A feltételeknek annak a szintnek, állapotnak, hívónak, erőforráscsoportnak, erőforrás-azonosítónak vagy erőforrástípus-eseménykategóriának kell lenniük, amelyen a riasztás konfigurálva van.
- Nincs "anyOf" feltétel vagy beágyazott feltételek a json-konfigurációban. Alapvetően csak egy "allOf" feltétel engedélyezett további "allOf" vagy "anyOf" feltételek nélkül.
- Ha a kategória "felügyeleti", meg kell adnia legalább az egyik előző feltételt a riasztásban. Előfordulhat, hogy nem hoz létre olyan riasztást, amely minden alkalommal aktiválódik, amikor egy esemény jön létre a tevékenységnaplókban.

## <a name="azure-portal"></a>Azure portál

Az Azure Portal segítségével létrehozhat és módosíthat tevékenységnapló-riasztási szabályokat. A felhasználói felület integrálva van egy Azure-tevékenységnaplóval, amely biztosítja a zökkenőmentes riasztás létrehozását az adott érdeklődésre számot tartó eseményekhez.

### <a name="create-with-the-azure-portal"></a>Létrehozás az Azure Portalon

Kövesse az alábbi eljárást.

1. Az Azure Portalon válassza a **Riasztások figyelése** > **Alerts**lehetőséget.
2. Válassza az **Új riasztási szabály** lehetőséget a **Riasztások** ablak bal felső sarkában.

     ![Új riasztási szabály](media/alerts-activity-log/AlertsPreviewOption.png)

     Megjelenik **a Szabály létrehozása** ablak.

      ![Új riasztási szabály beállításai](media/alerts-activity-log/create-new-alert-rule-options.png)

3. A **Riasztási feltétel megadása csoportban**adja meg a következő információkat, és válassza a **Kész**lehetőséget:

   - **Riasztási cél:** Az új riasztás céljának megtekintéséhez és kiválasztásához használja a **Szűrés előfizetés** / **szerint erőforrástípus szerint**parancsot. Válassza ki az erőforrást vagy erőforráscsoportot a megjelenített listából.

     > [!NOTE]
     > 
     > Csak az [Azure Resource Manager](../../azure-resource-manager/management/overview.md) nyomon követett erőforrást, erőforráscsoportot vagy egy teljes előfizetést választhat ki egy tevékenységnapló-jelhez. 

     **Riasztási cél mintanézete**

     ![Cél kiválasztása](media/alerts-activity-log/select-target.png)

   - A **Célfeltételek csoportban**válassza **a Feltétel hozzáadása**lehetőséget. A célhoz rendelkezésre álló összes jel megjelenik, amely a **tevékenységnapló**különböző kategóriáiból származójeleket tartalmazza. A kategória neve hozzáfűzi a **figyelő szolgáltatás** nevét.

   - Válassza ki a jelet a **Tevékenységnapló**típushoz lehetséges különböző műveletek listájából.

     Kiválaszthatja a naplóelőzmények idővonalát és a céljel megfelelő riasztási logikáját:

     **Feltétel képernyő hozzáadása**

     ![Feltételek hozzáadása](media/alerts-activity-log/add-criteria.png)

     - **Előzmények ideje**: A kiválasztott művelethez elérhető események az elmúlt 6, 12 vagy 24 órában vagy az elmúlt héten ábrázolhatók.

     - **Riasztási logika:**

       - **Eseményszint**: Az esemény súlyossági szintje: _Részletes_, _Tájékoztató_, _Figyelmeztetés_, _Hiba_vagy _Kritikus_.
       - **Állapot**: Az esemény állapota: _Elindítva_, _Sikertelen_vagy _Sikeres_.
       - **Esemény által kezdeményezett:** Más néven a hívó. A műveletet végző felhasználó e-mail-címe vagy Azure Active Directory-azonosítója.

       Ez a mintajelgrafikon a riasztási logikát alkalmazza:

       ![Kijelölt feltételek](media/alerts-activity-log/criteria-selected.png)

4. A **Riasztás részleteinek megadása**csoportban adja meg a következő adatokat:

    - **Riasztási szabály neve**: Az új riasztási szabály neve.
    - **Leírás**: Az új riasztási szabály leírása.
    - **Riasztás mentése az erőforráscsoportba**: Jelölje ki azt az erőforráscsoportot, amelybe menteni szeretné az új szabályt.

5. A **Művelet csoport**csoport legördülő menüjében adja meg az új riasztási szabályhoz rendelni kívánt műveletcsoportot. Vagy [hozzon létre egy új műveletcsoportot,](../../azure-monitor/platform/action-groups.md) és rendelje hozzá az új szabályhoz. Új csoport létrehozásához válassza a **+ Új csoport lehetőséget.**

6. Ha a szabályok létrehozását követően engedélyezni szeretné, válassza az **Igen** lehetőséget a **Szabály engedélyezése létrehozáskor** beállításhoz.
7. Válassza a **Riasztási szabály létrehozása** lehetőséget.

    Létrejön a tevékenységnapló új riasztási szabálya, és egy megerősítő üzenet jelenik meg az ablak jobb felső sarkában.

    Szabály engedélyezhető, letilthatja, szerkesztheti vagy törölheti a szabályokat. További információ a tevékenységnapló-szabályok kezeléséről.


Egy egyszerű analógia a feltételek megértéséhez, amelyek riasztási szabályok hozhatók létre egy tevékenységnaplóban, hogy feltárja vagy szűrje az eseményeket [az Azure Portalon lévő tevékenységnaplón](activity-log-view.md#azure-portal)keresztül. Az **Azure Monitor – Tevékenységnapló** képernyőn szűrheti vagy megtalálhatja a szükséges eseményt, majd létrehozhat egy riasztást a **Tevékenységnapló riasztáshozzáadása** gomb használatával. Ezután kövesse a 4-7.
    
 ![Értesítés hozzáadása a tevékenységnaplóból](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Megtekintés és kezelés az Azure Portalon

1. Az Azure Portalon válassza a **Riasztások figyelése** > **Alerts**lehetőséget. Válassza **a Riasztási szabályok kezelése lehetőséget** az ablak bal felső sarkában.

    ![Riasztási szabályok kezelése](media/alerts-activity-log/manage-alert-rules.png)

    Megjelenik az elérhető szabályok listája.

2. Keresse meg a módosítani kívánt tevékenységnapló-szabályt.

    ![Keresési tevékenységnapló riasztási szabályai](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    A szerkesztni kívánt tevékenységszabály megkereséséhez használhatja a rendelkezésre álló szűrőket, az _Előfizetés_, _az Erőforrás csoport_, az _Erőforrás_, a _Jeltípus_vagy az _Állapot_szűrőt.

   > [!NOTE]
   > 
   > Csak **a Leírás**, **A Célfeltételek**és a **Műveletcsoportok**szerkeszthetők.

3. Jelölje ki a szabályt, és kattintson duplán a szabálybeállítások szerkesztéséhez. Hajtsa végre a szükséges módosításokat, majd válassza a **Mentés gombot.**

   ![Riasztási szabályok kezelése](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Szabály engedélyezhető, letiltható vagy törölhető. A 2.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Ha egy Azure Resource Manager-sablon használatával szeretne létrehozni egy tevékenységnapló-riasztási szabályt, hozzon létre egy típusú `microsoft.insights/activityLogAlerts`erőforrást. Ezután töltse ki az összes kapcsolódó tulajdonságot. Az alábbiakban egy olyan sablont, amely tevékenységnapló-riasztási szabályt hoz létre:

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
Az előző minta JSON menthető például sampleActivityLogAlert.json a forgatókönyv céljából, és az Azure Resource Manager használatával telepíthető [az Azure Portalon.](../../azure-resource-manager/templates/deploy-portal.md)

A következő mezők az Azure Resource Manager sablonban a feltételek mezőihez használható beállítások: Figyelje meg, hogy a "Resource Health", a "Advisor" és a "Service Health" további tulajdonságmezőkkel rendelkezik a speciális mezőkhöz. 
1. resourceId: Az érintett erőforrás erőforrásazonosítója a tevékenységnapló-eseményben, amelyen a riasztást létre kell hozni.
2. kategória: A tevékenységnapló-esemény kategóriája. Például: Felügyeleti, ServiceHealth, ResourceHealth, Automatikus skálázás, Biztonság, Javaslat, Szabályzat.
3. hívó: A tevékenységnapló-esemény működését végző felhasználó e-mail-címe vagy Azure Active Directory-azonosítója.
4. szint: A tevékenység szintje a tevékenységnapló eseményében, amelyen a riasztást létre kell hozni. Például: Kritikus, Hiba, Figyelmeztetés, Tájékoztató, Részletes.
5. operationName: A művelet neve a tevékenységnapló-eseményben. Például: Microsoft.Resources/deployments/write
6. resourceGroup: Az érintett erőforrás erőforráscsoportjának neve a tevékenységnapló-eseményben.
7. resourceProvider: [Azure erőforrás-szolgáltatók és típusok magyarázata](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Az erőforrás-szolgáltatókat az Azure-szolgáltatásokhoz rendelő lista az [Azure-szolgáltatások erőforrás-szolgáltatói című](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)témakörben található.
8. állapot: A tevékenységesemény ben a művelet állapotát leíró karakterlánc. Például: Elindítva, Folyamatban, Sikeres, Sikertelen, Aktív, Megoldva
9. subStatus: Általában a megfelelő REST-hívás HTTP-állapotkódja, de tartalmazhat más, alállapotot leíró karakterláncokat is.   Például: OK (HTTP állapotkód: 200), Létrehozva (HTTP-állapotkód: 201), Elfogadva (HTTP-állapotkód: 202), Nincs tartalom (HTTP-állapotkód: 204), Rossz kérés (HTTP-állapotkód: 400), Nem található (HTTP állapotkód: 404), Ütközés (HTTP-állapotkód: 409), Belső kiszolgáló Hiba (HTTP-állapotkód: 500), A szolgáltatás nem érhető el (HTTP-állapotkód: 503), átjáró időkiírása (HTTP-állapotkód: 504).
10. resourceType: Az esemény által érintett erőforrás típusa. Például: Microsoft.Resources/deployments

Példa:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
További részletek a tevékenységnapló mezőkről [itt](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)található.



> [!NOTE]
> Az új tevékenységnapló-riasztási szabály aktívvá válása akár 5 percet is igénybe vehet.

## <a name="rest-api"></a>REST API 
Az [Azure Monitor tevékenységnapló-riasztások API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) egy REST API. Teljes mértékben kompatibilis az Azure Resource Manager REST API-val. A PowerShell-en keresztül a Resource Manager-parancsmag vagy az Azure CLI használatával használható.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Az Erőforrás-kezelő sablon központi telepítése a PowerShell használatával
Ha a PowerShell használatával szeretné telepíteni az előző [Azure Resource Manager-sablonszakaszban](#azure-resource-manager-template) látható minta Erőforrás-kezelő sablont, használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

ahol a sampleActivityLogAlert.parameters.json tartalmazza a riasztási szabály létrehozásához szükséges paraméterekhez megadott értékeket.

### <a name="use-activity-log-powershell-cmdlets"></a>Tevékenységnapló használata PowerShell-parancsmagok használata

A tevékenységnapló-riasztások dedikált PowerShell-parancsmagokkal rendelkeznek:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Új tevékenységnapló-riasztást hoz létre, vagy egy meglévő tevékenységnapló-riasztást frissít.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Egy vagy több tevékenységnapló-riasztási erőforrás t kap.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Meglévő tevékenységnapló-riasztás engedélyezése és a címkék beállítja.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Letiltja a meglévő tevékenységnapló-riasztást, és beállítja a címkéket.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Eltávolítja a tevékenységnapló-riasztást.

## <a name="azure-cli"></a>Azure CLI

Dedikált Azure CLI-parancsok a beállított [az monitor tevékenységnapló-riasztás](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) i érhetők el a tevékenységnapló riasztási szabályok kezelésére.

Új tevékenységnapló-riasztási szabály létrehozásához használja a következő parancsokat ebben a sorrendben:

1. [az monitor tevékenységnapló-riasztás létrehozása:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)Hozzon létre egy új tevékenységnapló riasztási szabály erőforrást.
1. [az monitor tevékenységnapló-riasztási hatókör:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)Hatókör hozzáadása a létrehozott tevékenységnapló-riasztási szabályhoz.
1. [az figyelheti a tevékenységnapló-riasztási műveletcsoportot:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)Műveletcsoport hozzáadása a tevékenységnapló riasztási szabályához.

Egy tevékenységnapló-riasztási szabály erőforrás ának lekéréséhez használja az Azure CLI parancsot, az [az figyelési tevékenységnapló-riasztási megjelenítést.](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) Az erőforráscsoport összes tevékenységnapló-riasztási szabályának erőforrásának megtekintéséhez használja [az az figyelési tevékenységnapló-riasztási listát.](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)
A tevékenységnapló riasztási szabályerőforrásai az Azure CLI parancs használatával eltávolíthatók, amely figyeli [a figyelési tevékenységnapló-riasztás törlését.](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)

## <a name="next-steps"></a>További lépések

- További információ [a tevékenységnaplók webhook-sémájáról.](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- Olvassa el [a tevékenységnaplók áttekintését.](../../azure-monitor/platform/activity-log-alerts.md)
- További információ a [műveletcsoportokról](../../azure-monitor/platform/action-groups.md).  
- További információ a [szolgáltatásállapot-értesítésekről.](../../azure-monitor/platform/service-notifications.md)
