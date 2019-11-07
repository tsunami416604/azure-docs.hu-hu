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
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645516"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Mi az Azure szinapszis Analytics (korábban SQL DW)?

Az Azure szinapszis egy korlátlan elemzési szolgáltatás, amely egyesíti a vállalati adattárházat és a Big adatelemzést. Lehetővé teszi, hogy a használati feltételek alapján, akár kiszolgáló nélküli igény szerinti vagy kiépített erőforrásokkal is lekérdezze az adatait. Az Azure szinapszis az azonnali BI-és gépi tanulási igényekhez kapcsolódóan egységes felhasználói élményt nyújt az adatfeldolgozáshoz,-előkészítéshez,-kezeléshez és-kiszolgáláshoz

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

## <a name="next-steps"></a>További lépések

- Az [Azure szinapszis architektúrájának](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture) megismerése
- [SQL-készlet gyors létrehozása](create-data-warehouse-portal.md)
- [Mintaadatok betöltése][load sample data].
- [Videók](/azure/sql-data-warehouse/sql-data-warehouse-videos) megismerése

Vagy tekintse meg a többi Azure szinapszis-erőforrást.  
* Keresési [Blogok]
* [Funkciókérések] elküldése
* [Az ügyféltanácsadói csapat blogjai] keresése
* [Támogatási jegy létrehozása]
* [MSDN-fórum] keresése
* [Stack Overflow-fórum] keresése


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Támogatási jegy létrehozása]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-fórum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-fórum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
