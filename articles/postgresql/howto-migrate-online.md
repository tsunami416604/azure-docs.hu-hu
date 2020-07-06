---
title: Minimális – állásidő áttelepítése Azure Database for PostgreSQL – egyetlen kiszolgálóra
description: Ez a cikk azt ismerteti, hogyan hajtható végre a PostgreSQL-adatbázisok minimális leállás utáni áttelepítése a Azure Database Migration Service használatával Azure Database for PostgreSQL egyetlen kiszolgálóra.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "65067511"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Minimális – állásidő áttelepítése Azure Database for PostgreSQL – egyetlen kiszolgálóra
A PostgreSQL áttelepítését minimális állásidővel Azure Database for PostgreSQLhatja a [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) újonnan bevezetett **folyamatos szinkronizálási funkciójának** használatával. Ez a funkció korlátozza az alkalmazás által felmerült állásidő mennyiségét.

## <a name="overview"></a>Áttekintés
Az Azure DMS egy kezdeti terhelést hajt végre a helyszíni Azure Database for PostgreSQLon, majd folyamatosan szinkronizál minden új tranzakciót az Azure-ba, amíg az alkalmazás fut. Miután az adatok bekerültek a cél Azure-ra, egy rövid pillanatra (a minimális állásidőig) megvárhatják az utolsó adatköteget (az alkalmazás leállításának időpontjától egészen addig, amíg az alkalmazás nem áll készen az új forgalom elvégzésére), majd frissítenie kell a kapcsolódási karakterláncot, hogy az Azure-ra mutasson. Ha elkészült, az alkalmazás az Azure-ban fog élni.

![Folyamatos szinkronizálás a Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>További lépések
- Tekintse meg a videós [alkalmazások modernizációját Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)segítségével, amely egy bemutatót tartalmaz, amely bemutatja, hogyan telepítse át a PostgreSQL-alkalmazásokat a Azure Database for PostgreSQLba.
- Tekintse [meg a PostgreSQL Áttelepítését Azure Database for PostgreSQL online-ba a DMS használatával](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)című oktatóanyagot.
