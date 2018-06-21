---
title: MySQL az Azure Database-áttelepítés minimális állásidő érdekében
description: A cikkből megtudhatja, hogyan hajthat végre az Azure Database-MySQL-adatbázis minimális állásidővel áttelepítését a MySQL az Azure-adatbázis áttelepítése szolgáltatás használatával.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: ecbd35bd45bd11292bbe4a032329d704858d4c77
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293921"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>MySQL az Azure Database-áttelepítés minimális állásidő érdekében
Áttelepítés végrehajtható, így MySQL az Azure Database a MySQL minimális állásidővel újonnan bevezetett használatával **folyamatos szinkronizálási funkció** a a [Azure adatbázis áttelepítési szolgáltatás](https://aka.ms/get-dms) (DMS). Ez a funkció, amely az alkalmazás keletkezett állásidő korlátozza.

## <a name="overview"></a>Áttekintés
DMS elvégzi a helyszíni Azure-adatbázishoz egy kezdeti terhelése MySQL, és majd az alkalmazás futása közben folyamatosan szinkronizál az Azure-bA új tranzakciók. Miután az adatok ki, a cél Azure oldalán, állítsa le az alkalmazást egy rövid pillanatra (minimális állásidő), várja meg az utolsó kötegben adatainak (az idő, amíg az alkalmazás nem érhető el gyakorlatilag bármely új forgalom érvénybe állítsa le az alkalmazást) van szüksége akár a célkiszolgálón, és frissítse az Azure mutasson a kapcsolati karakterláncot. Ha elkészült, az alkalmazás lesz élő Azure!

![Az Azure-adatbázis áttelepítése szolgáltatással folyamatos szinkronizálása](./media/howto-migrate-online/ContinuousSync.png)

A MySQL források DMS áttelepítési jelenleg előzetes verzió. Ha szeretné áttelepíteni a MySQL számítási feladatait a szolgáltatás kipróbálásához, regisztráljon az Azure DMS keresztül [villámnézeti lap](https://aka.ms/dms-preview) érdeklődését Express. Visszajelzése hasznos információt a további javítsák a szolgáltatást.

## <a name="next-steps"></a>További lépések
- Tekintse meg a videót [könnyen áttelepítése MySQL/PostgreSQL a felügyelt alkalmazások az Azure-bA](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), amely tartalmaz egy bemutató bemutatja, hogyan MySQL alkalmazások áttelepítése az Azure Database a MySQL.
- Feliratkozás a korlátozott előzetes verzióját a MySQL az Azure DMS keresztül MySQL az Azure Database minimális állásidővel áttelepítéséhez [villámnézeti lap](https://aka.ms/dms-preview).
