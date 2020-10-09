---
title: CLI-szkript – lassú lekérdezési naplók letöltése – Azure Database for MySQL
description: Ez az Azure CLI-példaszkript bemutatja, hogyan engedélyezheti és töltheti le egy Azure Database for MySQL-kiszolgáló naplóit.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 9d1841d54682764c01406c2e42689f1948a2bf3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502443"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure Database for MySQL-kiszolgáló lassú lekérdezési naplóinak engedélyezése és letöltése az Azure CLI-vel
Ez a CLI-példaszkript engedélyezi és letölti egy Azure Database for MySQL-kiszolgáló lassú lekérdezési naplóit.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. Cserélje le &lt; a &gt; parancsok log_file_name a `az monitor` saját kiszolgálói naplófájljának nevére.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Létrehoz egy MySQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) | Felsorolja egy kiszolgáló konfigurációs értékeit. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) | Frissíti egy kiszolgáló konfigurációját. |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) | Felsorolja egy kiszolgáló naplófájljait. |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) | Letölti a naplófájlokat. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További szkripteket az [Azure Database for MySQL-hez való Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
