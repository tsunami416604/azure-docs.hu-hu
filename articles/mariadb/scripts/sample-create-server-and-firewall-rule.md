---
title: Az Azure CLI szkript – Azure Database for MariaDB létrehozása
description: A CLI-példaszkript létrehoz egy Azure Database for MariaDB-kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: a7e8a4c8f8572cfdd236b1752e89b3a1b4e7b758
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546810"
---
# <a name="create-a-mariadb-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Hozzon létre egy MariaDB-kiszolgálót, és konfiguráljon egy tűzfalszabályt az Azure CLI használatával
A CLI-példaszkript létrehoz egy Azure Database for MariaDB-kiszolgálót, és konfigurál egy kiszolgálószintű tűzfalszabályt. Miután a szkript sikeresen lefutott, az összes Azure-szolgáltatások és a konfigurált IP-címet a MariaDB-kiszolgáló érhető el.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for mariadb, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/delete-mariadb.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb-kiszolgáló létrehozása](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amelyen az adatbázisok futnak. |
| [az mariadb kiszolgálói tűzfal létrehozása](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) | Létrehoz egy tűzfalszabályt, amely engedélyezi a kiszolgáló és az azon lévő adatbázisok elérését a megadott IP-címtartományból. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure CLI-vel kapcsolatos további információkért tekintse meg: [Az Azure CLI dokumentációját](/cli/azure).
- További szkripteket: [Azure CLI-minták az Azure Database for MariaDB](../sample-scripts-azure-cli.md)
