---
title: CLI-példa – aktív geo-replikáció – egyetlen Azure SQL Database
description: Az Azure CLI-szkripttel beállíthatja, hogy az aktív geo-replikáció egyetlen adatbázishoz legyen Azure SQL Databaseban, és feladatátvételt hajtson végre.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 93afb5b52df7ac3ff638ff5fc180c3c4774880b8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020387"
---
# <a name="use-cli-to-configure-active-geo-replication-for-a-single-database-in-azure-sql-database"></a>A CLI használatával konfigurálhatja az aktív földrajzi replikálást egyetlen adatbázishoz Azure SQL Database

Ez az Azure CLI parancsfájl-példa az aktív geo-replikálást konfigurálja egyetlen adatbázishoz, és feladatátvételt hajt végre az adatbázis egy másodlagos replikáján.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-single-database.sh "Set up active geo-replication for single database")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| | |
|---|---|
| [az SQL db replika](/cli/azure/sql/db/replica) | Adatbázis-replika parancsai. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../../azure-sql/database/az-cli-script-samples-content-guide.md) találhat.