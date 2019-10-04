---
title: Teljesítménnyel kapcsolatos javaslatok – Azure SQL Database | Microsoft Docs
description: Azure SQL Database javaslatokat nyújt az SQL-adatbázisokhoz, amelyek javíthatják az aktuális lekérdezési teljesítményt.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 08def3ac2fd94f01586bc690d867c04758b8856b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569528"
---
# <a name="performance-recommendations-for-sql-database"></a>Teljesítménnyel kapcsolatos javaslatok a SQL Database

Azure SQL Database tanul és alkalmazkodik az alkalmazáshoz. Testreszabott ajánlásokat biztosít, amelyek lehetővé teszik az SQL-adatbázisok teljesítményének maximalizálását. SQL Database folyamatosan vizsgálja és elemzi az SQL-adatbázisok használati előzményeit. A megadott javaslatok adatbázis-egyedi számítási feladatokon alapulnak, és javítják a teljesítményt.

> [!TIP]
> Az [automatikus hangolás](sql-database-automatic-tuning.md) az ajánlott módszer a leggyakoribb adatbázis-teljesítménnyel kapcsolatos problémák automatikus finomhangolására. [](sql-database-query-performance.md) Az alapszintű Azure SQL Database teljesítmény-figyelési igényeknek megfelelően a lekérdezési teljesítményre vonatkozó információ a javasolt módszer. A [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) ajánlott módszer az adatbázis-teljesítmény magas szinten történő figyelésére, beépített intelligenciával az automatikus teljesítménnyel kapcsolatos hibaelhárításhoz.
>

## <a name="create-index-recommendations"></a>Tárgymutató-javaslatok létrehozása
SQL Database folyamatosan figyeli a futó lekérdezéseket, és azonosítja azokat az indexeket, amelyek javíthatják a teljesítményt. Ha elég biztos abban, hogy egy adott index hiányzik, létrejön egy új **create index** -javaslat.

 Azure SQL Database megbízhatóságot épít az index teljesítményének becslésével. A becsült teljesítménytől függően a javaslatok magas, közepes vagy alacsony értékre vannak kategorizálva. 

A javaslatok használatával létrehozott indexek mindig automatikusan létrehozott indexként vannak megjelölve. Láthatja, hogy mely indexek automatikusan jönnek létre a sys. Indexes nézet megtekintésével. Az automatikusan létrehozott indexek nem gátolják meg a MÓDOSÍTÁSi/ÁTNEVEZÉSi parancsokat. 

Ha úgy próbálja eldobni az oszlopot, hogy az automatikusan létrehozott indextel rendelkezik, a parancs áthalad. Az automatikusan létrehozott index el lesz dobva a paranccsal is. A normál indexek letiltják az ALTER/Átnevezés parancsot az indexelt oszlopokon.

A Create index javaslat alkalmazása után Azure SQL Database összehasonlítja a lekérdezések teljesítményét az alapteljesítménysel. Ha az új index jobb teljesítményt biztosít, a javaslat sikeresként van megjelölve, és a hatás jelentés elérhető. Ha az index nem javítja a teljesítményt, a rendszer automatikusan visszaállította. SQL Database ezzel a folyamattal biztosítja, hogy a javaslatok javítsák az adatbázis teljesítményét.

A **create index** javaslat olyan biztonsági szabályzatot tartalmaz, amely nem engedélyezi a javaslat alkalmazását, ha egy adatbázis vagy készlet erőforrás-használata magas. A back-off szabályzat a CPU-t, az adatio-t, a log IO-t és a rendelkezésre álló tárolót veszi figyelembe. 

Ha a CPU, az adatio vagy a log IO nagyobb, mint 80% az elmúlt 30 percben, a Create index javaslat Elhalasztva lesz. Ha a rendelkezésre álló tárterület az index létrehozása után 10% alá esik, a javaslat hibás állapotba kerül. Ha néhány nap elteltével az automatikus hangolás továbbra is úgy véli, hogy az index hasznos lenne, a folyamat újra elindul. 

Ez a folyamat addig ismétlődik, amíg nincs elég elérhető tárterület egy index létrehozásához, vagy amíg az index nem látszik hasznosnak.

## <a name="drop-index-recommendations"></a>Tárgymutató-javaslatok eldobása
A hiányzó indexek észlelése mellett SQL Database folyamatosan elemzi a meglévő indexek teljesítményét. Ha a rendszer nem használ indexet, Azure SQL Database javasolja a ledobását. Az indexek eldobása két esetben ajánlott:
* Az index egy másik index duplikálása (azonos indexelt és belefoglalt oszlop, partíciós séma és szűrők).
* Az indexet nem használták hosszabb ideig (93 nap).

Az indexek eldobására vonatkozó javaslatok a megvalósítás után is meghaladják az ellenőrzést. Ha a teljesítmény javul, a hatás jelentés elérhető. Ha a teljesítmény csökken, a rendszer visszaállít egy javaslatot.


## <a name="parameterize-queries-recommendations"></a>Parametrizálja-lekérdezések javaslatai
A *parametrizálja-lekérdezések* javaslatai akkor jelennek meg, ha egy vagy több olyan lekérdezést tartalmaz, amely állandóan újrafordításra kerül, de ugyanazzal a lekérdezés-végrehajtási tervvel végződik. Ez a feltétel létrehoz egy lehetőséget a kényszerített paraméterezés alkalmazására. A kényszerített paraméterezés lehetővé teszi a lekérdezési tervek gyorsítótárazását és újbóli felhasználását a jövőben, ami javítja a teljesítményt, és csökkenti az erőforrás-használatot. 

A rendszer minden SQL Server kiállított lekérdezést először le kell fordítani egy végrehajtási terv létrehozásához. Minden létrehozott csomag hozzá lesz adva a terv gyorsítótárába. Ugyanannak a lekérdezésnek a későbbi végrehajtásai újra felhasználhatják ezt a csomagot a gyorsítótárból, így nincs szükség további fordításra. 

A nem paraméteres értékekkel rendelkező lekérdezések teljesítménybeli terheléshez vezethetnek, mert a végrehajtási terv minden alkalommal újrafordításra kerül, amikor a nem paraméteres értékek eltérőek. Sok esetben a különböző paraméterekkel rendelkező lekérdezések ugyanazt a végrehajtási tervet eredményezik. Ezek a csomagok azonban továbbra is külön vannak hozzáadva a terv gyorsítótárába. 

A végrehajtási tervek újrafordításának folyamata adatbázis-erőforrásokat használ, növeli a lekérdezés időtartamának időpontját, és túlcsordul a terv gyorsítótárán. Ezek az események a csomagok gyorsítótárból való kizárását okozzák. Ez a SQL Server viselkedés módosítható úgy, hogy a kényszerített paraméterezés beállítást állítja be az adatbázison. 

A javaslat hatásának becslése érdekében a rendszer összehasonlítja a tényleges CPU-használat és a tervezett CPU-használat közötti összehasonlítást (ahogy a javaslat alkalmazta volna). Ez a javaslat segítséget nyújt a CPU-megtakarítás megszerzésében. Emellett segít csökkenteni a lekérdezési időtartamot és a terhelést a csomag gyorsítótárában, ami azt jelenti, hogy több csomag is maradhat a gyorsítótárban, és újra felhasználható. Ezt az ajánlást gyorsan alkalmazhatja az **Apply** parancs kiválasztásával. 

A javaslat alkalmazása után a kényszerített paraméterezés perceken belül elérhetővé válik az adatbázisban. Elindítja a figyelési folyamatot, amely körülbelül 24 órát tart. Ezen időszak után megtekintheti az ellenőrzési jelentést. Ez a jelentés az adatbázis CPU-használatát jeleníti meg a javaslat alkalmazása előtt és után 24 órával. A SQL Database Advisor olyan biztonsági mechanizmussal rendelkezik, amely automatikusan visszaállít egy alkalmazott javaslatot, ha a rendszer a teljesítmény regresszióját észlelte.

## <a name="fix-schema-issues-recommendations-preview"></a>A séma problémáinak javítása javaslatok (előzetes verzió)

> [!IMPORTANT]
> A Microsoft jelenleg "a séma hibájának javítása" javaslatok elavult. Javasoljuk, hogy az adatbázis teljesítményével kapcsolatos problémák figyeléséhez [Intelligent Insights](sql-database-intelligent-insights.md) használjon, beleértve a sémával kapcsolatos problémákat is, amelyek a korábban lefedett "séma-probléma" megoldásra vonatkoznak.
> 

Ha a SQL Database szolgáltatás az SQL-adatbázisban előforduló, sémával kapcsolatos SQL-hibák számát észleli, akkor megjelenik a **séma-problémák javítása** . Ez az ajánlás általában akkor jelenik meg, ha az adatbázis több sémával kapcsolatos hibát észlel (érvénytelen oszlopnév, érvénytelen objektumnév stb.) egy órán belül.

A "sémával kapcsolatos problémák" a SQL Server szintaktikai hibáinak osztálya. Akkor fordulnak elő, ha az SQL-lekérdezés definíciója és az adatbázis-séma definíciója nincs igazítva. Előfordulhat például, hogy a lekérdezés által várt oszlopok egyike hiányzik a célként megadott táblából, vagy fordítva. 

A "séma javítása" javaslat akkor jelenik meg, ha az Azure SQL Database szolgáltatás anomália az SQL-adatbázison futó, a sémával kapcsolatos SQL-hibák számán. A következő táblázat a séma problémáira vonatkozó hibákat mutatja be:

| SQL-hibakód | Message |
| --- | --- |
| 201 |A (z) "" eljárás vagy függvény a (z) ""*paramétert várja*, amely nem lett megadva. |
| 207 |Érvénytelen oszlopnév: "*". |
| 208 |Érvénytelen objektumnév: "*". |
| 213 |Az oszlop neve vagy a megadott értékek száma nem felel meg a tábla definíciójának. |
| 2812 |A következő tárolt eljárás nem található: "*". |
| 8144 |Az eljárás vagy a függvény túl sok argumentumot adott meg. |

## <a name="custom-applications"></a>Egyéni alkalmazások

A fejlesztők fontolóra vehetik az egyéni alkalmazások fejlesztését a Azure SQL Database teljesítményével kapcsolatos javaslatok használatával. Az adatbázis-portálon felsorolt összes javaslat a [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API-n keresztül érhető el.

## <a name="next-steps"></a>További lépések
Figyelje a javaslatokat, és alkalmazza őket a teljesítmény pontosítására. Az adatbázis-számítási feladatok dinamikusak, és folyamatosan változnak. SQL Database Advisor folytatja az adatbázis teljesítményének növelésére szolgáló javaslatok figyelését és megadását. 

* Az adatbázis-indexek és a lekérdezés-végrehajtási tervek automatikus finomhangolásával kapcsolatos további információkért lásd: [Azure SQL Database automatikus hangolás](sql-database-automatic-tuning.md).
* Az adatbázis teljesítményének automatikus és a teljesítménnyel kapcsolatos problémák kiváltó okának elemzésével kapcsolatos további információkért lásd: [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
*  Ha további információt szeretne arról, hogyan használhatók a teljesítményre vonatkozó javaslatok a Azure Portalban, tekintse meg [a Azure Portal teljesítményével](sql-database-advisor-portal.md)kapcsolatos javaslatokat.
* A leggyakoribb lekérdezések teljesítményre gyakorolt hatásának megismeréséhez tekintse meg a [lekérdezési teljesítmény](sql-database-query-performance.md) elemzését ismertető témakört.


