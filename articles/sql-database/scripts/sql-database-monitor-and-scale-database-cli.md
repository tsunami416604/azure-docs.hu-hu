---
title: "Parancssori felület példa-figyelő-skálázási-egyetlen Azure SQL adatbázis |} Microsoft Docs"
description: "Az Azure parancssori felület a példaként megadott parancsfájlt figyelését, valamint egy Azure SQL-adatbázis méretezése"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: 34d935518f27bc465832b01a0d739f17ee0a13ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Parancssori felület használatával figyelheti és egy SQL-adatbázis méretezése

Az Azure CLI mintaparancsfájl egy Azure SQL-adatbázis különböző teljesítményszintet is méretezi a méretre vonatkozó adatok az adatbázis lekérdezése után. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az sql-kiszolgáló létrehozása](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Egy adatbázist tároló logikai kiszolgáló létrehozása. |
| [az sql db megjelenítése-használat](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_show_usage) | Az adatbázis méretének használati információit jeleníti meg. |
| [az sql-adatbázis frissítése](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_update) | Frissíti az adatbázis-tulajdonságai (például a szolgáltatási szint vagy a teljesítmény szint), vagy azokat, kívüli vagy rugalmas készletek között adatbázis helyezi. |
| [az csoport törlése](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |
|||

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További SQL-adatbázis CLI parancsfájl minták megtalálhatók a [dokumentáció az Azure SQL Database](../sql-database-cli-samples.md).
