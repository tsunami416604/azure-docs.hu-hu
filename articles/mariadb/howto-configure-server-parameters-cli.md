---
title: Kiszolgálóparaméterek konfigurálása - Azure CLI - Azure Database for MariaDB
description: Ez a cikk ismerteti, hogyan konfigurálhatja a szolgáltatás paramétereit az Azure Database for MariaDB az Azure CLI parancssori segédprogram használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 3ba06ea592d51eedbe827e1ab6418f65722d579c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632299"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Kiszolgálókonfigurációs paraméterek testreszabása az Azure CLI használatával
Az Azure CLI, az Azure parancssori segédprogram használatával listázhatja, megjelenítheti és frissítheti a MariaDB-kiszolgálókazure Database konfigurációs paramétereit. A motorkonfigurációk egy részhalmaza a kiszolgáló szintjén érhető el, és módosítható.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- [Azure-adatbázis a MariaDB-kiszolgálóhoz](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure Cloud Shell a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>A MariaDB-kiszolgálóhoz való Azure Database kiszolgálókonfigurációs paramétereinek listázása
A kiszolgáló összes módosítható paraméterének és értékeinek listázásához futtassa az [az mariadb kiszolgáló konfigurációs lista](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) parancsát.

A **kiszolgáló** mydemoserver.mariadb.database.azure.com kiszolgálókonfigurációs paramétereit a **myresourcegroup**erőforráscsoport alatt sorolhatja fel.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Az egyes felsorolt paraméterek meghatározásáról a [Kiszolgálórendszerváltozók](https://mariadb.com/kb/en/library/server-system-variables/)MariaDB referenciaszakaszában lehet részt látni.

## <a name="show-server-configuration-parameter-details"></a>Kiszolgálókonfigurációs paraméter részleteinek megjelenítése
A kiszolgáló adott konfigurációs paraméterének részleteinek megjelenítéséhez futtassa az [az mariadb kiszolgáló konfigurációs show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) parancsát.

Ez a példa a kiszolgáló **mydemoserver.mariadb.database.azure.com** a **myresourcegroup** erőforráscsoport alatti **lassú\_\_lekérdezésnapló-kiszolgáló** konfigurációs paraméterének részleteit mutatja be.
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgálókonfigurációs paraméter értékének módosítása
Módosíthatja egy bizonyos kiszolgálókonfigurációs paraméter értékét is, amely frissíti a MariaDB kiszolgálómotor alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja az [az mariadb kiszolgáló konfigurációkészlet parancsát.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) 

A **kiszolgálói mydemoserver.mariadb.database.azure.com** **lassú\_\_lekérdezésnapló-kiszolgálókonfigurációs** paraméterének frissítése a **myresourcegroup** erőforráscsoport alatt.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Ha alaphelyzetbe szeretné állítani egy konfigurációs paraméter `--value` értékét, hagyja ki a választható paramétert, és a szolgáltatás az alapértelmezett értéket alkalmazza. A fenti példában így nézne ki:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Ez a kód visszaállítja a **lassú\_lekérdezésnapló\_** konfigurációját az alapértelmezett **OFF**értékre. 

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna paraméterrel való együttműködés

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblázatok feltöltése

A kiszolgálón lévő időzóna-táblák feltölthetők, ha a `mysql.az_load_timezone` tárolt eljárást egy eszközből, például a MariaDB parancssorból vagy a MariaDB Workbench-ből hívja meg.

> [!NOTE]
> Ha a parancsot a MariaDB Workbench-ből futtatja, előfordulhat, hogy először ki kell kapcsolnia a `mysql.az_load_timezone` csökkentett frissítési módot a használatával. `SET SQL_SAFE_UPDATES=0;`

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Az időzóna-táblák megfelelő feltöltéséhez indítsa újra a kiszolgálót. A kiszolgáló újraindításához használja az [Azure Portalt](howto-restart-server-portal.md) vagy a [CLI-t.](howto-restart-server-cli.md)

A rendelkezésre álló időzóna-értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzóna az [az mariadb kiszolgáló konfigurációkészlet](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) parancsával állítható be.

A következő parancs frissíti a kiszolgáló **mydemoserver.mariadb.database.azure.com** **idikmydemoserver.mariadb.database.azure.com\_** **a myresourcegroup** erőforráscsoport alatti időzóna-kiszolgáló konfigurációs paraméterét **az USA/csendes-óceáni szintre.**

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenetszint időzónájának beállítása

A munkamenet-szint időzónája úgy `SET time_zone` állítható be, hogy a parancsot egy olyan eszközről futtatja, mint a MariaDB parancssorvagy a MariaDB workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.  

```sql
SET time_zone = 'US/Pacific';
```

A [Dátum- és időfüggvények](https://mariadb.com/kb/en/library/date-time-functions/)című dokumentumban a MariaDB dokumentációjában olvashat.

## <a name="next-steps"></a>További lépések

- [Kiszolgálóparaméterek konfigurálása az Azure Portalon](howto-server-parameters.md)
