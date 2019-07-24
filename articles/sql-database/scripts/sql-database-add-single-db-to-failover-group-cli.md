---
title: CLI-példa – önálló adatbázis hozzáadása a feladatátvételi csoporthoz – Azure SQL Database
description: Az Azure CLI példa parancsfájlt Azure SQL Database önálló adatbázis létrehozására, a feladatátvételi csoportba való felvételére és a feladatátvételi tesztre.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: jroth
ms.date: 07/16/2019
ms.openlocfilehash: 77fb1e8195c60d6f62baa72bee361c016463f3c2
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68445016"
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Azure SQL-adatbázis áthelyezése egy rugalmas SQL-készletben a CLI használatával

Ez a PowerShell-parancsfájl egy önálló adatbázist hoz létre, létrehoz egy feladatátvételi csoportot, hozzáadja az adatbázist, és teszteli a feladatátvételt. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Egy előfizetést állít be az aktuális aktív előfizetésre. | 
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Létrehoz egy kiszolgáló tűzfalszabály-szabályait. | 
| [az SQL feladatátvétel-csoport létrehozása](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Létrehoz egy feladatátvételi csoportot. | 
| [az SQL feladatátvételi csoport listája](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Felsorolja a kiszolgálók feladatátvételi csoportjait. |
| [az SQL feladatátvétel-csoport beállítása – elsődleges](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Állítsa be a feladatátvételi csoport elsődlegesét úgy, hogy az a jelenlegi elsődleges kiszolgáló összes adatbázisát elvégzi. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.
