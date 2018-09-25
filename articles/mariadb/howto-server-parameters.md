---
title: Kiszolgáló paramétereinek konfigurálása az Azure Database for MariaDB
description: Ez a cikk ismerteti a MariaDB-kiszolgáló paramétereinek konfigurálása az Azure Database for MariaDB az Azure portal használatával.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c992783db3b63c73feb18c8c493ca1b43f9ea370
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975207"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Hogyan lehet a kiszolgáló paramétereinek konfigurálása az Azure Database for MariaDB az Azure portal használatával

Azure Database for MariaDB egyes kiszolgáló-paraméterek konfigurációja támogatja. Ez a cikk ismerteti, hogyan konfigurálhatja ezeket a paramétereket az Azure portal használatával. Nem minden kiszolgáló paramétereinek módosítható.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Az Azure Portalon keresse meg a kiszolgáló paramétereinek

1. Jelentkezzen be az Azure Portalon, majd keresse meg az Azure Database for MariaDB-kiszolgáló.
2. Alatt a **beállítások** területén kattintson **kiszolgálóparaméterekkel** nyissa meg a kiszolgáló paramétereket az Azure Database for MariaDB-kiszolgáló számára.
![Az Azure portal kiszolgáló Paraméterek lap](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Keresse meg a beállításokat, hogy módosítani kell. Tekintse át a **leírás** megértéséhez célú és a megengedett értékek oszlopa.
![Lefelé eldobási számbavétele](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kattintson a **mentése** a módosítások mentéséhez.
![Mentés vagy a módosítások elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha mentette az új értékeket a paraméterekhez, bármikor visszatérhet az alapértelmezett értékekre mindent kiválasztásával **összes alaphelyzetbe állítása**.
![Összes alaphelyzetbe állítása](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>A kiszolgáló konfigurálható paraméterek listája

A támogatott kiszolgálói paraméterek listája folyamatosan nő. Kiszolgálói paraméterek lapon használja az Azure Portalon a definíció lekérése, és az alkalmazás igényei alapján a kiszolgáló paramétereinek konfigurálása.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálói paraméterek

InnoDB Pufferkészletben és kapcsolatok maximális száma nem konfigurálható és kötött a [tarifacsomag](concepts-pricing-tiers.md).

|**Tarifacsomag**| **vCore(s)**|**InnoDB Pufferkészletben (MB)**| **Kapcsolatok maximális száma**|
|---|---|---|---|--|
|Alapszintű| 1| 1024| 50|
|Alapszintű| 2| 2560| 100|
|Általános rendeltetés| 2| 3584| 300|
|Általános rendeltetés| 4| 7680| 625|
|Általános rendeltetés| 8| 15360| 1250|
|Általános rendeltetés| 16| 31232| 2500|
|Általános rendeltetés| 32| 62976| 5000|
|Memóriára optimalizált| 2| 7168| 600|
|Memóriára optimalizált| 4| 15360| 1250|
|Memóriára optimalizált| 8| 30720| 2500|
|Memóriára optimalizált| 16| 62464| 5000|

<!-- 
|**Pricing Tier**| **Compute Generation**|**vCore(s)**|**InnoDB Buffer Pool (MB)**| **Max Connections**|
|---|---|---|---|--|
|Basic| Gen 4| 1| 1024| 50|
|Basic| Gen 4| 2| 2560| 100|
|Basic| Gen 5| 1| 1024| 50|
|Basic| Gen 5| 2| 2560| 100|
|General Purpose| Gen 4| 2| 3584| 300|
|General Purpose| Gen 4| 4| 7680| 625|
|General Purpose| Gen 4| 8| 15360| 1250|
|General Purpose| Gen 4| 16| 31232| 2500|
|General Purpose| Gen 4| 32| 62976| 5000|
|General Purpose| Gen 5| 2| 3584| 300|
|General Purpose| Gen 5| 4| 7680| 625|
|General Purpose| Gen 5| 8| 15360| 1250|
|General Purpose| Gen 5| 16| 31232| 2500|
|General Purpose| Gen 5| 32| 62976| 5000|
|Memory Optimized| Gen 5| 2| 7168| 600|
|Memory Optimized| Gen 5| 4| 15360| 1250|
|Memory Optimized| Gen 5| 8| 30720| 2500|
|Memory Optimized| Gen 5| 16| 62464| 5000|
-->

Ezeket a további kiszolgáló paramétereket, amelyek nem konfigurálhatók, a rendszer:

|**A paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|az alapszintű díjcsomagban innodb_file_per_table|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Egyéb kiszolgáló paraméterei, amely nem szerepel itt az a MariaDB out-of-box alapértelmezett értékekre vannak beállítva [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

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

A globális szintű időzóna állítható be a **kiszolgálóparaméterekkel** oldal az Azure Portalon. Az alábbi állítja be a globális időzóna értéke "US / csendes-óceáni térség".

![Időzóna-paraméter beállítása](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>A munkamenet szintű időzóna beállítása

A munkamenet-szolgáltatói időzóna futtatásával állítható a `SET time_zone` egy eszköz, például a MySQL-parancssor vagy a MySQL Workbench parancsot. Az alábbi példa állítja be az időzónát a **USA / csendes-óceáni térség** időzóna.

```sql
SET time_zone = 'US/Pacific';
```

A MariaDB dokumentációjában [dátum és időpont függvényeinek](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->