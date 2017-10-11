---
title: "Az Azure PowerShell parancsfájl-hozzon létre egy Azure Cosmos DB tűzfalon |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - tűzfal létrehozása az Azure Cosmos DB rendszerhez"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: caad9212649dd3dc47ddb21555b5b8496c3d2da1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cosmos-db-create-a-firewall-using-powershell"></a>Azure Cosmos DB: Hozzon létre egy tűzfal PowerShell használatával

A PowerShell-parancsfájlpélda létrehoz egy tűzfal bármilyen Azure Cosmos DB API-fiók. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[fő](../../../powershell_scripts/cosmosdb/create-firewall/create-firewall.ps1?highlight=35-36,39-43 "tűzfal létrehozása az Azure Cosmos DB rendszerhez")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [Új AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresource?view=azurermps-3.8.0) | Adatbázis vagy a rugalmas készlet tároló logikai kiszolgáló létrehozása. |
| [Set-AzureRMResource](https://docs.microsoft.com/powershell/module/azurerm.resources/set-azurermresource?view=azurermps-3.8.0) | Módosítja az adatbázis-fiókot. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell további információkért lásd: [Azure PowerShell dokumentációs](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-parancsfájl példák találhatók a [Azure Cosmos DB PowerShell-parancsfájlok](../powershell-samples.md).