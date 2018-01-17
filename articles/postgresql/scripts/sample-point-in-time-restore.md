---
title: "Az Azure CLI-parancsfájlt: egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis visszaállítása"
description: "A parancsfájlpéldát Azure parancssori felület egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis és az adatbázisok visszaállítása egy korábbi időpontra időben jeleníti meg."
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
ms.openlocfilehash: dfc53ae10055b0e8583fb0c705e605bbbb999760
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Egy Azure parancssori felület használatával PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis visszaállítása
A parancsfájlpéldát CLI időben PostgreSQL-kiszolgáló egy Azure-adatbázis visszaállítása egy korábbi időpontra.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, ez a minta van szükség, hogy az Azure parancssori felület verzióját futtatja, 2.0-s vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl
Ez a parancsfájlpélda módosítsa a kijelölt sorok testre szabhatja a rendszergazda felhasználónevét és jelszavát. Cserélje le a saját előfizetés-azonosító. az-figyelő utasítással használt előfizetés-azonosító
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Parancsfájl ismertetése
A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| **A parancs** | **Megjegyzések** |
|---|---|
| [az csoport létrehozása](/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az postgresql-kiszolgáló létrehozása](/cli/azure/postgresql/server#az_msql_server_create) | Az adatbázisokat üzemeltető PostgreSQL-kiszolgáló létrehozása. |
| [az postgresql-kiszolgálójának visszaállítását](/cli/azure/postgresql/server#az_msql_server_restore) | A kiszolgáló helyreállítása biztonsági másolatból. |
| [az csoport törlése](/cli/azure/group#az_group_delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>További lépések
- További információt az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).
- Próbálja meg a további parancsfájlok: [PostgreSQL Azure-adatbázis Azure CLI-minták](../sample-scripts-azure-cli.md)
- [Biztonsági mentése és visszaállítása egy kiszolgálóhoz az Azure-adatbázis az Azure portál használatával PostgreSQL](../howto-restore-server-portal.md)
