---
title: "Teljesítmény ajánlásokkal – az Azure SQL Database |} Microsoft Docs"
description: "Az Azure SQL-adatbázis az SQL-adatbázisok, amelyek az aktuális lekérdezés teljesítményének vonatkozó javaslatokkal szolgál."
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: 
ms.openlocfilehash: b3cd5f2138f4d16a1a1590b025d020410ebcce3c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="performance-recommendations-for-sql-database"></a>SQL-adatbázis teljesítményének javaslatok

Az Azure SQL Database Tanulja meg, és az alkalmazással alkalmazkodik. Testre szabott javaslatok, amelyek lehetővé teszik az SQL-adatbázisok a teljesítmény maximalizálásához biztosít. SQL-adatbázis folyamatosan értékelésére és elemzi az SQL-adatbázisok használati előzményeit. A javaslatok, amelyek adatbázis-egyedi terhelési mintázatok alapulnak, és a teljesítmény növelése érdekében.

> [!TIP]
> [Automatikus hangolása](sql-database-automatic-tuning.md) teljesítményhangolás az ajánlott módszer. [Intelligens Insights](sql-database-intelligent-insights.md) az ajánlott módszer teljesítményének figyelése. 
>

## <a name="create-index-recommendations"></a>Ajánlott index létrehozása
SQL-adatbázis folyamatosan futó lekérdezések figyeli, és azonosítja az indexeket, javíthatja a teljesítményt. Nincs elég abban, hogy, hogy egy bizonyos index hiányzik, miután egy új **Create index** javaslat jön létre.

 Az Azure SQL Database abban, hogy úgy jobb a teljesítménye az index idő keresztül kellene kapcsolása hoz létre. Attól függően, hogy a becsült teljesítménynövekedés elérése érdekében ajánlott kategóriába sorolni magas, közepes vagy alacsony. 

Javaslatok használatával létrehozott indexek mindig, automatikusan létrehozott indexek vannak megjelölve. Láthatja, hogy mely indexek automatikusan létrehozott a sys.indexes nézet megtekintésével. Automatikusan létrehozott indexek ALTER/átnevezése parancsok nincs blokkolás. 

Ha az automatikusan létrehozott index kapcsolattal rendelkező oszlop eldobásához kísérli meg, a parancs továbbítja. Az automatikusan létrehozott index a paranccsal a rendszer eldobja. Szabályos indexei indexelt oszlopokon az ALTER/RENAME parancs letiltása.

A create index javaslat alkalmazásakor az Azure SQL Database alapteljesítményének a lekérdezések teljesítményét hasonlítja össze. Az új index növeli a teljesítményt, ha a javaslat sikeres van megjelölve, és a hatás jelentés érhető el. Ha az index nem a teljesítmény javításához, automatikusan visszaállította. SQL-adatbázis a folyamat használatával győződjön meg arról, hogy javaslatokat adatbázis teljesítményének javítása.

Bármely **index létrehozása** javaslat vissza az indító házirendje van, amely nem engedélyezi a javaslat alkalmazása, ha az erőforrás-felhasználás egy adatbázis vagy a készlet túl magas. A biztonsági ki házirend fiók CPU, az adatok IO, a napló IO és a rendelkezésre álló tár veszi. 

Ha a CPU, adat IO vagy napló IO 80 %-nál nagyobb az előző 30 percben, ajánlott a create index kerül sor. Ha a rendelkezésre álló tár 10 % alatt lesz az index létrehozása után, az ajánlás hibaállapotba kerül. Ha után néhány napon, automatikus hangolása továbbra is úgy véli, hogy az index hasznos lehet, a folyamat ismét elindul. 

Ez a folyamat ismétlődik, amíg nincs elég rendelkezésre álló tár index létrehozása, vagy az index nem tekinthető előnyös többé.

## <a name="drop-index-recommendations"></a>Indexek elvetésére vonatkozó javaslatok
Mellett a hiányzó indexek észlelésére, SQL-adatbázis folyamatosan elemzi a létező indexek teljesítményét. Az index nem használható, ha az Azure SQL Database azt javasolja, hogy eldobása. Az index elvetése két esetekben ajánlott:
* Az index már létezik egy másik index (azonos indexelt és oszlop, a partíciós séma és a szűrők része).
* Az index nem lett használva, hosszan tartó időszakra (93 nap).

Indexek elvetésére vonatkozó javaslatok is halad át az ellenőrzés végrehajtása után. Ha növeli a teljesítményt, a hatás jelentés érhető el. Ha csökken a teljesítmény, akkor a javaslat tért vissza.


## <a name="parameterize-queries-recommendations"></a>Lekérdezések javaslatok parametrizálja
*Lekérdezések parametrizálja* javaslatok jelennek meg, ha egy vagy több vannak folyamatosan alatt újrafordítani de end fel a lekérdezés végrehajtása csomagot a lekérdezések. Ez a feltétel hoz létre a kényszerített (egyszerű) paraméterezéssel alkalmazása lehetőséget. Kényszerített (egyszerű) paraméterezéssel, viszont lehetővé teszi a lekérdezésterveket gyorsítótárazott és a jövőben használni, ami javítja a teljesítményt és erőforrás-használat. 

Minden egyes lekérdezés, amely kezdetben a SQL Server állították ki kell fordítható le egy végrehajtási terv létrehozásához. Minden létrehozott terv a terv gyorsítótárba kerül. Az ugyanabban a lekérdezésben a későbbi végrehajtások során ez a gyorsítótárból, így nem kell további fordításának terv is felhasználhatja. 

Az értékek nem paraméteres lekérdezések teljesítményigény vezethet, mert a végrehajtási terv van újrafordítani minden alkalommal, amikor az nem paraméterezett értékek eltérőek. Sok esetben másik paraméterértékekkel azonos lekérdezések létrehozása a azonos végrehajtási tervek. Ezeket a csomagokat, azonban továbbra is külön-külön kerülnek gyorsítótárban. 

A folyamat végrehajtási tervek újrafordítás adatbázis erőforrást használ, növeli a lekérdezés időtartama, valamint túlcsordulások a gyorsítótárban. Ezeket az eseményeket, vezethet a gyorsítótárból eltávolítani kívánt tervek. Az SQL Server viselkedése úgy, hogy a kényszerített (egyszerű) paraméterezéssel lehetőséget, az adatbázis is módosul. 

Segítsen megbecsülni Ez a javaslat a hatását, hogy átadja a tényleges CPU-használat és a tervezett CPU-használat (mint, ha a javaslat alkalmazott). Ez a javaslat segítséget nyújt a CPU-megtakarítást kapnak. Azt is segíthetnek, csökkentse a lekérdezés időtartama, és terhet gyorsítótárban, ami azt jelenti, hogy a csomagok több gyorsítótárában marad, és használható fel újra. Ez a javaslat gyorsan lehet alkalmazni, kiválasztásával a **alkalmaz** parancsot. 

Ez a javaslat alkalmazása után az adatbázis percen belül kényszerített parameterization lehetővé teszi. A megfigyelési folyamat, amely körülbelül 24 óráig tart kezdődik. Ezt követően megtekintheti az ellenőrzési jelentésből. Ez a jelentés tartalmazza a processzorhasználat százalékos mértéke az adatbázis 24 órát előtt, és a javaslat alkalmazását követően. SQL Database Advisor segédprogramot, amely automatikusan helyreállítja az alkalmazott ajánlás, ha teljesítmény regressziós észlelt biztonsági mechanizmussal rendelkezik.

## <a name="fix-schema-issues-recommendations-preview"></a>Javítsa ki a séma problémák javaslatok (előzetes verzió)

> [!IMPORTANT]
> A Microsoft jelenleg van elavulttá "Javítsa ki a hibát a séma" javaslatok. Azt javasoljuk, hogy használjon [intelligens Insights](sql-database-intelligent-insights.md) figyelése a adatbázis teljesítményproblémákat, így például a séma, "Javítsa ki a hibát a séma" ajánlások korábban vonatkozik.
> 

**Séma kapcsolatos problémák megoldása** javaslatok jelennek meg, ha az SQL Database szolgáltatás észleli az anomáliadetektálási a séma kapcsolatos SQL hibák száma, hogy az SQL-adatbázis. Ez a javaslat jellemzően akkor jelenik meg, amikor az adatbázis több séma kapcsolatos hibákat (Érvénytelen oszlopnév, érvénytelen objektum nevét, és így tovább) észlel egy órán belül.

"Séma problémák" szintaktikai hibák az SQL Server osztály. Ezek fordulhat elő, amikor az SQL-lekérdezés a definíció- és az adatbázis-séma definíciója nem igazított. Például az oszlopok, a lekérdezés által várt lehetnek hiányzik a céloldali tábla, vagy fordítva. 

A "Javítsa ki a hibát a séma" javaslat jelenik meg, ha az Azure SQL Database szolgáltatás észleli az anomáliadetektálási a séma kapcsolatos SQL hibák száma, hogy az SQL-adatbázishoz. Az alábbi táblázat a hibákat, amelyek kapcsolódnak séma problémákat:

| SQL-hibakód | Üzenet |
| --- | --- |
| 201 |Az eljárás vagy függvény "*"paramétert vár"*", amely nem lett megadva. |
| 207 |Érvénytelen oszlopnév "*". |
| 208 |Érvénytelen objektum neve "*". |
| 213 |Oszlop neve vagy a megadott érték nem egyezik a tábla definíciójában. |
| 2812 |Nem található a tárolt eljárás ' *'. |
| 8144 |Az eljárás vagy függvény * megadott túl sok argumentum tartozik. |

## <a name="next-steps"></a>További lépések
A javaslatok figyelése, és továbbra is alkalmazandó, pontosítsa a teljesítményt. Adatbázis-terhelések dinamikusak, és folyamatosan módosítása. SQL Database Advisor továbbra is fennáll, figyeléséhez, és adja meg a javaslatok, javíthatja az adatbázis teljesítménye. 

* Adatbázis indexek és a lekérdezés végrehajtási tervek automatikus hangolásával kapcsolatos további információkért lásd: [Azure SQL Database automatikus hangolása](sql-database-automatic-tuning.md).
* Automatikusan az automatikus diagnosztikát és a teljesítménnyel kapcsolatos problémák kiváltó okának elemzése adatbázis teljesítményének figyelésével kapcsolatos további információkért lásd: [Azure SQL intelligens Insights](sql-database-intelligent-insights.md).
*  Teljesítmény javaslatok az Azure-portálon való használatával kapcsolatos további információkért lásd: [teljesítmény javaslatok az Azure portálon](sql-database-advisor-portal.md).
* Lásd: [lekérdezési teljesítmény Insights](sql-database-query-performance.md) és a teljesítményre gyakorolt hatás a leggyakoribb lekérdezések megtekintéséhez.


