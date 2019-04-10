---
title: Az Azure megelőzése érdekében zárolhat erőforrásokat módosítások |} A Microsoft Docs
description: Hogy a felhasználók frissítése vagy törlése a kritikus fontosságú Azure-erőforrások a zárolást az összes felhasználók és szerepkörök alkalmazásával.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: 8942ae9a24613f7b7896cf7124b344d9d9315954
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360442"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Erőforrások zárolása a váratlan módosítások megelőzése érdekében 

A rendszergazdák szükség lehet egy előfizetés, erőforráscsoport vagy erőforrás véletlen törlését vagy módosítását kiemelt fontosságú erőforrásait a munkahely más felhasználóinak megelőzése érdekében zárolja. A zárolási szintet **CanNotDelete** (nem törölhető) vagy **ReadOnly** (csak olvasható) értékre állíthatja be. A portálon a zárolások nevezzük **törlése** és **csak olvasható** jelölik.

* **Védve** azt jelenti, hogy a jogosult felhasználók továbbra is olvasni és módosítani az erőforrást, de azokat nem lehet törölni az erőforrást. 
* **Csak olvasható** azt jelenti, hogy a jogosult felhasználók olvashatják egy erőforrást, de nem lehet törölni vagy az erőforrás frissítése. A zárolás alkalmazása hasonlít az összes jogosult felhasználó által adott engedélyek korlátozása a **olvasó** szerepkör. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Zárolások alkalmazásának módja

Amikor alkalmazza a zárolást, egy szülő hatókörben, a hatókörön belüli összes erőforrás azonos zárolási öröklik. Hozzáadását a későbbiekben még akkor is, erőforrások a zárolási öröklik a szülő. A legszigorúbb zárolást az öröklési ciklus élvez elsőbbséget.

Szerepköralapú hozzáférés-vezérlés, ellentétben a felügyeleti zárolások összes felhasználók és szerepkörök korlátozások alkalmazásához használhat. A felhasználók és szerepkörök engedélyeinek beállításával kapcsolatos tudnivalókért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).

Erőforrás-kezelő zárolások csak vonatkozik, amelyek a felügyeleti sík, olyan küldött műveleteket tartalmaz, amely egy operations `https://management.azure.com`. A zárolása nem korlátozza, erőforrások hogyan hajthat végre a saját funkciók. Erőforrás-módosítások korlátozva, de az erőforrás-műveletek nem korlátozott. Például egy SQL-adatbázis írásvédett zárolásának megakadályozza, hogy Ön általi törlését vagy módosítását az adatbázis. Ez nem akadályozza meg a létrehozása, frissítése vagy törlése az adatbázis adatait. Adatok tranzakciója engedélyezettek, mert ezek a műveletek nem küldött `https://management.azure.com`.

Alkalmazása **ReadOnly** váratlan eredményekhez vezethet, mivel bizonyos műveletek, amelyek úgy tűnik, például olvasási műveletekhez ténylegesen szükséges további műveleteket. Például elhelyezése egy **ReadOnly** a storage-fiók zárolása megakadályozza, hogy minden felhasználó a kulcsok listázása. A lista kulcsok művelet POST-kérés történik, mert a visszaadott kulcsok érhetők el írási műveletek. A példában egy másik elhelyezése egy **ReadOnly** egy App Service erőforrás zárolása megakadályozza, hogy a Visual Studio Server Explorer fájl az erőforrás jelenik meg, mert a kapcsolati írási hozzáférésre van szüksége.

## <a name="who-can-create-or-delete-locks"></a>Akik létrehozhatja vagy törölheti a zárolások
Hozzon létre vagy felügyeleti zárolások törlése, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/*` vagy `Microsoft.Authorization/locks/*` műveleteket. A beépített szerepkörök esetén ezek a műveletek csak a **Tulajdonosi** és a **Felhasználói hozzáférés rendszergazdájának** vannak engedélyezve.

## <a name="managed-applications-and-locks"></a>Felügyelt alkalmazások és zárolások

Bizonyos Azure-szolgáltatások, például az Azure Databricks használata [által felügyelt alkalmazások](../managed-applications/overview.md) a szolgáltatás megvalósítása. A szolgáltatás ebben az esetben két erőforráscsoport hoz létre. Egy erőforráscsoport a szolgáltatás áttekintését tartalmazza, és nincs zárolva van. Az erőforráscsoport tartalmazza a szolgáltatás az infrastruktúra és zárolva van.

Az infrastruktúra erőforráscsoportot törölni próbál, ha hibaüzenet jelenik meg, hogy az erőforráscsoport zárolva van. Törölheti a zárolást az infrastruktúra erőforráscsoport meg, ha hibaüzenet jelenik meg, hogy a zárolás nem törölhető, mert a tulajdonában van egy rendszer-alkalmazást.

Próbálja meg törölni a szolgáltatást, amely az infrastruktúra erőforráscsoportot is törli.

Felügyelt alkalmazások esetében válassza ki a telepített szolgáltatás.

![Szolgáltatás kiválasztása](./media/resource-group-lock-resources/select-service.png)

Figyelje meg a szolgáltatás tartalmaz egy hivatkozást egy **felügyelt erőforráscsoport**. Erőforráscsoport tárolja az infrastruktúrát, és zárolva van. Nem lehet közvetlenül törli azt.

![Felügyelt csoport megjelenítése](./media/resource-group-lock-resources/show-managed-group.png)

Minden, az zárolt infrastruktúra erőforráscsoportban, beleértve a szolgáltatás törléséhez válassza ki **törlése** a szolgáltatáshoz.

![Szolgáltatás törlése](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>Portál
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Sablon

Resource Manager-sablon üzembe helyezéséhez a zárolás használatakor különböző értékek használhat nevet és a zárolás típusától függően.

Egy zárolást alkalmazásakor a **erőforrás**, használja a következő formátumok:

* név – `{resourceName}/Microsoft.Authorization/{lockName}`
* Írja be a- `{resourceProviderNamespace}/{resourceType}/providers/locks`

Egy zárolást alkalmazásakor a **erőforráscsoport** vagy **előfizetés**, használja a következő formátumok:

* név – `{lockName}`
* Írja be a- `Microsoft.Authorization/locks`

Az alábbi példa bemutatja egy sablont, amely a webhely app service-csomag, a webhely és a egy zárolási hoz létre. Az erőforrás típusa, a zárolás pedig az erőforrás típusa, az erőforrás zárolása és **/Providers/ zárolások**. A zárolás nevét jön létre elkülönített változó összefűzésével előállítjuk az erőforrás nevét **/Microsoft.Authorization/** és a zárolás nevét.

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

Zárolást beállítást egy erőforráscsoportot egy példa: [hozzon létre egy erőforráscsoportot, majd zárolja](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Zárolás használatával üzembe helyezett erőforrásokat az Azure PowerShell használatával a [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) parancsot.

Erőforrás zárolása, adja meg a nevét, az erőforrást, az erőforrás típusa és az erőforráscsoport nevét.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Zárolhatja az erőforráscsoport, adja meg az erőforráscsoport nevét.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Egy zárolás kapcsolatos információk lekéréséhez használja [Get-AzureRmResourceLock](/powershell/module/az.resources/get-azresourcelock). Az összes a zárolások beolvasása az előfizetésében, használja:

```azurepowershell-interactive
Get-AzResourceLock
```

Az összes zárolás erőforrás használja:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Az összes zárolás erőforráscsoport használja:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Egy zárolás törléséhez használja:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Zárolás használatával üzembe helyezett erőforrásokat az Azure CLI-vel a [az lock létrehozása](/cli/azure/lock#az-lock-create) parancsot.

Erőforrás zárolása, adja meg a nevét, az erőforrást, az erőforrás típusa és az erőforráscsoport nevét.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Zárolhatja az erőforráscsoport, adja meg az erőforráscsoport nevét.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Egy zárolás kapcsolatos információk lekéréséhez használja [az lock list](/cli/azure/lock#az-lock-list). Az összes a zárolások beolvasása az előfizetésében, használja:

```azurecli
az lock list
```

Az összes zárolás erőforrás használja:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Az összes zárolás erőforráscsoport használja:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Egy zárolás törléséhez használja:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
Az üzembe helyezett erőforrásokról zárolhatja a [REST API-felügyeleti zárolások](https://docs.microsoft.com/rest/api/resources/managementlocks). A REST API lehetővé teszi, hogy hozzon létre és zárolások törlése, illetve információkat olvasson meglévő zárolások.

Egy zárolás létrehozásához futtassa:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

A hatókör egy előfizetés, erőforráscsoport vagy erőforrás lehet. A zárolás neve akármilyen területen is fel kívánja hívni a zárolást. Api-Version paraméter használata **2016-09-01**.

A kérelem tartalmazza a JSON-objektum, amely meghatározza a tulajdonságait, a zárolás.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>További lépések
* Logikailag a az erőforrások rendszerezéséhez kapcsolatos további információkért lásd: [az erőforrások rendszerezése címkék használatával](resource-group-using-tags.md)
* Korlátozások és konvenciói alkalmazhat testreszabott házirendekkel az előfizetésében. További információ: [Mi az az Azure Policy?](../governance/policy/overview.md)
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

