---
title: Minimális állásidő-áttelepítés a PostgreSQL Azure-adatbázisba – Egyetlen kiszolgáló
description: Ez a cikk ismerteti, hogyan hajthatja végre a minimális állásidő-áttelepítés egy PostgreSQL-adatbázis az Azure Database for PostgreSQL - Single Server az Azure Database Migration Service használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 93cd390889c023adf1c30a8470e1c2298598439e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65067511"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Minimális állásidő-áttelepítés a PostgreSQL Azure-adatbázisba – Egyetlen kiszolgáló
A PostgreSQL-áttelepítéseket minimális állásidővel, minimális állásidővel hajthatja végre az [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) újonnan bevezetett folyamatos **szinkronizálási képességének** használatával. Ez a funkció korlátozza az alkalmazás által felmerülő állásidő mennyiségét.

## <a name="overview"></a>Áttekintés
Az Azure DMS a helyszíni Azure Database for PostgreSQL kezdeti terhelését hajtja végre, majd folyamatosan szinkronizálja az új tranzakciókat az Azure-ral, amíg az alkalmazás fut. Miután az adatok utolérik a cél Azure-oldalon, leállítja az alkalmazást egy rövid pillanatra (minimális állásidő), várja meg az utolsó adatköteget (az alkalmazás leállításától addig, amíg az alkalmazás gyakorlatilag nem érhető el az új forgalom elfogásához) fel a cél, majd frissítse a kapcsolati karakterláncot, hogy az Azure-ra mutasson. Ha végzett, az alkalmazás élő ben lesz az Azure-ban!

![Folyamatos szinkronizálás az Azure adatbázis-áttelepítési szolgáltatással](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>További lépések
- Tekintse meg az [App Modernization with Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)című videót, amely egy bemutatót tartalmaz, amely bemutatja, hogyan telepítheti át a PostgreSQL-alkalmazásokat az Azure Database for PostgreSQL-be.
- Tekintse meg a [PostgreSQL áttelepítése az Azure Database for PostgreSQL online dms használatával című oktatóanyagot.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)
