---
title: CLI-parancsfájl – Lassú lekérdezési naplók letöltése – Azure Database for MariaDB
description: Ez a minta Azure CLI-parancsfájl bemutatja, hogyan engedélyezheti és töltheti le a MariaDB-kiszolgáló azure-adatbázisának lassú lekérdezési naplóit.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: a6d4ed70418f39b24a68362d5006f3f374693cb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74777348"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>A MariaDB-kiszolgáló azure CLI-vel való azure-adatbázisának lassú lekérdezési naplóinak engedélyezése és letöltése
Ez a minta CLI-parancsfájl lehetővé teszi és letölti a MariaDB-kiszolgáló egyetlen Azure-adatbázisának lassú lekérdezési naplóit.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. Cserélje &lt;le&gt; a `az monitor` parancsokban log_file_name saját kiszolgálónaplófájlnevére.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb kiszolgáló létrehozása](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat üzemelteti. |
| [az mariadb kiszolgáló konfigurációs listája](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Felsorolja egy kiszolgáló konfigurációs értékeit. |
| [az mariadb kiszolgáló konfigurációkészlete](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Frissíti egy kiszolgáló konfigurációját. |
| [az mariadb szerver-naplók listája](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) | Felsorolja egy kiszolgáló naplófájljait. |
| [az mariadb szerver-naplók letöltése](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) | Letölti a naplófájlokat. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További parancsfájlok kipróbálása: [Azure CLI-minták a MariaDB Azure Database-hez](../sample-scripts-azure-cli.md)
