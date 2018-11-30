---
title: Az Azure CLI-szkript – kiszolgálói naplók letöltése az Azure Database for MariaDB
description: Az Azure CLI-példaszkript bemutatja, hogyan engedélyezheti és töltheti le egy Azure Database for MariaDB-kiszolgáló naplóit.
services: mariadb
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: cda2f1f02bf48c261da2fdda53c1c145154fe3ee
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585327"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Engedélyezése és letöltése, Azure Database for MariaDB-kiszolgálót az Azure CLI-kiszolgáló lassú lekérdezési naplóinak
A CLI-példaszkript engedélyezi, és a egy egyetlen Azure Database for MariaDB-kiszolgáló lassú lekérdezési naplóinak letölti.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. Cserélje le a &lt;naplófájl_neve&gt; a a `az monitor` -parancsok használata a saját kiszolgálói naplófájlja nevére.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mariadb-kiszolgáló létrehozása](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amelyen az adatbázisok futnak. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Felsorolja egy kiszolgáló konfigurációs értékeit. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Frissíti egy kiszolgáló konfigurációját. |
| [az mariadb-server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) | Felsorolja egy kiszolgáló naplófájljait. |
| [az mariadb-server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) | Letölti a naplófájlokat. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További szkripteket: [Azure CLI-minták az Azure Database for MariaDB](../sample-scripts-azure-cli.md)
