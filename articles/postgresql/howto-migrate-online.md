---
title: Az Azure Database-áttelepítés minimális állásidővel PostgreSQL
description: A cikkből megtudhatja, hogyan hajthat végre egy PostgreSQL-adatbázisban az Azure Database minimális állásidővel áttelepítését a PostgreSQL az Azure-adatbázis áttelepítése szolgáltatás használatával.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 9ab5d4615a8baf763d0b7ee47bf0890124f8665c
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292542"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Az Azure Database-áttelepítés minimális állásidővel PostgreSQL
Áttelepítés végrehajtható, így PostgreSQL Azure-adatbázishoz a PostgreSQL minimális állásidővel újonnan bevezetett használatával **folyamatos szinkronizálási funkció** a a [Azure adatbázis áttelepítési szolgáltatás](https://aka.ms/get-dms) (DMS) . Ez a funkció, amely az alkalmazás keletkezett állásidő korlátozza.

## <a name="overview"></a>Áttekintés
DMS elvégzi a helyszíni Azure-adatbázishoz egy kezdeti terhelése PostgreSQL, és majd az alkalmazás futása közben folyamatosan szinkronizál az Azure-bA új tranzakciók. Miután az adatok ki, a cél Azure oldalán, állítsa le az alkalmazást egy rövid pillanatra (minimális állásidő), várja meg az utolsó kötegben adatainak (az idő, amíg az alkalmazás nem érhető el gyakorlatilag bármely új forgalom érvénybe állítsa le az alkalmazást) van szüksége akár a célkiszolgálón, és frissítse az Azure mutasson a kapcsolati karakterláncot. Ha elkészült, az alkalmazás lesz élő Azure!

![Az Azure-adatbázis áttelepítése szolgáltatással folyamatos szinkronizálása](./media/howto-migrate-online/ContinuousSync.png)

DMS áttelepítési PostgreSQL források jelenleg előzetes verzió. Ha szeretné áttelepíteni a PostgreSQL számítási feladatait a szolgáltatás kipróbálásához, regisztráljon az Azure DMS keresztül [villámnézeti lap](https://aka.ms/dms-preview) érdeklődését Express. Visszajelzése hasznos információt a további javítsák a szolgáltatást.

## <a name="next-steps"></a>További lépések
- Tekintse meg a videót [a Microsoft Azure App korszerűsítése](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), egy bemutatja, hogyan PostgreSQL áttelepítendő PostgreSQL-alkalmazások az Azure Database-bemutató, amely tartalmazza.
- Iratkozzon fel a minimális állásidő áttelepítéséhez az Azure Database-PostgreSQL PostgreSQL keresztül az Azure DMS a korlátozott előzetes [villámnézeti lap](https://aka.ms/dms-preview).
