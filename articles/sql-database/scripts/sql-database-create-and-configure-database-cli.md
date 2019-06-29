---
title: CLI-példa – Azure SQL-adatbázis létrehozása | Microsoft Docs
description: Ezzel az Azure CLI-példaszkripttel létrehozhat egy SQL-adatbázist.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
manager: craigg
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 2a5fb6fcd9b1f0c327df23741284ec0d5fc26c71
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447878"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Egy Azure SQL-adatbázis létrehozása és egy tűzfalszabály konfigurálása a parancssori felület használatával

Ez az Azure CLI-példaszkript létrehoz egy Azure SQL-adatbázist, és konfigurál egy kiszolgálószintű tűzfalszabályt. Miután a szkript sikeresen lefutott, az SQL Database elérhető lesz az összes Azure-szolgáltatásból és a konfigurált IP-címről.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítsa el az erőforráscsoportot és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Egy SQL Database-kiszolgálót, amelyen egy önálló adatbázist vagy rugalmas készletet hoz létre. |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Létrehoz egy tűzfalszabályt, minden önálló adatbázis egy rugalmas készletek az SQL Database-kiszolgálón, a megadott IP-címtartományból hozzáférésének engedélyezéséhez. |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create) | Egy önálló adatbázis vagy a rugalmas készletet hoz létre. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.
