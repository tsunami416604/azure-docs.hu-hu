---
title: Azure CLI-szkript – Azure Database for MariaDB kiszolgáló méretezése
description: Ez a CLI-parancsfájl a metrikák lekérdezése után átméretezi Azure Database for MariaDB kiszolgálót egy másik teljesítményszint.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 08/01/2019
ms.openlocfilehash: 99f85abe0c8f08bedb3e9808d2b740d4b7842257
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720361"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure Database for MariaDB-kiszolgáló monitorozása és méretezése az Azure CLI-vel
Ez a CLI-parancsfájl a metrikák lekérdezése után egy Azure Database for MariaDB kiszolgáló számítási és tárolási kapacitását méretezi.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. Cserélje le a `az monitor` parancsokban használt előfizetés-azonosítót a saját előfizetés-azonosítójával.  
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh?highlight=15-16 "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az MariaDB Server Create](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat üzemelteti. |
| [az MariaDB Server Update](/cli/azure/mariadb/server#az-mariadb-server-update) | Frissíti a MariaDB-kiszolgáló tulajdonságait. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- További információ a [Azure Database for MariaDB számítási és tárolási](../concepts-pricing-tiers.md) feladatokról
- További parancsfájlok kipróbálása: [Azure CLI-minták a Azure Database for MariaDBhoz](../sample-scripts-azure-cli.md)
- További információ az [Azure CLI](/cli/azure) -ről
