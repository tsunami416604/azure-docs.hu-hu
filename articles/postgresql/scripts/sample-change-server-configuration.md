---
title: "Az Azure CLI-parancsfájlt: kiszolgáló konfigurációjának módosításához"
description: "Ez a parancsfájlpélda CLI kiszolgáló elérhető beállításokat ismerteti, és frissíti a a lehetőségek közül."
services: postgres
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 334322557c83d5576c9a11def6bb0dc0e3ad1fec
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Az Azure parancssori felület használatával PostgreSQL-kiszolgáló Azure adatbázisának lista és a frissítés konfigurációk
Ez a parancsfájlpélda CLI felsorolja az összes rendelkezésre álló konfigurációs paraméterek, valamint az engedélyezett értékek az Azure Database PostgreSQL-kiszolgáló, és beállítja a *log_retention_days* , amely az egyik alapértelmezett értéktől eltérő értékre.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Mintaparancsfájl
Ez a parancsfájlpélda módosítsa a kijelölt sorok testre szabhatja a rendszergazda felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot és a vele társított összes erőforrást.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Parancsfájl ismertetése
A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| **A parancs** | **Megjegyzések** |
|---|---|
| [az csoport létrehozása](/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az postgres kiszolgáló létrehozása](/cli/azure/postgres/server#az_postgres_server_create) | Az adatbázisokat üzemeltető PostgreSQL-kiszolgáló létrehozása. |
| [az postgres konfigurációs kiszolgálólista](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis beállításait a listában. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis konfigurációjának frissítése. |
| [az postgres kiszolgáló konfiguráció megjelenítése](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) | Egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis konfigurációjának megjelenítése. |
| [az csoport törlése](/cli/azure/group#az_group_delete) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>További lépések
- További információt az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).
- Próbálja meg a további parancsfájlok: [PostgreSQL Azure-adatbázis Azure CLI-minták](../sample-scripts-azure-cli.md)
- A kiszolgáló paraméterek további információkért lásd: [server paraméterek beállítása az Azure portálon](../howto-configure-server-parameters-using-portal.md).
