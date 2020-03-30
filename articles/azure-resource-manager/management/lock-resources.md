---
title: Erőforrások zárolása a módosítások megelőzése érdekében
description: Megakadályozhatja, hogy a felhasználók frissítsék vagy kiijálják a kritikus Azure-erőforrásokat, ha zárolást alkalmaznak az összes felhasználóra és szerepkörre.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274007"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Erőforrások zárolása a váratlan módosítások megelőzése érdekében

Rendszergazdaként szüksége lehet egy előfizetés, erőforráscsoport vagy erőforrás zárolására annak érdekében, hogy a szervezet többi felhasználója ne tudja véletlenül törölni vagy módosítani a kritikus fontosságú erőforrásokat. A zárolási szintet **CanNotDelete** (nem törölhető) vagy **ReadOnly** (csak olvasható) értékre állíthatja be. A portálon a zárolások neve **Törlés** és **írásvédett.**

* **A CanNotDelete** azt jelenti, hogy a jogosult felhasználók továbbra is olvashatnak és módosíthatnak egy erőforrást, de nem törölhetik az erőforrást. 
* **A Csak** olvasás azt jelenti, hogy a jogosult felhasználók olvashatnak egy erőforrást, de nem törölhetik vagy frissíthetik az erőforrást. A zárolás alkalmazása hasonló ahhoz, hogy az összes jogosult felhasználót az **Olvasó** szerepkör által megadott engedélyekre korlátozza.

## <a name="how-locks-are-applied"></a>A zárak alkalmazásának megóvása

Ha zárolást alkalmaz egy szülőhatókörben, a hatókörön belüli összes erőforrás ugyanazt a zárolást örökli. Még a később hozzáadott erőforrások is öröklik a zárolást a szülőtől. Az öröklődés legszigorúbb zárolása élvez elsőbbséget.

A felügyeleti zárolás a szerepköralapú hozzáférés-vezérléssel szemben minden felhasználóra és szerepkörre érvényes korlátozásokat alkalmaz. A felhasználók és szerepkörök engedélyeinek beállításáról az [Azure szerepköralapú hozzáférés-vezérlés című témakörben](../../role-based-access-control/role-assignments-portal.md)olvashat.

A Resource Manager zárolásai csak a felügyeleti síkon történő műveletekre érvényesek, ezek pedig a `https://management.azure.com` címre küldött műveletek. A zárolások nem korlátozzák, hogy az erőforrások hogyan végzik saját funkcióikat. Az erőforrás változásai korlátozva vannak, de az erőforrás működése nincs korlátozva. Egy SQL-adatbázis csak olvasható zárolása például megakadályozza az adatbázis törlését vagy módosítását. Nem akadályozza meg azonban az adatok létrehozását, frissítését és törlését az adatbázison belül. Az adattranzakciók engedélyezve vannak, mert ezek a műveletek nem lesznek elküldve a `https://management.azure.com` webhelyre.

**ReadOnly** alkalmazása nem várt eredményeket eredményezhet, mert egyes műveletek, amelyek úgy tűnik, hogy nem módosítja az erőforrást, ténylegesen olyan műveleteket igényelnek, amelyeket a zárolás blokkol. A **Csak-olvasás** zárolás alkalmazható az erőforrásra vagy az erőforrást tartalmazó erőforráscsoportra. Néhány gyakori példa a **readonly** zárolás által blokkolt műveletekre:

* A **tárfiók csak olvasható** zárolása megakadályozza, hogy minden felhasználó felsorolja a kulcsokat. A kulcsok listázásának művelete POST kérelmen keresztül történik, mert a visszaadott kulcsok írási műveletekkel elérhetők.

* Az App Service-erőforrás **csak olvasási** zárolása megakadályozza, hogy a Visual Studio Server Explorer fájlokat jelenítsen meg az erőforráshoz, mert az interakció írási hozzáférést igényel.

* A virtuális gépet tartalmazó erőforráscsoport **csak olvasható** zárolása megakadályozza, hogy minden felhasználó elindítsa vagy újraindítsa a virtuális gépet. Ezek a műveletek postai kérelmet igényelnek.

## <a name="who-can-create-or-delete-locks"></a>Ki hozhat létre vagy törölhet zárolásokat?

Felügyeleti zárolások létrehozásához vagy törléséhez `Microsoft.Authorization/*` `Microsoft.Authorization/locks/*` hozzáféréssel vagy műveletekkel kell rendelkeznie. A beépített szerepkörök esetén ezek a műveletek csak a **Tulajdonosi** és a **Felhasználói hozzáférés rendszergazdájának** vannak engedélyezve.

## <a name="managed-applications-and-locks"></a>Felügyelt alkalmazások és zárolások

Egyes Azure-szolgáltatások, például az Azure Databricks, [felügyelt alkalmazások](../managed-applications/overview.md) használatával valósítja meg a szolgáltatást. Ebben az esetben a szolgáltatás két erőforráscsoportot hoz létre. Egy erőforráscsoport áttekintést nyújt a szolgáltatásról, és nincs zárolva. A másik erőforráscsoport tartalmazza a szolgáltatás infrastruktúráját, és zárolva van.

Ha megpróbálja törölni az infrastruktúra erőforráscsoportot, hibaüzenetet kap arról, hogy az erőforráscsoport zárolva van. Ha megpróbálja törölni az infrastruktúra erőforráscsoport zárolását, hibaüzenetet kap arról, hogy a zárolás nem törölhető, mert egy rendszeralkalmazás tulajdonában van.

Ehelyett törölje a szolgáltatást, amely az infrastruktúra erőforráscsoportot is törli.

Felügyelt alkalmazások esetén válassza ki a telepített szolgáltatást.

![Szolgáltatás kiválasztása](./media/lock-resources/select-service.png)

Figyelje meg, hogy a szolgáltatás **egy felügyelt erőforráscsoporthoz**tartalmaz egy hivatkozást. Ez az erőforráscsoport rendelkezik az infrastruktúrával, és zárolva van. Nem törölhető közvetlenül.

![Felügyelt csoport megjelenítése](./media/lock-resources/show-managed-group.png)

Ha törölni szeretne mindent a szolgáltatáshoz, beleértve a zárolt infrastruktúra erőforráscsoportot is, válassza a **Törlés** lehetőséget a szolgáltatáshoz.

![Szolgáltatás törlése](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Azure biztonsági mentések és zárolások

Ha zárolja az Azure Backup Service által létrehozott erőforráscsoportot, a biztonsági mentések sikertelenek lesznek. A szolgáltatás legfeljebb 18 visszaállítási pontot támogat. **CanNotDelete** zárolással a biztonsági mentési szolgáltatás nem tudja megtisztítani a visszaállítási pontokat. További információ: [Gyakori kérdések- Az Azure virtuális gépeinek biztonsági másolatot.](../../backup/backup-azure-vm-backup-faq.md)

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Sablon

Ha egy Erőforrás-kezelő sablont használ a zárolás üzembe helyezéséhez, a zárolás hatókörétől függően különböző értékeket kell használnia a névhez és a típushoz.

Zárolás alkalmazásakor használja **resource**a következő formátumokat:

* név -`{resourceName}/Microsoft.Authorization/{lockName}`
* típus -`{resourceProviderNamespace}/{resourceType}/providers/locks`

Zárolás alkalmazásakor egy **erőforráscsoportra** vagy **-előfizetésre**a következő formátumokat használja:

* név -`{lockName}`
* típus -`Microsoft.Authorization/locks`

A következő példa egy olyan sablont mutat be, amely létrehoz egy alkalmazásszolgáltatási csomagot, egy webhelyet és egy zárolást a webhelyen. A zárolás erőforrástípusa a zárolandó erőforrás és a **/providers/locks**erőforrástípusa. A zárolás neve úgy jön létre, hogy összefűzi az erőforrás nevét a **/Microsoft.Authorization/** és a zárolás nevével.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
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
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
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

Például egy erőforráscsoport zárolásának beállításához olvassa el az [Erőforráscsoport létrehozása és zárolása című témakört.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)

## <a name="powershell"></a>PowerShell
Az Üzembe helyezett erőforrások zárolása az Azure PowerShell használatával a [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) paranccsal.

Erőforrás zárolásához adja meg az erőforrás nevét, erőforrástípusát és erőforráscsoportjának nevét.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Erőforráscsoport zárolásához adja meg az erőforráscsoport nevét.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

A zárolással kapcsolatos információkért használja a [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Az előfizetés összes zárolásának lefagyásához használja a következőket:

```azurepowershell-interactive
Get-AzResourceLock
```

Egy erőforrás összes zárolásának lekérnie, használja a következőket:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Egy erőforráscsoport összes zárolásának lekérnie, használja a következőket:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Zárolás törléséhez használja a következőket:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Az üzembe helyezett erőforrásokzárolása az Azure CLI-vel az [az lock create](/cli/azure/lock#az-lock-create) paranccsal.

Erőforrás zárolásához adja meg az erőforrás nevét, erőforrástípusát és erőforráscsoportjának nevét.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Erőforráscsoport zárolásához adja meg az erőforráscsoport nevét.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

A zárolással kapcsolatos információk hozása érdekében használja [az az lock list ( az zárolási lista](/cli/azure/lock#az-lock-list). Az előfizetés összes zárolásának lefagyásához használja a következőket:

```azurecli
az lock list
```

Egy erőforrás összes zárolásának lekérnie, használja a következőket:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Egy erőforráscsoport összes zárolásának lekérnie, használja a következőket:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Zárolás törléséhez használja a következőket:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
Az üzembe helyezett erőforrásokat a REST API-val zárolhatja [a felügyeleti zárolások hoz.](https://docs.microsoft.com/rest/api/resources/managementlocks) A REST API lehetővé teszi zárolások létrehozását és törlését, valamint a meglévő zárolásokkal kapcsolatos információk lekérését.

Zárolás létrehozásához futtassa a következőket:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

A hatókör lehet előfizetés, erőforráscsoport vagy erőforrás. A zár az, amit csak akarsz. Api-verzió esetén használja a **2016-09-01-et.**

A kérelemben vegyen fel egy JSON-objektumot, amely megadja a zárolás tulajdonságait.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>További lépések
* Az erőforrások logikai rendszerezéséről az [Erőforrások rendszerezésének használata a Címkék használata az erőforrások rendszerezéséhez(használja) témakört](tag-resources.md)
* A testreszabott szabályzatokkal korlátozásokat és konvenciókat alkalmazhat az előfizetésben. További információ: [Mi az az Azure Policy?](../../governance/policy/overview.md)
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

