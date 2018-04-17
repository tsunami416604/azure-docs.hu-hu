---
title: Azure-erőforrások módosítható zárolása |} Microsoft Docs
description: Egyes felhasználók a frissítése vagy törlése a kritikus fontosságú Azure-erőforrások a zárolási összes felhasználók és szerepkörök alkalmazásával.
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
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 9edf49a404e5030c05acf17efcbc66123c67ad62
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Váratlan módosítható erőforrások zárolása 

Ha Ön rendszergazda szükség lehet egy előfizetés, erőforráscsoportból vagy erőforrás véletlen törlése vagy a kritikus erőforrásokat módosítása a munkahely más felhasználóinak megelőzése érdekében zárolja. Beállíthatja a zárolási szint beállítása azokhoz a **CanNotDelete** vagy **ReadOnly**. A portálon, a zárolás neve **törlése** és **írásvédett** kulcsattribútumokkal.

* **CanNotDelete** azt jelenti, hogy a jogosult felhasználók továbbra is olvasni és módosítani az erőforrást, de azokat nem lehet törölni az erőforrás. 
* **Csak olvasható** azt jelenti, hogy a jogosult felhasználók olvashatják egy erőforrást, de nem lehet törölni vagy az erőforrás frissítése. A zárolás alkalmazása hasonlít összes jogosult felhasználó által megadott engedélyek korlátozása a **olvasó** szerepkör. 

## <a name="how-locks-are-applied"></a>Hogyan alkalmazza a zárolásokat

A szülő hatókörben zárolást alkalmazásakor hatókörön belüli összes erőforrás öröklik az azonos zárolása. A későbbiekben még akkor is, erőforrások örökölt a zárolást. Az öröklés a legszigorúbb zár lép érvénybe.

Szerepköralapú hozzáférés-vezérlés, ellentétben a felügyeleti zárolás korlátozás alkalmazandó összes felhasználók és szerepkörök használhat. A felhasználók és szerepkörök engedélyeinek beállításával kapcsolatos további tudnivalókért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).

Csak a fordul elő a felügyeleti vezérlősík, amely küldött műveletek műveletek érvényes erőforrás-kezelő zárolások `https://management.azure.com`. A zárolásokat korlátozza, hogy milyen erőforrások feladatokat a saját. Erőforrás módosítások korlátozott, de erőforrás műveletek nem korlátozottak. Például egy SQL-adatbázis csak olvasható zárolást megakadályozza az törölhessék vagy módosíthassák az adatbázist, de nem akadályozza meg a létrehozása, frissítése vagy törlése az adatbázisban. Adatok tranzakciója engedélyezettek, mert a rendszer nem küldi el a műveletek `https://management.azure.com`.

Alkalmazása **ReadOnly** váratlan eredményekhez vezethet, mivel az egyes műveletek, amelyek az adatok, például olvasási műveletek ténylegesen szükséges további műveleteket. Például, ha kialakít egy **ReadOnly** zárolását egy tárfiókot minden felhasználót megakadályoz a kulcsainak listázása. A listában kulcsok művelet segítségével egy POST kérést kezel, mert a visszaadott kulcsok érhetők el az írási műveletek. Például ha kialakít egy **csak olvasható** egy App Service erőforrás zárolását megakadályozza, hogy a Visual Studio Server Explorer megjelenítése az erőforrás fájlok, mert adott interakció írási hozzáféréssel kell rendelkeznie.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Aki létrehozhat vagy törölhet zárolásokat a szervezetében
Hozzon létre, vagy törölje a felügyeleti zárolás, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/*` vagy `Microsoft.Authorization/locks/*` műveletek. A beépített szerepkörök, csak **tulajdonos** és **felhasználói hozzáférés adminisztrátora** kapnak a csatolási műveleteket.

## <a name="portal"></a>Portál
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Sablon
A következő példa bemutatja a sablont, amely a webhely az app service-csomag, a webhely és a zárolási hoz létre. A zárolás erőforrás típusa az erőforrás típusa az erőforrás zárolását, és **/szolgáltatók/zárolások**. A zárolás nevét hozza létre az erőforrásnév hozzáfűzésével **/Microsoft.Authorization/** és a zárolás nevét.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroup -Name sitegroup -Location southcentralus
New-AzureRmResourceGroupDeployment -ResourceGroupName sitegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json -hostingPlanName plan0103
```

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli
az group create --name sitegroup --location southcentralus
az group deployment create --resource-group sitegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json --parameters hostingPlanName=plan0103
```

## <a name="powershell"></a>PowerShell
Ön zárolása az erőforrások az Azure PowerShell használatával központilag telepítenek a [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) parancsot.

Egy erőforrás zárolását, adja meg az erőforrást, az erőforrás típusa és az erőforráscsoport neve nevét.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Erőforráscsoport zárolásához adja meg az erőforráscsoport nevét.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

A zárolási információ használatához [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Az előfizetés összes zárolás beszerzéséhez használja:

```powershell
Get-AzureRmResourceLock
```

Az összes zárolás erőforrás, amelyet:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Az összes zárolás erőforráscsoport megtekintéséhez használja:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

A zárolási törléséhez használja:

```powershell
$lockId = (Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzureRmResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Meg zárolási az erőforrások az Azure parancssori felület használatával központilag telepítenek a [az zárolási létrehozása](/cli/azure/lock#az_lock_create) parancsot.

Egy erőforrás zárolását, adja meg az erőforrást, az erőforrás típusa és az erőforráscsoport neve nevét.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Erőforráscsoport zárolásához adja meg az erőforráscsoport nevét.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

A zárolási információ használatához [az zárolási lista](/cli/azure/lock#az_lock_list). Az előfizetés összes zárolás beszerzéséhez használja:

```azurecli
az lock list
```

Az összes zárolás erőforrás, amelyet:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Az összes zárolás erőforráscsoport megtekintéséhez használja:

```azurecli
az lock list --resource-group exampleresourcegroup
```

A zárolási törléséhez használja:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
A telepített erőforrások zárolhatja a [REST API-t a felügyeleti zárolás](https://docs.microsoft.com/rest/api/resources/managementlocks). A REST API lehetővé teszi létrehozása és törlése a zárolások és meglévő zárolások vonatkozó információk lekéréséhez.

A zárolási létrehozásához futtassa:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

A hatókör lehet egy előfizetés, az erőforráscsoportot, vagy az erőforrás. A zárolás nevét, a zárolás hívni kívánt függetlenül. Api-version, használjon **2015-01-01**.

A kérelemben egy JSON-objektum, amely meghatározza a zárolás tulajdonságait tartalmazza.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>További lépések
* Az erőforrások logikailag rendszerezéséhez, lásd: [az erőforrások rendszerezése címkék használatával](resource-group-using-tags.md)
* Mely erőforrás található erőforráscsoport módosításához lásd [erőforrások áthelyezése új erőforráscsoportba](resource-group-move-resources.md)
* Az előfizetés testreszabott házirendekkel korlátozások és egyezmények alkalmazhat. További információ: [Mi az az Azure Policy?](../azure-policy/azure-policy-introduction.md)
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

