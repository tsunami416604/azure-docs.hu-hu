---
title: A szolgáltatás paramétereinek konfigurálása – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a szolgáltatás paramétereit Azure Database for MySQL az Azure CLI parancssori segédprogram használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: a107c5130968ca960036d7e0f948cf6ea5d209a8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350330"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Kiszolgáló konfigurációs paramétereinek testreszabása az Azure CLI használatával
Az Azure CLI, az Azure parancssori segédprogram használatával listázhatja, megjelenítheti és frissítheti az Azure Database for MySQL-kiszolgáló konfigurációs paramétereit. A motor konfigurációjának egy részhalmaza a kiszolgáló szintjén érhető el, és módosítható. 

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Egy Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja a Azure Cloud shellt a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Azure Database for MySQL kiszolgáló kiszolgáló-konfigurációs paramétereinek listázása
A kiszolgálók és azok értékei összes módosítható paraméterének listázásához futtassa az az [MySQL Server Configuration List](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) parancsot.

A kiszolgálói **mydemoserver.mysql.database.Azure.com** tartozó kiszolgálói konfigurációs paramétereket a **myresourcegroup**erőforráscsoport alatt listázhatja.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Az egyes felsorolt paraméterek definícióját a MySQL-referenciával foglalkozó szakaszban találja a [kiszolgálói rendszerváltozók](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)című részben.

## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paramétereinek megjelenítése – részletek
A kiszolgálók egy adott konfigurációs paraméterének részleteinek megjelenítéséhez futtassa az az [MySQL Server Configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) parancsot.

Ez a példa a **lassú\_lekérdezés** részleteit mutatja be\_a log Server konfigurációs paraméterét a kiszolgáló **mydemoserver.mysql.database.Azure.com** az erőforráscsoport **myresourcegroup területen.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgáló-konfigurációs paraméter értékének módosítása
Egy bizonyos kiszolgáló-konfigurációs paraméter értékét is módosíthatja, amely frissíti a MySQL-kiszolgáló motorjának alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja az az [MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) parancsot. 

Ha frissíteni szeretné a **lassú\_lekérdezést\_a log** Server konfigurációs paraméterét a kiszolgáló **mydemoserver.mysql.database.Azure.com** az erőforráscsoport **myresourcegroup területen.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Ha alaphelyzetbe kívánja állítani egy konfigurációs paraméter értékét, hagyja ki a választható `--value` paramétert, és a szolgáltatás alkalmazza az alapértelmezett értéket. A fenti példában a következőképpen fog kinézni:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ez a kód visszaállítja a **lassú\_lekérdezési\_naplózási** konfigurációját az **alapértelmezett értékre**. 

## <a name="working-with-the-time-zone-parameter"></a>Időzóna-paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna táblák feltöltése

A kiszolgálón az időzóna táblázatok meghívásával lehet adatokkal feltölteni a `az_load_timezone` tárolt eljárás egy eszközt, például a MySQL parancssori vagy a MySQL Workbench segítségével.

> [!NOTE]
> Ha futtatja a `az_load_timezone` parancsot a MySQL Workbench, szükség lehet az első biztonságos frissítési mód kikapcsolása használatával `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Rendelkezésre álló időzóna értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzónát az az [MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) paranccsal lehet beállítani.

A következő parancs frissíti az **idő\_zóna** kiszolgálójának konfigurációs paraméterét a kiszolgáló **mydemoserver.mysql.database.Azure.com** az **USA/csendes-óceáni**csoport **myresourcegroup** .

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenet szintű időzóna beállítása

A munkamenet-szolgáltatói időzóna futtatásával állítható a `SET time_zone` egy eszköz, például a MySQL-parancssor vagy a MySQL Workbench parancsot. Az alábbi példa állítja be az időzónát a **USA / csendes-óceáni térség** időzóna.  

```sql
SET time_zone = 'US/Pacific';
```

A MySQL dokumentációjában [dátum és időpont függvényeinek](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Következő lépések

- [Kiszolgáló paramétereinek konfigurálása Azure Portal](howto-server-parameters.md)