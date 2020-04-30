---
title: CLI-példa-monitor-Scale-Single Azure SQL Database
description: Azure CLI-példaszkript – Egyetlen Azure SQL-adatbázis monitorozása és méretezése
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 191de1fdbbee3e31bfcd366cbec8a70732b23b5c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061817"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Egyetlen SQL-adatbázis monitorozása és méretezése CLI-vel

Ez az Azure CLI-példaszkript egyetlen Azure SQL-adatbázist méretez más számítási méretre az adatbázis méretadatainak lekérdezése után.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

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

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.
