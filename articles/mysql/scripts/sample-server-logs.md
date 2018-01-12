---
title: "Azure parancssori felület: Azure Database server-naplók letöltése a MySQL"
description: "A parancsfájlpéldát Azure CLI bemutatja, hogyan engedélyezze, és töltse le a kiszolgálói naplók az Azure MySQL-kiszolgáló adatbázisában."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/11/2018
ms.openlocfilehash: b0d34009d189ab136dcb6f28fdccc49b6da9e108
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Engedélyezi, és töltse le a server lassú lekérdezés talál egy Azure-adatbázis MySQL-kiszolgáló Azure parancssori felület használatával
A parancsfájlpéldát CLI lehetővé teszi, és letölti a MySQL-kiszolgáló egy Azure-adatbázis lassú lekérdezés rögzít.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, ez a minta van szükség, hogy az Azure parancssori felület verzióját futtatja, 2.0-s vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl
Ez a parancsfájlpélda módosítsa a kijelölt sorok testre szabhatja a rendszergazda felhasználónevét és jelszavát. Cserélje le a < naplófájl_neve > a figyelő az parancsokban a saját kiszolgáló naplófájl neve.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Parancsfájl ismertetése
A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| **A parancs** | **Megjegyzések** |
|---|---|
| [az csoport létrehozása](/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az mysql-kiszolgáló létrehozása](/cli/azure/mysql/server#az_msql_server_create) | Az adatbázisokat üzemeltető MySQL kiszolgáló létrehozása. |
| [az mysql konfigurációs kiszolgálólista](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | A kiszolgáló konfigurációs értékeket listában. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | A kiszolgáló konfigurációjának frissítése. |
| [az mysql server-naplók listája](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) | A naplófájlok felsorolását a kiszolgálón. |
| [az mysql server-naplók letöltése](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) | Töltse le a rendszernapló fájljaiban. |
| [az csoport törlése](/cli/azure/group#az_group_delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>További lépések
- További információt az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).
- Próbálja meg a további parancsfájlok: [MySQL az Azure-adatbázis Azure CLI-minták](../sample-scripts-azure-cli.md)
