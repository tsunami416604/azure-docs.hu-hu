---
title: Kiszolgálóparaméterek konfigurálása - Azure portal – Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan konfigurálhatja a MySQL-kiszolgáló paramétereit az Azure Database for MySQL-ben az Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: bd0a867cce9b2a9ad793b491b9042034ef5810f5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605157"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Kiszolgálóparaméterek konfigurálása az Azure Database for MySQL szolgáltatásban az Azure Portal használatával

Az Azure Database for MySQL támogatja bizonyos kiszolgálóparaméterek konfigurálását. Ez a cikk ismerteti, hogyan konfigurálhatja ezeket a paramétereket az Azure Portal használatával. Nem minden kiszolgálóparaméter állítható be.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigálás a kiszolgálóparamétereire az Azure Portalon

1. Jelentkezzen be az Azure Portalon, majd keresse meg az Azure-adatbázis ta-kiszolgáló.
2. A **BEÁLLÍTÁSOK** szakaszban kattintson a **Kiszolgáló paramétereire** az Azure Database for MySQL-kiszolgáló kiszolgálóparamétereinek megnyitásához.
![Az Azure Portal kiszolgáló paramétereinek lapja](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Keresse meg a módosítani kívánt beállításokat. Tekintse át a **Leírás** oszlopot a cél és az engedélyezett értékek megértéséhez.
![Legördülő menü számbavétele](./media/howto-server-parameters/3-toggle_parameter.png)
4. A módosítások mentéséhez kattintson a **Mentés** gombra.
![Módosítások mentése vagy elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha új értékeket mentett a paraméterekhez, akkor mindig visszaállíthatja az összeset az alapértelmezett értékekre, ha az **Összes visszaállítása lehetőséget választja az alapértelmezettre**lehetőségre.
![Az összes visszaállítása az alapértelmezettre](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Konfigurálható kiszolgálóparaméterek listája

A támogatott kiszolgálóparaméterek listája folyamatosan növekszik. Az Azure Portal kiszolgálóparaméterei lapon lejuthat a definícióra, és konfigurálhatja a kiszolgálóparamétereit az alkalmazáskövetelményei alapján.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálóparaméterek

Az InnoDB pufferkészlet mérete nem konfigurálható, és a [tarifacsomaghoz](concepts-service-tiers.md)van kötve.

|**Árképzési szint**|**virtuális mag(k)**|**InnoDB pufferkészlet mérete <br>MB-ban (legfeljebb 4 TB-os tárolót támogató kiszolgálók)**| **InnoDB pufferkészlet mérete <br>MB-ban (akár 16 TB-os tárolót támogató kiszolgálók)**|
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

Ezek a további kiszolgálóparaméterek nem konfigurálhatók a rendszerben:

|**Paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|innodb_file_per_table alapszinten|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Az itt fel nem sorolt egyéb kiszolgálóparaméterek a MySQL beépített alapértelmezett értékeiaz [5.7-es](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) és [az 5.6-os](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html)verziókban.

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna paraméterrel való együttműködés

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblázatok feltöltése

A kiszolgálón lévő időzóna-táblák feltölthetők a `mysql.az_load_timezone` tárolt eljárás hívásával egy eszközről, például a MySQL parancssorból vagy a MySQL Workbench-ből.

> [!NOTE]
> Ha a parancsot a MySQL Workbench-ből futtatja, előfordulhat, hogy először ki kell kapcsolnia a `mysql.az_load_timezone` csökkentett frissítési módot a használatával. `SET SQL_SAFE_UPDATES=0;`

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

A globális szintű időzóna az Azure Portal **Kiszolgálóparaméterei** lapjáról állítható be. Az alábbi beállítás a globális időzónát az "US/Pacific" értékre állítja be.

![Időzóna-paraméter beállítása](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>A munkamenetszint időzónájának beállítása

A munkamenet-szint időzónája beállítható a `SET time_zone` parancs futtatásával egy eszközről, például a MySQL parancssorból vagy a MySQL Workbench-ből. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.

```sql
SET time_zone = 'US/Pacific';
```

Olvassa el a MySQL dokumentációját a [Dátum és idő függvények .](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)

## <a name="next-steps"></a>További lépések

- [A MySQL Azure Database kapcsolattárak.](concepts-connection-libraries.md)
