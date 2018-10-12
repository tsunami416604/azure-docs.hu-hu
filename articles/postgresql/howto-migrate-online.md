---
title: Migrálás minimális állásidővel az Azure Database for postgresql-hez
description: Ez a cikk bemutatja, hogyan áttelepítése egy minimális állásidővel PostgreSQL-adatbázis, Azure database for postgresql-hez az Azure Database Migration Service használatával.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 80e5d30677735b35d90fda6288d7bf6f2ea4aa1b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093833"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migrálás minimális állásidővel az Azure Database for postgresql-hez
Hajthat végre PostgreSQL migrálást az Azure Database for postgresql-hez minimális állásidővel segítségével az újonnan bevezetett **folyamatos szinkronizálás képesség** számára a [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) . Ez a funkció az alkalmazás által felmerülő állásidő mennyiségét korlátozza.

## <a name="overview"></a>Áttekintés
A DMS hajt végre egy kezdeti betöltése a helyszíni az Azure Database for postgresql-hez, és ezután közben az alkalmazás továbbra is futni fog folyamatosan szinkronizálja az Azure-ban új tranzakciók. Után a cél Azure oldaláról behozza az adatokat, akkor állítsa le az alkalmazást egy pillanatra (minimális üzemkimaradással) számára, várja meg a legutóbbi köteg adatok (az az idő, amíg az alkalmazás hatékonyan érhető el minden olyan új forgalom érvénybe állítsa le az alkalmazást) olvasásra beállítása a célkiszolgálón, és frissítse a kapcsolati karakterláncot, mutasson az Azure-bA. Ha elkészült, az alkalmazás lesz élő Azure-ban!

![Az Azure Database Migration Service folyamatos szinkronban](./media/howto-migrate-online/ContinuousSync.png)

A PostgreSQL-források DMS áttelepítési jelenleg előzetes verzióban érhető el. Ha szeretné kipróbálni a szolgáltatás a PostgreSQL számítási feladatok migrálása, iratkozzon fel az Azure DMS használatával [előzetes verziójú szolgáltatásainak weblapján](https://aka.ms/dms-preview) Express érdeklődését. A visszajelzés felbecsülhetetlen abban, hogy a szolgáltatás további fejlesztéséhez.

## <a name="next-steps"></a>További lépések
- A videó megtekintésének [alkalmazások korszerűsítése, a Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), amely tartalmaz egy bemutatót: hogyan kell PostgreSQL alkalmazások migrálása az Azure Database for postgresql-hez.
- Lásd a következő oktatóanyagot [PostgreSQL Migrálása az Azure Database for postgresql-hez a DMS használatával online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
