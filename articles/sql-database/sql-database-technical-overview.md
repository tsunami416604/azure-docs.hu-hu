---
title: "Mi az Azure SQL Database szolgáltatás? | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "bevezetés az sql-be,az sql bemutatása,mi az sql database"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/17/2017
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a139b97e20685a5ced513c1fd180a74e91c497a7
ms.lasthandoff: 04/15/2017

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>Mi az SQL Database? Bevezetés az SQL Database-be
Az SQL Database a Microsoft Cloud egy, a piacvezető Microsoft SQL Server motorom alapuló relációs adatbázis-szolgáltatása, amely képes a kritikus fontosságú számítási feladatok kezelésére. Az SQL Database minimális felügyelet mellett megbízható teljesítményt, leállási idő nélküli skálázhatóságot, beépített üzletmenet-folytonosságot és adatvédelmet kínál számos szolgáltatási szinten. Ezen képességek birtokában az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat ahelyett, hogy értékes időt és erőforrásokat kellene lefoglalnia a virtuális gépek és infrastruktúra kezeléséhez. Az SQL Database az [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) motoron alapul, így támogatja az SQL Serverhez készült meglévő eszközöket, könyvtárakat és API-alkalmazásokat. Ezáltal megkönnyíti az új megoldások fejlesztését, a meglévő SQL Server-megoldások áthelyezését, valamint a meglévő SQL Server-megoldások kiterjesztését a Microsoft Cloudra anélkül, hogy mindehhez új készségeket kellene elsajátítania.

Jelen cikkünk bemutatja az SQL Database teljesítménnyel, skálázhatósággal és kezelhetőséggel kapcsolatos alapfogalmait és jellemzőit, és a további részletes ismertetőkre mutató hivatkozásokat tartalmaz. Tekintse meg ezeket a rövid útmutatókat a kezdéshez:
 - [SQL Database létrehozása az Azure Portalon](sql-database-get-started-portal.md)  
 - [SQL Database létrehozása az Azure CLI-vel](sql-database-get-started-cli.md)
 - [SQL Database létrehozása PowerShell használatával](sql-database-get-started-powershell.md)

Több Azure CLI és PowerShell-mintát talál itt:
 - [Azure CLI-minták az Azure SQL Database-hez](sql-database-cli-samples.md)
 - [Azure PowerShell-minták az Azure SQL Database-hez](sql-database-powershell-samples.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Teljesítmény módosítása és skálázása leállási idő nélkül
Az SQL Database szolgáltatás három különböző szolgáltatásszinttel rendelkezik: Alapszintű, Standard, Prémium és Prémium RS. Az egyes szolgáltatásszintek [különböző teljesítmény- és képességszinteket](sql-database-service-tiers.md) kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz munkaterhelésig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később az aktuális szükségletek alapján manuálisan vagy programon keresztül bármikor [módosíthatja a szolgáltatásszintet](sql-database-service-tiers.md). Mindezt anélkül teheti meg, hogy a leállással járna az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

## <a name="elastic-pools-to-maximize-resource-utilization"></a>Rugalmas készletek az erőforrások kihasználtságának maximalizálásához
Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. A [rugalmas készletek](sql-database-elastic-pool.md) megoldást jelentenek erre a problémára. A koncepció egyszerű. A készletekhez az önálló adatbázis helyett teljesítmény-erőforrásokat foglalhat le, így nem egy önálló adatbázis teljesítményéért, hanem a készlet közös teljesítményéért fizet. A rugalmas készletekkel az erőforrásigények ingadozásakor sem kell a az adatbázis teljesítményének fel- és leskálázására koncentrálnia. A készletezett adatbázisok szükség szerint használják fel a teljesítmény-erőforrásokat. A készletezett adatbázisok használják az adatbáziskészlethez hozzárendelt teljesítményt, de nem lépik túl az adatbáziskészlet teljesítménykorlátait, így költségei előre jelezhetők még akkor is, ha az egyéni adatbázis-használat nem jelezhető előre. Ráadásul [adatbázisokat adhat hozzá a készlethez, és távolíthat el a készletből](sql-database-elastic-pool-manage-portal.md), így előre jelezhető költségek mellett néhány adatbázisról több ezer adatbázisra skálázhatja fel alkalmazását. Végül a készlet adatbázisai számára elérhető erőforrások minimális és maximális mennyiségét is szabályozhatja annak érdekében, hogy a készlet egyetlen adatbázisa se használja fel a készlet összes erőforrását, és hogy minden készletezett adatbázis rendelkezzen egy garantált minimális erőforrás-mennyiséggel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

## <a name="blend-single-databases-with-pooled-databases"></a>Önálló adatbázisok beolvasztása a készletezett adatbázisokba
Akár önálló adatbázisokat, akár rugalmas készleteket választ, a későbbiekben is sok mindent megváltoztathat. Az önálló adatbázisokat rugalmas készletekkel kombinálhatja, továbbá gyorsan és egyszerűen módosíthatja az önálló adatbázisok és a rugalmas készletek szolgáltatásszintjét, hogy mindig az adott helyzethez tudjon igazodni. Az Azure sokoldalúságának és széles körű alkalmazhatóságának köszönhetően tetszés szerint kombinálhatja az Azure-szolgáltatásokat SQL Database adatbázisokkal, így kielégíthetők az egyedi, modern alkalmazástervezési igények, növelhető a költség- és erőforrás-hatékonyság, és új üzleti lehetőségek tárhatók fel.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás
Hogyan hasonlítható össze az önálló adatbázisok és a rugalmas készletek relatív teljesítménye? Hogyan lehet megállapítani a fel- és leskálázás megfelelő mértékét? Használja [az önálló adatbázisok esetében a DTU-kon, rugalmas adatbázisok és adatbáziskészletek esetében pedig az eDTU-kon](sql-database-what-is-a-dtu.md) alapuló, teljesítmény-értékeléssel kombinált, [beépített teljesítményfigyelő](sql-database-performance.md) és [riasztási](sql-database-insights-alerts-portal.md) eszközöket. Ezek lehetővé teszik az aktuális vagy a projekthez kapcsolódó teljesítményigényeken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. A részletekért olvassa el [Az SQL Database beállításai és teljesítménye: mi érhető el az egyes szolgáltatásszinteken](sql-database-service-tiers.md) című részt.

## <a name="keep-your-app-and-business-running"></a>Biztosítsa alkalmazása és vállalkozása folyamatos működését
Az Azure szolgáltatói szerződésében [(SLA)](http://azure.microsoft.com/support/legal/sla/) az ágazatban élenjáró módon 99,99 százalékos elérhetőséget biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Az SQL-adatbázisok használatakor élvezheti a beépített biztonság, hibatűrés és [adatvédelem](sql-database-automated-backups.md) előnyeit, amelyeket egyébként külön kellene megvásárolnia vagy megterveznie, kiépítenie és kezelnie. Az SQL Database minden egyes szolgáltatásszintje az üzletmenet folytonosságát biztosító szolgáltatások és lehetőségek átfogó készletét kínálja, amelyeket tetszés szerint futtathat és helyezhet üzembe. Az [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md) segítségével az adatbázist visszaállíthatja egy legfeljebb 35 nappal korábbi állapotba. A [biztonsági mentések hosszú távú megőrzésének](sql-database-long-term-retention.md) konfigurálásával biztonsági mentéseit biztonságos tárolóban tarthatja akár 10 évig. Továbbá, ha az adatbázisokat üzemeltető adatközpontban szolgáltatáskimaradás következik be, visszaállíthatja az adatbázisokat a [legújabb biztonsági mentések georedundáns példányaiból](sql-database-recovery-using-backups.md). Szükség esetén a gyors feladatátvétel érdekében egy vagy több régióban [georedundáns olvasható replikákat](sql-database-geo-replication-overview.md) is konfigurálhat az adatközpont-leállások esetére. Ezeket a replikákat a különböző földrajzi régiók olvasási teljesítményének gyorsítására, illetve [leállás nélküli alkalmazásfrissítéshez](sql-database-manage-application-rolling-upgrade.md) is felhasználhatja. 

![Az SQL Database georeplikációja](./media/sql-database-technical-overview/azure_sqldb_map.png)

A különböző szolgáltatásszinteken elérhető üzleti folytonossági funkciókról [Az üzletmenet folytonossága](sql-database-business-continuity.md) című részben találhat további információkat.

## <a name="secure-your-data"></a>Az adatok védelme
Az SQL Server megbízható adatbiztonságát az SQL Database olyan funkciói garantálják, amelyek korlátozzák a hozzáférést, védik az adatokat, és hozzájárulnak a tevékenységek figyeléséhez. Az SQL Database biztonsági beállításait az [SQL-adatbázis védelme](sql-database-security-overview.md) részben foglaltuk össze. A biztonsági funkciók alaposabb bemutatását [Az SQL Server adatbázismotor és SQL Database biztonsági központja](https://msdn.microsoft.com/library/bb510589) a részben találja. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/security/) részben talál információkat.

## <a name="next-steps"></a>Következő lépések
Most, hogy elolvasta az SQL Database-be történő bevezetést, és választ kapott a "Mi az SQL Database?" kérdésre, a következő lépésekkel folytathatja:

* Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/sql-database/) az önálló adatbázisok és a rugalmas készletek költségeinek összehasonlításáért és árkalkulációjáért.
* Tudjon meg többet a [rugalmas készletekről](sql-database-elastic-pool.md).
* Első lépésként [Hozza létre első adatbázisát](sql-database-get-started-portal.md).
* Hozza létre első alkalmazását C#, Java, Node.js, PHP, Python vagy Ruby programozási nyelven: [Adatkapcsolattárak SQL Database és SQL Server használatához](sql-database-libraries.md)

