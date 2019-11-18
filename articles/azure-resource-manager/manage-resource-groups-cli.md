---
title: Erőforráscsoportok kezelése – Azure CLI
description: Az Azure CLI-vel kezelheti az erőforráscsoportokat Azure Resource Manager használatával. Megjeleníti az erőforráscsoportok létrehozását, listázását és törlését.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 68b82425fe5eb7bc9dce633b21a251de3fe463c9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149806"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure Resource Manager erőforráscsoportok kezelése az Azure CLI használatával

Ismerje meg, hogyan kezelheti Azure-erőforráscsoportait az Azure CLI és a [Azure Resource Manager](resource-group-overview.md) használatával. Az Azure-erőforrások kezelésével kapcsolatban lásd: az Azure- [erőforrások kezelése az Azure CLI használatával](./manage-resources-cli.md).

Az erőforráscsoportok kezelésével kapcsolatos további cikkek:

- [Azure-erőforráscsoportok kezelése a Azure Portal használatával](./manage-resources-portal.md)
- [Azure-erőforráscsoportok kezelése Azure PowerShell használatával](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Mi az erőforráscsoport?

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általában olyan erőforrásokat adjon hozzá, amelyek ugyanazt az életciklust használják ugyanahhoz az erőforráscsoporthoz, így egyszerűen üzembe helyezheti, frissítheti és törölheti őket csoportként.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Amikor megad egy helyet az erőforráscsoporthoz, meg kell adnia, hogy hol tárolja a metaadatokat.

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

A következő CLI-szkript létrehoz egy erőforráscsoportot, majd megjeleníti az erőforráscsoportot.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Erőforráscsoportok listázása

A következő CLI-parancsfájl felsorolja az előfizetéséhez tartozó erőforráscsoportokat.

```azurecli-interactive
az group list
```

Egy erőforráscsoport beszerzése:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Erőforráscsoportok törlése

A következő CLI-szkript töröl egy erőforráscsoportot:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

További információ arról, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését: [Azure Resource Manager erőforráscsoport törlése](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások központi telepítése meglévő erőforráscsoporthoz

Lásd: [erőforrások központi telepítése meglévő erőforráscsoporthoz](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Erőforráscsoport és erőforrások üzembe helyezése

Létrehozhat egy erőforráscsoportot, és erőforrásokat telepíthet a csoportba egy Resource Manager-sablon használatával. További információ: [erőforráscsoport létrehozása és erőforrások telepítése](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Újratelepítése, ha a telepítés sikertelen

Ez a szolgáltatás a *hiba miatt visszagörgetés*néven is ismert. További információ: [újratelepítése, ha a telepítés sikertelen](./rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Áthelyezés másik erőforráscsoporthoz vagy előfizetésbe

A csoportban található erőforrásokat áthelyezheti egy másik erőforráscsoporthoz. További információ: [erőforrások áthelyezése](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Erőforráscsoportok zárolása

A zárolás megakadályozza a szervezet más felhasználói számára a kritikus erőforrások, például az Azure-előfizetés, az erőforráscsoport vagy az erőforrás véletlen törlését vagy módosítását. 

Az alábbi parancsfájl zárol egy erőforráscsoportot, így az erőforráscsoport nem törölhető.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Az alábbi parancsfájl egy erőforráscsoport összes zárolását lekéri:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

A következő szkript törli a zárolást:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Erőforráscsoportok címkézése

Címkéket alkalmazhat az erőforráscsoportok és az erőforrások számára, hogy logikailag szervezze az eszközöket. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Erőforráscsoportok exportálása sablonokba

Az erőforráscsoport sikeres beállítása után érdemes megtekinteni az erőforráscsoport Resource Manager-sablonját. A sablon exportálása két előnyt kínál:

- Automatizálja a megoldás jövőbeli üzembe helyezéseit, mert a sablon tartalmazza az összes teljes infrastruktúrát.
- A sablon szintaxisának megismeréséhez tekintse meg a megoldást jelölő JavaScript Object Notation (JSON).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

A parancsfájl megjeleníti a sablont a konzolon.  Másolja a JSON-t, és mentse fájlként.

További információkért lásd: [egy-és többerőforrásos exportálás a Azure Portal sablonba](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Erőforráscsoportok hozzáférésének kezelése

A [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. További információ: a [hozzáférés kezelése a RBAC és az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>További lépések

- Azure Resource Manager megismeréséhez tekintse meg a [Azure Resource Manager áttekintése](./resource-group-overview.md)című témakört.
- A Resource Manager-sablon szintaxisának megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](./resource-group-authoring-templates.md)című témakört.
- A sablonok fejlesztésének megismeréséhez tekintse meg az útmutató [lépésről lépésre szóló oktatóanyagokat](/azure/azure-resource-manager/).
- A Azure Resource Manager sablon sémáinak megtekintéséhez lásd: [sablon-hivatkozás](/azure/templates/).