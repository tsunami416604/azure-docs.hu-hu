---
title: "Kiszolgáló paraméterek MySQL az Azure-adatbázis konfigurálása"
description: "Ez a cikk ismerteti a MySQL-kiszolgáló adatait az Azure-adatbázis konfigurálása MySQL az Azure portál használatával."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b3510c616d2a9ba66cb83cb998c42e03fdbb0f2b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Hogyan állítsa be a kiszolgáló adatait az Azure Database MySQL az Azure portál használatával

Azure MySQL-adatbázis támogatja az egyes kiszolgáló-paraméterek konfigurációja. Ez a cikk ismerteti, hogyan ezek a paraméterek konfigurálása az Azure-portál használatával. Nem minden kiszolgáló paraméterek módosítható. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Keresse meg a kiszolgáló paraméterek Azure-portál
1. Jelentkezzen be az Azure-portálon, majd keresse meg a MySQL-kiszolgálóhoz tartozó Azure-adatbázis.
2. Az a **beállítások** területén kattintson **Server paraméterek** Server paraméterek lapjának megnyitásához az Azure-adatbázis a MySQL.
![Az Azure portál kiszolgálójának Paraméterek lap](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Keresse meg a beállításokat, akkor módosítania kell. Tekintse át a **leírás** megtudhatja, hogy a cél és a megengedett értékek oszlopa. 
![Lefelé eldobási számbavétele](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kattintson a **mentése** menti a módosításokat.
![Mentés vagy a módosítások elvetése](./media/howto-server-parameters/4-save_parameters.png)
5. Ha mentette a paraméter új értékét, mindig visszaállíthatja az alapértelmezett értékekre mindent kiválasztásával **minden, az alapértelmezett visszaállítása**.
![Minden, az alapértelmezett visszaállítása](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>A kiszolgáló konfigurálható paraméterek listája

A támogatott kiszolgálói paraméterek listáját folyamatosan nő. Azure-portálon a kiszolgáló Paraméterek lap segítségével a definíciójának lekérdezése, és konfigurálja a kiszolgáló adatait az alkalmazás követelmények alapján. 

## <a name="nonconfigurable-server-parameters"></a>Nonconfigurable server paraméterek
InnoDB pufferkészlet és a kapcsolatok maximális számának elérésekor nincsenek konfigurálható és kötött a [tarifacsomag](concepts-service-tiers.md). 

|**Tarifacsomag**| **Számítási létrehozása**|**vCore(s)**|**InnoDB pufferkészlet (MB)**| **Kapcsolatok maximális száma**|
|---|---|---|---|--|
|Alapszintű| 4. generációból| 1| 1024| 50 |
|Alapszintű| 4. generációból| 2| 2560| 100 |
|Alapszintű| 5. generációból| 1| 1024| 50 |
|Alapszintű| 5. generációból| 2| 2560| 100 |
|Általános rendeltetés| 4. generációból| 2| 2560| 200|
|Általános rendeltetés| 4. generációból| 4| 5120| 400|
|Általános rendeltetés| 4. generációból| 8| 10240| 800|
|Általános rendeltetés| 4. generációból| 16| 20480| 1600|
|Általános rendeltetés| 4. generációból| 32| 40960| 3200|
|Általános rendeltetés| 5. generációból| 2| 2560| 200|
|Általános rendeltetés| 5. generációból| 4| 5120| 400|
|Általános rendeltetés| 5. generációból| 8| 10240| 800|
|Általános rendeltetés| 5. generációból| 16| 20480| 1600|
|Általános rendeltetés| 5. generációból| 32| 40960| 3200|
|Memóriára optimalizált| 5. generációból| 2| 7168| 600|
|Memóriára optimalizált| 5. generációból| 4| 15360| 1250|
|Memóriára optimalizált| 5. generációból| 8| 30720| 2500|
|Memóriára optimalizált| 5. generációból| 16| 62464| 5000|
|Memóriára optimalizált| 5. generációból| 32| 125952| 10000| 

A további kiszolgáló paraméterei nem konfigurálható a rendszerben:

|**Parameter**|**Rögzített érték**|
| :------------------------ | :-------- |
|Alapszintű rétegben innodb_file_per_table|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Egyéb kiszolgáló adatait, amely nem az itt felsorolt MySQL out-of-box alapértékekre verzióihoz értékre van beállítva [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) és [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>További lépések
- [MySQL az Azure-adatbázis adatkapcsolattárak](concepts-connection-libraries.md).
