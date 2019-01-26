---
title: Az Azure megelőzése érdekében zárolhat erőforrásokat módosítások |} A Microsoft Docs
description: Hogy a felhasználók frissítése vagy törlése a kritikus fontosságú Azure-erőforrások a zárolást az összes felhasználók és szerepkörök alkalmazásával.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 20810c3b32274129ce82f4efeca9efb31016189c
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079759"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Erőforrások zárolása a váratlan módosítások megelőzése érdekében 

A rendszergazdák szükség lehet egy előfizetés, erőforráscsoport vagy erőforrás véletlen törlését vagy módosítását kiemelt fontosságú erőforrásait a munkahely más felhasználóinak megelőzése érdekében zárolja. A zárolási szintet **CanNotDelete** (nem törölhető) vagy **ReadOnly** (csak olvasható) értékre állíthatja be. A portálon a zárolások nevezzük **törlése** és **csak olvasható** jelölik.

* **Védve** azt jelenti, hogy a jogosult felhasználók továbbra is olvasni és módosítani az erőforrást, de azokat nem lehet törölni az erőforrást. 
* **Csak olvasható** azt jelenti, hogy a jogosult felhasználók olvashatják egy erőforrást, de nem lehet törölni vagy az erőforrás frissítése. A zárolás alkalmazása hasonlít az összes jogosult felhasználó által adott engedélyek korlátozása a **olvasó** szerepkör. 

## <a name="how-locks-are-applied"></a>Zárolások alkalmazásának módja

Amikor alkalmazza a zárolást, egy szülő hatókörben, a hatókörön belüli összes erőforrás azonos zárolási öröklik. Hozzáadását a későbbiekben még akkor is, erőforrások a zárolási öröklik a szülő. A legszigorúbb zárolást az öröklési ciklus élvez elsőbbséget.

Szerepköralapú hozzáférés-vezérlés, ellentétben a felügyeleti zárolások összes felhasználók és szerepkörök korlátozások alkalmazásához használhat. A felhasználók és szerepkörök engedélyeinek beállításával kapcsolatos tudnivalókért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).

Erőforrás-kezelő zárolások csak vonatkozik, amelyek a felügyeleti sík, olyan küldött műveleteket tartalmaz, amely egy operations `https://management.azure.com`. A zárolás, erőforrások hogyan hajthat végre a saját függvények nem korlátozzák. Erőforrás-módosítások korlátozva, de az erőforrás-műveletek nem korlátozódnak. Például egy SQL-adatbázis írásvédett zárolásának meggátolja, hogy általi törlését vagy módosítását az adatbázist, de nem akadályozza meg, létrehozása, frissítése vagy törölni az adatbázisban található adatok. Adatok tranzakciója engedélyezettek, mert az nem kap meg ezek a műveletek `https://management.azure.com`.

Alkalmazása **ReadOnly** váratlan eredményekhez vezethet, mivel bizonyos műveletek, amelyek úgy tűnik, például olvasási műveletekhez ténylegesen szükséges további műveleteket. Például elhelyezése egy **ReadOnly** a storage-fiók zárolása megakadályozza, hogy minden felhasználó a kulcsok listázása. A lista kulcsok művelet POST-kérés történik, mert a visszaadott kulcsok érhetők el írási műveletek. A példában egy másik elhelyezése egy **ReadOnly** egy App Service erőforrás zárolása megakadályozza, hogy a Visual Studio Server Explorer fájl az erőforrás jelenik meg, mert a kapcsolati írási hozzáférésre van szüksége.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Akik hozhatók létre, vagy a szervezet zárolások törlése
Hozzon létre vagy felügyeleti zárolások törlése, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/*` vagy `Microsoft.Authorization/locks/*` műveleteket. A beépített szerepkörök esetén ezek a műveletek csak a **Tulajdonosi** és a **Felhasználói hozzáférés rendszergazdájának** vannak engedélyezve.

## <a name="portal"></a>Portál
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Sablon
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

Ez a PowerShell használatával például a sablon üzembe helyezéséhez használja:

```azurepowershell-interactive
New-AzResourceGroup -Name sitegroup -Location southcentralus
New-AzResourceGroupDeployment -ResourceGroupName sitegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json -hostingPlanName plan0103
```

Az Azure CLI-vel ebben a példában sablon üzembe helyezéséhez használja:

```azurecli
az group create --name sitegroup --location southcentralus
az group deployment create --resource-group sitegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json --parameters hostingPlanName=plan0103
```

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

A hatókör egy előfizetés, erőforráscsoport vagy erőforrás lehet. A zárolás neve akármilyen területen is fel kívánja hívni a zárolást. Api-Version paraméter használata **2015-01-01**.

A kérelem tartalmazza a JSON-objektum, amely meghatározza a tulajdonságait, a zárolás.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>További lépések
* Logikailag a az erőforrások rendszerezéséhez kapcsolatos további információkért lásd: [az erőforrások rendszerezése címkék használatával](resource-group-using-tags.md)
* Melyik erőforrás található az erőforráscsoport módosításához lásd [erőforrások áthelyezése új erőforráscsoportba](resource-group-move-resources.md)
* Korlátozások és konvenciói alkalmazhat testreszabott házirendekkel az előfizetésében. További információ: [Mi az az Azure Policy?](../azure-policy/azure-policy-introduction.md)
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

