---
title: Minimális állásidő-áttelepítés – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan hajtható végre a MySQL-adatbázisok minimális állásidőre való áttelepítése a Azure Database Migration Service használatával Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80063347"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimális – állásidő áttelepítése Azure Database for MySQLre
A MySQL áttelepítését minimális állásidővel Azure Database for MySQLhatja a [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) újonnan bevezetett **folyamatos szinkronizálási funkciójának** használatával. Ez a funkció korlátozza az alkalmazás által felmerült állásidő mennyiségét.

## <a name="overview"></a>Áttekintés
Az Azure DMS egy kezdeti terhelést hajt végre a helyszíni Azure Database for MySQLon, majd folyamatosan szinkronizál minden új tranzakciót az Azure-ba, amíg az alkalmazás fut. Miután az adatok bekerültek a cél Azure-ra, egy rövid pillanatra (a minimális állásidőig) megvárhatják az utolsó adatköteget (az alkalmazás leállításának időpontjától egészen addig, amíg az alkalmazás nem áll készen az új forgalom elvégzésére), majd frissítenie kell a kapcsolódási karakterláncot, hogy az Azure-ra mutasson. Ha elkészült, az alkalmazás az Azure-ban fog élni.

![Folyamatos szinkronizálás a Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>További lépések
- A videó megtekintésével [egyszerűen áttelepítheti a MySQL/PostgreSQL-alkalmazásokat az Azure felügyelt szolgáltatásba](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), amely egy bemutatót tartalmaz, amely bemutatja, hogyan telepíthet át MySQL-alkalmazásokat a Azure Database for MySQL.
- Tekintse meg a következő oktatóanyagot: a [MySQL migrálása Azure Database for MySQL online-ba a DMS használatával](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
