---
title: A szolgáltatás paramétereinek konfigurálása az Azure Database for postgresql-hez
description: Ez a cikk ismerteti a szolgáltatás paramétereinek konfigurálása az Azure Database for postgresql-hez az Azure CLI parancssori használatával.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 5520c08d2bf5dba85ece1de0bca7329286625911
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968050"
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Azure CLI-vel a kiszolgáló konfigurációs paramétereinek testreszabása
Lista, megjelenítése és a parancssori felület (Azure CLI) használatával egy Azure PostgreSQL-kiszolgáló konfigurációs paramétereinek frissítése. Egy részhalmazát motor konfigurációk kiszolgálói szinten érhető el, és módosíthatók. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Azure Database for PostgreSQL-kiszolgáló- és adatbázis létrehozása a következő [hozzon létre egy Azure Database for postgresql-hez](quickstart-create-server-database-azure-cli.md)
- Telepítés [Azure CLI-vel](/cli/azure/install-azure-cli) a gépen, vagy használja a parancssori felület a [Azure Cloud Shell](../cloud-shell/overview.md) a böngésző használata az Azure Portalon.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Kiszolgáló konfigurációs paramétereinek listázása az Azure Database for PostgreSQL-kiszolgáló
A kiszolgáló és az értéküket az összes módosíthatóvá paraméter listázásához, futtassa a [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) parancsot.

A kiszolgáló a kiszolgáló konfigurációs paramétereinek listázhatja **mydemoserver.postgres.database.azure.com** erőforráscsoportba tartozó **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paraméter részletek megjelenítése
Egy adott konfigurációs paraméter a kiszolgáló részleteinek megjelenítéséhez, futtassa a [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) parancsot.

Ebben a példában a részleteket jeleníti meg a **log\_min\_üzenetek** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.postgres.database.azure.com** erőforráscsoport alatt **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Kiszolgáló konfigurációs paraméter értékének módosítása
Egy bizonyos kiszolgáló konfigurációs paraméter értékét, amely frissíti az alapul szolgáló konfigurációs értéket a PostgreSQL-kiszolgáló motor módosíthatja is. Frissítse a konfigurációt, használja a [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) parancsot. 

Frissítése az **log\_min\_üzenetek** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.postgres.database.azure.com** erőforráscsoportba tartozó  **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Ha szeretne egy konfigurációs paraméter értékének alaphelyzetbe állítása, egyszerűen választhatja hagyja ki a nem kötelező `--value` paraméterhez, és a szolgáltatás az alapértelmezett érték vonatkozik. A fenti példában, jelenne meg:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
A parancs alaphelyzetbe állítja a **log\_min\_üzenetek** konfigurációját, és az alapértelmezett érték **figyelmeztetés**. További információ a konfigurációs kiszolgáló és a megengedett értékek PostgreSQL a dokumentációjában talál [kiszolgálókonfiguráció](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>További lépések
- Kiszolgálói naplók elérése és konfigurálása: [, Azure Database for PostgreSQL Server-naplók](concepts-server-logs.md)
