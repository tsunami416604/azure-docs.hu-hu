---
title: Kiszolgáló paramétereinek konfigurálása – Azure Portal-Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan konfigurálhatja a MySQL-kiszolgáló paramétereit Azure Database for MySQL a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: bd0a867cce9b2a9ad793b491b9042034ef5810f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605157"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for MySQLban a Azure Portal használatával

Azure Database for MySQL támogatja egyes kiszolgálói paraméterek konfigurációját. Ez a cikk azt ismerteti, hogyan konfigurálhatja ezeket a paramétereket a Azure Portal használatával. Nem minden kiszolgáló paraméter módosítható.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigáljon a kiszolgálói paraméterek Azure Portal

1. Jelentkezzen be a Azure Portalba, majd keresse meg a Azure Database for MySQL-kiszolgálót.
2. A **Beállítások** szakaszban kattintson a **kiszolgálói paraméterek** elemre a Azure Database for MySQL kiszolgáló kiszolgálói paraméterek lapjának megnyitásához.
![Azure Portal kiszolgáló paramétereinek lapja](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Keresse meg a módosítani kívánt beállításokat. A **description (Leírás** ) oszlopban tekintse át a cél és az engedélyezett értékek ismeretét.
![Számbavétel legördülő lista](./media/howto-server-parameters/3-toggle_parameter.png)
4. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra.
![Módosítások mentése vagy elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha új értékeket mentett a paraméterek számára, az **összes visszaállítása az alapértelmezett**értékre lehetőség kiválasztásával bármikor visszaállíthatja az alapértelmezett értékeket.
![Az összes visszaállítása az alapértelmezett értékre](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Konfigurálható kiszolgálói paraméterek listája

A támogatott kiszolgálói paraméterek listája folyamatosan bővül. A Azure Portal Server parameters (kiszolgálói paraméterek) lapján megtekintheti a definíciót és konfigurálhatja a kiszolgálói paramétereket az alkalmazás követelményei alapján.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálói paraméterek

A InnoDB-puffer mérete nem konfigurálható, és az [árképzési szintjéhez](concepts-service-tiers.md)van kötve.

|**Díjszabási csomag**|**Virtuális mag (ok)**|**InnoDB-puffer mérete (MB <br>) (legfeljebb 4 TB tárhelyet támogató kiszolgálók)**| **InnoDB-puffer mérete (MB <br>) (legfeljebb 16 TB tárterületet támogató kiszolgálók)**|
|:---|---:|---:|---:|
|Basic| 1| 832| |
|Basic| 2| 2560| |
|Általános célú| 2| 3584| 7168|
|Általános célú| 4| 7680| 15360|
|Általános célú| 8| 15360| 30720|
|Általános célú| 16| 31232| 62464|
|Általános célú| 32| 62976| 125952|
|Általános célú| 64| 125952| 251904|
|Memóriaoptimalizált| 2| 7168| 14336|
|Memóriaoptimalizált| 4| 15360| 30720|
|Memóriaoptimalizált| 8| 30720| 61440|
|Memóriaoptimalizált| 16| 62464| 124928|
|Memóriaoptimalizált| 32| 125952| 251904|

Ezek a további kiszolgálói paraméterek nem konfigurálhatók a rendszeren:

|**Paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|alapszintű innodb_file_per_table|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

Az itt felsorolt egyéb kiszolgálói paraméterek az [5,7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) -es és a [5,6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html)-es verziókhoz tartozó MySQL beépített alapértelmezett értékeire vannak beállítva.

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

Tekintse meg a MySQL dokumentációját a [dátum-és Időfüggvényekhez](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>További lépések

- [Azure Database for MySQLhoz tartozó kapcsolatok kódtárai](concepts-connection-libraries.md).
