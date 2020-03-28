---
title: CLI-parancsfájl – Kiszolgáló méretezése – Azure-adatbázis a MariaDB-hez
description: Ez a minta CLI-parancsfájl a Metrikák lekérdezése után egy másik teljesítményszintre méretezi a MariaDB-kiszolgáló Azure-adatbázisát.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 562f265cccf444740c177a41e516f9066188613e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771635"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure-adatbázis figyelése és méretezése a MariaDB-kiszolgálóhoz az Azure CLI használatával
Ez a minta CLI-parancsfájl egyetlen Azure-adatbázis számítási és tárolási méretezését a MariaDB-kiszolgálószámára a metrikák lekérdezése után méretezi. A számítás fel- vagy leskálázható. A tárhely csak felskálázható.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
Frissítse a parancsfájlt az előfizetés-azonosítójával.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb kiszolgáló létrehozása](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat üzemelteti. |
| [az mariadb kiszolgáló frissítése](/cli/azure/mariadb/server#az-mariadb-server-update) | Frissíti a MariaDB-kiszolgáló tulajdonságait. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- További információ [az Azure Database for MariaDB számítási és tárolási adatbázisáról](../concepts-pricing-tiers.md)
- További parancsfájlok kipróbálása: [Azure CLI-minták a MariaDB Azure Database-hez](../sample-scripts-azure-cli.md)
- További információ az [Azure CLI-ről](/cli/azure)
