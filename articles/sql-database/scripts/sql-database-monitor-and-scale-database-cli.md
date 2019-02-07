---
title: CLI-példaszkript – Egyetlen Azure SQL-adatbázis monitorozása és méretezése | Microsoft Docs
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
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 18d4127e8babfcb541312d14b80d2d5477feb331
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766436"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Egyetlen SQL-adatbázis monitorozása és méretezése CLI-vel

Ez az Azure CLI-példaszkript egyetlen Azure SQL-adatbázist méretez más számítási méretre az adatbázis méretadatainak lekérdezése után. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Az [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) paranccsal lekérheti az adatbázison végrehajtott műveletek listáját, az [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) paranccsal pedig megszakíthat egy, az adatbázison végzett frissítési műveletet.

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy SQL Database-kiszolgálót, amelyen az önálló adatbázisok és rugalmas készleteket. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db) | Egy önálló vagy készletezett adatbázis méretkihasználtsági adatait jeleníti meg. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Frissíti az adatbázis tulajdonságait (például a szolgáltatási szintet vagy a számítási méretet), vagy áthelyezi az adatbázist egy rugalmas készletbe vagy rugalmas készletek között, vagy kiveszi egy rugalmas készletből. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.
