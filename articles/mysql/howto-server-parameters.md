---
title: Kiszolgáló paramétereinek konfigurálása az Azure Database MySQL-hez
description: Ez a cikk ismerteti a MySQL-kiszolgáló paramétereinek konfigurálása az Azure Database for MySQL-hez az Azure portal használatával.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 12/06/2018
ms.openlocfilehash: 451316b32bcda43d62bbabf0c4f73d239698c85b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078722"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Hogyan lehet a kiszolgáló paramétereinek konfigurálása az Azure Database for MySQL-hez az Azure portal használatával

Azure Database for MySQL támogatja az egyes kiszolgáló-paraméterek konfigurációja. Ez a cikk ismerteti, hogyan konfigurálhatja ezeket a paramétereket az Azure portal használatával. Nem minden kiszolgáló paramétereinek módosítható.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Az Azure Portalon keresse meg a kiszolgáló paramétereinek

1. Jelentkezzen be az Azure Portalon, majd keresse meg az Azure Database for MySQL-kiszolgálóhoz.
2. Alatt a **beállítások** területén kattintson **kiszolgálóparaméterekkel** nyissa meg a kiszolgáló paramétereket az Azure Database for MySQL-kiszolgáló számára.
![Az Azure portal kiszolgáló Paraméterek lap](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Keresse meg a beállításokat, hogy módosítani kell. Tekintse át a **leírás** megértéséhez célú és a megengedett értékek oszlopa.
![Lefelé eldobási számbavétele](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kattintson a **mentése** a módosítások mentéséhez.
![Mentés vagy a módosítások elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha mentette az új értékeket a paraméterekhez, bármikor visszatérhet az alapértelmezett értékekre mindent kiválasztásával **összes alaphelyzetbe állítása**.
![Összes alaphelyzetbe állítása](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>A kiszolgáló konfigurálható paraméterek listája

A támogatott kiszolgálói paraméterek listája folyamatosan nő. Kiszolgálói paraméterek lapon használja az Azure Portalon a definíció lekérése, és az alkalmazás igényei alapján a kiszolgáló paramétereinek konfigurálása.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálói paraméterek

InnoDB Pufferkészletben és kapcsolatok maximális száma nem konfigurálható és kötött a [tarifacsomag](concepts-service-tiers.md).

|**Tarifacsomag**| **Számítási generáció**|**vCore(s)**|**InnoDB Pufferkészletben (MB)**| **Kapcsolatok maximális száma**|
|---|---|---|---|--|
|Alapszintű| Gen 4| 1| 960| 50|
|Alapszintű| Gen 4| 2| 2560| 100|
|Alapszintű| Gen 5| 1| 960| 50|
|Alapszintű| Gen 5| 2| 2560| 100|
|Általános rendeltetés| Gen 4| 2| 3584| 300|
|Általános rendeltetés| Gen 4| 4| 7680| 625|
|Általános rendeltetés| Gen 4| 8| 15360| 1250|
|Általános rendeltetés| Gen 4| 16| 31232| 2500|
|Általános rendeltetés| Gen 4| 32| 62976| 5000|
|Általános rendeltetés| Gen 5| 2| 3584| 300|
|Általános rendeltetés| Gen 5| 4| 7680| 625|
|Általános rendeltetés| Gen 5| 8| 15360| 1250|
|Általános rendeltetés| Gen 5| 16| 31232| 2500|
|Általános rendeltetés| Gen 5| 32| 62976| 5000|
|Általános rendeltetés| Gen 5| 64| 125952| 10000|
|Memóriára optimalizált| Gen 5| 2| 7168| 600|
|Memóriára optimalizált| Gen 5| 4| 15360| 1250|
|Memóriára optimalizált| Gen 5| 8| 30720| 2500|
|Memóriára optimalizált| Gen 5| 16| 62464| 5000|
|Memóriára optimalizált| Gen 5| 32| 125952| 10000|

Ezeket a további kiszolgáló paramétereket, amelyek nem konfigurálhatók, a rendszer:

|**A paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|az alapszintű díjcsomagban innodb_file_per_table|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Egyéb kiszolgáló paraméterei nem az itt felsorolt verziók MySQL out-of-box alapértelmezett értékre vannak beállítva [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) és [5.6-os](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

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

A MySQL dokumentációjában [dátum és időpont függvényeinek](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>További lépések

- [Az Azure Database for MySQL adatkapcsolattárak](concepts-connection-libraries.md).
