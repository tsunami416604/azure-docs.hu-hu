---
title: Minimális állásidő-áttelepítés – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan hajtható végre a MySQL-adatbázisok minimális állásidőre való áttelepítése a Azure Database Migration Service használatával Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9f166323cc72cd22cc4dd28babdfd056100a32e0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774143"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimális – állásidő áttelepítése Azure Database for MySQLre
A MySQL áttelepítését minimális állásidővel Azure Database for MySQLhatja a [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) újonnan bevezetett **folyamatos szinkronizálási funkciójának** használatával. Ez a funkció korlátozza az alkalmazás által felmerült állásidő mennyiségét.

## <a name="overview"></a>Áttekintés
Az Azure DMS egy kezdeti terhelést hajt végre a helyszíni Azure Database for MySQLon, majd folyamatosan szinkronizál minden új tranzakciót az Azure-ba, amíg az alkalmazás fut. Miután az adatok bekerültek a cél Azure-ra, egy rövid pillanatra leállítja az alkalmazást (minimális állásidő), várja meg az utolsó adatköteget (az alkalmazás leállításának időpontjától egészen addig, amíg az alkalmazás nem érhető el az új forgalom elvégzéséhez). felveszi a célt, majd frissíti a kapcsolódási karakterláncot, hogy az Azure-ra mutasson. Ha elkészült, az alkalmazás az Azure-ban fog élni.

![Folyamatos szinkronizálás a Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Következő lépések
- A videó megtekintésével [egyszerűen áttelepítheti a MySQL/PostgreSQL-alkalmazásokat az Azure felügyelt szolgáltatásba](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), amely egy bemutatót tartalmaz, amely bemutatja, hogyan telepíthet át MySQL-alkalmazásokat a Azure Database for MySQL.
- Tekintse meg a következő oktatóanyagot: a [MySQL migrálása Azure Database for MySQL online-ba a DMS használatával](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
