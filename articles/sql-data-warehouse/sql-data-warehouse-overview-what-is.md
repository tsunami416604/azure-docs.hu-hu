---
title: Mi az Azure SQL Data Warehouse? | Microsoft Docs
description: "Vállalati szintű elosztott adatbázis, amely petabájtnyi mennyiségű relációs és nem relációs adatot képes feldolgozni. Ez az iparág első felhőalapú adatraktára, amely másodpercek alatt szüneteltethető, illetve növelhető vagy csökkenthető a mérete."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: d5ad5b566bd8d40ab6d7a9151af54890fd47cc88
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>Mi az Azure SQL Data Warehouse?

Az SQL Data Warehouse egy felhőalapú vállalati adattárház (EDW), amely a nagymértékben párhuzamos feldolgozás (MPP) kiaknázásával adatok petabájtjain képes komplex lekérdezéseket végrehajtani. Az SQL Data Warehouse a big data-megoldások egyik fő összetevőjeként használható. Importálja a big data-adatokat az SQL Data Warehouse-ba egyszerű PolyBase T-SQL lekérdezések használatával, majd az MPP nyújtotta lehetőségeket kiaknázva nagy teljesítményű elemzéseket futtathat. Az adatok integrálásával és elemzésével az adattárház válik azzá az egyetlen egységes forrássá, ahonnan a vállalat a valós adatokat és kielemzett eredményeket lekérheti.  


## <a name="key-component-of-big-data-solution"></a>A big data-megoldások fő összetevője
Az SQL Data Warehouse a felhőben futó teljes körű big data-megoldások egyik fő összetevője.

![Adattárház-megoldás](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

A felhőalapú adatkezelő megoldásokban az adatok számos forrásból kerülnek a big data-tárakba. A big data-tárakba behúzott adatokat Hadoop-, Spark- és gépi tanulási algoritmusok készítik elő és dolgozzák be az intelligenciába. Amikor az adatok készek az összetett elemzésekre, az SQL Data Warehouse a PolyBase használatával kérdezi le a big data-tárakat. A PolyBase standard T-SQL-lekérdezések használatával húzza be az adatokat az SQL Data Warehouse-ba.
 
Az SQL Data Warehouse az adatokat oszlopos szerkezetű relációs adattáblákban tárolja. Ez a formátum jelentős mértékben csökkenti a tárolási költségeket és javítja a lekérdezési teljesítményt. Miután az adatokat eltárolta az SQL Data Warehouse-ban, nagy léptékű elemzéseket végezhet. A hagyományos adatbázisrendszerekhez képest az elemzési lekérdezések percek helyett másodpercek, napok helyett órák alatt végeznek. 

Az elemzések eredményei globális jelentéskészítési adatbázisokba vagy alkalmazásokba küldhetőek. Az üzleti elemzők így az ezekből nyerhető betekintések révén tájékozott üzleti döntéseket hozhatnak.

## <a name="optimization-choices"></a>Optimalizálási lehetőségek

Az SQL Data Warehouse rugalmas kialakítású [teljesítményszintjei](performance-tiers.md) mindenféle adatigénynek megfelelnek, a kisebbektől a nagyobbakig. Választhat a rugalmasságra vagy a számítási teljesítményre optimalizált adattárházak közt. 

- A **rugalmasságra optimalizált teljesítményszint** különválasztja a számítási és tárolási rétegeket az architektúrában. Ez a lehetőség az olyan számítási feladatok esetében teljesít kiemelkedően, amelyek teljes mértékben képesek kiaknázni a számítási és a tárolási kapacitások elkülönítése nyújtotta lehetőségeket a rövid tevékenységcsúcsokat kielégítő gyakori átméretezések során. Ez a számítási teljesítményszint rendelkezik a legalacsonyabb belépő költségszinttel, és a skálázásnak köszönhetően jól szolgálja ki az ügyfelek számítási feladatainak nagy részét.

- A **számítási teljesítményre optimalizált teljesítményszint** az Azure legújabb hardverein egy új NVMe SSD-alapú gyorsítótárat vezet be, amely a leggyakrabban lehívott adatokat a processzorokhoz közel tárolja, azaz épp ott, ahol lenniük kell. A tárolás automatikus szintezésével ez a teljesítményszint kiemelkedően teljesít a komplex lekérdezések esetében, mivel az összes be- és kimeneti adat a helyi számítási rétegben található. Emellett a fejlesztett oszlopcentrikus tárolás révén korlátlan mennyiségű adat tárolható az SQL Data Warehouse-ban. A számítási teljesítményre optimalizált teljesítményszint kínálja a legnagyobb szintű vertikális felskálázást, akár 30 000 számítási adattárházegységig (cDWU). Ezt a teljesítményszintet folyamatos, villámgyors teljesítményt igénylő számítási feladatokhoz válassza.

## <a name="next-steps"></a>Következő lépések
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
