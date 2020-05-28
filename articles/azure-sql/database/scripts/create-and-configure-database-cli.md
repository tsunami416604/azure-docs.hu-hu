---
title: 'Azure CLI: egyetlen adatbázis létrehozása'
description: Az Azure CLI példa szkriptjét egyetlen Azure SQL Database létrehozására használhatja.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ab847f13b6c75eeb31c4d20877d7a314e53f47ac
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054088"
---
# <a name="use-the-azure-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Egyetlen Azure SQL Database létrehozása és tűzfalszabály konfigurálása az Azure CLI használatával
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez az Azure CLI-parancsfájl egy Azure SQL Database hoz létre, és konfigurál egy kiszolgálói szintű tűzfalszabály-szabályt. Miután a szkript sikeresen lefutott, az SQL Database elérhető lesz az összes Azure-szolgáltatásból és a konfigurált IP-címről.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server#az-sql-server-create) | Kiszolgálói parancsok |
| [az SQL Server Firewall](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Kiszolgálói tűzfal parancsai. |
| [az SQL db](/cli/azure/sql/db#az-sql-db-create) | Adatbázis-parancsok. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../az-cli-script-samples-content-guide.md) találhat.
