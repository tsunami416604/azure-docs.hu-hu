---
title: PowerShell-parancsfájl egy Azure Cosmos DB fiók kulcs-és kapcsolódási karakterlánc-műveleteinek lekéréséhez
description: Azure PowerShell parancsfájl-minta – fiók kulcsa és a kapcsolatok karakterláncának műveletei egy Azure Cosmos DB-fiókhoz
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 04ed417d05a9770162dfe4bfbbf85a32e85e0761
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488197"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-db-account-using-powershell"></a>Azure Cosmos DB-fiókhoz tartozó, a PowerShell használatával létesített kapcsolatok sztringje és a fiók kulcsának műveletei

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

> [!NOTE]
> Ez a minta egy SQL API-fiók használatát mutatja be. Ha más API-khoz szeretné használni ezt a mintát, másolja a kapcsolódó tulajdonságokat, és alkalmazza az API-specifikus parancsfájlra.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) | Lekérdezi a Cosmos DB fiókhoz tartozó kapcsolatok sztringjét vagy kulcsát (írható-olvasható vagy írásvédett). |
| [Új – AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/new-azcosmosdbaccountkey) | A megadott kulcs újralétrehozása egy Cosmos DB-fiókhoz. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).