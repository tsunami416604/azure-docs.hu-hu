---
title: Azure PowerShell parancsfájl – feladatátvételi prioritás módosítása vagy feladatátvételi trigger indítása Azure Cosmos-fiókhoz
description: Azure PowerShell parancsfájl-minta – feladatátvételi prioritás módosítása vagy feladatátvételi trigger indítása Azure Cosmos-fiókhoz
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: e4406124a7ea4eac213d830d0e5960e76fb6d364
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155407"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-using-powershell"></a>Feladatátvételi prioritás módosítása vagy feladatátvétel indítása Azure Cosmos-fiókhoz a PowerShell használatával

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

> [!NOTE]
> Egy régió `failoverPriority=0` bármely változása manuális feladatátvételt indít el, és csak manuális feladatátvételre konfigurált fiókkal végezhető el. Az összes többi régió módosításai egyszerűen módosítják a Cosmos-fiókok feladatátvételi prioritását.
> [!NOTE]
> Ez a példa egy SQL-(Core-) API-fiók használatát mutatja be. Ha más API-khoz szeretné használni ezt a mintát, másolja a kapcsolódó tulajdonságokat, és alkalmazza az API-specifikus parancsfájlra.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

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
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Műveletet hív meg egy erőforráson. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.
