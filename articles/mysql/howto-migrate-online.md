---
title: Minimális állásidő-áttelepítés – Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan hajthatja végre a minimális állásidő-áttelepítés egy MySQL-adatbázis az Azure Database for MySQL az Azure Database Migration Service használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: df818fa4106aec341607d8142b2a672699b8e9d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063347"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimális állásidő-áttelepítés a MySQL Azure Database szolgáltatásba
A MySQL-áttelepítések az Azure Database for MySQL minimális állásidő használatával az [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) újonnan bevezetett **folyamatos szinkronizálási képesség** használatával. Ez a funkció korlátozza az alkalmazás által felmerülő állásidő mennyiségét.

## <a name="overview"></a>Áttekintés
Az Azure DMS a helyszíni Azure Database for MySQL kezdeti terhelését hajtja végre, majd folyamatosan szinkronizálja az új tranzakciókat az Azure-ral, amíg az alkalmazás fut. Miután az adatok utolérik a cél Azure-oldalon, leállítja az alkalmazást egy rövid pillanatra (minimális állásidő), várja meg az utolsó adatköteget (az alkalmazás leállításától addig, amíg az alkalmazás gyakorlatilag nem érhető el az új forgalom elfogásához) fel a cél, majd frissítse a kapcsolati karakterláncot, hogy az Azure-ra mutasson. Ha végzett, az alkalmazás élő ben lesz az Azure-ban!

![Folyamatos szinkronizálás az Azure adatbázis-áttelepítési szolgáltatással](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>További lépések
- Tekintse meg a [videót Egyszerűen migrálja a MySQL/PostgreSQL alkalmazásokat az Azure felügyelt szolgáltatásába,](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)amely egy bemutatót tartalmaz, amely bemutatja, hogyan telepítheti át a MySQL-alkalmazásokat az Azure Database for MySQL-be.
- Tekintse meg a [MySQL áttelepítése az Azure Database for MySQL online dms használatával](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online).
