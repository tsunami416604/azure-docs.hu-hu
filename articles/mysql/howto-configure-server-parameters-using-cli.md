---
title: Kiszolgálóparaméterek konfigurálása - Azure CLI - Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan konfigurálhatja a szolgáltatás paramétereit az Azure Database for MySQL az Azure CLI parancssori segédprogram használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f3027909d1c4684e2ef5d1b6e967cb11f570fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062433"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Kiszolgálói paraméterek testreszabása az Azure CLI használatával
Az Azure CLI, az Azure parancssori segédprogram használatával listázhatja, megjelenítheti és frissítheti a MySQL-kiszolgálók azure database konfigurációs paramétereit. A motorkonfigurációk egy részhalmaza a kiszolgáló szintjén érhető el, és módosítható. 

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- [Azure-adatbázis a MySQL-kiszolgálóhoz](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure Cloud Shell a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Az Azure Database for MySQL-kiszolgáló kiszolgálókonfigurációs paramétereinek listázása
Ha egy kiszolgáló összes módosítható paraméterét és azok értékeit szeretné felsorolni, futtassa az [az mysql kiszolgáló konfigurációs listájának](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) parancsát.

A **kiszolgáló** mydemoserver.mysql.database.azure.com kiszolgáló konfigurációs paramétereit a **myresourcegroup**erőforráscsoport alatt sorolhatja fel.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Az egyes felsorolt paraméterek meghatározásáról a [Kiszolgálórendszerváltozók](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)MySQL referenciaszakaszában lehet részt látni.

## <a name="show-server-configuration-parameter-details"></a>Kiszolgálókonfigurációs paraméter részleteinek megjelenítése
A kiszolgáló adott konfigurációs paraméterének részleteinek megjelenítéséhez futtassa az [az mysql kiszolgáló konfigurációs show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) parancsát.

Ez a példa a kiszolgáló **mydemoserver.mysql.database.azure.com** a **myresourcegroup** erőforráscsoport alatti **lassú\_\_lekérdezésnapló-kiszolgáló** konfigurációs paraméterének részleteit mutatja be.
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgálókonfigurációs paraméter értékének módosítása
Módosíthatja egy bizonyos kiszolgálókonfigurációs paraméter értékét is, amely frissíti a MySQL kiszolgálómotor alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja az [az mysql kiszolgáló konfigurációkészlet parancsát.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) 

A **kiszolgáló** ima-csoport mydemoserver.mysql.database.azure.com **\_lassú lekérdezésnapló-kiszolgálókonfigurációs\_** paraméterének frissítése a **myresourcegroup** erőforráscsoport alatt.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Ha alaphelyzetbe szeretné állítani egy konfigurációs paraméter `--value` értékét, hagyja ki a választható paramétert, és a szolgáltatás az alapértelmezett értéket alkalmazza. A fenti példában így nézne ki:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ez a kód visszaállítja a **lassú\_lekérdezésnapló\_** konfigurációját az alapértelmezett **OFF**értékre. 

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna paraméterrel való együttműködés

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblázatok feltöltése

A kiszolgálón lévő időzóna-táblák feltölthetők a `az_load_timezone` tárolt eljárás hívásával egy eszközről, például a MySQL parancssorból vagy a MySQL Workbench-ből.

> [!NOTE]
> Ha a parancsot a MySQL Workbench-ből futtatja, előfordulhat, hogy először ki kell kapcsolnia a `az_load_timezone` csökkentett frissítési módot a használatával. `SET SQL_SAFE_UPDATES=0;`

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

A globális szintű időzóna az [az mysql szerver konfigurációs készlet](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) parancsával állítható be.

A következő parancs frissíti a kiszolgáló **mydemoserver.mysql.database.azure.com** a **myresourcegroup** erőforráscsoport alatti **\_időzóna-kiszolgáló** konfigurációs paraméterét **az USA/csendes-óceáni szintre.**

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenetszint időzónájának beállítása

A munkamenet-szint időzónája beállítható a `SET time_zone` parancs futtatásával egy eszközről, például a MySQL parancssorból vagy a MySQL Workbench-ből. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.  

```sql
SET time_zone = 'US/Pacific';
```

Olvassa el a MySQL dokumentációját a [Dátum és idő függvények .](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)


## <a name="next-steps"></a>További lépések

- [Kiszolgálóparaméterek konfigurálása az Azure Portalon](howto-server-parameters.md)