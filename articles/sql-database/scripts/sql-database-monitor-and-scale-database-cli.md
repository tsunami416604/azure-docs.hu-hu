---
title: CLI-példaszkript – Egyetlen Azure SQL-adatbázis monitorozása és méretezése | Microsoft Docs
description: Azure CLI-példaszkript – Egyetlen Azure SQL-adatbázis monitorozása és méretezése
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 00464de212b7a2faa62eeac15bc4db1fd0f84b4f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364437"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Egyetlen SQL-adatbázis monitorozása és méretezése CLI-vel

Ez az Azure CLI-példaszkript egyetlen Azure SQL-adatbázist méretez más teljesítményszintre az adatbázis méretadatainak lekérdezése után. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Az [az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list) paranccsal lekérheti az adatbázison végrehajtott műveletek listáját, az [az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel) paranccsal pedig megszakíthat egy, az adatbázison végzett frissítési műveletet.

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Létrehoz egy, az adatbázist üzemeltető logikai kiszolgálót. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_show_usage) | Megjeleníti egy adatbázis méretkihasználtsági adatait. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_update) | Frissíti az adatbázis tulajdonságait (például a szolgáltatási vagy a teljesítményszintet), vagy áthelyezi az adatbázist egy rugalmas készletbe vagy rugalmas készletek között, vagy kiveszi egy rugalmas készletből. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.
