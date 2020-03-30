---
title: Mi az Azure Synapse Analytics (korábban SQL DW)?
description: Az Azure Synapse Analytics (korábban SQL DW) egy korlátlan elemzési szolgáltatás, amely egyesíti a vállalati adattárházat és a Big Data-elemzéseket.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a118d028cc85eb858dd0dc1fa6d5d2268f7db43b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350406"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Mi az Azure Synapse Analytics (korábban SQL DW)?

Az Azure Synapse egy korlátok nélküli elemzőszolgáltatás, amely egyesíti a vállalati adattárházakat és a Big Data-elemzéseket. Lehetővé teszi, hogy saját tetszőleges módon kérje le az adatokat, kiszolgáló nélküli igény szerinti vagy kiosztott erőforrásokkal, nagy mennyiségben. Az Azure Synapse egyesíti ezt a két világot egy egységes felhasználói felülettel, amely lenyeli, előkészíti, kezeli és kiszolgálja az adatokat az azonnali BI- és gépi tanulási igényekhez

Az Azure Synapse négy összetevőből áll:
- SQL Analytics: Teljes T-SQL alapú elemzés – Általánosan elérhető
    - SQL-készlet (kiépített DWU-nkénti fizetés) 
    - SQL on-demand (fizetés feldolgozott TB-nként) – (előzetes verzió)
- Spark: Mélyen integrált Apache Spark (előzetes verzió) 
- Adatintegráció: Hibrid adatintegráció (előzetes verzió)
- Stúdió: Egységes felhasználói élmény.  (Előzetes verzió)

> [!NOTE]
> Az Azure Synapse előzetes funkcióinak eléréséhez kérjen hozzáférést [itt.](https://aka.ms/synapsepreview) A Microsoft minden kérést betartat, és a lehető leghamarabb válaszol.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics és SQL-készlet az Azure Synapse-ban

Az SQL Analytics az Azure Synapse-ban általánosan elérhető vállalati adattárház-funkciókra hivatkozik. 

Az SQL-készlet az SQL Analytics használata során kiépített analitikus erőforrások gyűjteményét jelenti. Az SQL-készlet méretét az adatraktározási egységek (DWU) határozzák meg.

Importáljon big data-adatokat egyszerű [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-lekérdezésekkel, majd használja az MPP erejét a nagy teljesítményű elemzések futtatásához. Az integráció és az elemzés adódik, az SQL Analytics lesz az igazság egyetlen verziója, amire vállalkozása számíthat a gyorsabb és megbízhatóbb elemzések hez.  

## <a name="key-component-of-a-big-data-solution"></a>A big data-megoldás fő összetevője

Az adattárház a felhőalapú, végpontok között big data megoldás kulcsfontosságú összetevője.

![Adattárház-megoldás](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

A felhőalapú adatkezelő megoldásokban az adatok számos forrásból kerülnek a big data-tárakba. A big data-tárakba behúzott adatokat Hadoop-, Spark- és gépi tanulási algoritmusok készítik elő és dolgozzák be az intelligenciába. Ha az adatok készen állnak az összetett elemzésre, az SQL Analytics a PolyBase használatával küld lekérdezéseket a big data-tárolókba. A PolyBase standard T-SQL-lekérdezésekkel kéri le az adatokat az SQL Analytics-táblákba.
 
Az SQL Analytics az adatokat relációs táblákban tárolja oszlopos tárolással. Ez a formátum jelentős mértékben csökkenti a tárolási költségeket és javítja a lekérdezési teljesítményt. Az adattárolást követően nagy méretekben futtathat elemzéseket. A hagyományos adatbázisrendszerekhez képest az elemzési lekérdezések percek helyett másodpercek, napok helyett órák alatt végeznek. 

Az elemzések eredményei globális jelentéskészítési adatbázisokba vagy alkalmazásokba küldhetőek. Az üzleti elemzők így az ezekből nyerhető betekintések révén tájékozott üzleti döntéseket hozhatnak.

## <a name="next-steps"></a>További lépések

- Az [Azure Synapse architektúra felfedezése](massively-parallel-processing-mpp-architecture.md)
- [SQL-készlet gyors létrehozása](create-data-warehouse-portal.md)
- [Mintaadatok betöltése](load-data-from-azure-blob-storage-using-polybase.md).
- [Videók felfedezése](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Vagy tekintse meg ezeket a többi Azure Synapse-erőforrást.  
* [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) keresése
* [Szolgáltatáskérések](https://feedback.azure.com/forums/307516-sql-data-warehouse) elküldése
* [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
* Keresés [AZ MSDN fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* Keresés [Stack túlcsordulás fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)
