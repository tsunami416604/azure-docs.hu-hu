---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal-Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a MariaDB Server-paramétereket a Azure Database for MariaDB a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: ba091d05aa243fab08138c96827d2f657d9755de
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363551"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for MariaDBban a Azure Portal használatával

Azure Database for MariaDB támogatja egyes kiszolgálói paraméterek konfigurációját. Ez a cikk ismerteti, hogyan konfigurálhatja ezeket a paramétereket az Azure portal használatával. Nem minden kiszolgáló paramétereinek módosítható.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Az Azure Portalon keresse meg a kiszolgáló paramétereinek

1. Jelentkezzen be a Azure Portalba, majd keresse meg a Azure Database for MariaDB-kiszolgálót.
2. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MariaDB kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
![Azure Portal Server paraméterek lap](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Keresse meg a beállításokat, hogy módosítani kell. A **description (Leírás** ) oszlopban tekintse át a cél és az engedélyezett értékek ismeretét.
![enumerálás legördülő](./media/howto-server-parameters/3-toggle_parameter.png)
4. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra.
![a módosítások mentése vagy elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett**értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
![az összes visszaállítása az alapértelmezettre](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>A kiszolgáló konfigurálható paraméterek listája

A támogatott kiszolgálói paraméterek listája folyamatosan nő. Kiszolgálói paraméterek lapon használja az Azure Portalon a definíció lekérése, és az alkalmazás igényei alapján a kiszolgáló paramétereinek konfigurálása.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálói paraméterek

A InnoDB-puffer és a maximális kapcsolatok nem konfigurálhatók, és az [árképzési szintjéhez](concepts-pricing-tiers.md)vannak kötve.

|**Tarifacsomag**| **Virtuális mag (ok)**|**InnoDB puffer készlete (MB)**|
|---|---|---|
|Alapszintű| 1| 1024|
|Alapszintű| 2| 2560|
|Általános célú| 2| 3584|
|Általános célú| 4| 7680|
|Általános célú| 8| 15360|
|Általános célú| 16| 31232|
|Általános célú| 32| 62976|
|Általános célú| 64| 125952|
|Memóriára optimalizált| 2| 7168|
|Memóriára optimalizált| 4| 15360|
|Memóriára optimalizált| 8| 30720|
|Memóriára optimalizált| 16| 62464|
|Memóriára optimalizált| 32| 125952|

Ezeket a további kiszolgáló paramétereket, amelyek nem konfigurálhatók, a rendszer:

|**Paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|az alapszintű díjcsomagban innodb_file_per_table|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Az itt felsorolt egyéb kiszolgálói paraméterek a [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/)alapértelmezett értékeit adják meg a MariaDB.

## <a name="working-with-the-time-zone-parameter"></a>Időzóna-paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna táblák feltöltése

A kiszolgálón található időzóna-táblákat úgy töltheti fel, hogy meghívja a `az_load_timezone` tárolt eljárást egy olyan eszközről, mint a MySQL parancssori vagy a MySQL Workbench.

> [!NOTE]
> Ha a MySQL Workbench `az_load_timezone` parancsát futtatja, előfordulhat, hogy először a `SET SQL_SAFE_UPDATES=0;`használatával kell kikapcsolnia a biztonságos frissítési módot.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Indítsa újra a kiszolgálót az időzóna-táblák megfelelő kitöltésének biztosításához. A kiszolgáló újraindításához használja a [Azure Portal](howto-restart-server-portal.md) vagy a [parancssori](howto-restart-server-cli.md)felületet.
Rendelkezésre álló időzóna értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzónát a Azure Portal **kiszolgáló paraméterek** lapján lehet beállítani. Az alábbi állítja be a globális időzóna értéke "US / csendes-óceáni térség".

![Időzóna-paraméter beállítása](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>A munkamenet szintű időzóna beállítása

A munkamenet-szint időzónája beállítható úgy, hogy a `SET time_zone` parancsot egy olyan eszközről futtatja, mint a MySQL parancssori vagy a MySQL Workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.

```sql
SET time_zone = 'US/Pacific';
```

A [dátum-és Időfüggvényekhez](https://mariadb.com/kb/en/library/convert_tz/)tekintse meg a MariaDB dokumentációját.

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
