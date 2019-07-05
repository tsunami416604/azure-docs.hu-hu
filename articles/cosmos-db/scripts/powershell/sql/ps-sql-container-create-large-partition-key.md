---
title: Az Azure PowerShell parancsfájl - tároló létrehozása egy Azure Cosmos-fiók egy nagy partíciókulccsal rendelkező
description: Azure PowerShell-példaszkript – egy nagy partíciós kulccsal, egy Azure Cosmos-fiók egy tároló létrehozása
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: c4e8f26c59312b5216a6834e34c5bbc2bf58563a
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566803"
---
# <a name="create-a-container-with-a-large-partition-key-in-an-azure-cosmos-account-using-powershell"></a>Hozzon létre egy tárolót egy nagy partíciós kulccsal, egy PowerShell-lel az Azure Cosmos-fiókban

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-large-partition-key.ps1 "Create a container with a large partition key in an Azure Cosmos account")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|**Azure-erőforrások**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Létrehoz egy erőforrást. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.