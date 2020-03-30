---
title: Adatbázis-tanácsadói teljesítményjavaslatok egy- és készletezésű adatbázisokhoz
description: Az Azure SQL Database javaslatokat nyújt az egy- és készletalapú adatbázisokhoz, amelyek javíthatják a lekérdezési teljesítményt az Azure SQL-adatbázisban.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: bd7473813722fd413947535413b98d493058634a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214138"
---
# <a name="database-advisor-performance-recommendations-for-single-and-pooled-databases"></a>Adatbázis-tanácsadó teljesítményre vonatkozó javaslatai egy- és készletezésű adatbázisokhoz

Az Azure SQL Database megtanulja és alkalmazkodik az alkalmazáshoz. Az egy- és készletezésű adatbázisok esetében az SQL Database számos adatbázis-tanácsadóval rendelkezik, amelyek személyre szabott javaslatokat nyújtanak, amelyek lehetővé teszik a teljesítmény maximalizálását. Ezek az adatbázis-tanácsadók folyamatosan értékelik és elemzik a használati előzményeket, és a teljesítmény javítását segítő számítási feladatok mintái alapján ajánlásokat nyújtanak.

## <a name="performance-overview"></a>Teljesítmény – áttekintés

A teljesítmény áttekintése az adatbázis teljesítményének összegzését, valamint a teljesítményhangolást és a hibaelhárítást segíti.

![Az Azure SQL Database teljesítményének áttekintése](./media/sql-database-performance/performance-overview-annotated.png)

- A **Javaslatok** csempe az adatbázis hangolási javaslatainak bontását biztosítja (az első három javaslat jelenik meg, ha több van). Erre a csempére kattintva megkell adni **[a Teljesítményre vonatkozó javaslat beállításait.](sql-database-advisor-portal.md#viewing-recommendations)**
- A **Hangolási tevékenység** csempe összegzést nyújt az adatbázis folyamatban lévő és befejezett hangolási műveletekről, így gyorsan áttekintheti a hangolási tevékenységek előzményeit. Erre a csempére kattintva megnyílik az adatbázis teljes hangolási előzménynézete.
- Az **automatikus hangolás** csempe az adatbázis **[automatikus hangolási konfigurációját](sql-database-automatic-tuning-enable.md)** jeleníti meg (az adatbázisra automatikusan alkalmazott hangolási beállításokat). Erre a csempére kattintva megnyílik az automatizálási konfigurációs párbeszédpanel.
- Az **Adatbázis-lekérdezések** csempe az adatbázis lekérdezési teljesítményének összegzését jeleníti meg (a teljes DTU-használat és a legfelső erőforrás-fogyasztó lekérdezések). Erre a csempére kattintva a **[Lekérdezési teljesítmény betekintése](sql-database-query-performance.md)** lép.

## <a name="performance-recommendation-options"></a>Teljesítményajánlási beállítások

Az Azure SQL Database-ben az egy- és készletalapú adatbázisokhoz elérhető teljesítményajánlási lehetőségek a következők:

| Teljesítményre vonatkozó javaslat | Egyadatbázis- és készletezésű adatbázis-támogatás | Példányadatbázis-támogatás |
| :----------------------------- | ----- | ----- |
| **Indexjavaslatok létrehozása** – Indexek létrehozását javasolja, amelyek javíthatják a számítási feladatok teljesítményét. | Igen | Nem |
| **Indexjavaslatok eldobása** – Javasolja a redundáns és duplikált indexek eltávolítását naponta, kivéve az egyedi indexeket és a hosszú ideig (>90 napig) használt indexeket. Kérjük, vegye figyelembe, hogy ez a beállítás nem kompatibilis a partíciókapcsolást és indextippeket használó alkalmazásokkal. A nem használt indexek eldobása nem támogatott a prémium és az üzleti legkritikusabb szolgáltatási szinteken. | Igen | Nem |
| **A lekérdezések paraméterezése (előzetes verzió)** – Kényszerített paraméterezést javasol olyan esetekben, amikor egy vagy több lekérdezés folyamatosan újrafordítás alatt áll, de ugyanaz a lekérdezés-végrehajtási terv jelenik meg. | Igen | Nem |
| **Sémaproblémák javítása javaslatok (előzetes verzió)** – Sémajavítási javaslatok jelennek meg, amikor az SQL Database szolgáltatás anomáliát észlel az SQL-adatbázisban előforduló sémakapcsolatos SQL-hibák számában. A Microsoft jelenleg elavulttal "Sémaprobléma-javítás" javaslatokat. | Igen | Nem |

![Teljesítményjavaslatok az Azure SQL Database-hez](./media/sql-database-performance/performance-recommendations-annotated.png)

A teljesítményre vonatkozó javaslatok alkalmazásáról az Ajánlások alkalmazása című témakörben [van.](sql-database-advisor-portal.md#applying-recommendations) A javaslatok állapotának megtekintéséhez olvassa el a [Műveletek figyelése című témakört.](sql-database-advisor-portal.md#monitoring-operations)

A korábban alkalmazott hangolási műveletek teljes előzményeit is megtalálhatja.

## <a name="create-index-recommendations"></a>Indexjavaslatok létrehozása

Az SQL Database folyamatosan figyeli a futó lekérdezéseket, és azonosítja azokat az indexeket, amelyek javíthatják a teljesítményt. Miután elegendő megbízhatóságot, hogy egy bizonyos index hiányzik, egy új **index létrehozása** javaslat jön létre.

Az Azure SQL Database bizalmat épít ki az index által az idő által hozott teljesítménynyereség becslésével. A becsült teljesítménynövekedéstől függően a javaslatok magas, közepes vagy alacsony kategóriába vannak sorolva.

A javaslatok használatával létrehozott indexek mindig automatikusan létrehozott indexekként vannak megjelölve. A [sys.indexes nézetben](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql)megtekintheti, hogy mely indexek jönnek létre automatikusan. Az automatikusan létrehozott indexek nem tiltják le az ALTER/RENAME parancsokat.

Ha megpróbálja eldobni azt az oszlopot, amelyen automatikusan létrehozott index van, a parancs átmegy. Az automatikusan létrehozott index a paranccsal is ellesz dobva. A normál indexek blokkolják az ALTER/ÁTNEVEZÉS parancsot az indexelt oszlopokon.

A create index javaslat alkalmazása után az Azure SQL Database összehasonlítja a lekérdezések teljesítményét az alapteljesítmény. Ha az új index javította a teljesítményt, a javaslat sikeresként lesz megjelölve, és a hatásjelentés elérhető. Ha az index nem javítja a teljesítményt, a rendszer automatikusan visszaállítja. Az SQL Database ezt a folyamatot használja annak biztosítására, hogy a javaslatok javítsák az adatbázis teljesítményét.

Minden **létrehozási index** javaslat rendelkezik egy back-off szabályzat, amely nem teszi lehetővé a javaslat alkalmazása, ha az erőforrás-használat egy adatbázis vagy készlet magas. A back-off házirend figyelembe veszi a CPU, data IO, Log IO és a rendelkezésre álló tároló.

Ha a PROCESSZOR, az adatok IO vagy a log IO magasabb, mint 80%, az előző 30 percben, a create index javaslat elhalasztva. Ha a rendelkezésre álló tárterület az index létrehozása után 10% alatt lesz, a javaslat hibaállapotba kerül. Ha néhány nap múlva az automatikus hangolás továbbra is úgy véli, hogy az index előnyös lenne, a folyamat újraindul.

Ez a folyamat addig ismétlődik, amíg nincs elég szabad tárhely egy index létrehozásához, vagy amíg az index nem tekinthető már hasznosnak.

## <a name="drop-index-recommendations"></a>Indexjavaslatok eldobása

A hiányzó indexek észlelése mellett az SQL Database folyamatosan elemzi a meglévő indexek teljesítményét. Ha egy index nem használja, az Azure SQL Database azt javasolja, hogy dobja el. Az index eldobása két esetben ajánlott:

- Az index egy másik index másolata (azonos indexelt és mellékelt oszlop, partícióséma és szűrők).
- Az indexet hosszabb ideig (93 napig) nem használták.

Drop index ajánlások is megy keresztül az ellenőrzés végrehajtása után. Ha a teljesítmény javul, a hatásjelentés elérhetővé válik. Ha a teljesítmény romlik, a javaslat visszaáll.

## <a name="parameterize-queries-recommendations-preview"></a>A lekérdezések paraméterezése javaslatok (előzetes verzió)

*A paraméterek paraméterezése lekérdezésekre* vonatkozó javaslatok akkor jelennek meg, ha egy vagy több lekérdezés folyamatosan újrafordítás alatt áll, de ugyanaza lekérdezés-végrehajtási terv vel végződik. Ez a feltétel lehetőséget teremt a kényszerített paraméterezés alkalmazására. A kényszerített paraméterezés viszont lehetővé teszi a lekérdezési tervek gyorsítótárazását és újrafelhasználását a jövőben, ami javítja a teljesítményt és csökkenti az erőforrás-használatot.

Az SQL Server kiszolgálón kiadott minden lekérdezést először le kell fordítani a végrehajtási terv létrehozásához. Minden egyes létrehozott terv hozzáadódik a terv gyorsítótárához. Ugyanezen lekérdezés későbbi végrehajtásai újra felhasználhatják ezt a tervet a gyorsítótárból, így nincs szükség további fordításra.

A nem paraméterezett értékekkel rendelkező lekérdezések teljesítményterheléshez vezethetnek, mivel a végrehajtási terv minden alkalommal újrafordításra kerül, amikor a nem paraméterezett értékek eltérőek. Sok esetben ugyanazok a lekérdezések különböző paraméterértékekkel ugyanazt a végrehajtási terveket hozlétre. Ezek a tervek azonban továbbra is külön-külön kerülnek a terv gyorsítótárába.

A végrehajtási tervek újrafordítása adatbázis-erőforrásokat használ, növeli a lekérdezés időtartamát, és túlcsordul a terv gyorsítótárában. Ezek az események viszont a tervek et a gyorsítótárból való kizárásra késztetik. Ez az SQL Server-viselkedés az adatbázis kényszerített paraméterezési beállításának beállításával módosítható.

Ennek a javaslatnak a hatásának becsléséhez a rendszer a tényleges CPU-használat és a tervezett CPU-használat összehasonlítását biztosítja (mintha a javaslatot alkalmazták volna). Ez a javaslat segíthet a CPU-megtakarítások megszerzésében. Segíthet a lekérdezés időtartamának és a séma-gyorsítótár terhelésének csökkentésében is, ami azt jelenti, hogy a csomagok közül több is a gyorsítótárban maradhat, és újra felhasználható. Ezt a javaslatot gyorsan alkalmazhatja az **Alkalmaz** paranccsal.

A javaslat alkalmazása után lehetővé teszi a kényszerített paraméterezést perceken belül az adatbázisban. Elindítja a megfigyelési folyamatot, amely körülbelül 24 órán át tart. Ezen időszak után láthatja az érvényesítési jelentést. Ez a jelentés az adatbázis processzorhasználatát mutatja 24 órával a javaslat alkalmazása előtt és után. Az SQL Database Advisor rendelkezik egy biztonsági mechanizmus, amely automatikusan visszaállítja az alkalmazott ajánlást, ha a teljesítmény regresszió észlelt.

## <a name="fix-schema-issues-recommendations-preview"></a>Sémaproblémák javítása javaslatok (előzetes verzió)

> [!IMPORTANT]
> A Microsoft jelenleg elavulttal "Sémaprobléma-javítás" javaslatokat. Azt javasoljuk, hogy az [Intelligens insights](sql-database-intelligent-insights.md) segítségével figyelje az adatbázis teljesítményével kapcsolatos problémákat, beleértve a séma problémákat, amelyek a "Séma probléma megoldása" javaslatok korábban tárgyalt.

**A sémaproblémák javítása** javaslatok akkor jelennek meg, amikor az SQL Database szolgáltatás asémahibát észlel az SQL-adatbázisban előforduló sémakapcsolatos SQL-hibák számában. Ez a javaslat általában akkor jelenik meg, ha az adatbázis egy órán belül több sémával kapcsolatos hibát (érvénytelen oszlopnév, érvénytelen objektumnév és így tovább) észlel.

A "sémaproblémák" az SQL Server szintaktikai hibáinak egy osztálya. Ezek akkor fordulnak elő, ha az SQL-lekérdezés és az adatbázisséma definíciója nincs egymáshoz igazítva. Előfordulhat például, hogy a lekérdezés által várt oszlopok egyike hiányzik a céltáblából, vagy fordítva.

A "Fix séma probléma" javaslat akkor jelenik meg, amikor az Azure SQL Database szolgáltatás észleli a rendellenességek száma sémával kapcsolatos SQL-hibák, amelyek az SQL-adatbázisban történik. Az alábbi táblázat a sémaproblémákkal kapcsolatos hibákat mutatja be:

| SQL-hibakód | Üzenet |
| --- | --- |
| 201 |Eljárás vagy függvény '*' ' paramétert vár '*, amelyet nem adott meg. |
| 207 |Érvénytelen oszlopnév :*". |
| 208 |Érvénytelen objektumnév '*'. |
| 213 |Az oszlop név vagy a megadott értékek száma nem egyezik meg a tábladefinícióval. |
| 2812 |Nem található a tárolt eljárás '*'. |
| 8144 |Az eljárás vagy függvény * túl sok argumentumot adott meg. |

## <a name="custom-applications"></a>Egyéni alkalmazások

A fejlesztők fontolóra vehetik egyéni alkalmazások fejlesztését az Azure SQL Database teljesítményjavaslatainak használatával. Az adatbázis portálján felsorolt összes javaslat elérhető a [Get-AzSqlDatabaseRecommendedAction API-n](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) keresztül.

## <a name="next-steps"></a>További lépések

- Az adatbázis-indexek automatikus hangolásáról és a lekérdezés-végrehajtási tervekről az [Azure SQL Database automatikus hangolása című](sql-database-automatic-tuning.md)témakörben talál további információt.
- Az adatbázisok teljesítményének automatikus diagnosztikával és a teljesítményproblémák kiváltó okainak elemzésével kapcsolatos további tudnivalókért tekintse meg az Azure SQL Intelligent Insights című [témakört.](sql-database-intelligent-insights.md)
- Lásd: [A Query Performance Insights](sql-database-query-performance.md) című témakörben megismerheti és megtekintheti a leggyakoribb lekérdezések teljesítményre gyakorolt hatását.
