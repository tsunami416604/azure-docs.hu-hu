---
title: Kiszolgáló paraméterek MySQL az Azure-adatbázis konfigurálása
description: Ez a cikk ismerteti a MySQL-kiszolgáló adatait az Azure-adatbázis konfigurálása MySQL az Azure portál használatával.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 32414cb034bf6ee95a284021111759959054c00e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267305"
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
|Alapszintű| Gen 4| 1| 1024| 50|
|Alapszintű| Gen 4| 2| 2560| 100|
|Alapszintű| Gen 5| 1| 1024| 50|
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
|Memóriára optimalizált| Gen 5| 2| 7168| 600|
|Memóriára optimalizált| Gen 5| 4| 15360| 1250|
|Memóriára optimalizált| Gen 5| 8| 30720| 2500|
|Memóriára optimalizált| Gen 5| 16| 62464| 5000|

A további kiszolgáló paraméterei nem konfigurálható a rendszerben:

|**A paraméter**|**Rögzített érték**|
| :------------------------ | :-------- |
|Alapszintű rétegben innodb_file_per_table|KI|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Egyéb kiszolgáló adatait, amely nem az itt felsorolt MySQL out-of-box alapértékekre verzióihoz értékre van beállítva [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) és [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>További lépések
- [MySQL az Azure-adatbázis adatkapcsolattárak](concepts-connection-libraries.md).
