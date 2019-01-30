---
title: Az Azure Resource Manager személyes adatok |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure Resource Manager-műveletet tartozó személyes adatok.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 01e2b1608d55d7852db947181482d34d6d8010d2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215045"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Az Azure Resource Manager tartozó személyes adatok kezelése

Kiküszöbölése bizalmas adatokat, törölje a központi telepítések, erőforráscsoportok vagy címkék megadott személyes adatokat. Az Azure Resource Manager biztosít, amelyekkel kezelheti a személyes adatokat a központi telepítések, erőforráscsoportok vagy címkék megadott műveleteket.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Személyes adatok az üzembe helyezési előzmények törlése

Telepítések esetén a Resource Manager megőrzi a paraméterértékeket, és az üzembe helyezési előzmények az állapotüzenetek. Ezek az értékek maradnak, amíg nem törli az üzembe helyezés az előzményekben tekintheti át. Ha megadta-e ezeket az értékeket a személyes adatok megtekintéséhez a központi telepítések listája. Személyes adatainak megkeresése, ha törli a központi telepítéseket az előzményekben tekintheti át.

A lista **központi telepítések** az előzményekben található használja:

* [Lista erőforráscsoport alapján](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az üzembe helyezés listájából](/cli/azure/group/deployment#az-group-deployment-list)

Törlendő **központi telepítések** használja az előzményekben tekintheti át:

* [Törlés](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az csoport központi telepítésének törlése](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Az erőforráscsoportok nevei a személyes adatok törlése

Az erőforráscsoport nevét továbbra is fennáll, amíg nem törli az erőforráscsoportot. Ha megadta-e a neveket a személyes adatok megtekintéséhez az erőforráscsoportok listája. Ha megtalálta a személyes adatok [az erőforrások áthelyezése](resource-group-move-resources.md) egy új erőforráscsoportot, és töröljük az erőforráscsoport nevét a személyes adatokkal.

A lista **erőforráscsoportok**, használja:

* [List](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az csoport listája](/cli/azure/group#az-group-list)

A törlendő **erőforráscsoportok**, használja:

* [Törlés](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>A címkék a személyes adatok törlése

A címkék nevei és értékei megmarad, amíg nem törli vagy módosíthatja a címke. Ha a megadott személyes adatokat a címkék megtekintéséhez a címkék listája. Személyes adatainak megkeresése, ha a címkék törlése.

A lista **címkék**, használja:

* [List](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az a listán](/cli/azure/tag#az-tag-list)

A törlendő **címkék**, használja:

* [Törlés](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az címke törlése](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>További lépések
* Az Azure Resource Manager áttekintése, tekintse meg a [Mi az a Resource Manager?](resource-group-overview.md)