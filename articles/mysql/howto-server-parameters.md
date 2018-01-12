---
title: "Kiszolgáló adatait az Azure-adatbázis konfigurálása MySQL |} Microsoft Docs"
description: "Ez a cikk ismerteti a MySQL-kiszolgáló adatait az Azure-adatbázis konfigurálása MySQL az Azure portál használatával."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/10/2017
ms.openlocfilehash: f3b32c1f6b33bc60b50f1496414a300db468dc92
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Hogyan állítsa be a kiszolgáló adatait az Azure Database MySQL az Azure portál használatával

Azure MySQL-adatbázis támogatja az egyes kiszolgáló-paraméterek konfigurációja. Ez a témakör ismerteti, hogyan ezek a paraméterek konfigurálása az Azure-portál használatával. Nem minden kiszolgáló paraméterek módosítható. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Keresse meg a kiszolgáló paraméterek Azure-portál
1. Jelentkezzen be az Azure-portálon, majd keresse meg a MySQL-kiszolgálóhoz tartozó Azure-adatbázis.
2. Az a **beállítások** területén kattintson **Server paraméterek** Server paraméterek lapjának megnyitásához az Azure-adatbázis a MySQL.
3. Keresse meg a beállításokat, akkor módosítania kell. Tekintse át a **leírás** megtudhatja, hogy a cél és a megengedett értékek oszlopa. 
4. Kattintson a **mentése** menti a módosításokat.

![Az Azure portál kiszolgálójának (paraméterek) panelen](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>A kiszolgáló konfigurálható paraméterek listája

A támogatott kiszolgálói paraméterek listáját folyamatosan nő. Azure-portálon a kiszolgáló Paraméterek lap segítségével a definíciójának lekérdezése, és konfigurálja a kiszolgáló adatait az alkalmazás követelmények alapján. 

## <a name="nonconfigurable-server-parameters"></a>Nonconfigurable server paraméterek
InnoDB pufferkészlet és a kapcsolatok maximális számának elérésekor nincsenek konfigurálható és kötött a [tarifacsomag](concepts-service-tiers.md). 

| **Tarifacsomag** | **InnoDB pufferkészlet (MB)** | **Kapcsolatok maximális száma** |
| :------------------------ | :-------- | :----------- |
| Alapszintű 50 | 1024 | 50 | 
| Alapszintű 100  | 2560 | 100 | 
| Standard 100 | 2560 | 200 | 
| Standard 200 | 5120 | 400 | 
| Standard 400 | 10240 | 800 | 
| Standard 800 | 20480 | 1600 |

A további kiszolgáló paraméterei nonconfigurable a rendszerben <br>
 Alapszintű rétegben Innodb_file_per_table: kikapcsolása<br>
 innodb_flush_log_at_trx_commit = 1<br>
 sync_binlog = 1<br>
 innodb_log_file_size = 512MB<br>
 
Egyéb kiszolgáló adatait, amely nem az itt felsorolt MySQL out-of-box alapértékekre verzióihoz értékre van beállítva [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) és [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>További lépések
- [MySQL az Azure-adatbázis adatkapcsolattárak](concepts-connection-libraries.md).
