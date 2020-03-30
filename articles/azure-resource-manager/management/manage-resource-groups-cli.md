---
title: Erőforráscsoportok kezelése - Azure CLI
description: Az Azure CLI segítségével kezelheti az erőforráscsoportokat az Azure Resource Manager en keresztül. Erőforráscsoportok létrehozása, listázása és törlése.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248332"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Az Azure Resource Manager erőforráscsoportok kezelése az Azure CLI használatával

Ismerje meg, hogyan használhatja az Azure CLI-t az [Azure Resource Managerrel](overview.md) az Azure-erőforráscsoportok kezeléséhez. Az Azure-erőforrások kezeléséről az [Azure-erőforrások kezelése az Azure CLI használatával.](manage-resources-cli.md)

További cikkek az erőforráscsoportok kezeléséről:

- [Azure-erőforráscsoportok kezelése az Azure Portal használatával](manage-resources-portal.md)
- [Azure-erőforráscsoportok kezelése az Azure PowerShell használatával](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Mi az erőforráscsoport?

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általában adja hozzá az azonos életciklusú erőforrásokat ugyanahhoz az erőforráscsoporthoz, így könnyen telepítheti, frissítheti és törölheti őket csoportként.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Amikor megadja az erőforráscsoport helyét, megadja a metaadatok tárolási helyét.

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

A következő CLI-parancsfájl létrehoz egy erőforráscsoportot, majd megjeleníti az erőforráscsoportot.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Erőforráscsoportok listázása

A következő CLI-parancsfájl felsorolja az előfizetés alatt található erőforráscsoportokat.

```azurecli-interactive
az group list
```

Egy erőforráscsoport bekése:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Erőforráscsoportok törlése

A következő CLI-parancsfájl töröl egy erőforráscsoportot:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Ha többet szeretne tudni arról, hogy az Azure Resource Manager hogyan rendeli meg az erőforrások törlését, olvassa el az [Azure Resource Manager erőforráscsoport-törlése című témakört.](delete-resource-group.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások üzembe helyezése meglévő erőforráscsoportba

Lásd: [Erőforrások telepítése egy meglévő erőforráscsoportba](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Erőforráscsoport és erőforrások üzembe helyezése

Erőforrás-kezelő sablon használatával létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet a csoportba. További információ: [Erőforráscsoport létrehozása és erőforrások üzembe helyezése.](../templates/deploy-to-subscription.md#resource-group-and-resources)

## <a name="redeploy-when-deployment-fails"></a>Újratelepítés, ha az üzembe helyezés sikertelen

Ezt a szolgáltatást *hiba visszaállításának is nevezik.* További információ: [Újratelepítés, ha az üzembe helyezés sikertelen.](../templates/rollback-on-error.md)

## <a name="move-to-another-resource-group-or-subscription"></a>Áthelyezés másik erőforráscsoportra vagy -előfizetésre

A csoport erőforrásait áthelyezheti egy másik erőforráscsoportba. További információt az [Erőforrások áthelyezése](manage-resources-cli.md#move-resources)című témakörben talál.

## <a name="lock-resource-groups"></a>Erőforráscsoportok zárolása

A zárolás megakadályozza, hogy a szervezet más felhasználói véletlenül kiirtsák vagy módosítsák a kritikus erőforrásokat, például az Azure-előfizetést, az erőforráscsoportot vagy az erőforrást. 

A következő parancsfájl zárol egy erőforráscsoportot, így az erőforráscsoport nem törölhető.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

A következő parancsfájl lekéri az összes zárolást egy erőforráscsoporthoz:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

A következő parancsfájl törli a zárolást:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](lock-resources.md).

## <a name="tag-resource-groups"></a>Erőforráscsoportok címkézése

Címkéket alkalmazhat az erőforráscsoportokra és erőforrásokra az eszközök logikai rendszerezéséhez. További információ: [Címkék használata az Azure-erőforrások rendszerezéséhez című témakörben.](tag-resources.md#azure-cli)

## <a name="export-resource-groups-to-templates"></a>Erőforráscsoportok exportálása sablonokba

Az erőforráscsoport sikeres beállítása után érdemes megtekinteni az erőforráscsoport Erőforrás-kezelő sablonját. A sablon exportálása két előnnyel jár:

- Automatizálja a megoldás jövőbeli üzembe helyezését, mert a sablon tartalmazza az összes teljes infrastruktúrát.
- Ismerje meg a sablon szintaxisát a megoldást képviselő JavaScript-objektumnotikát (JSON) című filmben.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

A parancsfájl megjeleníti a sablont a konzolon.  Másolja a JSON-t, és mentse fájlként.

Az exportálási sablon funkció nem támogatja az Azure Data Factory-erőforrások exportálását. Ha többet szeretne megtudni arról, hogyan exportálhatja a Data Factory-erőforrásokat, olvassa el [az Adatgyár másolása vagy klónozása az Azure Data Factoryban című témakört.](https://aka.ms/exportTemplateViaAdf)

A klasszikus központi telepítési modellen keresztül létrehozott erőforrások exportálásához át kell [telepítenie őket az Erőforrás-kezelő telepítési modelljébe.](https://aka.ms/migrateclassicresourcetoarm)

További információ: [Egy-és többerőforrásos exportálás sablonba az Azure Portalon.](../templates/export-template-portal.md)

## <a name="manage-access-to-resource-groups"></a>Erőforráscsoportokhoz való hozzáférés kezelése

[A szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-beli](../../role-based-access-control/overview.md) erőforrásokhoz való hozzáférés kezelésének módja. További információ: [Hozzáférés kezelése az RBAC és az Azure CLI használatával című témakörben.](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>További lépések

- Az Azure Resource Manager megismeréséhez olvassa el az [Azure Resource Manager áttekintése című témakört.](overview.md)
- Az Erőforrás-kezelő sablon szintaxisának megismeréséről [Az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](../templates/template-syntax.md)
- A sablonok fejlesztéséről részletesen olvashat [az oktatóanyagokról.](/azure/azure-resource-manager/)
- Az Azure Resource Manager sablonsémák megtekintéséhez olvassa el a [sablon hivatkozási .](/azure/templates/)