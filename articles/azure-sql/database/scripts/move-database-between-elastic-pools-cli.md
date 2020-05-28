---
title: 'Azure CLI: adatbázis áthelyezése rugalmas készletek között'
description: Azure CLI-példa parancsfájl, amely két rugalmas készletet hoz létre, és áthelyez egy adatbázist SQL Database egy rugalmas készletből egy másikba az Azure CLI használatával.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 2bcf9f4218f535556ff83ccaad130bb1da28ef69
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054058"
---
# <a name="use-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>A CLI használata egy adatbázis SQL Databaseba való áthelyezéséhez egy rugalmas SQL-készletben


Ez az Azure CLI-parancsfájl két rugalmas készletet hoz létre, és egy adatbázist helyez át SQL Database egy rugalmas SQL-készletből egy másik SQL rugalmas készletbe, majd áthelyezi az adatbázist a rugalmas készletből egyetlen adatbázis-számítási méretre.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

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
| [az SQL rugalmas-készletek](/cli/azure/sql/elastic-pool) | Rugalmas készlet parancsai. |
| [az SQL db](/cli/azure/sql/db) | Adatbázis-parancsok. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../az-cli-script-samples-content-guide.md) találhat.
