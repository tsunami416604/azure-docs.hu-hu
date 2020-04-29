---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal-Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a MariaDB Server-paramétereket a Azure Database for MariaDB a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: f39e9450fb922e5b93d7b4b809df73cf5ab007c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81602405"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for MariaDBban a Azure Portal használatával

Azure Database for MariaDB támogatja egyes kiszolgálói paraméterek konfigurációját. Ez a cikk azt ismerteti, hogyan konfigurálhatja ezeket a paramétereket a Azure Portal használatával. Nem minden kiszolgáló paraméter módosítható.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigáljon a kiszolgálói paraméterek Azure Portal

1. Jelentkezzen be a Azure Portalba, majd keresse meg a Azure Database for MariaDB-kiszolgálót.
2. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MariaDB kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
![Azure Portal kiszolgáló paramétereinek lapja](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Keresse meg a módosítani kívánt beállításokat. A **description (Leírás** ) oszlopban tekintse át a cél és az engedélyezett értékek ismeretét.
![Számbavétel legördülő lista](./media/howto-server-parameters/3-toggle_parameter.png)
4. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra.
![Módosítások mentése vagy elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett**értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
![Az összes visszaállítása az alapértelmezett értékre](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Konfigurálható kiszolgálói paraméterek listája

A támogatott kiszolgálói paraméterek listája folyamatosan bővül. A Azure Portal Server parameters (kiszolgálói paraméterek) lapján megtekintheti a definíciót és konfigurálhatja a kiszolgálói paramétereket az alkalmazás követelményei alapján.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálói paraméterek

A InnoDB-puffer és a maximális kapcsolatok nem konfigurálhatók, és az [árképzési szintjéhez](concepts-pricing-tiers.md)vannak kötve.

|**Díjszabási csomag**| **Virtuális mag (ok)**|**InnoDB puffer készlete (MB)**|
|---|---|---|
|Basic| 1| 1024|
|Basic| 2| 2560|
|Általános célú| 2| 3584|
|Általános célú| 4| 7680|
|Általános célú| 8| 15360|
|Általános célú| 16| 31232|
|Általános célú| 32| 62976|
|Általános célú| 64| 125952|
|Memóriaoptimalizált| 2| 7168|
|Memóriaoptimalizált| 4| 15360|
|Memóriaoptimalizált| 8| 30720|
|Memóriaoptimalizált| 16| 62464|
|Memóriaoptimalizált| 32| 125952|

Ezek a további kiszolgálói paraméterek nem konfigurálhatók a rendszeren:

|**Paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|alapszintű innodb_file_per_table|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

Az itt felsorolt egyéb kiszolgálói paraméterek a [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/)alapértelmezett értékeit adják meg a MariaDB.

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna-paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblák feltöltése

A kiszolgálón található időzóna-táblákat úgy töltheti fel, hogy meghívja a `mysql.az_load_timezone` tárolt eljárást egy olyan eszközről, mint a MySQL parancssor vagy a MySQL Workbench.

> [!NOTE]
> Ha a MySQL Workbenchből `mysql.az_load_timezone` futtatja a parancsot, előfordulhat, hogy először ki kell kapcsolnia a biztonságos frissítési `SET SQL_SAFE_UPDATES=0;`módot a használatával.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Indítsa újra a kiszolgálót az időzóna-táblák megfelelő kitöltésének biztosításához. A kiszolgáló újraindításához használja a [Azure Portal](howto-restart-server-portal.md) vagy a [parancssori](howto-restart-server-cli.md)felületet.
A rendelkezésre álló időzóna-értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzónát a Azure Portal **kiszolgáló paraméterek** lapján lehet beállítani. Az alábbi beállítás a globális időzónát az "USA/csendes-óceáni" értékre állítja be.

![Időzóna-paraméter beállítása](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>A munkamenet-szint időzónájának beállítása

A munkamenet-szint időzónája beállítható úgy, `SET time_zone` hogy a parancsot egy olyan eszközről futtatja, mint a MySQL parancssor vagy a MySQL Workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.

```sql
SET time_zone = 'US/Pacific';
```

A [dátum-és Időfüggvényekhez](https://mariadb.com/kb/en/library/convert_tz/)tekintse meg a MariaDB dokumentációját.

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
