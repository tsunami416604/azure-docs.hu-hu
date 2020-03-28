---
title: CLI példa-egyetlen adatbázis hozzáadása a feladatátvételi csoporthoz - Azure SQL Database
description: Azure CLI példaparancsfájl egy Azure SQL Database egyetlen adatbázis létrehozásához, adja hozzá egy feladatátvételi csoporthoz, és tesztelje a feladatátvételt.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: bc238f08021bb9fb16b8c7319e63acebdfec3948
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061903"
---
# <a name="use-cli-to-add-an-azure-sql-database-into-a-failover-group"></a>Azure SQL-adatbázis hozzáadása feladatátvételi csoporthoz a CLI használatával

Ez az Azure CLI-parancsfájl-példa egyetlen adatbázist hoz létre, létrehoz egy feladatátvételi csoportot, hozzáadja az adatbázist, és teszteli a feladatátvételt.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az erőforráscsoport és a hozzá tartozó összes erőforrás eltávolításához használja a következő parancsot.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Mintahivatkozás

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | Adatbázis-parancsok. |
| [az sql feladatátvételi csoport](/cli/azure/sql/failover-group) | Feladatátvételi csoport parancsai. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.
