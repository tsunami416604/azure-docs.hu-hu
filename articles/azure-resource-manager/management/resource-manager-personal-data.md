---
title: Személyes adatok
description: Ismerje meg, hogyan kezelheti az Azure Resource Manager műveleteihez társított személyes adatokat.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 22cfc1b6096980f3d10db404a1c4e02f2de355d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485259"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Az Azure Resource Managerhez társított személyes adatok kezelése

A bizalmas adatok felfedésének elkerülése érdekében törölje a központi telepítésekben, erőforráscsoportokban vagy címkékben megadott személyes adatokat. Az Azure Resource Manager olyan műveleteket biztosít, amelyek lehetővé teszik a központi telepítések, erőforráscsoportok vagy címkék során esetleg megadott személyes adatok kezelését.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Személyes adatok törlése a telepítési előzményekből

A központi telepítések esetében az Erőforrás-kezelő megtartja a paraméterértékeket és az állapotüzeneteket a központi telepítési előzményekben. Ezek az értékek mindaddig megmaradnak, amíg nem törli a központi telepítést az előzményekből. Ha meg szeretné tekinteni, hogy megadott-e személyes adatokat ezekben az értékekben, sorolja fel a központi telepítéseket. Ha személyes adatokat talál, törölje a központi telepítéseket az előzményekből.

A központi telepítések felsorolásához használja a **következőket:**

* [Lista erőforráscsoport szerint](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [csoport telepítési listája](/cli/azure/group/deployment#az-group-deployment-list)

A központi telepítések törléséhez használja a **következőket:**

* [Szabályzat](/rest/api/resources/deployments/delete)
* [Azerőforrás-csoport telepítése](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [csoport központi telepítésének törlése](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Személyes adatok törlése az erőforráscsoport nevekben

Az erőforráscsoport neve az erőforráscsoport törléséig megmarad. Ha meg szeretné tudni, hogy megadott-e személyes adatokat a nevekben, sorolja fel az erőforráscsoportokat. Ha személyes adatokat talál, [helyezze át az erőforrásokat](move-resource-group-and-subscription.md) egy új erőforráscsoportba, és törölje az erőforráscsoportot a személyes adatokkal a névben.

**Erőforráscsoportok**listázásához használja a következőt:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az csoportlista](/cli/azure/group#az-group-list)

**Erőforráscsoportok**törléséhez használja a következőt:

* [Szabályzat](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Személyes adatok törlése címkékben

A címkék neve és értéke a címke törléséig vagy módosításáig megmarad. Ha meg szeretné tudni, hogy megadott-e személyes adatokat a címkékben, sorolja fel a címkéket. Ha személyes adatokat talál, törölje a címkéket.

Címkék **listázásához**használja a következőt:

* [Lista](/rest/api/resources/tags/list)
* [Get-Aztag](/powershell/module/az.resources/Get-AzTag)
* [az tag lista](/cli/azure/tag#az-tag-list)

Címkék **törléséhez**használja a következőt:

* [Szabályzat](/rest/api/resources/tags/delete)
* [Eltávolítás-Aztag](/powershell/module/az.resources/Remove-AzTag)
* [az tag törlése](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>További lépések
* Az Azure Resource Manager áttekintését a [Mi az erőforrás-kezelő?](overview.md)