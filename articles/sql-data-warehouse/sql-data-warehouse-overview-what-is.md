---
title: Mi az Azure SQL Data Warehouse? | Microsoft Docs
description: Nagyvállalati szintű elosztott adatbázis, amely petabájtnyi mennyiségű relációs és nem relációs adatokat tud feldolgozni. Az iparág első felhőalapú adattárház lehetővé teszi az növelhető, csökkenthető és felfüggeszthető másodpercek alatt.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: a9126e9023091dd8c3df71f2aa2558a01227a8be
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428027"
---
# <a name="what-is-azure-sql-data-warehouse"></a>Mi az Azure SQL Data Warehouse?

Az SQL Data Warehouse egy felhőalapú vállalati adatok adattárház (EDW), amely a nagymértékben párhuzamos feldolgozási (MPP) segítségével gyorsan összetett lekérdezések futtatása több petabájtnyi adat. Az SQL Data Warehouse a big data-megoldások egyik fő összetevőjeként használható. Big Data típusú adatok importálása az SQL Data Warehouse-bA az egyszerű [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-lekérdezéseket, és majd használja a power MPP nyújtotta lehetőségeket kiaknázva nagy teljesítményű elemzési futtatásához. Az adatok integrálásával és elemzésével az adattárház válik azzá az egyetlen egységes forrássá, ahonnan a vállalat a valós adatokat és kielemzett eredményeket lekérheti.  

## <a name="key-component-of-big-data-solution"></a>A big data-megoldások fő összetevője

Az SQL Data Warehouse a felhőben futó teljes körű big data-megoldások egyik fő összetevője.

![Adattárház-megoldás](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

A felhőalapú adatkezelő megoldásokban az adatok számos forrásból kerülnek a big data-tárakba. A big data-tárakba behúzott adatokat Hadoop-, Spark- és gépi tanulási algoritmusok készítik elő és dolgozzák be az intelligenciába. Amikor az adatok készek az összetett elemzésekre, az SQL Data Warehouse a PolyBase használatával kérdezi le a big data-tárakat. A PolyBase standard T-SQL-lekérdezések használatával húzza be az adatokat az SQL Data Warehouse-ba.
 
Az SQL Data Warehouse az adatokat oszlopos szerkezetű relációs adattáblákban tárolja. Ez a formátum jelentős mértékben csökkenti a tárolási költségeket és javítja a lekérdezési teljesítményt. Miután az adatokat eltárolta az SQL Data Warehouse-ban, nagy léptékű elemzéseket végezhet. A hagyományos adatbázisrendszerekhez képest az elemzési lekérdezések percek helyett másodpercek, napok helyett órák alatt végeznek. 

Az elemzések eredményei globális jelentéskészítési adatbázisokba vagy alkalmazásokba küldhetőek. Az üzleti elemzők így az ezekből nyerhető betekintések révén tájékozott üzleti döntéseket hozhatnak.

## <a name="next-steps"></a>További lépések

- Ismerkedés a [Azure SQL Data Warehouse architektúrája](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- Gyorsan [SQL Data Warehouse létrehozása][create a SQL Data Warehouse]
- [Mintaadatokat tölthet be][load sample data].
- Ismerkedés a [videók](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  
* Keresés [blogok]
* Küldje el a [Funkciókérések]
* Keresés [Az ügyféltanácsadói csapat blogjai]
* [Támogatási jegy létrehozása]
* Keresés [MSDN-fórum]
* Keresés [Stack Overflow-fórum]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Támogatási jegy létrehozása]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
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
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
