---
title: A szolgáltatás paramétereinek konfigurálása az Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk ismerteti a szolgáltatás paramétereinek konfigurálása az Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure CLI-vel parancssor használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: f276247076438a03973148b5cf65ddbeb409b024
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274780"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Kiszolgáló konfigurációs paramétereinek testreszabása az Azure Database for postgresql-hez - Azure CLI használatával egyetlen kiszolgáló
Lista, megjelenítése és a parancssori felület (Azure CLI) használatával egy Azure PostgreSQL-kiszolgáló konfigurációs paramétereinek frissítése. Egy részhalmazát motor konfigurációk kiszolgálói szinten érhető el, és módosíthatók. 

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Azure Database for PostgreSQL-kiszolgáló- és adatbázis létrehozása a következő [hozzon létre egy Azure Database for postgresql-hez](quickstart-create-server-database-azure-cli.md)
- Telepítés [Azure CLI-vel](/cli/azure/install-azure-cli) a gépen, vagy használja a parancssori felület a [Azure Cloud Shell](../cloud-shell/overview.md) a böngésző használata az Azure Portalon.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Kiszolgáló konfigurációs paramétereinek listázása az Azure Database for PostgreSQL-kiszolgáló
A kiszolgáló és az értéküket az összes módosíthatóvá paraméter listázásához, futtassa a [az postgres server configuration list](/cli/azure/postgres/server/configuration) parancsot.

A kiszolgáló a kiszolgáló konfigurációs paramétereinek listázhatja **mydemoserver.postgres.database.azure.com** erőforráscsoportba tartozó **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paraméter részletek megjelenítése
Egy adott konfigurációs paraméter a kiszolgáló részleteinek megjelenítéséhez, futtassa a [az postgres server configuration show](/cli/azure/postgres/server/configuration) parancsot.

Ebben a példában a részleteket jeleníti meg a **log\_min\_üzenetek** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.postgres.database.azure.com** erőforráscsoport alatt **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Kiszolgáló konfigurációs paraméter értékének módosítása
Egy bizonyos kiszolgáló konfigurációs paraméter értékét, amely frissíti az alapul szolgáló konfigurációs értéket a PostgreSQL-kiszolgáló motor módosíthatja is. Frissítse a konfigurációt, használja a [az postgres server configuration set](/cli/azure/postgres/server/configuration) parancsot. 

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
- [Ismerje meg, hogyan indítsa újra a kiszolgálót](howto-restart-server-cli.md)
- Kiszolgálói naplók elérése és konfigurálása: [, Azure Database for PostgreSQL Server-naplók](concepts-server-logs.md)
