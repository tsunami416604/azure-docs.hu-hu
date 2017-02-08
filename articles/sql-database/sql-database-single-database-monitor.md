---
title: "Adatbázis teljesítményének figyelése Azure SQL Database adatbázisokban | Microsoft Docs"
description: "Tudja meg, hogyan figyelheti az adatbázisokat Azure- eszközökkel és dinamikus felügyeleti nézetekkel."
keywords: "adatbázis-megfigyelés, felhőalapú adatbázis teljesítménye"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a2e47475-c955-4a8d-a65c-cbef9a6d9b9f
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/27/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: c0c9d107ff1642d66e96de5409863e4e894d1b6b


---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Adatbázis teljesítményének figyelése Azure SQL Database adatbázisokban
Egy Azure SQL-adatbázis teljesítményének figyelése az erőforrás-használatnak a kiválasztott adatbázis teljesítményszintjéhez viszonyított figyelésével kezdődik. A figyelés segítségével megállapítható, ha az adatbázis többletkapacitással rendelkezik, vagy éppen elérte a maximumot az erőforrások kihasználtságában, és emiatt problémák jelentkezhetnek, így az is eldönthető, hogy módosítani kell-e az adatbázis teljesítményszintjét és [szolgáltatásszintjét](sql-database-service-tiers.md). Az adatbázist figyelheti grafikus eszközök használatával az [Azure Portalon](https://portal.azure.com) vagy SQL [dinamikus felügyeleti nézetek](https://msdn.microsoft.com/library/ms188754.aspx) használatával.

## <a name="monitor-databases-using-the-azure-portal"></a>Adatbázisok figyelése Azure Portal használatával
Az [Azure Portalon](https://portal.azure.com/) az adatbázis kiválasztásával és a **Figyelés** diagramra történő kattintással figyelheti egy önálló adatbázis kihasználtságát. Ekkor megjelenik a **Metrika** ablak, amelyet a **Diagram szerkesztése** gombra kattintva módosíthat. Adja hozzá a következő metrikákat:

* Processzorhasználat (%)
* DTU-kihasználtság (%)
* Adat IO kihasználtsága (%)
* Adatbázis méretének kihasználtsága

A fenti metrikák hozzáadása után azokat a **Figyelés** diagramon tekintheti meg, további részleteket pedig a **Metrika** ablakban talál. A négy metrika az átlagos kihasználtság százalékos arányát jeleníti meg az adatbázis **DTU-jához** viszonyítva. A DTU-król részletesebben a [szolgáltatásszintekről](sql-database-service-tiers.md) szóló cikkben olvashat.

![Adatbázis-teljesítményének szolgáltatásszint-figyelése.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

A metrikákhoz riasztásokat is lehet konfigurálni. Kattintson a **Riasztás hozzáadása** gombra a **Metrika** ablakban. A riasztás konfigurálásához kövesse a Varázslót. Lehetőség van riasztást kérni, ha a metrikák túllépnek egy bizonyos küszöböt, vagy egy bizonyos küszöb alá esnek.

Például ha az adatbázisban munkaterhelés-növekedésére számít, beállíthatja, hogy riasztást kapjon elektronikus üzenet formájában abban az esetben, ha az adatbázisra vonatkozó bármelyik metrika eléri a 80 százalékot. Ez korai figyelmeztetésként szolgálhat annak eldöntésére, hogy mikor kell a következő, magasabb teljesítményszintre váltani.

A metrikák annak megállapításában is segíthetnek, hogy alacsonyabb teljesítményszintre válthat-e. Tegyük fel, hogy Standard S2 adatbázist használ, és a metrikák azt mutatják, hogy az adatbázis átlagos kihasználtsága egy adott időpontban nem több, mint 10 százalék. Ebben az esetben valószínű, hogy az adatbázis Standard S1 teljesítményszinten is megfelelően fog működni. Mielőtt azonban alacsonyabb teljesítményszintre váltana, vegye figyelembe az esetlegesen hirtelen megugró vagy ingadozó munkaterheléseket.

## <a name="monitor-databases-using-dmvs"></a>Adatbázisok figyelése dinamikus felügyeleti nézetek használatával
A portálon elérhető metrikák a következő rendszernézeteken keresztül is elérhetők: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) a kiszolgáló logikai **fő**adatbázisában és [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) a felhasználói adatbázisban. Ha hosszabb ideig kevesebb részletes adatot kell figyelnie, akkor a **sys.resource_stats** nézetet válassza. Ha rövidebb ideig több részletes adatot kell figyelnie, akkor a **sys.dm_db_resource_stats** nézetet válassza. További információkat az [Útmutató az Azure SQL Database teljesítményfigyeléséhez](sql-database-performance-guidance.md#monitor-resource-use) részben talál.

> [!NOTE]
> A **sys.dm_db_resource_stats** eredményhalmaza üres lesz, ha a kivezetett Web vagy Business kiadású adatbázisokra alkalmazzák.
>
>

Rugalmas készletek esetén az ebben a szakaszban leírt technikákkal az adatbáziskészlet egyes adatbázisait is figyelheti. De ugyanígy figyelheti az adatbáziskészlet egészét is. További információkért lásd: [Rugalmas készlet figyelése és kezelése](sql-database-elastic-pool-manage-portal.md).



<!--HONumber=Dec16_HO2-->


