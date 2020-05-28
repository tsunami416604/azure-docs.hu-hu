---
title: 'Azure CLI: & skálázási adatbázis monitorozása Azure SQL Database'
description: Példa az Azure CLI-szkriptre egyetlen adatbázis monitorozásához és méretezéséhez Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: be57309e4b327027ed0185c8eabf783a18cc957e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053722"
---
# <a name="use-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Egyetlen adatbázis figyelése és méretezése a CLI használatával Azure SQL Database
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez az Azure CLI-parancsfájl például az adatbázis méretére vonatkozó információk lekérése után egy különálló adatbázist Azure SQL Database egy másik számítási méretre.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a single database in Azure SQL Database")]

> [!TIP]
> Az az [SQL db op List](/cli/azure/sql/db/op?#az-sql-db-op-list) paranccsal lekérheti az adatbázison végrehajtott műveletek listáját, az [az SQL db op Cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) paranccsal pedig megszakíthatja a frissítési műveletet az adatbázison.

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | Kiszolgálói parancsok. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Egy vagy készletezett adatbázis méret-használati információit jeleníti meg. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További CLI-parancsfájlok is megtalálhatók az [Azure CLI-minták parancsfájljaiban](../az-cli-script-samples-content-guide.md).
