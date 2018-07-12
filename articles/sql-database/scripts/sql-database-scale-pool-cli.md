---
title: CLI-példaszkript – Rugalmas SQL-készlet méretezése az Azure SQL Database-ben | Microsoft Docs
description: Egy Azure CLI-példaszkript egy rugalmas SQL-készlet méretezésére az Azure SQL Database-ben
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
ms.openlocfilehash: 704cfae5be9e23d359f93452e6f45a87deb0f69f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697665"
---
# <a name="use-cli-to-scale-a-sql-elastic-pool-in-azure-sql-database"></a>Rugalmas SQL-készlet méretezésére az Azure SQL Database-ben a CLI használatával

Ez az Azure CLI-példaszkript rugalmas SQL-készleteket hoz létre, áthelyezi a készletezett adatbázisokat, és módosítja a rugalmas készletek teljesítményszintjét. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy logikai kiszolgáló, egy SQL Database-adatbázis és tűzfalszabályok létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Létrehoz egy logikai kiszolgálót, amelyen az SQL Database szolgáltatás fut. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) | Létrehoz egy rugalmas adatbáziskészletet a logikai kiszolgálón. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_create) | Létrehozza az SQL Database-adatbázist a logikai kiszolgálón. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) | Frissít egy rugalmas adatbáziskészletet. Példánkban a hozzárendelt eDTU-t változtatja meg. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.
