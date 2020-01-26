---
title: Mi az Azure szinapszis Analytics (korábban SQL DW)?
description: Az Azure szinapszis Analytics (korábbi nevén SQL DW) egy korlátlan elemzési szolgáltatás, amely a nagyvállalati adattárházat és a Big adatelemzéseket is egyesíti.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 40f779fa5545e0e9c8fd1aa2cc2689b29dffdb82
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760759"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Mi az Azure szinapszis Analytics (korábban SQL DW)?

Az Azure Synapse egy korlátok nélküli elemzőszolgáltatás, amely egyesíti a vállalati adattárházakat és a Big Data-elemzéseket. Lehetővé teszi, hogy saját tetszőleges módon kérje le az adatokat, kiszolgáló nélküli igény szerinti vagy kiosztott erőforrásokkal, nagy mennyiségben. Az Azure szinapszis az azonnali BI-és gépi tanulási igényekhez kapcsolódóan egységes felhasználói élményt nyújt az adatfeldolgozáshoz,-előkészítéshez,-kezeléshez és-kiszolgáláshoz

Az Azure szinapszis négy összetevőből áll:
- SQL Analytics: teljes T-SQL-alapú elemzés – általánosan elérhető
    - SQL-készlet (fizetés/DWU kiépítve) 
    - Igény szerinti SQL-szolgáltatás (fizetés/TB feldolgozott) – (előzetes verzió)
- Spark: mélyen integrált Apache Spark (előzetes verzió) 
- Adatintegráció: hibrid Adatintegráció (előzetes verzió)
- Studio: egyesített felhasználói élmény.  (Előzetes verzió)

> [!NOTE]
> Az Azure szinapszis előzetes verzió funkcióinak eléréséhez kérjen hozzáférést [itt](https://aka.ms/synapsepreview). A Microsoft az összes kérelem osztályozását és a lehető leghamarabb válaszol.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>SQL Analytics és SQL-készlet az Azure Szinapszisban

Az SQL Analytics az Azure szinapszis szolgáltatással általánosan elérhető nagyvállalati adattárház-funkciókra utal. 

Az SQL-készlet az SQL Analytics használatakor kiépített analitikus erőforrások gyűjteményét jelöli. Az SQL-készlet méretét az adattárház-egységek (DWU-EK) határozzák meg.

Importálja big data egyszerű, [alapszintű](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-lekérdezésekkel, majd használja az MPP erejét a nagy teljesítményű elemzések futtatásához. Az integráció és az elemzés során az SQL Analytics az igazság egyetlen verzióját fogja kiszolgálni, amellyel a vállalat gyorsabban és megbízhatóbban elemezheti az eredményeket.  

## <a name="key-component-of-a-big-data-solution"></a>Egy big data-megoldás fő összetevője

Az adattárházak a felhőalapú, végpontok közötti big data megoldások egyik kulcsfontosságú összetevője.

![Adattárház-megoldás](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

A felhőalapú adatkezelő megoldásokban az adatok számos forrásból kerülnek a big data-tárakba. A big data-tárakba behúzott adatokat Hadoop-, Spark- és gépi tanulási algoritmusok készítik elő és dolgozzák be az intelligenciába. Ha az adatok készen állnak az összetett elemzésre, az SQL Analytics a következőt használja a big data-tárolók lekérdezéséhez: albase. A Base standard T-SQL-lekérdezéseket használ az adatok SQL Analytics-táblákba való bevonásához.
 
Az SQL Analytics oszlopos tárolással rendelkező, kapcsolódó táblákban tárolja az adatkészleteket. Ez a formátum jelentős mértékben csökkenti a tárolási költségeket és javítja a lekérdezési teljesítményt. Az adattárolást követően nagy léptékű elemzéseket futtathat. A hagyományos adatbázisrendszerekhez képest az elemzési lekérdezések percek helyett másodpercek, napok helyett órák alatt végeznek. 

Az elemzések eredményei globális jelentéskészítési adatbázisokba vagy alkalmazásokba küldhetőek. Az üzleti elemzők így az ezekből nyerhető betekintések révén tájékozott üzleti döntéseket hozhatnak.

## <a name="next-steps"></a>Következő lépések

- Az [Azure szinapszis architektúrájának](massively-parallel-processing-mpp-architecture.md) megismerése
- [SQL-készlet gyors létrehozása](create-data-warehouse-portal.md)
- [Mintaadatok betöltése](sql-data-warehouse-load-sample-databases.md).
- [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse) megismerése

Vagy tekintse meg a többi Azure szinapszis-erőforrást.  
* Keresési [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Szolgáltatási kérelmek](https://feedback.azure.com/forums/307516-sql-data-warehouse) elküldése
* [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse) keresése
* [Stack overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw) keresése