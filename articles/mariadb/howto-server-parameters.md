---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal-Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a MariaDB Server-paramétereket a Azure Database for MariaDB a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3477820cb20d856c2e979cdfbe5528113bf4b562
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769404"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for MariaDBban a Azure Portal használatával

Azure Database for MariaDB támogatja egyes kiszolgálói paraméterek konfigurációját. Ez a cikk azt ismerteti, hogyan konfigurálhatja ezeket a paramétereket a Azure Portal használatával. Nem minden kiszolgáló paraméter módosítható.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigáljon a kiszolgálói paraméterek Azure Portal

1. Jelentkezzen be a Azure Portalba, majd keresse meg a Azure Database for MariaDB-kiszolgálót.
2. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MariaDB kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
![Azure Portal Server paraméterek lap](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Keresse meg a módosítani kívánt beállításokat. A **description (Leírás** ) oszlopban tekintse át a cél és az engedélyezett értékek ismeretét.
![enumerálás legördülő](./media/howto-server-parameters/3-toggle_parameter.png)
4. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra.
![a módosítások mentése vagy elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett**értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
![az összes visszaállítása az alapértelmezettre](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Konfigurálható kiszolgálói paraméterek listája

A támogatott kiszolgálói paraméterek listája folyamatosan bővül. A Azure Portal Server parameters (kiszolgálói paraméterek) lapján megtekintheti a definíciót és konfigurálhatja a kiszolgálói paramétereket az alkalmazás követelményei alapján.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálói paraméterek

A InnoDB-puffer és a maximális kapcsolatok nem konfigurálhatók, és az [árképzési szintjéhez](concepts-pricing-tiers.md)vannak kötve.

|**Tarifacsomag**| **Virtuális mag (ok)**|**InnoDB puffer készlete (MB)**| **Kapcsolatok maximális száma**|
|---|---|---|---|
|Basic| 1| 1024| 50|
|Basic| 2| 2560| 100|
|Általános rendeltetés| 2| 3584| 300|
|Általános rendeltetés| 4| 7680| 625|
|Általános rendeltetés| 8| 15360| 1250|
|Általános rendeltetés| 16| 31232| 2500|
|Általános rendeltetés| 32| 62976| 5000|
|Általános rendeltetés| 64| 125952| 10000|
|Memóriára optimalizált| 2| 7168| 600|
|Memóriára optimalizált| 4| 15360| 1250|
|Memóriára optimalizált| 8| 30720| 2500|
|Memóriára optimalizált| 16| 62464| 5000|
|Memóriára optimalizált| 32| 125952| 10000|

Ezek a további kiszolgálói paraméterek nem konfigurálhatók a rendszeren:

|**Paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|alapszintű innodb_file_per_table|KIKAPCSOLÁSA|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Az itt felsorolt egyéb kiszolgálói paraméterek a [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/)alapértelmezett értékeit adják meg a MariaDB.

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna-paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblák feltöltése

A kiszolgálón található időzóna-táblákat úgy töltheti fel, hogy meghívja a `az_load_timezone` tárolt eljárást egy olyan eszközről, mint a MySQL parancssori vagy a MySQL Workbench.

> [!NOTE]
> Ha a MySQL Workbench `az_load_timezone` parancsát futtatja, előfordulhat, hogy először a `SET SQL_SAFE_UPDATES=0;`használatával kell kikapcsolnia a biztonságos frissítési módot.

```sql
CALL mysql.az_load_timezone();
```

A rendelkezésre álló időzóna-értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzónát a Azure Portal **kiszolgáló paraméterek** lapján lehet beállítani. Az alábbi beállítás a globális időzónát az "USA/csendes-óceáni" értékre állítja be.

![Időzóna-paraméter beállítása](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>A munkamenet-szint időzónájának beállítása

A munkamenet-szint időzónája beállítható úgy, hogy a `SET time_zone` parancsot egy olyan eszközről futtatja, mint a MySQL parancssori vagy a MySQL Workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.

```sql
SET time_zone = 'US/Pacific';
```

A [dátum-és Időfüggvényekhez](https://mariadb.com/kb/en/library/convert_tz/)tekintse meg a MariaDB dokumentációját.

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
