---
title: CLI-parancsfájl – Kiszolgálóparaméterek módosítása – Azure Database for MariaDB
description: Ez a CLI-példaszkript felsorolja az összes elérhető kiszolgálókonfigurációt, és frissíti az innodb_lock_wait_timeout értékét.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 515eb7523c5a08d52ad5eb4f7bd261f3f4e03fc4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771808"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>A MariaDB-kiszolgálóazure-adatbázis konfigurációinak listája és frissítése az Azure CLI használatával
Ez a minta CLI-parancsfájl felsorolja az összes rendelkezésre álló konfigurációs paramétert, valamint azok megengedett értékeit a MariaDB-kiszolgáló Azure Database számára, és a *innodb_lock_wait_timeout* az alapértelmezetttől eltérő értékre állítja be.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb kiszolgáló létrehozása](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat üzemelteti. |
| [az mariadb kiszolgáló konfigurációs listája](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | A MariaDB-kiszolgáló azure-adatbázisának konfigurációi. |
| [az mariadb kiszolgáló konfigurációkészlete](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Frissítse a MariaDB-kiszolgálóazure-adatbázisának konfigurációját. |
| [az mariadb kiszolgáló konfigurációs megjelenítése](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | A MariaDB-kiszolgáló azure-adatbázisának konfigurációjának megjelenítése. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További parancsfájlok kipróbálása: [Azure CLI-minták a MariaDB Azure Database-hez](../sample-scripts-azure-cli.md)
- A kiszolgálóparamétereiről a [Kiszolgálóparaméterkonfigurálás az Azure Database for MariaDB alkalmazásban](../howto-server-parameters.md)című témakörben talál további információt.
