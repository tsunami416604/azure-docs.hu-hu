---
title: Mi az Azure SQL Data Warehouse? | Microsoft Docs
description: Vállalati szintű elosztott adatbázis, amely petabájtnyi mennyiségű relációs és nem relációs adatot képes feldolgozni. Ez az iparág első felhőalapú adatraktára, amely másodpercek alatt szüneteltethető, illetve növelhető vagy csökkenthető a mérete.
services: sql-data-warehouse
author: igorstanko
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: overview
ms.component: design
ms.date: 04/17/2018
ms.author: igorstan
ms.reviewer: igorstan
ms.openlocfilehash: 57b94a57ae0279a391cb4269f7b1625499748bae
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="what-is-azure-sql-data-warehouse"></a>Mi az Azure SQL Data Warehouse?

Az SQL Data Warehouse egy felhőalapú vállalati adattárház (EDW), amely a nagymértékben párhuzamos feldolgozás (MPP) kiaknázásával adatok petabájtjain képes komplex lekérdezéseket végrehajtani. Az SQL Data Warehouse a big data-megoldások egyik fő összetevőjeként használható. Importálja a big data-adatokat az SQL Data Warehouse-ba egyszerű PolyBase T-SQL lekérdezések használatával, majd az MPP nyújtotta lehetőségeket kiaknázva nagy teljesítményű elemzéseket futtathat. Az adatok integrálásával és elemzésével az adattárház válik azzá az egyetlen egységes forrássá, ahonnan a vállalat a valós adatokat és kielemzett eredményeket lekérheti.  


## <a name="key-component-of-big-data-solution"></a>A big data-megoldások fő összetevője
Az SQL Data Warehouse a felhőben futó teljes körű big data-megoldások egyik fő összetevője.

![Adattárház-megoldás](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

A felhőalapú adatkezelő megoldásokban az adatok számos forrásból kerülnek a big data-tárakba. A big data-tárakba behúzott adatokat Hadoop-, Spark- és gépi tanulási algoritmusok készítik elő és dolgozzák be az intelligenciába. Amikor az adatok készek az összetett elemzésekre, az SQL Data Warehouse a PolyBase használatával kérdezi le a big data-tárakat. A PolyBase standard T-SQL-lekérdezések használatával húzza be az adatokat az SQL Data Warehouse-ba.
 
Az SQL Data Warehouse az adatokat oszlopos szerkezetű relációs adattáblákban tárolja. Ez a formátum jelentős mértékben csökkenti a tárolási költségeket és javítja a lekérdezési teljesítményt. Miután az adatokat eltárolta az SQL Data Warehouse-ban, nagy léptékű elemzéseket végezhet. A hagyományos adatbázisrendszerekhez képest az elemzési lekérdezések percek helyett másodpercek, napok helyett órák alatt végeznek. 

Az elemzések eredményei globális jelentéskészítési adatbázisokba vagy alkalmazásokba küldhetőek. Az üzleti elemzők így az ezekből nyerhető betekintések révén tájékozott üzleti döntéseket hozhatnak.


## <a name="next-steps"></a>További lépések
Most, hogy jobban megismerte az SQL Data Warehouse szolgáltatást, tudjon meg többet az [SQL Data Warehouse gyors létrehozásáról][create a SQL Data Warehouse] és a [mintaadatok betöltéséről][load sample data]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][Azure glossary], amikor az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Támogatási jegy létrehozása]
* [MSDN-fórum]
* [Stack Overflow-fórum]
* [Twitter]

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
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-fórum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/en-us/support/legal/sla/
