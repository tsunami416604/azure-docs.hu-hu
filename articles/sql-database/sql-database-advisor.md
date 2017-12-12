---
title: "Teljesítmény ajánlásokkal – az Azure SQL Database |} Microsoft Docs"
description: "Az Azure SQL-adatbázis az SQL-adatbázisok, amelyek az aktuális lekérdezés teljesítményének vonatkozó javaslatokkal szolgál."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: sstein
ms.openlocfilehash: 84706837aeb416d13dab617f51a33d62a934c016
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="performance-recommendations"></a>Teljesítmény javaslatok

Az Azure SQL Database Tanulja meg, és alkalmazkodik az alkalmazással, és ajánlásokat testreszabott így lehetővé teszi az SQL-adatbázisok a teljesítmény maximalizálásához. A teljesítmény folyamatosan megfelelőségét ellenőrizni kell az SQL-adatbázis használati előzmények elemzésével. A javaslatok, amelyek egy adatbázis egyedi munkaterhelés mintát alapulnak, és a teljesítmény növelése érdekében.

> [!TIP]
> [Automatikus hangolása](sql-database-automatic-tuning.md) teljesítményhangolás ajánlott módja van. [Intelligens Insights](sql-database-intelligent-insights.md) az ajánlott módszer a teljesítmény figyelése. 
>

## <a name="create-index-recommendations"></a>Ajánlott index létrehozása
Az Azure SQL-adatbázis folyamatosan figyeli a lekérdezések végrehajtása zajlik, és azonosítja az indexeket, javíthatja a teljesítményt. Miután abban, hogy elegendő épül, amely egy bizonyos index hiányoznak, egy új **Create index** javaslat jön létre. Az Azure SQL Database abban, hogy az index idő keresztül kellene kapcsolása jobb teljesítménye becslés alapján hoz létre. Attól függően, hogy a becsült teljesítménynövekedés elérése érdekében ajánlott kategóriába sorolni magas, közepes vagy alacsony. 

Javaslatok használatával létrehozott indexek mindig auto_created indexek szerint vannak megjelölve. Láthatja, hogy mely indexek auto_created sys.indexes nézet megtekintésével. Automatikusan létrehozott indexek ALTER/átnevezése parancsok nincs blokkolás. Ha, amely egy automatikusan létrehozott index kapcsolattal rendelkezik az oszlop eldobásához, a parancs továbbítja, és az index létrehozása automatikus az paranccsal megszakad. Az ALTER/RENAME parancs indexelt oszlopokon megakadályozza a szabályos indexei.

A create index javaslat van érvényben, miután az Azure SQL Database összehasonlítja alapteljesítményének a lekérdezések teljesítményét. Ha új index fejlesztései a teljesítmény, javaslat lesz megjelölve, sikeres, és hatás jelentés lesz elérhető. Abban az esetben, ha az index nem kapcsolja a következő előnyöket, akkor a rendszer automatikusan visszaállítja. Így az Azure SQL Database biztosítja, hogy javaslatokat használatával csak javítja az adatbázis teljesítménye.

Bármely **Create index** javaslat rendelkezik egy biztonsági házirendet, amely nem engedélyezi a javaslat alkalmazása, ha az elmúlt 20 perc, vagy ha a tárhely 90 %-a használati 80 % felett volt az adatbázis vagy a készlet DTU-használatát ki. Ebben az esetben a javaslat lesz elhalasztva.

## <a name="drop-index-recommendations"></a>Indexek elvetésére vonatkozó javaslatok
Mellett egy hiányzó index észlelésére, az Azure SQL Database folyamatosan elemzi a létező indexek teljesítményét. Ha az index nem használható, az Azure SQL Database azt javasolja, eldobása. Az index elvetése két esetekben ajánlott:
* Index megegyezik egy másik index (azonos indexelt és oszlop, a partíciós séma és a szűrők része)
* Az index az nem használt hosszabb időre (93 nap)

Indexek elvetésére vonatkozó javaslatok is halad át az ellenőrzés végrehajtása után. A teljesítmény akkor javul, ha a hatás jelentés lesz elérhető. Abban az esetben, ha teljesítménycsökkenést észlel, a javaslat vissza lesznek vonva.


## <a name="parameterize-queries-recommendations"></a>Lekérdezések javaslatok parametrizálja
**Lekérdezések parametrizálja** javaslatok jelennek meg, ha egy vagy több vannak folyamatosan alatt újrafordítani de end fel a lekérdezés végrehajtása csomagot a lekérdezések. Ez a feltétel megnyílik egy kényszerített (egyszerű) paraméterezéssel, amely lehetővé teszi a gyorsítótárazott és újra felhasználhatók a jövőbeni a teljesítmény fokozása és alacsonyabb erőforrás-használatot lekérdezésterveket alkalmazandó lehetőséget. 

Minden egyes lekérdezés kezdetben az SQL Server állították ki kell fordítható le egy végrehajtási terv létrehozásához. Minden létrehozott terv a terv gyorsítótárba kerül, és az ugyanabban a lekérdezésben a későbbi végrehajtások során ez a gyorsítótárból, így nincs szükség további fordításának terv is felhasználhatja. 

Alkalmazásokat, amelyek a lekérdezéseket, többek között nem paraméterezett értékek, küldenek vezethet a teljesítményigény, ahol minden egyes ilyen lekérdezés különböző paraméterértékekkel a végrehajtási terv fordítását, akkor újra. Sok esetben másik paraméterrel azonos lekérdezések értékek a azonos végrehajtási tervek létrehozása, de ezek a csomagok továbbra is külön-külön hozzáadódnak a gyorsítótárban. Újrafordítás végrehajtási tervek adatbázis erőforrásainak használatához, növelje a lekérdezési időtartama, és a gyorsítótárból eltávolítani kívánt tervek okozó gyorsítótárban túlcsordulás. Ez a viselkedés az SQL Server is módosítható úgy, hogy az adatbázis a kényszerített (egyszerű) paraméterezéssel lehetőséget. 

Segítsen megbecsülni Ez a javaslat a hatását, hogy átadja a tényleges CPU-használat és a tervezett CPU-használat (mint, ha a javaslat alkalmazott). Mellett CPU kevésbé kell kihasználni a lekérdezés időtartama csökkenti az az idő a fordítás. Is lesz sokkal kevesebb terhet terv gyorsítótár, lehetővé téve a tervek többsége a gyorsítótárban maradnak, és használható fel újra. Is alkalmazhat, ez a javaslat gyorsan és egyszerűen kattintson a **alkalmaz** parancsot. 

Ha ez a javaslat alkalmaz, lehetővé teszi az kényszerített (egyszerű) paraméterezéssel az adatbázis percen belül, és elindítja a megfigyelési folyamat, amely körülbelül 24 óráig tart. Ezt követően lesz a 24 órát előtt és a javaslat alkalmazását követően a CPU-használat az adatbázis megjelenítő ellenőrzési jelentés megtekintéséhez. SQL Database Advisor segédprogramot, amely automatikusan helyreállítja az alkalmazott ajánlás, abban az esetben, ha a teljesítmény regressziós észlelt biztonsági mechanizmussal rendelkezik.

## <a name="fix-schema-issues-recommendations-preview"></a>Javítsa ki a séma problémák javaslatok (előzetes verzió)

> [!IMPORTANT]
> Microsoft "Javítsa ki a hibát a séma" javaslatok elavulttá folyamatban van. El kell kezdenie használatával [intelligens Insights](sql-database-intelligent-insights.md) az adatbázis-teljesítménnyel kapcsolatos problémák automatikus figyelésére, amelyek magukban foglalják séma problémák korábban "Javítsa ki a hibát a séma" javaslatok jelez.
> 

**Séma kapcsolatos problémák megoldása** javaslatok jelennek meg, ha az SQL Database szolgáltatás észleli az anomáliadetektálási séma kapcsolatos SQL hiba történt az az Azure SQL Database számát. Ez a javaslat jellemzően akkor jelenik meg, amikor a adatbázisnál több séma kapcsolatos hibákat (Érvénytelen oszlopnév, érvénytelen objektumnév stb.) egy órán belül.

"Séma problémák" szintaktikai hibák az SQL Server fordulhat elő, ha az SQL-lekérdezés a definíció- és az adatbázis-séma definíciója nem illeszkednek osztály. Például egy, a lekérdezés által várt oszlopok esetleg hiányzik a céloldali tábla, vagy fordítva. 

"Javítsa ki a hibát a séma" javaslat jelenik meg, ha az Azure SQL Database szolgáltatás észleli az anomáliadetektálási séma kapcsolatos SQL hiba történt az az Azure SQL Database számának. Az alábbi táblázat a hibákat, amelyek kapcsolódnak séma problémákat:

| SQL-hibakód: | Üzenet |
| --- | --- |
| 201 |Az eljárás vagy függvény "*"paramétert vár"*", amely nem lett megadva. |
| 207 |Érvénytelen oszlopnév "*". |
| 208 |Érvénytelen objektum neve "*". |
| 213 |Oszlop neve vagy a megadott érték nem egyezik a tábla definíciójában. |
| 2812 |Nem található a tárolt eljárás ' *'. |
| 8144 |Az eljárás vagy függvény * megadott túl sok argumentum tartozik. |

## <a name="next-steps"></a>Következő lépések
A javaslatok figyelése, és továbbra is alkalmazandó, pontosítsa a teljesítményt. Adatbázis-terhelések dinamikusak, és folyamatosan módosítása. SQL Database advisor továbbra is fennáll, figyeléséhez, és adja meg a javaslatok, javíthatja az adatbázis teljesítménye. 

* Lásd: [Azure SQL Database automatikus hangolása](sql-database-automatic-tuning.md) az adatbázis-indexek és a lekérdezés végrehajtási tervek automatikus hangolása.
* Lásd: [Azure SQL intelligens Insights](sql-database-intelligent-insights.md) az automatikus diagnosztikát és a legfelső szintű automatikusan adatbázis teljesítményfigyeléshez okok a teljesítményproblémák.
* Lásd: [teljesítmény javaslatok az Azure portálon](sql-database-advisor-portal.md) teljesítmény javaslatok az Azure portálon használandó lépéseit.
* Lásd: [lekérdezési teljesítmény Insights](sql-database-query-performance.md) és a teljesítményre gyakorolt hatás a leggyakoribb lekérdezések megtekintéséhez.


