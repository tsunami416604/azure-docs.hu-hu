---
title: "Az Azure CLI-parancsfájlt: letöltési-kiszolgáló naplóit Azure-adatbázis PostgreSQL"
description: "Ez a parancsfájlpélda Azure CLI bemutatja, hogyan engedélyezheti, és töltse le a kiszolgálói naplók az Azure-adatbázisának PostgreSQL-kiszolgáló."
services: postgresql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 8bd6e193ca52401b16e141162a76d39bdef840f1
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Engedélyezi, és töltse le a server lassú lekérdezés talál egy Azure-adatbázis PostgreSQL-kiszolgáló Azure parancssori felület használatával
A parancsfájlpéldát CLI lehetővé teszi, és letölti a lassú lekérdezés rögzít PostgreSQL-kiszolgáló egy Azure-adatbázis.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, ez a minta van szükség, hogy az Azure parancssori felület verzióját futtatja, 2.0-s vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl
Ez a parancsfájlpélda módosítsa a kijelölt sorok testre szabhatja a rendszergazda felhasználónevét és jelszavát. Cserélje le a < naplófájl_neve > a figyelő az parancsokban a saját kiszolgáló naplófájl neve.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Parancsfájl ismertetése
A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| **A parancs** | **Megjegyzések** |
|---|---|
| [az csoport létrehozása](/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az postgres kiszolgáló létrehozása](/cli/azure/postgres/server#az_msql_server_create) | Az adatbázisokat üzemeltető PostgreSQL-kiszolgáló létrehozása. |
| [az postgres konfigurációs kiszolgálólista](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | A kiszolgáló konfigurációs értékeket listában. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | A kiszolgáló konfigurációjának frissítése. |
| [az postgres server-naplók listája](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | A naplófájlok felsorolását a kiszolgálón. |
| [az postgres server-naplók letöltése](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | Töltse le a rendszernapló fájljaiban. |
| [az csoport törlése](/cli/azure/group#az_group_delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>További lépések
- További információt az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).
- Próbálja meg a további parancsfájlok: [PostgreSQL Azure-adatbázis Azure CLI-minták](../sample-scripts-azure-cli.md)
- [Konfigurálja, és hozzáférést kiszolgálónaplókban olvashatók az Azure-portálon](../howto-configure-server-logs-in-portal.md)
