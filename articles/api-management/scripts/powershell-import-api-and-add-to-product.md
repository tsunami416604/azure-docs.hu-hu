---
title: Azure PowerShell-példaszkript – API importálása | Microsoft Docs
description: Azure PowerShell-példaszkript – API importálása
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 10c53af14df7689e1a4b3ae0f092543a6476b02c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86249478"
---
# <a name="import-an-api"></a>API importálása

Ez a példaszkript importál egy API-t, és hozzáadja azt egy API Management-termékhez. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell modul 1,0-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/api-management/import-api-and-add-to-product/import_an_api_and_add_to_product.ps1 "Import an API")]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

Az Azure API Managementhez a [PowerShell-minták](../powershell-samples.md) között találhat további Azure PowerShell-mintákat.
