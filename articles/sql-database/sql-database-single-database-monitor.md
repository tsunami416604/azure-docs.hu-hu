<properties
    pageTitle="Adatbázis teljesítményének figyelése Azure SQL Database adatbázisokban | Microsoft Azure"
    description="Tudja meg, hogyan figyelheti az adatbázisokat Azure- eszközökkel és dinamikus felügyeleti nézetekkel."
    keywords="database monitoring, cloud database performance"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="03/25/2016"
    ms.author="carlrab"/>

# Adatbázis teljesítményének figyelése Azure SQL Database adatbázisokban
Egy Azure SQL-adatbázis teljesítményének figyelése az erőforrás-használatnak a kiválasztott adatbázis teljesítményszintjéhez viszonyított figyelésével kezdődik. A figyelés segítségével megállapítható, ha az adatbázis többletkapacitással rendelkezik, vagy éppen elérte a maximumot az erőforrások kihasználtságában, és emiatt problémák jelentkezhetnek, így az is eldönthető, hogy módosítani kell-e az adatbázis teljesítményszintjét és [szolgáltatásszintjét](sql-database-service-tiers.md). Az adatbázist figyelheti grafikus eszközök használatával az [Azure portálon](https://portal.azure.com) vagy SQL [dinamikus felügyeleti nézetek](https://msdn.microsoft.com/library/ms188754.aspx) használatával.

## Adatbázisok figyelése Azure portál használatával

Az [Azure portálon](https://portal.azure.com/) az adatbázis kiválasztásával és a **Figyelés** diagramra történő kattintással figyelheti egy önálló adatbázist erőforrás-használat. Ekkor megjelenik a **Metrika** ablak, amelyet a **Diagram szerkesztése** gombra kattintva módosíthat. Adja hozzá a következő metrikákat:

- Processzorhasználat (%)
- DTU-kihasználtság (%)
- Adat IO kihasználtsága (%)
- Tárterület-használat (%)

A fenti metrikák hozzáadása után azokat a **Figyelés** diagramon tekintheti meg, további részleteket pedig a **Metrika** ablakban talál. A négy metrika az átlagos kihasználtság százalékos arányát jeleníti meg az adatbázis **DTU-jához** viszonyítva. A DTU-król részletesebben a [szolgáltatásszintekről](sql-database-service-tiers.md) szóló cikkben olvashat.

![Adatbázis-teljesítményének szolgáltatásszint-figyelése.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

A metrikákhoz riasztásokat is lehet konfigurálni. Kattintson a **Riasztás hozzáadása** gombra a **Metrika** ablakban.  A riasztás konfigurálásához kövesse a Varázslót. Lehetőség van riasztást kérni, ha a metrikák túllépnek egy bizonyos küszöböt, vagy egy bizonyos küszöb alá esnek.

Például ha az adatbázisban munkaterhelés-növekedésére számít, beállíthatja, hogy riasztást kapjon elektronikus üzenet formájában abban az esetben, ha az adatbázisra vonatkozó bármelyik metrika eléri a 80 százalékot. Ez korai figyelmeztetésként szolgálhat annak eldöntésére, hogy mikor kell a következő, magasabb teljesítményszintre váltani.

A metrikák annak megállapításában is segíthetnek, hogy alacsonyabb teljesítményszintre válthat-e. Tegyük fel, hogy Standard S2 adatbázist használ, és a metrikák azt mutatják, hogy az adatbázis átlagos kihasználtsága egy adott időpontban nem több, mint 10 százalék. Ebben az esetben valószínű, hogy az adatbázis Standard S1 teljesítményszinten is megfelelően fog működni. Mielőtt azonban alacsonyabb teljesítményszintre váltana, vegye figyelembe az esetlegesen hirtelen megugró vagy ingadozó munkaterheléseket.

## Adatbázisok figyelése dinamikus felügyeleti nézetek használatával

A portálon elérhető metrikák a következő rendszernézeteken keresztül is elérhetők: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) a kiszolgáló logikai **fő**adatbázisában és [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) a felhasználói adatbázisban. Ha hosszabb ideig kevesebb részletes adatot kell figyelnie, akkor a **sys.resource_stats** nézetet válassza. Ha rövidebb ideig több részletes adatot kell figyelnie, akkor a **sys.dm_db_resource_stats** nézetet válassza. További információkat az [Útmutató az Azure SQL Database teljesítményfigyeléséhez](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats) részben talál.

>[AZURE.NOTE] A **sys.dm_db_resource_stats** eredményhalmaza üres lesz, ha a kivezetett Web vagy Business kiadású adatbázisokra alkalmazzák.

Rugalmas adatbáziskészletek esetén az ebben a szakaszban leírt technikákkal az adatbáziskészlet egyes adatbázisait is figyelheti. De ugyanígy figyelheti az adatbáziskészlet egészét is. További információkat a [Rugalmas készlet figyelése és kezelése](sql-database-elastic-pool-manage-portal.md) részben talál.



<!--HONumber=Jun16_HO2-->


