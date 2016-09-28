<properties
    pageTitle="Mi az SQL Database? Bevezetés az SQL Database-be| Microsoft Azure"
    description="Bevezetés az SQL Database-be: a Microsoft felhőalapú relációs adatbázis-kezelő rendszerének (RDBMS) technikai részletei és funkciói."
    keywords="bevezetés az sql-be,az sql bemutatása,mi az sql database"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>


# Mi az SQL Database? Bevezetés az SQL Database-be

Az SQL Database a piacvezető Microsoft SQL Server motoron alapuló, felhőalapú relációs adatbázis-szolgáltatás, amely az üzletmenet szempontjából kritikus funkciókkal rendelkezik. Az SQL Database minimális felügyelet mellett megbízható teljesítményt, leállási idő nélküli skálázhatóságot, az üzletmenet folytonosságát és adatvédelmet kínál. Így a virtuális gépek és infrastruktúra kezelése helyett az alkalmazások gyors fejlesztésére és forgalomba hozásának felgyorsítására összpontosíthat. Az SQL Database az [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) motoron alapul, így támogatja az SQL Serverhez készült meglévő eszközöket, könyvtárakat és API-alkalmazásokat, megkönnyítve ezáltal a felhőbe történő áthelyezést és kiterjesztést.

Jelen cikkünk bemutatja az SQL Database teljesítménnyel, skálázhatósággal és kezelhetőséggel kapcsolatos alapfogalmait és jellemzőit, és a további részletes ismertetőkre mutató hivatkozásokat tartalmaz. Ha készen áll, akkor percek alatt [létrehozhatja első SQL-adatbázisát](sql-database-get-started.md) vagy [rugalmas adatbáziskészletét](sql-database-elastic-pool-create-portal.md). Ha alaposabban szeretne tájékozódni, nézze meg ezt a 30 perces videót.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## Teljesítmény módosítása és skálázása leállási idő nélkül

Az SQL-adatbázisok a Basic, Standard és Premium *szolgáltatásszinteken* érhetők el. Az egyes szolgáltatásszintek [különböző teljesítmény- és képességszinteket](sql-database-service-tiers.md) kínálnak, így különböző adatbázis-tevékenységprofilokat képesek támogatni, a könnyűtől a nehéz munkaterhelésig. Havi pár dollárért létrehozhatja első, kisméretű adatbázis-alkalmazását, majd később, ha alkalmazása világszerte elterjed, manuálisan vagy programon keresztül bármikor [módosíthatja a szolgáltatásszintet](sql-database-scale-up.md) anélkül, hogy ez üzemszünettel járna az alkalmazás vagy az ügyfelek számára.

Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd az önálló adatbázisok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése.

Az SQL Database [rugalmas készletei](sql-database-elastic-pool.md) megoldást jelentenek erre a problémára. A koncepció egyszerű. A készlethez lefoglalhat egy bizonyos teljesítményt, és nem egy önálló adatbázis teljesítményéért, hanem a készlet közös teljesítményéért fizet. Az adatbázis teljesítményét így nem szükséges felfelé vagy lefelé skáláznia. Az adatbáziskészlet úgynevezett *rugalmas adatbázisainak* fel- és leskálázása szükség szerint automatikusan megtörténik. A rugalmas adatbázisok használják az adatbáziskészlethez hozzárendelt teljesítményt, de nem lépik túl az adatbáziskészlet teljesítménykorlátait, így költségei előre jelezhetők még akkor is, ha az adatbázis-használat nem jelezhető előre. Ráadásul [adatbázisokat adhat hozzá a készlethez, és távolíthat el a készletből](sql-database-elastic-pool-manage-portal.md), így előre jelezhető költségek mellett néhány adatbázisról több ezer adatbázisra skálázhatja fel alkalmazását. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.

Akár az önálló adatbázist, akár a rugalmas adatbáziskészletet választja, a későbbiekben is sok mindent megváltoztathat. Az önálló adatbázisokat rugalmas adatbáziskészletekkel kombinálhatja, továbbá módosíthatja az önálló adatbázisok és az adatbáziskészletek szolgáltatásszintjét újszerű kialakítások létrehozásához. Ezenkívül az Azure teljesítménye és elérése révén modern alkalmazástervezési igényeinek kielégítése, a költségek és az erőforrások hatékonyságának irányítása, valamint az új üzleti lehetőségek kiaknázása érdekében tetszés szerint kombinálhatja az Azure-szolgáltatásokat SQL Database adatbázisokkal.

Hogyan hasonlítható össze az adatbázisok és adatbáziskészletek relatív teljesítménye? Hogyan lehet megállapítani a fel- és leskálázás megfelelő mértékét? A válasz az önálló adatbázisok adatbázis-tranzakciós egységei (DTU-k) és a rugalmas adatbázisok, illetve adatbáziskészletek rugalmas adatbázis-tranzakciós egységei (eDTU-k) alapján adható meg. A részletekért olvassa el [Az SQL Database beállításai és teljesítménye: mi érhető el az egyes szolgáltatásszinteken](sql-database-service-tiers.md) című részt.

## Biztosítsa alkalmazása és vállalkozása folyamatos működését

Az Azure szolgáltatói szerződésében [(SLA)](http://azure.microsoft.com/support/legal/sla/) az ágazatban élenjáró módon 99,99 százalékos elérhetőséget biztosítunk – a Microsoft által kezelt adatbázisok globális hálózata teszi lehetővé, hogy alkalmazása a hét mind a 7 napján, napi 24 órában fusson. Az SQL-adatbázisok használatakor élvezheti a beépített adatvédelem és hibatűrés előnyeit, amelyeket egyébként külön kellene megterveznie, megvásárolnia, kiépítenie és kezelnie. Üzleti igényeitől függően kiegészítő védelmi rétegeket is igényelhet, hogy katasztrófa, hiba vagy más probléma esetén biztosítva legyen az alkalmazás gyors helyreállítása és az üzletmenet folytonossága. Az SQL Database minden egyes szolgáltatásszintje különböző funkcióválasztékot kínál, amelyeket tetszés szerint üzembe helyezhet és futtathat. Az időponthoz kötött visszaállítás segítségével az adatbázist visszaállíthatja egy legfeljebb 35 nappal korábbi állapotba. Ezenkívül ha az adatbázisokat üzemeltető adatközpontban szolgáltatáskimaradás történik, az adatbázis-replikákra egy másik régióba történő feladatátvételt kérhet. A replikákat frissítheti, vagy áthelyezheti más régiókba.

![Az SQL Database georeplikációja](./media/sql-database-technical-overview/azure_sqldb_map.png)


A különböző szolgáltatásszinteken elérhető üzleti folytonossági funkciókról [Az üzletmenet folytonossága](sql-database-business-continuity.md) című részben találhat további információkat.

## Az adatok védelme
Az SQL Server megbízható adatbiztonságát az SQL Database olyan funkciói garantálják, amelyek korlátozzák a hozzáférést, védik az adatokat, és hozzájárulnak a tevékenységek figyeléséhez. Az SQL Database biztonsági beállításait az [SQL-adatbázis védelme](sql-database-security.md) részben foglaltuk össze. A biztonsági funkciók alaposabb bemutatását [Az SQL Server adatbázismotor és SQL Database biztonsági központja](https://msdn.microsoft.com/library/bb510589) a részben találja. Az Azure platform biztonságáról az [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/security/) részben talál információkat.

## Következő lépések
Most, hogy elolvasta az SQL Database-be történő bevezetést, és választ kapott a "Mi az SQL Database?" kérdésre, a következő lépésekkel folytathatja:

- Tekintse meg az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/sql-database/) az önálló adatbázisok és a rugalmas adatbázisok költségeinek összehasonlításáért és árkalkulációjáért.
- Tudjon meg többet a [rugalmas készletekről](sql-database-elastic-pool.md).
- Első lépésként [Hozza létre első adatbázisát](sql-database-get-started.md).
- [Kapcsolódás és lekérdezés SSMS alkalmazásával](sql-database-connect-query-ssms.md)
- Hozza létre első alkalmazását C#, Java, Node.js, PHP, Python vagy Ruby programozási nyelven: [Adatkapcsolattárak SQL Database és SQL Server használatához](sql-database-libraries.md)
- Nézze meg az [Azure SQL Database szolgáltatás összes témakörére](sql-database-index-all-articles.md) vonatkozó címmutatót és leírásokat.



<!--HONumber=Sep16_HO4-->


