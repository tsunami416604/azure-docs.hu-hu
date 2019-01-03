---
title: Migrálás minimális állásidővel az Azure Database for postgresql-hez
description: Ez a cikk bemutatja, hogyan áttelepítése egy minimális állásidővel PostgreSQL-adatbázis, Azure database for postgresql-hez az Azure Database Migration Service használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: ceb64781dc7e5243f785ad239c24e5f21b0481ce
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543648"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migrálás minimális állásidővel az Azure Database for postgresql-hez
Hajthat végre PostgreSQL migrálást az Azure Database for postgresql-hez minimális állásidővel segítségével az újonnan bevezetett **folyamatos szinkronizálás képesség** számára a [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Ez a funkció az alkalmazás által felmerülő állásidő mennyiségét korlátozza.

## <a name="overview"></a>Áttekintés
Az Azure DMS hajt végre egy kezdeti betöltése a helyszíni az Azure Database for postgresql-hez, és ezután közben az alkalmazás továbbra is futni fog folyamatosan szinkronizálja az Azure-ban új tranzakciók. Után a cél Azure oldaláról behozza az adatokat, akkor állítsa le az alkalmazást egy pillanatra (minimális üzemkimaradással) számára, várja meg a legutóbbi köteg adatok (az az idő, amíg az alkalmazás hatékonyan érhető el minden olyan új forgalom érvénybe állítsa le az alkalmazást) olvasásra beállítása a célkiszolgálón, és frissítse a kapcsolati karakterláncot, mutasson az Azure-bA. Ha elkészült, az alkalmazás lesz élő Azure-ban!

![Az Azure Database Migration Service folyamatos szinkronban](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>További lépések
- A videó megtekintésének [alkalmazások korszerűsítése, a Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), amely tartalmaz egy bemutatót: hogyan kell PostgreSQL alkalmazások migrálása az Azure Database for postgresql-hez.
- Lásd a következő oktatóanyagot [PostgreSQL Migrálása az Azure Database for postgresql-hez a DMS használatával online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
