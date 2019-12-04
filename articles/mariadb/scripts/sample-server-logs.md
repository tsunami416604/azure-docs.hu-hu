---
title: CLI-szkript – lassú lekérdezési naplók letöltése – Azure Database for MariaDB
description: Ez a minta Azure CLI-szkript bemutatja, hogyan engedélyezheti és töltheti le egy Azure Database for MariaDB-kiszolgáló lassú lekérdezési naplóit.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: a6d4ed70418f39b24a68362d5006f3f374693cb6
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777348"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure Database for MariaDB-kiszolgáló lassú lekérdezési naplóinak engedélyezése és letöltése az Azure CLI használatával
Ez a CLI-szkript engedélyezi és letölti az egyetlen Azure Database for MariaDB-kiszolgáló lassú lekérdezési naplóit.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. Cserélje le a &lt;log_file_name&gt;t az `az monitor` parancsokban a saját kiszolgáló naplófájljának nevére.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az MariaDB Server Create](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat üzemelteti. |
| [az MariaDB Server Configuration List](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Felsorolja egy kiszolgáló konfigurációs értékeit. |
| [az MariaDB Server Configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Frissíti egy kiszolgáló konfigurációját. |
| [az MariaDB Server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) | Felsorolja egy kiszolgáló naplófájljait. |
| [az MariaDB Server-logs Letöltés](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) | Letölti a naplófájlokat. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További parancsfájlok kipróbálása: [Azure CLI-minták a Azure Database for MariaDB](../sample-scripts-azure-cli.md)
