---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal-Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan konfigurálhatja a MySQL-kiszolgáló paramétereit Azure Database for MySQL a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f803b7cccf3520c309e6b33d99b5565cfc4fdd01
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764916"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for MySQLban a Azure Portal használatával

Azure Database for MySQL támogatja egyes kiszolgálói paraméterek konfigurációját. Ez a cikk azt ismerteti, hogyan konfigurálhatja ezeket a paramétereket a Azure Portal használatával. Nem minden kiszolgáló paraméter módosítható.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigáljon a kiszolgálói paraméterek Azure Portal

1. Jelentkezzen be a Azure Portalba, majd keresse meg a Azure Database for MySQL-kiszolgálót.
2. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MySQL kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
![Azure Portal Server paraméterek lap](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Keresse meg a módosítani kívánt beállításokat. A **description (Leírás** ) oszlopban tekintse át a cél és az engedélyezett értékek ismeretét.
![enumerálás legördülő](./media/howto-server-parameters/3-toggle_parameter.png)
4. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra.
![a módosítások mentése vagy elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett**értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
![az összes visszaállítása az alapértelmezettre](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Konfigurálható kiszolgálói paraméterek listája

A támogatott kiszolgálói paraméterek listája folyamatosan bővül. A Azure Portal Server parameters (kiszolgálói paraméterek) lapján megtekintheti a definíciót és konfigurálhatja a kiszolgálói paramétereket az alkalmazás követelményei alapján.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálói paraméterek

A InnoDB-puffer mérete nem konfigurálható, és az [árképzési szintjéhez](concepts-service-tiers.md)van kötve.

|**Tarifacsomag**|**Virtuális mag (ok)**|**InnoDB-puffer mérete MEGABÁJTban <br>(legfeljebb 4 TB tárhelyet támogató kiszolgálók)**| **InnoDB-puffer mérete MEGABÁJTban <br>(legfeljebb 16 TB tárterületet támogató kiszolgálók)**|
|:---|---:|---:|---:|
|Basic| 1| 832| |
|Basic| 2| 2560| |
|Általános rendeltetés| 2| 3584| 7168|
|Általános rendeltetés| 4| 7680| 15360|
|Általános rendeltetés| 8| 15360| 30720|
|Általános rendeltetés| 16| 31232| 62464|
|Általános rendeltetés| 32| 62976| 125952|
|Általános rendeltetés| 64| 125952| 251904|
|Memóriára optimalizált| 2| 7168| 14336|
|Memóriára optimalizált| 4| 15360| 30720|
|Memóriára optimalizált| 8| 30720| 61440|
|Memóriára optimalizált| 16| 62464| 124928|
|Memóriára optimalizált| 32| 125952| 251904|

Ezek a további kiszolgálói paraméterek nem konfigurálhatók a rendszeren:

|**Paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|alapszintű innodb_file_per_table|KIKAPCSOLÁSA|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Az itt felsorolt egyéb kiszolgálói paraméterek az [5,7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) -es és a [5,6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html)-es verziókhoz tartozó MySQL beépített alapértelmezett értékeire vannak beállítva.

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

Tekintse meg a MySQL dokumentációját a [dátum-és Időfüggvényekhez](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Következő lépések

- [Azure Database for MySQLhoz tartozó kapcsolatok kódtárai](concepts-connection-libraries.md).
