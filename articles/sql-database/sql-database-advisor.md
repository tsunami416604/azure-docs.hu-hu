---
title: Teljesítménnyel kapcsolatos javaslatok – Azure SQL Database |} A Microsoft Docs
description: Az Azure SQL Database az SQL-adatbázisok, amely javítja a jelenlegi lekérdezési teljesítményt vonatkozó javaslatokkal szolgál.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: e1e6d22face70980370a5b6a19845fe0d39ed821
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863448"
---
# <a name="performance-recommendations-for-sql-database"></a>Az SQL Database teljesítménnyel kapcsolatos javaslatok

Az Azure SQL Database együtt tanuló és formálódó alkalmazását. Személyre szabott javaslatok, amelyek lehetővé teszik, hogy az SQL Database-adatbázisok a teljesítmény maximalizálásához biztosít. Az SQL Database folyamatosan értékeli, és elemzi az SQL-adatbázisok használati előzményeit. A javaslatok adatbázis egyedi munkaterhelési mintákat alapul, és a teljesítmény javítása érdekében.

> [!TIP]
> [Az automatikus hangolás](sql-database-automatic-tuning.md) az ajánlott módszer a teljesítmény finomhangolásának. [Intelligent Insights](sql-database-intelligent-insights.md) az ajánlott módszer a teljesítmény figyelése. 
>

## <a name="create-index-recommendations"></a>Ajánlott indexek létrehozása
SQL Database folyamatosan figyeli a futó lekérdezéseket, és azonosítja a teljesítmény javítása sikerült indexeket. Elég, hogy egy bizonyos index hiányzik, megbízhatósági után egy új **Create index** hozza létre a javaslatot.

 Az Azure SQL Database magabiztosan a teljesítmény nyereség az index idő függvényében szeretné használata becslés alapján hoz létre. A becsült teljesítménynövekedés elérése érdekében függően ajánlások szerint vannak kategóriába sorolva magas, közepes vagy alacsony. 

Javaslatok használatával létrehozott indexek mindig automatikusan létrehozott indexeket, vannak megjelölve. Láthatja, hogy melyik indexek is automatikusan létrehozott megnézzük a sys.indexes nézetet. Automatikusan létrehozott indexek nem blokkolja a ALTER/átnevezése parancsokat. 

Ha az oszlop, amely rendelkezik egy automatikusan létrehozott index visszakapom a névjegyeimet, továbbítja a parancsot. Az automatikusan létrehozott index, valamint a parancs el lett vetve. Szabályos indexei az indexelt oszlopokon ALTER/átnevezése parancs letiltása.

Miután a létrehozás indexjavaslatot alkalmazott, az Azure SQL Database teljesítménnyel és a lekérdezések teljesítményének hasonlítja össze. Az új indexre jobb teljesítmény, ha a javaslat meg van jelölve, a sikeres és a hatás jelentés érhető el. Ha az index nem a teljesítmény javítása érdekében azt automatikusan visszaáll. Annak érdekében, hogy a javaslatok adatbázis-teljesítmény javítása az SQL Database adatbázisaihoz ezt a folyamatot.

Bármely **index létrehozása** javaslat rendelkezik egy visszatartási szabályzatot, amely nem engedélyezi az ajánlás alkalmazásának, ha az erőforrás-használatát egy adatbázis vagy készlet túl magas. A visszatartási szabályzat fiók CPU, adat IO, naplózási IO és rendelkezésre álló tár figyelembe veszi. 

Ha a CPU, adat IO és naplózási IO 80 %-nál nagyobb az előző 30 perc, a létrehozás indexjavaslatot elhalasztva. Ha a rendelkezésre álló tár 10 % alatt lesz az index létrehozása után, az ajánlás hibaállapotban hiányzóra. Ha után néhány nap alatt, az automatikus hangolás továbbra is úgy véli, hogy az index hasznos lenne, a folyamat ismét elindul. 

Ez a folyamat addig, amíg nincs elég rendelkezésre álló tár index létrehozása, vagy amíg az index nem látható, előnyös többé ismétlődik.

## <a name="drop-index-recommendations"></a>DROP index javaslatok
Amellett, hogy hiányzó indexek észlelése, SQL Database folyamatosan elemzi a teljesítmény a létező indexek. Az index nem használatos, ha az Azure SQL Database azt javasolja, eldobja. Két esetben javasolt index elvetését:
* Az index egy másik index (azonos indexelt és tartalmazza a oszlop, a partíció séma és a szűrők) többször szerepel.
* Az index egy hosszan tartó időszakra (93 nap) használták utoljára.

Az ellenőrzés végrehajtása után is használhatunk javaslatok index dobható el. Ha növeli a teljesítményt, a hatás jelentés érhető el. Ha azért rontja a teljesítményt, az ajánlás vissza lett vonva.


## <a name="parameterize-queries-recommendations"></a>Javaslatok lekérdezések paraméterezése
*Lekérdezések paraméterezése* jelennek meg javaslatok, ha egy vagy több olyan lekérdezést, amely a rendszer folyamatosan folyamatban újrafordítani azonban teljes fel az ugyanazon lekérdezés végrehajtási csomaggal rendelkezik. Ez a feltétel lehetőséget a alkalmazni kényszerített paraméterezés hoz létre. Kényszerített paraméterezés, lehetővé teszi a gyorsítótárazott és a jövőben újra felhasználhatók a lekérdezésterveket, amely javítja a teljesítményt, és csökkenti az erőforrás-használat. 

Minden egyes lekérdezés, amely kezdetben a vonatkozóan az SQL Server kiadott kell fordítani, egy végrehajtási terv létrehozásához. A tervek gyorsítótárának hozzáadódik minden egyes létrehozott csomagot. Az azt követő végrehajtások az ugyanabból a lekérdezés a csomagot a gyorsítótárból, így nem kell további fordításhoz felhasználhatja. 

Értékek nem paraméterezett lekérdezések teljesítménybeli terhelést okoz vezethet, mert a végrehajtási terv van újrafordítani minden alkalommal, amikor a nem paraméterezett érték nem egyezik. Sok esetben az azonos lekérdezések exportálásánál különböző paraméterértékekkel létrehozása ugyanazon végrehajtási tervét. Az alábbi díjcsomagok azonban továbbra is külön-külön kerülnek a tervek gyorsítótárában. 

Végrehajtási tervét újrafordítás folyamata használ az adatbázis-erőforrások, növeli a lekérdezés időtartama és a tervek gyorsítótárának túlcsordult. Ezeket az eseményeket, a gyorsítótárból kimaradásához tervek miatt. Ez a viselkedés az SQL Server is módosítható a kényszerített paraméterezés lehetőséget, az adatbázis beállításával. 

Ez a javaslat hatásának megbecsüléséhez segítségével biztosítunk, a tényleges CPU-használat és a tervezett CPU-használat összehasonlítását (mint, ha a javaslat alkalmazása is). Ez a javaslat segítséget kaphat a CPU-megtakarítás. Is segít, csökkentse a lekérdezés időtartama, és terhelés a tervek gyorsítótárában, ami azt jelenti, a csomagok több maradhat a gyorsítótárban, és újra fel kell használni. A javaslat gyorsan lehet alkalmazni, kiválasztásával a **alkalmaz** parancsot. 

Ez a javaslat alkalmazása, után lehetővé teszi a kényszerített paraméterezést az adatbázison történő percen belül. Elindítja a monitorozási folyamat, amely körülbelül 24 óráig tart. Ezt követően megjelenik az ellenőrzési jelentésből. Ez a jelentés tartalmazza a CPU-használat az adatbázis megelőző 24 órán belül, és a javaslat alkalmazása utáni. Az SQL Database Advisor, amely automatikusan visszaáll az alkalmazott ajánlás, ha teljesítmény regressziós észlelt biztonsági mechanizmussal rendelkezik.

## <a name="fix-schema-issues-recommendations-preview"></a>Javítsa ki a sémát problémák javaslatok (előzetes verzió)

> [!IMPORTANT]
> A Microsoft jelenleg az kivezetése "Séma probléma megoldásához" javaslatok. Javasoljuk, hogy használjon [Intelligent Insights](sql-database-intelligent-insights.md) a adatbázis teljesítményproblémákat, többek között, a "Schema probléma megoldásához" javaslatok korábban jelez séma problémáinak figyelésére.
> 

**Séma problémáinak javítása** jelennek meg javaslatok, amikor az SQL Database szolgáltatás megjegyzések anomáliát séma kapcsolatos SQL hibák száma, amelyek az SQL-adatbázisban történik. Ez a javaslat általában jelenik meg, ha az adatbázis egy órán belül több séma kapcsolatos hibák (Érvénytelen oszlopnév érvénytelen objektumnév és így tovább) fordul elő.

"Kapcsolatos hibák elhárítása" olyan szintaktikai hibák az SQL Server egy osztályt. Ha az SQL-lekérdezés definícióját, és az adatbázis-séma meghatározása nem igazított bekövetkezésük. Például, hogy a lekérdezés által várhatóan az oszlopok egyikének előfordulhat, hogy nem érhető el a céloldali tábla, vagy fordítva. 

A "Schema probléma megoldásához" javaslat jelenik meg, ha az Azure SQL Database szolgáltatás megjegyzések anomáliát séma kapcsolatos SQL hibák száma, amelyek az SQL-adatbázisban történik. Az alábbi táblázat a séma problémáinak kapcsolódó hibákat:

| SQL-hibakód | Üzenet |
| --- | --- |
| 201 |Eljárás vagy függvény "*"paramétert vár:*", amely nem lett megadva. |
| 207 |Érvénytelen oszlopnév "*". |
| 208 |Érvénytelen objektum neve "*". |
| 213 |Oszlop neve vagy a megadott értékek száma nem egyezik tábla definíciójában. |
| 2812 |Nem található a tárolt eljárás "*". |
| 8144 |Az eljárás vagy függvény * a megadott túl sok argumentum van. |

## <a name="next-steps"></a>További lépések
Figyelheti a javaslatokat, és továbbra is alkalmazhatja őket, amellyel pontosíthatja a teljesítményt. Adatbázis-munkaterhelés dinamikusak, és folyamatosan változik. Az SQL Database Advisor továbbra is megfigyelheti és javíthatja az adatbázis teljesítményét javaslatokkal. 

* Adatbázis-indexek és lekérdezések végrehajtási tervét az automatikus hangolás kapcsolatos további információkért lásd: [Azure SQL Database automatikus finomhangolása](sql-database-automatic-tuning.md).
* Automatikusan az automatikus diagnosztikát és a kiváltó okok elemzése a teljesítményproblémák adatbázis teljesítményének figyelésével kapcsolatos további információkért lásd: [Azure SQL-Intelligent Insights](sql-database-intelligent-insights.md).
*  Teljesítménnyel kapcsolatos javaslatok az Azure Portalon való használatával kapcsolatos további információkért lásd: [teljesítménnyel kapcsolatos javaslatok az Azure Portalon](sql-database-advisor-portal.md).
* Lásd: [lekérdezési teljesítmény Elemzéseihez](sql-database-query-performance.md) ismerje meg, és a teljesítményre gyakorolt hatás, a lekérdezések megtekintéséhez.


