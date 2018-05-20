---
title: Az Azure Resource Manager személyes adatok |} Microsoft Docs
description: 'Útmutató: Azure Resource Manager operations társított személyes adatok kezelésére.'
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 5c176dd185a9d2a245045e9c954279506d713e5f
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Az Azure Resource Manager kapcsolódó személyes adatok kezelése

A kiküszöbölése bizalmas adatokat, törölje a központi telepítések, erőforráscsoport-sablonok vagy címkék megadott személyes adatokat. Az Azure Resource Manager biztosít, amelyekkel kezelheti a központi telepítések, erőforráscsoport-sablonok vagy címkék megadott személyes adatokat műveletek.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>A központi telepítés előzményei a személyes adatok törlése

Központi telepítése esetén a Resource Manager paraméterértékek és az üzembe helyezési előzményeket az állapotüzenetek megőrzi. Ezek az értékek továbbra is fennáll, addig, amíg a telepítés az előzmények törlése. Ha megadta ezeket az értékeket a személyes adatok megtekintéséhez a központi telepítések felsorolása Ha személyes adatokat, törölje a központi telepítések az előzmények.

A lista **központi telepítések** az előzményekben található használja:

* [Erőforráscsoport listája](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Get-AzureRmResourceGroupDeployment)
* [az telepítés listájából](/cli/azure/group/deployment#az-group-deployment-list)

A törlendő **központi telepítések** az előzmények használja:

* [Törlés](/rest/api/resources/deployments/delete)
* [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroupDeployment)
* [az csoport központi telepítésének törlése](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Az erőforráscsoportok nevei a személyes adatok törlése

Az erőforráscsoport neve továbbra is fennáll, amíg nem törli az erőforráscsoportot. Ha megadta a nevét, a személyes adatok megtekintéséhez az erőforráscsoportok felsorolása Ha megtalálta a személyes adatok [az erőforrások áthelyezése](resource-group-move-resources.md) egy új erőforráscsoportot, és törölje az erőforráscsoport neve személyes adatokat.

A lista **erőforráscsoportok**, használja:

* [List](/rest/api/resources/resourcegroups/list)
* [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/Get-AzureRmResourceGroup)
* [az listájából](/cli/azure/group#az-group-list)

A törlendő **erőforráscsoportok**, használja:

* [Törlés](/rest/api/resources/resourcegroups/delete)
* [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>A címkék személyes adatok törlése

Címkék neveit és értékeit továbbra is fennáll, addig, amíg törlése vagy a címke módosítása. Ha megadta a címkék a személyes adatok megtekintéséhez a címkék felsorolása Ha személyes adatokat, törölje a címkék.

A lista **címkék**, használja:

* [List](/rest/api/resources/tags/list)
* [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag)
* [az címkelista](/cli/azure/tag#az-tag-list)

A törlendő **címkék**, használja:

* [Törlés](/rest/api/resources/tags/delete)
* [Remove-AzureRmTag](/powershell/module/azurerm.tags/remove-azurermtag)
* [az címke törlése](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>További lépések
* Az Azure Resource Manager áttekintése, tekintse meg a [Mi az erőforrás-kezelő?](resource-group-overview.md)