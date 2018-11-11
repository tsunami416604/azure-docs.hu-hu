---
title: A szolgáltatás paramétereinek konfigurálása az Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogy a paraméterek konfigurálása az Azure Database for MariaDB az Azure CLI parancssori segédprogram használatával.
services: mariadb
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/09/2018
ms.openlocfilehash: 4e391ce56b31f35da67b3d975bbc1290c17cb139
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516227"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Kiszolgáló konfigurációs paramétereinek testreszabása az Azure CLI-vel
Lista, megjelenítése és konfigurációs paramétereket egy Azure Database for MariaDB-kiszolgáló frissítése az Azure CLI, az Azure parancssori segédprogram használatával. Motor konfigurációk egy részét a kiszolgálói szinten érhető el, és módosíthatók.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Egy Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Az Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure Cloud Shellt a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Kiszolgáló konfigurációs paramétereinek listázása az Azure Database for MariaDB-kiszolgáló
A kiszolgáló és az értéküket az összes módosíthatóvá paraméter listázásához, futtassa a [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) parancsot.

A kiszolgáló a kiszolgáló konfigurációs paramétereinek listázhatja **mydemoserver.mariadb.database.azure.com** erőforráscsoportba tartozó **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Definíciója a listán szereplő paraméterek, tekintse meg a MariaDB referenciaszakasz [kiszolgáló rendszerváltozók](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paraméter részletek megjelenítése
Egy adott konfigurációs paraméter a kiszolgáló részleteinek megjelenítéséhez, futtassa a [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) parancsot.

Ebben a példában a részleteket jeleníti meg a **lassú\_lekérdezés\_log** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.mariadb.database.azure.com** erőforráscsoport alatt **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Módosítsa a kiszolgáló konfigurációs paraméter értéke
Módosíthatja egy bizonyos kiszolgáló konfigurációs paramétert, amely frissíti az alapul szolgáló konfigurációs értéket a MariaDB server motor értékét is. Frissítse a konfigurációt, használja a [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) parancsot. 

Frissítése az **lassú\_lekérdezés\_log** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.mariadb.database.azure.com** erőforráscsoportba tartozó  **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Ha szeretne egy konfigurációs paraméter értékének alaphelyzetbe állítása, hagyja el a választható `--value` paraméterhez, és a szolgáltatás az alapértelmezett érték vonatkozik. A fenti példában akkor jelenne meg:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Ez a kód alaphelyzetbe állítása a **lassú\_lekérdezés\_log** konfigurációját, és az alapértelmezett érték **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Időzóna-paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna táblák feltöltése

A kiszolgálón az időzóna táblázatok meghívásával lehet adatokkal feltölteni a `az_load_timezone` tárolt eljárás egy eszközt, például a MariaDB-parancssor vagy a MariaDB Workbench.

> [!NOTE]
> Ha futtatja a `az_load_timezone` parancsot a MariaDB Workbench, szükség lehet az első biztonságos frissítési mód kikapcsolása használatával `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Rendelkezésre álló időzóna értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzóna lze nastavit pomocí vlastností a [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) parancsot.

A következő parancsot a frissítések a **idő\_zóna** kiszolgáló konfigurációs paraméter kiszolgáló **mydemoserver.mariadb.database.azure.com** erőforráscsoportba tartozó  **myresourcegroup** való **USA / csendes-óceáni térség**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenet szintű időzóna beállítása

A munkamenet-szolgáltatói időzóna futtatásával állítható a `SET time_zone` egy eszköz, például a MariaDB-parancssorból vagy a MariaDB Workbench parancsot. Az alábbi példa állítja be az időzónát a **USA / csendes-óceáni térség** időzóna.  

```sql
SET time_zone = 'US/Pacific';
```

A MariaDB dokumentációjában [dátum és időpont függvényeinek](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>További lépések

- Konfigurálása [kiszolgálóparaméterek az Azure Portalon](howto-server-parameters.md)