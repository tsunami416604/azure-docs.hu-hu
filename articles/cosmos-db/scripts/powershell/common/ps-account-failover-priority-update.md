---
title: PowerShell-parancsfájl az egyfős Azure Cosmos-fiók feladatátvételi prioritásának módosításához
description: Azure PowerShell-parancsfájlminta – Feladatátvételi prioritás módosítása vagy feladatátvétel idáig váltása egy Azure Cosmos DB egyfős fiókhoz
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: a81938675e72d9ec3a18c920121951e38580b91e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366114"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-db-single-master-account-using-powershell"></a>Feladatátvételi prioritás módosítása vagy feladatátvétel módosítása egy Azure Cosmos DB egyfős fiókhoz a PowerShell használatával

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

> [!NOTE]
> A régió minden `failoverPriority=0` olyan módosítása, amely elindítja a manuális feladatátvételt, és csak a manuális feladatátvételre konfigurált fiókkal végezhető el. Az összes többi régió módosításai egyszerűen módosítja a Cosmos-fiók feladatátvételi prioritását.
> [!NOTE]
> Ez a minta egy SQL (Core) API-fiók használatával szemlélteti. Ha ezt a mintát más API-khoz szeretné használni, másolja a kapcsolódó tulajdonságokat, és alkalmazza az API-specifikus parancsfájlra

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
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Listázza a Cosmos DB-fiókokat, vagy lead egy megadott Cosmos DB-fiókot. |
| [Update-AzCosmosDBAccountFailoverPriority](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Frissítse a Cosmos DB-fiók régióinak feladatátvételi prioritási sorrendjét. |
|**Azure erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.
