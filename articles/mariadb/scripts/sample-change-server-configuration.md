---
title: CLI-parancsfájl – kiszolgáló paramétereinek módosítása – Azure Database for MariaDB
description: Ez a CLI-példaszkript felsorolja az összes elérhető kiszolgálókonfigurációt, és frissíti az innodb_lock_wait_timeout értékét.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 515eb7523c5a08d52ad5eb4f7bd261f3f4e03fc4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74771808"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure Database for MariaDB-kiszolgáló konfigurációinak listázása és frissítése az Azure CLI használatával
Ez a CLI-parancsfájl felsorolja az összes rendelkezésre álló konfigurációs paramétert, valamint a Azure Database for MariaDB kiszolgáló számára engedélyezett értékeit, és beállítja a *innodb_lock_wait_timeoutt* olyan értékre, amely nem az alapértelmezett.

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
| [az MariaDB Server Create](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat üzemelteti. |
| [az MariaDB Server Configuration List](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Egy Azure Database for MariaDB-kiszolgáló konfigurációinak listázása. |
| [az MariaDB Server Configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Azure Database for MariaDB-kiszolgáló konfigurációjának frissítése. |
| [az MariaDB Server Configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Azure Database for MariaDB-kiszolgáló konfigurációjának megjelenítése. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További parancsfájlok kipróbálása: [Azure CLI-minták a Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- A kiszolgálói paraméterekkel kapcsolatos további információkért lásd: [kiszolgálói paraméterek konfigurálása Azure Database for MariaDBban](../howto-server-parameters.md).
