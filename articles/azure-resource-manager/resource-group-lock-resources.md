---
title: "Azure-erőforrások módosítható zárolása |} Microsoft Docs"
description: "Egyes felhasználók a frissítése vagy törlése a kritikus fontosságú Azure-erőforrások a zárolási összes felhasználók és szerepkörök alkalmazásával."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: 44c87b00f4fc63dbfd45a07d9a8cddc5eaf1a65c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Váratlan módosítható erőforrások zárolása 
Ha Ön rendszergazda szükség lehet egy előfizetés, erőforráscsoportból vagy erőforrás véletlen törlése vagy a kritikus erőforrásokat módosítása a munkahely más felhasználóinak megelőzése érdekében zárolja. Beállíthatja a zárolási szint beállítása azokhoz a **CanNotDelete** vagy **ReadOnly**. 

* **CanNotDelete** azt jelenti, hogy a jogosult felhasználók továbbra is olvasni és módosítani az erőforrást, de azokat nem lehet törölni az erőforrás. 
* **Csak olvasható** azt jelenti, hogy a jogosult felhasználók olvashatják egy erőforrást, de nem lehet törölni vagy az erőforrás frissítése. A zárolás alkalmazása hasonlít összes jogosult felhasználó által megadott engedélyek korlátozása a **olvasó** szerepkör. 

## <a name="how-locks-are-applied"></a>Hogyan alkalmazza a zárolásokat

A szülő hatókörben zárolást alkalmazásakor hatókörön belüli összes erőforrás öröklik az azonos zárolása. A későbbiekben még akkor is, erőforrások örökölt a zárolást. Az öröklés a legszigorúbb zár lép érvénybe.

Szerepköralapú hozzáférés-vezérlés, ellentétben a felügyeleti zárolás korlátozás alkalmazandó összes felhasználók és szerepkörök használhat. A felhasználók és szerepkörök engedélyeinek beállításával kapcsolatos további tudnivalókért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md).

Csak a fordul elő a felügyeleti vezérlősík, amely küldött műveletek műveletek érvényes erőforrás-kezelő zárolások `https://management.azure.com`. A zárolásokat korlátozza, hogy milyen erőforrások feladatokat a saját. Erőforrás módosítások korlátozott, de erőforrás műveletek nem korlátozottak. Például egy SQL-adatbázis csak olvasható zárolást megakadályozza az törölhessék vagy módosíthassák az adatbázist, de nem akadályozza meg a létrehozása, frissítése vagy törlése az adatbázisban. Adatok tranzakciója engedélyezettek, mert a rendszer nem küldi el a műveletek `https://management.azure.com`.

Alkalmazása **ReadOnly** váratlan eredményekhez vezethet, mivel az egyes műveletek, amelyek az adatok, például olvasási műveletek ténylegesen szükséges további műveleteket. Például, ha kialakít egy **ReadOnly** zárolását egy tárfiókot minden felhasználót megakadályoz a kulcsainak listázása. A listában kulcsok művelet segítségével egy POST kérést kezel, mert a visszaadott kulcsok érhetők el az írási műveletek. Például ha kialakít egy **csak olvasható** egy App Service erőforrás zárolását megakadályozza, hogy a Visual Studio Server Explorer megjelenítése az erőforrás fájlok, mert adott interakció írási hozzáféréssel kell rendelkeznie.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Aki létrehozhat vagy törölhet zárolásokat a szervezetében
Hozzon létre, vagy törölje a felügyeleti zárolás, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/*` vagy `Microsoft.Authorization/locks/*` műveletek. A beépített szerepkörök, csak **tulajdonos** és **felhasználói hozzáférés adminisztrátora** kapnak a csatolási műveleteket.

## <a name="portal"></a>Portál
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Sablon
A következő példa bemutatja a sablont, amely a zárolási létrehoz egy tárfiókot. A storage-fiók alkalmazásának a zárolás valósul meg paraméterként. A zárolás nevét hozza létre az erőforrásnév hozzáfűzésével **/Microsoft.Authorization/** és a nevét, a zárolás, ebben az esetben **myLock**.

A megadott típus jellemző az erőforrás típusát. A tároláshoz állítsa be az "Microsoft.Storage/storageaccounts/providers/locks" típust.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="powershell"></a>PowerShell
Ön zárolása az erőforrások az Azure PowerShell használatával központilag telepítenek a [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) parancsot.

Egy erőforrás zárolását, adja meg az erőforrást, az erőforrás típusa és az erőforráscsoport neve nevét.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite `
  -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Erőforráscsoport zárolásához adja meg az erőforráscsoport nevét.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete `
  -ResourceGroupName exampleresourcegroup
```

A zárolási információ használatához [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Az előfizetés összes zárolás beszerzéséhez használja:

```powershell
Get-AzureRmResourceLock
```

Az összes zárolás erőforrás, amelyet:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Az összes zárolás erőforráscsoport megtekintéséhez használja:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Az Azure PowerShell biztosít a más parancsok működő zárolásokat, például a [Set-AzureRmResourceLock](/powershell/module/azurerm.resources/set-azurermresourcelock) zárolást, frissítése és [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) zárolást törlése.

## <a name="azure-cli"></a>Azure CLI

Meg zárolási az erőforrások az Azure parancssori felület használatával központilag telepítenek a [az zárolási létrehozása](/cli/azure/lock#create) parancsot.

Egy erőforrás zárolását, adja meg az erőforrást, az erőforrás típusa és az erőforráscsoport neve nevét.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete \
  --resource-group exampleresourcegroup --resource-name examplesite \
  --resource-type Microsoft.Web/sites
```

Erőforráscsoport zárolásához adja meg az erőforráscsoport nevét.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete \
  --resource-group exampleresourcegroup
```

A zárolási információ használatához [az zárolási lista](/cli/azure/lock#list). Az előfizetés összes zárolás beszerzéséhez használja:

```azurecli
az lock list
```

Az összes zárolás erőforrás, amelyet:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite \
  --namespace Microsoft.Web --resource-type sites --parent ""
```

Az összes zárolás erőforráscsoport megtekintéséhez használja:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Az Azure CLI biztosít a más parancsok működő zárolásokat, például a [az zárolási frissítés](/cli/azure/lock#update) frissíteni a zárolást, és [az zárolási törlése](/cli/azure/lock#delete) zárolást törlése.

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

## <a name="next-steps"></a>Következő lépések
* Működő erőforrás zárral kapcsolatos további információkért lásd: [zár le az Azure-erőforrások](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
* Az erőforrások logikailag rendszerezéséhez, lásd: [az erőforrások rendszerezése címkék használatával](resource-group-using-tags.md)
* Mely erőforrás található erőforráscsoport módosításához lásd [erőforrások áthelyezése új erőforráscsoportba](resource-group-move-resources.md)
* Az előfizetés testreszabott házirendekkel korlátozások és egyezmények alkalmazhat. További információ: [Erőforrások kezelése és hozzáférés szabályozása házirendekkel](resource-manager-policy.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

