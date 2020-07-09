---
title: Erőforrások zárolása a módosítások megakadályozása érdekében
description: Megakadályozhatja, hogy a felhasználók a kritikus Azure-erőforrások frissítését vagy törlését az összes felhasználó és szerepkör zárolásának alkalmazásával.
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 7fe735cf523758f51fd9d6751de8507b2af46737
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057585"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Erőforrások zárolása a váratlan módosítások megelőzése érdekében

Rendszergazdaként szüksége lehet egy előfizetés, erőforráscsoport vagy erőforrás zárolására annak érdekében, hogy a szervezet többi felhasználója ne tudja véletlenül törölni vagy módosítani a kritikus fontosságú erőforrásokat. A zárolási szintet **CanNotDelete** (nem törölhető) vagy **ReadOnly** (csak olvasható) értékre állíthatja be. A portálon a zárolások neve **Törlés** és **csak olvasható** .

* A **CanNotDelete** azt jelzi, hogy a jogosult felhasználók továbbra is olvashatják és módosíthatják az erőforrásokat, de nem tudják törölni az erőforrást.
* A **readonly** érték azt jelenti, hogy a jogosult felhasználók olvasni tudnak egy erőforrást, de nem tudják törölni vagy frissíteni az erőforrást. A zárolás alkalmazása hasonló ahhoz, hogy korlátozza az összes jogosult felhasználót az **olvasó** szerepkör által megadott engedélyekkel.

## <a name="how-locks-are-applied"></a>A zárolások alkalmazása

Ha egy fölérendelt hatókörben zárolja a zárolást, akkor a hatókörben lévő összes erőforrás örökli ugyanazt a zárolást. A később hozzáadott erőforrások még a szülőtől öröklik a zárolást. Az öröklés legszigorúbb zárolása elsőbbséget élvez.

A felügyeleti zárolás a szerepköralapú hozzáférés-vezérléssel szemben minden felhasználóra és szerepkörre érvényes korlátozásokat alkalmaz. További információ a felhasználók és szerepkörök engedélyeinek beállításáról: [Azure szerepköralapú Access Control](../../role-based-access-control/role-assignments-portal.md).

A Resource Manager zárolásai csak a felügyeleti síkon történő műveletekre érvényesek, ezek pedig a `https://management.azure.com` címre küldött műveletek. A zárolások nem korlátozzák, hogy az erőforrások hogyan végzik saját funkcióikat. Az erőforrás változásai korlátozva vannak, de az erőforrás működése nincs korlátozva. Egy SQL Database írásvédett zárolása például megakadályozza az adatbázis törlését vagy módosítását. Nem akadályozza meg azonban az adatok létrehozását, frissítését és törlését az adatbázison belül. Az adattranzakciók engedélyezve vannak, mert ezek a műveletek nem lesznek elküldve a `https://management.azure.com` webhelyre.

## <a name="considerations-before-applying-locks"></a>Szempontok a zárolások alkalmazása előtt

A zárolások alkalmazása váratlan eredményekhez vezethet, mert egyes olyan műveletek, amelyek látszólag nem módosítják az erőforrást, ténylegesen megkövetelik a zárolás által letiltott műveleteket. Néhány gyakori példa a zárolások által blokkolt műveletekre:

* A **Storage-fiók** írásvédett zárolása megakadályozza, hogy minden felhasználó hozzáférjen a kulcsokhoz. A kulcsok listázásának művelete POST kérelmen keresztül történik, mert a visszaadott kulcsok írási műveletekkel elérhetők.

* Egy **app Service** erőforrás írásvédett zárolása megakadályozza, hogy a Visual Studio Server Explorer megjelenítse az erőforráshoz tartozó fájlokat, mert az interakcióhoz írási hozzáférés szükséges.

* Egy olyan **erőforráscsoport** írásvédett zárolása, amely egy **virtuális gépet** tartalmaz, megakadályozza, hogy minden felhasználó elindítsa vagy újraindítsa a virtuális gépet. Ezeknek a műveleteknek POST kérelemre van szükségük.

* Egy **erőforráscsoport** nem törölhető zárolása megakadályozza, hogy Azure Resource Manager az előzményekben lévő [központi telepítések automatikus törlését](../templates/deployment-history-deletions.md) . Ha az előzményekben eléri a 800-es üzemelő példányokat, az üzemelő példányok sikertelenek lesznek.

* A **Azure Backup szolgáltatás** által létrehozott **erőforráscsoport** nem törölhető zárolása miatt a biztonsági mentések sikertelenek lesznek. A szolgáltatás legfeljebb 18 visszaállítási pontot támogat. Zárolt állapotban a Backup szolgáltatás nem tudja törölni a visszaállítási pontokat. További információk: [Gyakori kérdések – Azure-beli virtuális gépek biztonsági mentése](../../backup/backup-azure-vm-backup-faq.md).

* Az **előfizetés** írásvédett zárolása megakadályozza, hogy a **Azure Advisor** megfelelően működjön. Az Advisor nem tudja tárolni a lekérdezések eredményét.

## <a name="who-can-create-or-delete-locks"></a>Kik hozhatnak létre vagy törölhetnek zárolásokat

Felügyeleti zárolások létrehozásához vagy törléséhez hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/*` vagy a `Microsoft.Authorization/locks/*` műveletekhez. A beépített szerepkörök esetén ezek a műveletek csak a **Tulajdonosi** és a **Felhasználói hozzáférés rendszergazdájának** vannak engedélyezve.

## <a name="managed-applications-and-locks"></a>Felügyelt alkalmazások és zárolások

Bizonyos Azure-szolgáltatások, például a Azure Databricks a szolgáltatás megvalósításához a [felügyelt alkalmazásokat](../managed-applications/overview.md) használják. Ebben az esetben a szolgáltatás két erőforráscsoportot hoz létre. Egy erőforráscsoport a szolgáltatás áttekintését tartalmazza, és nincs zárolva. A másik erőforráscsoport tartalmazza a szolgáltatás infrastruktúráját, és zárolva van.

Ha megpróbálja törölni az infrastruktúra-erőforráscsoportot, hibaüzenet jelenik meg arról, hogy az erőforráscsoport zárolva van. Ha megpróbálja törölni az infrastruktúra-erőforráscsoport zárolását, a rendszer hibaüzenetet kap arról, hogy a zárolás nem törölhető, mert egy rendszeralkalmazás tulajdonosa.

Ehelyett törölje a szolgáltatást, amely az infrastruktúra-erőforráscsoportot is törli.

Felügyelt alkalmazások esetében válassza ki a telepített szolgáltatást.

![Szolgáltatás kiválasztása](./media/lock-resources/select-service.png)

Figyelje meg, hogy a szolgáltatás tartalmaz egy hivatkozást egy **felügyelt erőforráscsoporthoz**. Ez az erőforráscsoport tárolja az infrastruktúrát, és zárolva van. Nem lehet közvetlenül törölni.

![Felügyelt csoport megjelenítése](./media/lock-resources/show-managed-group.png)

A szolgáltatás összes elemének törléséhez, beleértve a zárolt infrastruktúra erőforráscsoportot is, válassza a **Törlés** lehetőséget a szolgáltatáshoz.

![Szolgáltatás törlése](./media/lock-resources/delete-service.png)

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Sablon

Ha Resource Manager-sablont használ a zárolás üzembe helyezéséhez, a név és a típus eltérő értékeket használ a zárolás hatóköre alapján.

Ha egy **erőforráshoz**zárolást alkalmaz, használja a következő formátumokat:

* neve`{resourceName}/Microsoft.Authorization/{lockName}`
* típusa`{resourceProviderNamespace}/{resourceType}/providers/locks`

Ha egy **erőforráscsoport** vagy **előfizetés**esetében zárolást alkalmaz, használja a következő formátumokat:

* neve`{lockName}`
* típusa`Microsoft.Authorization/locks`

Az alábbi példa egy olyan sablont mutat be, amely egy app Service-csomagot, egy webhelyet és egy zárolást hoz létre a webhelyen. A zárolás erőforrástípus a zárolási és **/providers/Locks**erőforrás típusa. A zárolás neve úgy jön létre, hogy összefűzi az erőforrás nevét a **/Microsoft.Authorization/** és a zárolás nevével.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2019-08-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2019-08-01",
      "name": "[variables('siteName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Web/sites/providers/locks",
      "apiVersion": "2016-09-01",
      "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

Az erőforráscsoportok zárolásának beállítására példát a következő témakörben talál: [erőforráscsoport létrehozása és zárolása](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell

A [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) parancs használatával zárolja a telepített erőforrásokat a Azure PowerShell.

Egy erőforrás zárolásához adja meg az erőforrás nevét, típusát és az erőforráscsoport nevét.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Egy erőforráscsoport zárolásához adja meg az erőforráscsoport nevét.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

A zárolással kapcsolatos információk beszerzéséhez használja a [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Az előfizetés összes zárolásának beszerzéséhez használja a következőt:

```azurepowershell-interactive
Get-AzResourceLock
```

Egy erőforrás összes zárolásának beszerzéséhez használja a következőt:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Egy erőforráscsoport összes zárolásának beszerzéséhez használja a következőt:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

A zárolás törléséhez használja a következőt:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Az üzembe helyezett erőforrásokat az az [Lock Create](/cli/azure/lock#az-lock-create) paranccsal zárolhatja az Azure CLI használatával.

Egy erőforrás zárolásához adja meg az erőforrás nevét, típusát és az erőforráscsoport nevét.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Egy erőforráscsoport zárolásához adja meg az erőforráscsoport nevét.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

A zárolással kapcsolatos információk lekéréséhez használja [az az Lock List](/cli/azure/lock#az-lock-list). Az előfizetés összes zárolásának beszerzéséhez használja a következőt:

```azurecli
az lock list
```

Egy erőforrás összes zárolásának beszerzéséhez használja a következőt:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Egy erőforráscsoport összes zárolásának beszerzéséhez használja a következőt:

```azurecli
az lock list --resource-group exampleresourcegroup
```

A zárolás törléséhez használja a következőt:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API

A telepített erőforrásokat zárolhatja a [felügyeleti zárolások Rest APIával](/rest/api/resources/managementlocks). A REST API lehetővé teszi zárolások létrehozását és törlését, valamint a meglévő zárolásokkal kapcsolatos információk lekérését.

Zárolás létrehozásához futtassa a következő parancsot:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

A hatókör lehet előfizetés, erőforráscsoport vagy erőforrás. A zárolási név a zárolás meghívásához szükséges. Az API-Version esetében használja az **2016-09-01**-es verziót.

A kérelemben adjon meg egy JSON-objektumot, amely meghatározza a zárolás tulajdonságait.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>További lépések

* Az erőforrások logikus rendszerezésével kapcsolatos további információkért lásd: [címkék használata az erőforrások rendszerezéséhez](tag-resources.md).
* Az előfizetésre vonatkozó korlátozásokat és konvenciókat egyéni szabályzatokkal is alkalmazhat. További információ: [Mi az az Azure Policy?](../../governance/policy/overview.md)
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
