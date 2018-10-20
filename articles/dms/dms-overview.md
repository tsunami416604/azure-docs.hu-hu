---
title: Az Azure-adatbázis áttelepítésének szolgáltatás áttekintése |} A Microsoft Docs
description: Az Azure Database Migration Service, amely zökkenőmentes migrálást a számos adatbázis-forrásokhoz, az Azure-adatplatformokra biztosít áttekintése.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 71d79ac34c4797cceb8374d65afb6fd7662f1c21
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456169"
---
# <a name="what-is-the-azure-database-migration-service"></a>Mi az az Azure Database Migration Service?
Az Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a zökkenőmentes migrálást a több adatbázis-forrásokhoz, az Azure-adatplatformokra minimális állásidővel (online áttelepítések).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Adatbázisok migrálása az Azure-bA a jól ismert eszközökkel
Az Azure Database Migration Service integrálható a meglévő eszközök és szolgáltatások működésének néhány. Ügyfelek biztosít egy átfogó, magas rendelkezésre állású megoldás. A szolgáltatás által használt a [Data Migration Assistant](http://aka.ms/dma) , amely végigvezeti a szükséges áttelepítés előtt módosításokat javaslatokkal elemzéseket és jelentéseket létrehozni. Van arra, hogy minden szükséges szervizelés végrehajtása. Ha készen áll az áttelepítési folyamat megkezdéséhez, az Azure Database Migration Service hajt végre a szükséges lépéseket. Indul el, és felejtse el a migrálási projektek, a biztonságot, tudván, hogy a folyamat kihasználja a Microsoft meghatározása szerint ajánlott eljárásokat.

> [!NOTE]
> Az Azure Database Migration Service egy online migrálás végrehajtásához használatához hozzon létre egy példányt az üzletileg kritikus (előzetes verzió) réteg díjszabása alapján.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás
Jelenleg az Azure Database Migration Service érhető el a következő régiókban:

![Azure Database Migration Service régiónkénti rendelkezésre állás](media\overview\dms-regional-availability1.png)

A legfrissebb információt régiónkénti rendelkezésre állás az Azure Database Migration Service, a globális Azure-infrastruktúra-webhely [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>További lépések
- [Az Azure Database Migration Service példányát létre az Azure portal használatával](quickstart-create-data-migration-service-portal.md).
- [SQL Server migrálása az Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
- [Az Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése](pre-reqs.md).
- [Az Azure Database Migration Service használatával kapcsolatos gyakori kérdések](faq.md).
