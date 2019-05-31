---
title: Migrálás minimális állásidővel az Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk bemutatja, hogyan minimális állásidővel áttelepítése egy PostgreSQL-adatbázis, Azure database for PostgreSQL – egyetlen kiszolgáló az Azure Database Migration Service használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067511"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migrálás minimális állásidővel az Azure Database for PostgreSQL – egyetlen kiszolgáló
Hajthat végre PostgreSQL migrálást az Azure Database for postgresql-hez minimális állásidővel segítségével az újonnan bevezetett **folyamatos szinkronizálás képesség** számára a [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Ez a funkció az alkalmazás által felmerülő állásidő mennyiségét korlátozza.

## <a name="overview"></a>Áttekintés
Az Azure DMS hajt végre egy kezdeti betöltése a helyszíni az Azure Database for postgresql-hez, és ezután közben az alkalmazás továbbra is futni fog folyamatosan szinkronizálja az Azure-ban új tranzakciók. Után a cél Azure oldaláról behozza az adatokat, akkor állítsa le az alkalmazást egy pillanatra (minimális üzemkimaradással) számára, várja meg a legutóbbi köteg adatok (az az idő, amíg az alkalmazás hatékonyan érhető el minden olyan új forgalom érvénybe állítsa le az alkalmazást) olvasásra beállítása a célkiszolgálón, és frissítse a kapcsolati karakterláncot, mutasson az Azure-bA. Ha elkészült, az alkalmazás lesz élő Azure-ban!

![Az Azure Database Migration Service folyamatos szinkronban](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>További lépések
- A videó megtekintésének [alkalmazások korszerűsítése, a Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), amely tartalmaz egy bemutatót: hogyan kell PostgreSQL alkalmazások migrálása az Azure Database for postgresql-hez.
- Lásd a következő oktatóanyagot [PostgreSQL Migrálása az Azure Database for postgresql-hez a DMS használatával online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
