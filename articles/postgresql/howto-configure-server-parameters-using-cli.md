---
title: Paraméterek konfigurálása - Azure Database for PostgreSQL - Single Server
description: Ez a cikk ismerteti, hogyan konfigurálhatja a Postgres paramétereit az Azure Database for PostgreSQL – Single Server az Azure CLI használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 4e029428a3709bacdbcd50a6ac3714e730377242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763623"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Az Azure Database for PostgreSQL kiszolgálókonfigurációs paramétereinek testreszabása – Egyetlen kiszolgáló az Azure CLI használatával
Az Azure PostgreSQL-kiszolgáló konfigurációs paramétereit a parancssori felület (Azure CLI) segítségével listázhatja, jelenítheti meg és frissítheti. A motorkonfigurációk egy részhalmaza kiszolgálószinten érhető el, és módosítható. 

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- Azure-adatbázis létrehozása postgreSQL-kiszolgálóhoz és -adatbázishoz az [Azure-adatbázis létrehozása a PostgreSQL számára](quickstart-create-server-database-azure-cli.md) című szöveget követve
- Telepítse [az Azure CLI](/cli/azure/install-azure-cli) parancssori felületét a gépére, vagy használja az [Azure Cloud Shellt](../cloud-shell/overview.md) az Azure Portalon a böngésző használatával.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Az Azure Database for PostgreSQL kiszolgáló kiszolgálókonfigurációs paramétereinek listázása
Ha egy kiszolgáló összes módosítható paraméterét és azok értékeit szeretné felsorolni, futtassa az [az postgres kiszolgálókonfigurációs lista](/cli/azure/postgres/server/configuration) parancsot.

A **kiszolgáló** mydemoserver.postgres.database.azure.com kiszolgáló konfigurációs paramétereit a **myresourcegroup**erőforráscsoport alatt sorolhatja fel.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Kiszolgálókonfigurációs paraméter részleteinek megjelenítése
A kiszolgáló adott konfigurációs paraméterének részleteinek megjelenítéséhez futtassa az [az postgres kiszolgálókonfigurációs show](/cli/azure/postgres/server/configuration) parancsot.

Ez a példa a kiszolgáló ima-csoport **on-mydemoserver.postgres.database.azure.com** **naplómin-üzenetek\_\_** kiszolgálókonfigurációs paraméterének részleteit mutatja **be.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Kiszolgáló konfigurációs paraméterértékének módosítása
Módosíthatja egy bizonyos kiszolgálókonfigurációs paraméter értékét is, amely frissíti a PostgreSQL kiszolgálómotor alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja az [az postgres kiszolgálókonfigurációkészlet parancsát.](/cli/azure/postgres/server/configuration) 

A **kiszolgáló\_ima-csoport mydemoserver.postgres.database.azure.com naplómin-üzenetek\_** kiszolgálójának konfigurációs paraméterének frissítése a **myresourcegroup** erőforráscsoport alatt. **mydemoserver.postgres.database.azure.com**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Ha vissza szeretné állítani egy konfigurációs paraméter értékét, egyszerűen `--value` hagyja ki a választható paramétert, és a szolgáltatás az alapértelmezett értéket alkalmazza. A fenti példában így nézne ki:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Ez a parancs visszaállítja a **naplómin\_üzenetek\_** konfigurációját a **FIGYELMEZTETÉS**alapértelmezett értékre. A kiszolgáló konfigurációjáról és a megengedett értékekről a PostgreSQL dokumentációja a [kiszolgálókonfigurációról](https://www.postgresql.org/docs/9.6/static/runtime-config.html)című témakörben olvashat bővebben.

## <a name="next-steps"></a>További lépések
- [A kiszolgáló újraindításának elsajátítása](howto-restart-server-cli.md)
- A kiszolgálónaplók konfigurálásához és eléréséhez olvassa el [a Kiszolgálónaplók az Azure Database for PostgreSQL szolgáltatásban című témakört.](concepts-server-logs.md)
