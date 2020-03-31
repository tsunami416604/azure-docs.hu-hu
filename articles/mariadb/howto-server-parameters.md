---
title: Kiszolgálóparaméterek konfigurálása - Azure portal – Azure Database for MariaDB
description: Ez a cikk ismerteti, hogyan konfigurálhatja a MariaDB-kiszolgáló paramétereit az Azure Database for MariaDB-ben az Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 243530b4badb9b19d288a91f247eefbcf622fb87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536411"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Kiszolgálóparaméterek konfigurálása a MariaDB Azure Database szolgáltatásában az Azure Portal használatával

A MariaDB Azure Database támogatja egyes kiszolgálóparaméterek konfigurálását. Ez a cikk ismerteti, hogyan konfigurálhatja ezeket a paramétereket az Azure Portal használatával. Nem minden kiszolgálóparaméter állítható be.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigálás a kiszolgálóparamétereire az Azure Portalon

1. Jelentkezzen be az Azure Portalon, majd keresse meg az Azure Database for MariaDB-kiszolgálót.
2. A **BEÁLLÍTÁSOK csoportban** kattintson a **Kiszolgáló paraméterei** elemre az Azure Database for MariaDB kiszolgáló kiszolgálói paramétereinek megnyitásához.
![Az Azure Portal kiszolgáló paramétereinek lapja](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Keresse meg a módosítani kívánt beállításokat. Tekintse át a **Leírás** oszlopot a cél és az engedélyezett értékek megértéséhez.
![Legördülő menü számbavétele](./media/howto-server-parameters/3-toggle_parameter.png)
4. A módosítások mentéséhez kattintson a **Mentés** gombra.
![Módosítások mentése vagy elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha új értékeket mentett a paraméterekhez, akkor mindig visszaállíthatja az összeset az alapértelmezett értékekre, ha az **Összes visszaállítása lehetőséget választja az alapértelmezettre**lehetőségre.
![Az összes visszaállítása az alapértelmezettre](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Konfigurálható kiszolgálóparaméterek listája

A támogatott kiszolgálóparaméterek listája folyamatosan növekszik. Az Azure Portal kiszolgálóparaméterei lapon lejuthat a definícióra, és konfigurálhatja a kiszolgálóparamétereit az alkalmazáskövetelményei alapján.

## <a name="non-configurable-server-parameters"></a>Nem konfigurálható kiszolgálóparaméterek

Az InnoDB pufferkészlet és a maximális kapcsolatok nem konfigurálhatók, és a [tarifacsomaghoz](concepts-pricing-tiers.md)vannak kötve.

|**Árképzési szint**| **virtuális mag(k)**|**InnoDB pufferkészlet (MB)**|
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

Ezek a további kiszolgálóparaméterek nem konfigurálhatók a rendszerben:

|**Paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|innodb_file_per_table alapszinten|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Az itt nem felsorolt egyéb kiszolgálóparaméterek a [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/)beépített alapértelmezett értékeire vannak beállítva.

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

A globális szintű időzóna az Azure Portal **Kiszolgálóparaméterei** lapjáról állítható be. Az alábbi beállítás a globális időzónát az "US/Pacific" értékre állítja be.

![Időzóna-paraméter beállítása](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>A munkamenetszint időzónájának beállítása

A munkamenet-szint időzónája beállítható a `SET time_zone` parancs futtatásával egy eszközről, például a MySQL parancssorból vagy a MySQL Workbench-ből. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.

```sql
SET time_zone = 'US/Pacific';
```

A [Dátum- és időfüggvények](https://mariadb.com/kb/en/library/convert_tz/)című dokumentumban a MariaDB dokumentációjában olvashat.

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
