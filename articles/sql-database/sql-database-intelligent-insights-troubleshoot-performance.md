---
title: Teljesítménnyel kapcsolatos problémák elhárítása Intelligent Insights
description: A Intelligent Insights segítségével elháríthatja Azure SQL Database teljesítménnyel kapcsolatos problémákat.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: b6ec1952d730b6515032572def65806a1ccbc0b2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810368"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>A Intelligent Insights teljesítményével kapcsolatos hibák elhárítása Azure SQL Database

Ez a lap a [Intelligent Insights](sql-database-intelligent-insights.md) adatbázis-teljesítmény diagnosztikai naplójában észlelt Azure SQL Database és felügyelt példányok teljesítményével kapcsolatos problémákat ismerteti. A diagnosztikai napló telemetria továbbítható [Azure monitor naplókba](../azure-monitor/insights/azure-sql.md), az [Azure Event Hubsba](../azure-monitor/platform/resource-logs-stream-event-hubs.md), az [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage)-ba vagy egy harmadik féltől származó megoldásra az egyéni DevOps riasztási és jelentéskészítési képességeinek kihasználásához.

> [!NOTE]
> A Intelligent Insights használatával kapcsolatos gyors SQL Database teljesítmény-hibaelhárítási útmutatóért tekintse meg a jelen dokumentum [javasolt hibaelhárítási folyamat](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) folyamatábráját.
>

## <a name="detectable-database-performance-patterns"></a>Észlelhető adatbázis-teljesítménnyel kapcsolatos minták

A Intelligent Insights automatikusan észleli a teljesítménnyel kapcsolatos problémákat a SQL Database és a felügyelt példányok adatbázisain a lekérdezés végrehajtásának várakozási ideje, hibái vagy időtúllépése alapján. Ez a kimenet a diagnosztikai naplóba felderített teljesítménnyel kapcsolatos mintákat észlelt. A észlelhető teljesítményi mintákat az alábbi táblázat foglalja össze.

| Észlelhető teljesítményi minták | Azure SQL Database és rugalmas készletek leírása | A felügyelt példányban lévő adatbázisok leírása |
| :------------------- | ------------------- | ------------------- |
| [Erőforrás-korlátok elérése](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | A megfigyelt előfizetésben elérhető erőforrások (DTU), adatbázis-feldolgozó szálak vagy adatbázis-bejelentkezési munkamenetek felhasználása elérte a határértékeket. Ez hatással van a SQL Database teljesítményére. | A CPU-erőforrások felhasználása a felügyelt példányok korlátainak elérése. Ez hatással van az adatbázis teljesítményére. |
| [Munkaterhelés növekedése](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | A rendszer észlelte a munkaterhelés növelését vagy a számítási feladatok folyamatos felhalmozódását az adatbázisban. Ez hatással van a SQL Database teljesítményére. | A rendszer a munkaterhelés növelését észlelte. Ez hatással van az adatbázis teljesítményére. |
| [Memória nyomása](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | A memória-támogatást kérő feldolgozóknak meg kell várniuk a memória kiosztását a statisztikailag jelentős mennyiségű időszakra. Vagy a rendelkezésre álló memória-támogatást igénylő feldolgozók megnövekedett felhalmozódása. Ez hatással van a SQL Database teljesítményére. | A rendelkezésre álló memória-támogatást igénylő munkatársak statisztikaian jelentős mennyiségű időt várnak a memória kiosztására. Ez hatással van az adatbázis teljesítményére. |
| [Zárolási](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | A rendszer túl sok adatbázis-zárolást észlelt a SQL Database teljesítményének befolyásolása érdekében. | A rendszer túlzott adatbázis-zárolást észlelt, ami hatással volt az adatbázis teljesítményére. |
| [Megnövekedett MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | A maximális párhuzamossági lehetőség (MAXDOP) módosult a lekérdezés végrehajtásának hatékonyságát érintően. Ez hatással van a SQL Database teljesítményére. | A maximális párhuzamossági lehetőség (MAXDOP) módosult a lekérdezés végrehajtásának hatékonyságát érintően. Ez hatással van az adatbázis teljesítményére. |
| [Pagelatch-tartalom](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Több szál párhuzamosan próbálkozik ugyanahhoz a memóriában tárolt adatpuffer-lapok eléréséhez, ami megnöveli a várakozási időt, és pagelatch-tartalmat okoz. Ez hatással van az SQL-adatbázis teljesítményére. | Több szál párhuzamosan próbálkozik ugyanahhoz a memóriában tárolt adatpuffer-lapok eléréséhez, ami megnöveli a várakozási időt, és pagelatch-tartalmat okoz. Ez hatással van az adatbázis teljesítményére. |
| [Hiányzó index](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | A rendszer hiányzó indexet észlelt az SQL Database teljesítményének befolyásolására. | A rendszer hiányzó indexet észlelt, ami hatással volt az adatbázis teljesítményére. |
| [Új lekérdezés](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | A rendszer a teljes SQL Database teljesítményt érintő új lekérdezést észlelt. | A rendszer új lekérdezést észlelt, ami hatással volt az adatbázis általános teljesítményére. |
| [Megnövekedett várakozási statisztika](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | A rendszer megnövelte az adatbázis-várakozási időt, ami hatással volt az SQL Database teljesítményére. | A megnövelt adatbázis-várakozási idő észlelhető az adatbázis teljesítményére. |
| [TempDB-tartalom](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Több szál megpróbál hozzáférni ugyanahhoz a TempDB-erőforráshoz, ami szűk keresztmetszetet okoz. Ez hatással van a SQL Database teljesítményére. | Több szál megpróbál hozzáférni ugyanahhoz a TempDB-erőforráshoz, ami szűk keresztmetszetet okoz. Ez hatással van az adatbázis teljesítményére. |
| [A rugalmas készlet DTU hiánya](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | A rugalmas készletben rendelkezésre álló Edtu hiánya SQL Database teljesítményt érint. | A felügyelt példányhoz nem érhető el, mert a virtuális mag modellt használja. |
| [Regressziós terv](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Az új terv vagy egy meglévő csomag munkaterhelésének változása észlelhető. Ez hatással van a SQL Database teljesítményére. | Az új terv vagy egy meglévő csomag munkaterhelésének változása észlelhető. Ez hatással van az adatbázis teljesítményére. |
| [Adatbázis hatókörű konfigurációs értékének változása](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | A rendszer a SQL Database konfigurációs változását észlelte az adatbázis teljesítményére. | A rendszer észlelte az adatbázis konfigurációs változását, ami hatással volt az adatbázis teljesítményére. |
| [Lassú ügyfél](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | A lassú alkalmazás-ügyfél nem tudja elég gyorsan felhasználni az adatbázis kimenetét. Ez hatással van a SQL Database teljesítményére. | A lassú alkalmazás-ügyfél nem tudja elég gyorsan felhasználni az adatbázis kimenetét. Ez hatással van az adatbázis teljesítményére. |
| [Díjszabási szintek visszalépése](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | A díjszabási szintek visszalépési művelete csökkent a rendelkezésre álló erőforrásokat. Ez hatással van a SQL Database teljesítményére. | A díjszabási szintek visszalépési művelete csökkent a rendelkezésre álló erőforrásokat. Ez hatással van az adatbázis teljesítményére. |

> [!TIP]
> SQL Database folyamatos teljesítményének optimalizálása érdekében engedélyezze [Azure SQL Database automatikus finomhangolását](sql-database-automatic-tuning.md). A SQL Database beépített intelligencia egyedi funkciója folyamatosan figyeli az SQL-adatbázist, automatikusan összehangolja az indexeket, és alkalmazza a lekérdezés-végrehajtási terv helyesbítéseit.
>

A következő szakasz a észlelhető teljesítményi mintákat ismerteti részletesebben.

## <a name="reaching-resource-limits"></a>Erőforrás-korlátok elérése

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményi minta ötvözi a rendelkezésre álló erőforrás-korlátok, a munkavégző korlátok és a munkamenet-korlátok eléréséhez kapcsolódó teljesítménnyel kapcsolatos problémákat. A probléma észlelése után a diagnosztikai napló leírás mezőjében látható, hogy a teljesítménnyel kapcsolatos probléma erőforrás-, feldolgozó vagy munkamenet-korlátokhoz kapcsolódik-e.

A SQL Database erőforrásait általában [DTU](sql-database-what-is-a-dtu.md) vagy [virtuális mag](sql-database-service-tiers-vcore.md) -erőforrásoknak nevezzük. A rendszer felismeri az erőforrás-korlátok elérésének mintáját, ha a rendszer az észlelt lekérdezési teljesítmény romlását okozza a mért erőforrás-korlátok bármelyikének elérésekor.

A munkamenet-korlátok erőforrás az SQL Database-ben elérhető egyidejű bejelentkezések számát jelöli. Ezt a teljesítménytesztet akkor ismeri fel a rendszer, ha az SQL-adatbázisokhoz csatlakozó alkalmazások megkaptak a rendelkezésre álló párhuzamos bejelentkezések számát az adatbázisba. Ha az alkalmazások az adatbázison elérhetőnél több munkamenetet próbálnak használni, a rendszer a lekérdezési teljesítményt érinti.

A feldolgozói korlátok elérése egy konkrét eset az erőforrás-korlátok elérésekor, mivel a rendelkezésre álló munkavégzők nem számítanak bele a DTU vagy a virtuális mag-felhasználásba. Az adatbázis-feldolgozói korlátok elérése az erőforrás-specifikus várakozási idő emelkedését okozhatja, ami a lekérdezés teljesítményének romlását eredményezi.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimenete lekérdezési kivonatokat jelenít meg, amelyek hatással voltak a teljesítményre és az erőforrás-felhasználási arányra. Ezt az információt kiindulási pontként használhatja az adatbázis-számítási feladatok optimalizálásához. Az indexek hozzáadásával különösen optimalizálhatja a teljesítmény romlását befolyásoló lekérdezéseket. Vagy optimalizálhatja az alkalmazásokat még a számítási feladatok eloszlásával is. Ha nem tudja csökkenteni a munkaterheléseket, vagy optimalizálásokat hajt végre, érdemes lehet növelni az SQL Database-előfizetés díjszabási szintjét, hogy növelje az elérhető erőforrások mennyiségét.

Ha elérte az elérhető munkamenet-korlátokat, az alkalmazások optimalizálásához csökkentse az adatbázisba való bejelentkezések számát. Ha nem tudja csökkenteni az alkalmazásaiból az adatbázisba való bejelentkezések számát, érdemes lehet növelni az adatbázis díjszabási szintjét. Az adatbázist több adatbázisra is feloszthatja és áthelyezheti kiegyensúlyozottabb számítási feladatok elosztása érdekében.

A munkamenet-korlátok feloldásával kapcsolatos további javaslatokért tekintse meg a következő témakört: [hogyan kezelheti SQL Database maximális bejelentkezések korlátozásait](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). A kiszolgálók és az előfizetési szintek korlátaival kapcsolatos információkért lásd: [SQL Database-kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-database-server.md) .

## <a name="workload-increase"></a>Munkaterhelés növekedése

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményi minta a munkaterhelés növekedésével, illetve a számítási feladatok felhalmozása által okozott problémákat azonosítja.

Ez az észlelés több mérőszám kombinációján keresztül történik. A mért alapszintű mérőszám a számítási feladatok növekedését észleli a korábbi számítási feladatokhoz képest. Az észlelés másik formája az aktív munkavégző szálak nagy növekedésének mérésén alapul, amely elég nagy a lekérdezés teljesítményének befolyásolásához.

Súlyosabb formában a munkaterhelés folyamatosan felhalmozódik, mert az SQL-adatbázis nem képes kezelni a munkaterhelést. Ennek eredményeként a munkaterhelés felhalmozása folyamatosan növekszik. Ennek a feltételnek az az ideje, hogy a munkaterhelés a végrehajtásra vár. Ez az állapot az egyik legsúlyosabb adatbázis-teljesítménnyel kapcsolatos problémát jelenti. Ez a probléma a megszakított munkaszálak számának növekedésének figyelésével észlelhető. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kiírja a végrehajtással megnövelt lekérdezések számát, valamint a lekérdezés lekérdezési kivonatát, amely a legnagyobb mértékben járul hozzá a munkaterhelés növekedéséhez. Ezeket az információkat kiindulási pontként használhatja a munkaterhelés optimalizálásához. A számítási feladatok növekedésének legnagyobb közreműködője azonosított lekérdezés különösen hasznos a kiindulási pontként.

Érdemes lehet egyenletesen kiosztani a munkaterheléseket az adatbázisra. Az indexek hozzáadásával érdemes lehet optimalizálni a teljesítményt befolyásoló lekérdezést. A számítási feladatokat több adatbázis között is eloszthatja. Ha ezek a megoldások nem lehetségesek, érdemes lehet növelni az SQL Database-előfizetés díjszabási szintjét, hogy növelje az elérhető erőforrások mennyiségét.

## <a name="memory-pressure"></a>Memória nyomása

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményteszt azt jelzi, hogy a jelenlegi adatbázis-teljesítmény romlása a memória nyomása okozta, vagy a memóriában felhalmozott mennyiségű, az elmúlt hét nap során kiinduló alapkonfigurációhoz képest.

A memória terhelése egy olyan teljesítménnyel kapcsolatos feltételt jelöl, amelyben nagy számú munkavégző szál igényel memóriát az SQL Database-ben. A nagy mennyiség nagy memória-kihasználtsági feltételt okoz, amelyben az SQL-adatbázis nem képes hatékonyan lefoglalni a memóriát az azt kérelmező összes feldolgozónak. A probléma egyik leggyakoribb oka az, hogy az SQL Database számára elérhető memória mennyisége az egyik oldalon van. Másfelől a munkaterhelés növekedése a munkaszálak és a memória terhelésének növekedését eredményezi.

A memória terhelésének súlyosabb formája a memória felhalmozása. Ez az állapot azt jelzi, hogy a feldolgozói szálak nagyobb számú memóriát igényelnek, mint a memóriát kiadó lekérdezések. A memória-támogatást kérő munkavégző szálak száma folyamatosan növekszik (halmozása), mert az SQL Database motorja nem tudja megfelelően kiosztani a memóriát ahhoz, hogy megfeleljen az igénynek. A memóriát felhalmozó feltétel a leggyakoribb adatbázis-teljesítménnyel kapcsolatos problémák egyikét jelenti.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimenete a memória-objektum tárolására szolgáló adatokat a jegyző (azaz a munkaszál) számára a nagy memóriahasználat és a kapcsolódó időbélyegek legmagasabb okának jelöli. Ezeket az információkat a hibaelhárítás alapjául használhatja. 

A legmagasabb memóriahasználat használatával optimalizálhatja vagy eltávolíthatja a titkárokkal kapcsolatos lekérdezéseket. Azt is megteheti, hogy nem kérdezi le azokat az adatlekérdezéseket, amelyeket nem kíván használni. A bevált gyakorlat az, hogy mindig a WHERE záradékot használják a lekérdezésekben. Emellett azt javasoljuk, hogy nem fürtözött indexeket hozzon létre az adatok kereséséhez.

Emellett csökkentheti a munkaterhelést, ha több adatbázisra optimalizálja vagy terjeszti azt. A számítási feladatokat több adatbázis között is eloszthatja. Ha ezek a megoldások nem lehetségesek, érdemes lehet növelni az SQL Database-előfizetés díjszabási szintjét, hogy növelje az adatbázis számára elérhető memória-erőforrások mennyiségét.

További hibaelhárítási javaslatokért lásd [: memória-ösztöndíjak meditáció: a titokzatos SQL Server memória fogyasztója sok névvel](https://blogs.msdn.microsoft.com/sqlmeditation/20../../memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Zárolási

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményteszt azt jelzi, hogy a jelenlegi adatbázis-teljesítmény romlása az elmúlt hét nap során felderített nagy mennyiségű adatbázis-zárolást mutatja. 

A modern RDBMS a zárolás elengedhetetlen a többszálas rendszerek megvalósításához, amelyekben a teljesítmény maximalizálható azáltal, hogy több egyidejű feldolgozót és párhuzamos adatbázis-tranzakciót futtat, ahol lehetséges. A zárolás ebben a kontextusban a beépített hozzáférési mechanizmusra vonatkozik, amelyben csak egyetlen tranzakció férhet hozzá a szükséges sorokhoz, lapokhoz, táblákhoz és fájlokhoz, és nem versenyez más erőforrásokkal kapcsolatos tranzakciókkal. Ha az erőforrásokat zároló tranzakciót velük együtt végzik, a rendszer felszabadítja az erőforrások zárolását, ami lehetővé teszi, hogy más tranzakciók hozzáférjenek a szükséges erőforrásokhoz. További információ a zárolásról: [az adatbázismotor zárolása](https://msdn.microsoft.com/library/ms190615.aspx).

Ha az SQL-motor által végrehajtott tranzakciók hosszabb ideig tartanak a használatra zárolt erőforrásokhoz való hozzáféréshez, ez a várakozási idő a munkaterhelés-végrehajtás teljesítményének lassulását okozza. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimenete a hibaelhárítás alapjaként használható zárolási adatokat jeleníti meg. Elemezheti a jelentett letiltási lekérdezéseket, azaz a zárolási teljesítmény romlását bevezető lekérdezéseket, és eltávolíthatja őket. Bizonyos esetekben sikeres lehet a blokkoló lekérdezések optimalizálása.

A probléma enyhítésének legegyszerűbb és legbiztonságosabb módja a tranzakciók rövid és a legdrágább lekérdezések zárolási lábnyomának csökkentése. A műveletek nagy kötegeit kisebb műveletekre lehet bontani. Az ajánlott eljárás a lekérdezési zárolási lábnyom csökkentése a lekérdezés lehető leghatékonyabb megtételével. Csökkentse a nagy mennyiségű vizsgálatot, mivel azok egyre nagyobb eséllyel csökkentik a holtpontokat, és a teljes adatbázis-teljesítményt befolyásolhatják. A zárolást okozó azonosított lekérdezések esetén létrehozhat új indexeket, vagy hozzáadhat oszlopokat a meglévő indexhez, hogy elkerülje a tábla vizsgálatát. 

További javaslatokat a következő témakörben talál: az [SQL Server zárolásának feloldása által okozott blokkoló problémák megoldása](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Megnövekedett MAXDOP

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményi minta azt a feltételt jelzi, amelyben egy kiválasztott lekérdezés-végrehajtási terv nagyobb mértékben párhuzamosan lett. A SQL Database lekérdezés-optimalizáló képes növelni a számítási feladatok teljesítményét azáltal, hogy a lekérdezéseket párhuzamosan hajtja végre, ahol lehetséges a dolgok felgyorsítása. Bizonyos esetekben a lekérdezéseket feldolgozó párhuzamos dolgozóknak több időt kell várniuk arra, hogy szinkronizálják és összevonják az eredményeket, mint a kevesebb párhuzamos feldolgozóval rendelkező lekérdezéseket, vagy akár bizonyos esetekben egyetlen munkaszálhoz képest.

A szakértői rendszerek a jelenlegi adatbázis-teljesítményt elemzik az alapidőszakhoz képest. Azt határozza meg, hogy egy korábban futó lekérdezés lassabban fut-e, mert a lekérdezés végrehajtási terve sokkal párhuzamos, mint amilyennek lennie kell.

A SQL Database MAXDOP-kiszolgáló konfigurációs beállításával szabályozható, hogy hány CPU-magot lehet párhuzamosan végrehajtani ugyanazzal a lekérdezéssel. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimeneti lekérdezési kivonatai olyan lekérdezésekkel kapcsolatosak, amelyekhez a végrehajtás időtartama nagyobb, mint amennyinek a végrehajtása nagyobb volt. A napló a CXP várakozási idejét is megjeleníti. Ez az idő azt jelzi, hogy az egyes szervezők/koordinátorok szála (0. szál) arra vár, hogy az összes többi szálat befejezze az eredmények egyesítése előtt, és haladjon előre. Emellett a diagnosztikai napló kimenete a várakozási idő, ameddig a gyenge teljesítményű lekérdezések a végrehajtásra várnak. Ezeket az információkat a hibaelhárítás alapjául használhatja.

Először is optimalizálja vagy egyszerűsítse a komplex lekérdezéseket. Az ajánlott eljárás a hosszú batch-feladatok kisebbekre bontása. Emellett győződjön meg arról, hogy a lekérdezések támogatásához indexeket hozott létre. Azt is megteheti, hogy manuálisan érvényesíti a maximális mértékű párhuzamosságot (MAXDOP) egy olyan lekérdezéshez, amely gyenge végrehajtásként lett megjelölve. A művelet T-SQL használatával történő konfigurálásához lásd: [a MAXDOP-kiszolgáló konfigurációs beállításának konfigurálása](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Ha a MAXDOP-kiszolgáló konfigurációs beállítását nullára (0) állítja be, akkor az alapértelmezett érték azt jelzi, hogy SQL Database az összes rendelkezésre álló logikai CPU-magot felhasználhatja, hogy egyetlen lekérdezés végrehajtásához integrálással a szálakat. A MAXDOP beállítása eggyel (1) azt jelzi, hogy egyetlen lekérdezés-végrehajtáshoz csak egy mag használható. A gyakorlatban ez azt jelenti, hogy a párhuzamosság ki van kapcsolva. Az adott esettől függően az adatbázishoz rendelkezésre álló magok és a diagnosztikai napló adatai alapján beállíthatja a MAXDOP beállítást a párhuzamos lekérdezés-végrehajtáshoz használt magok számára, amelyek megoldhatja a problémát az adott esetben.

## <a name="pagelatch-contention"></a>Pagelatch-tartalom

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményteszt azt jelzi, hogy az adatbázis aktuális számítási feladatának romlása az elmúlt hét napos munkaterhelési alaptervhez képest a pagelatch-tartalom miatt történt.

A zárolások az SQL Database által a többszálas küldés engedélyezéséhez használt könnyű szinkronizációs mechanizmusok. Garantálják az indexeket, az adatlapokat és az egyéb belső struktúrákat is tartalmazó memóriabeli struktúrák konzisztenciáját.

Az SQL-adatbázisban számos típusú zárolás érhető el. Az egyszerűség kedvéért a puffer-Zárolások segítségével biztosítható a memóriában lévő lapok használata a pufferben. Az i/o-zárak a pufferben még be nem töltött lapok elleni védelemhez használhatók. Amikor az adatok bekerülnek a pufferbe, vagy egy oldalról beolvasják őket, a feldolgozói szálnak először egy puffer-zárolást kell beolvasnia az oldalhoz. Ha egy munkavégző szál olyan oldalhoz próbál hozzáférni, amely még nem érhető el a memóriában lévő puffer-készletben, akkor a szükséges információk a tárolóból való betöltéséhez i/o-kérés készül. Az események ezen eseménysorozat a teljesítmény romlásának súlyosabb formáját jelzi.

A lap zárolásai akkor fordulnak elő, ha több szál párhuzamosan kísérli meg a zárolások beolvasását ugyanazon a memórián belüli struktúrában, ami megnövelt várakozási időt eredményez a lekérdezés végrehajtásához. A pagelatch IO-verseny esetén, ha adatokat kell elérni a tárterületről, ez a várakozási idő még nagyobb. Jelentős mértékben befolyásolhatja a munkaterhelés teljesítményét. A Pagelatch-tartalom a leggyakoribb forgatókönyv, amellyel a szálak egymásra várnak, és több CPU-rendszeren lévő erőforrásokkal versengenek.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló megjeleníti a pagelatch-tartalom részleteit. Ezeket az információkat a hibaelhárítás alapjául használhatja.

Mivel a pagelatch a SQL Database belső vezérlési mechanizmusa, automatikusan meghatározza, hogy mikor kell használni őket. Az alkalmazásra vonatkozó döntések, beleértve a séma kialakítását, befolyásolhatják a pagelatch viselkedését a determinisztikus viselkedése miatt.

A zárolási tartalom kezelésére szolgáló egyik módszer egy szekvenciális index-kulcs lecserélése egy nem szekvenciális kulccsal, hogy egyenletesen terjesszen be lapkákat egy index tartományon belül. Az index egyik vezető oszlopa általában a számítási feladatokat arányos módon osztja el. Egy másik módszer a tábla particionálásának megfontolására. A particionált tábla számított oszlopával létrehozott kivonatoló particionálási séma a túlzott zárolások elleni támadás mérséklésének gyakori módszere. Pagelatch IO-tartalom esetén az indexek bemutatása segít enyhíteni a teljesítménnyel kapcsolatos problémát. 

További információkért lásd: a SQL Server (PDF-letöltés) zárolásának [diagnosztizálása és megoldása](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) .

## <a name="missing-index"></a>Hiányzó index

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményteszt azt jelzi, hogy az aktuális adatbázis-munkaterhelés teljesítményének csökkenése az elmúlt hét napos alaptervhez képest hiányzó index miatt történt.

Az index a lekérdezések teljesítményének felgyorsítására szolgál. A táblázatos adatokat gyors hozzáférést biztosít a meglátogatni vagy beolvasott adatkészlet-lapok számának csökkentésével.

A teljesítmény romlását okozó lekérdezések azonosítása ezen észlelésen keresztül történik, amely esetében az indexek létrehozása hasznos lehet a teljesítmény szempontjából.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimeneti lekérdezési kivonatokat jelenít meg az azonosított lekérdezésekhez, amelyek hatással vannak a munkaterhelés teljesítményére. Ezeket a lekérdezéseket indexeket hozhat létre. Ezeket a lekérdezéseket optimalizálhatja vagy eltávolíthatja, ha azok nem szükségesek. A legjobb teljesítmény a nem használt adat lekérdezésének elkerülése.

> [!TIP]
> Tudta, hogy SQL Database beépített intelligencia automatikusan képes kezelni az adatbázisok legjobban teljesítő indexeit?
>
> A SQL Database folyamatos teljesítményének optimalizálása érdekében javasoljuk, hogy engedélyezze [SQL Database automatikus finomhangolását](sql-database-automatic-tuning.md). A SQL Database beépített intelligencia egyedi funkciója folyamatosan figyeli az SQL-adatbázist, és automatikusan összehangolja és indexeket hoz létre az adatbázisaihoz.
>

## <a name="new-query"></a>Új lekérdezés

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményi minta azt jelzi, hogy a rendszer egy új lekérdezést észlel, amely rosszul teljesít, és a munkaterhelési teljesítményt befolyásolja a hétéves teljesítményhez képest.

A jó teljesítményű lekérdezéseket néha nehéz feladatként lehet megírni. További információ a lekérdezések írásához: SQL- [lekérdezések írása](https://msdn.microsoft.com/library/bb264565.aspx). A meglévő lekérdezési teljesítmény optimalizálása: [lekérdezés finomhangolása](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló a lekérdezési kivonatokkal együtt legfeljebb két új, legnagyobb CPU-felhasználású lekérdezést eredményez. Mivel az észlelt lekérdezés hatással van a munkaterhelés teljesítményére, optimalizálhatja a lekérdezést. A bevált gyakorlat az, hogy csak a használni kívánt adatértékeket kéri le. Azt is javasoljuk, hogy használjon WHERE záradékkal rendelkező lekérdezéseket. Javasoljuk továbbá, hogy egyszerűsítse az összetett lekérdezéseket, és bontsa ki őket kisebb lekérdezésekre. Egy másik helyes gyakorlat a nagyméretű batch-lekérdezések kisebb batch-lekérdezésekre való bontása. Az új lekérdezések indexelése általában jó megoldás a teljesítménnyel kapcsolatos probléma mérséklésére.

Vegye fontolóra [Azure SQL Database lekérdezési terheléselemző](sql-database-query-performance.md)használatát.

## <a name="increased-wait-statistic"></a>Megnövekedett várakozási statisztika

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítmény azt jelzi, hogy a munkaterhelés teljesítményének romlása, amelyben a rendszer a gyenge teljesítményű lekérdezéseket azonosítja az elmúlt hét napos számítási feladathoz képest.

Ebben az esetben a rendszer nem tudja osztályozni a gyenge teljesítményt nyújtó lekérdezéseket bármely más szabványos észlelhető teljesítmény-kategóriába, de a regresszióért felelős várakozási statisztikát észlelte. Ezért a *megnövelt várakozási statisztikának*minősülő lekérdezéseknek tekinti őket, ahol a regresszióért felelős várakozási statisztika is elérhetővé válik. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló az érintett lekérdezések megnövekedett várakozási idejének részleteivel és lekérdezési kivonatával kapcsolatos információkat jeleníti meg.

Mivel a rendszer nem tudta sikeresen azonosítani a gyenge teljesítményű lekérdezések okát, a diagnosztikai információk jó kiindulási pont a manuális hibaelhárításhoz. Optimalizálhatja a lekérdezések teljesítményét. A bevált gyakorlat az, hogy csak a használni kívánt adatmennyiséget kell lekérnie, és leegyszerűsíteni és lebontani az összetett lekérdezéseket kisebbekre. 

A lekérdezési teljesítmény optimalizálásával kapcsolatos további információkért lásd: [lekérdezés finomhangolása](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>TempDB-tartalom

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményi minta egy adatbázis-teljesítménnyel kapcsolatos feltételt jelez, amelyben a tempDB-erőforrásokhoz hozzáférő szálak szűk keresztmetszete létezik. (Ez az állapot nem kapcsolódik az IO-hoz.) A probléma tipikus forgatókönyve több száz egyidejű lekérdezés, amely az összes létrehozás, használat és eldobás kis tempDB-táblákat használja. A rendszer azt észlelte, hogy az azonos tempDB-táblákat használó egyidejű lekérdezések száma a megfelelő statisztikai jelentőséggel bővült, hogy az adatbázis teljesítménye az elmúlt hét napra hasonlítson.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló megjeleníti a tempDB-tartalom részleteit. Az információkat kiindulási pontként használhatja a hibaelhárításhoz. Az ilyen jellegű és a teljes munkaterhelés teljesítményének növelésére két dolgot kell követnie: az ideiglenes táblák használatát le is állíthatja. Használhatja a memóriára optimalizált táblákat is. 

További információ: [Bevezetés a memóriába optimalizált táblákba](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>A rugalmas készlet DTU hiánya

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményi minta az aktuális adatbázis-munkaterhelés teljesítményének csökkenését jelzi az elmúlt hét napos alaptervhez képest. Ez az előfizetése rugalmas készletében rendelkezésre álló DTU hiánya miatt. 

Az SQL Database erőforrásait általában [DTU-erőforrásoknak](sql-database-purchase-models.md#dtu-based-purchasing-model)nevezzük, amelyek a CPU és az IO (adat-és tranzakciónapló-i/o-erőforrások) kevert mértékét tartalmazzák. Az [Azure rugalmas készlet erőforrásai](sql-database-elastic-pool.md) a rendelkezésre álló eDTU-erőforrások készletét használják, amelyek több adatbázis között vannak elosztva méretezési célokra. Ha a rugalmas készletben rendelkezésre álló eDTU-erőforrások nem elég nagyok a készletben lévő összes adatbázis támogatásához, a rendszer a rugalmas készlet DTU-hiányát észleli.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló a rugalmas készletre vonatkozó adatokat jeleníti meg, felsorolja a legfontosabb DTU-adatbázisokat, és a készletnek a legfelső szintű adatbázis által használt DTU százalékát adja meg.

Mivel ez a teljesítmény-feltétel több adatbázishoz kapcsolódik, és a rugalmas készletben lévő Edtu azonos készletét használja, a hibaelhárítási lépések a legnépszerűbb DTU-adatbázisokra összpontosítanak. Csökkentheti a munkaterhelést a legfelső szintű adatbázisokon, beleértve az ezen adatbázisokra vonatkozó legfelső szintű lekérdezések optimalizálását is. Emellett gondoskodhat arról is, hogy a nem használt adatlekérdezések ne legyenek lekérdezve. Egy másik megoldás, hogy optimalizálja az alkalmazásokat a legfelső DTU adatbázisokkal, és a számítási feladatokat több adatbázis között terjesztheti.

Ha a legfontosabb DTU-adatbázisokra vonatkozó aktuális számítási feladatok csökkentése és optimalizálása nem lehetséges, érdemes lehet növelni a rugalmas készlet díjszabási szintjét. Ez a növekedés a rugalmas készletben elérhető DTU növekedését eredményezi.

## <a name="plan-regression"></a>Regressziós terv

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményi minta azt a feltételt jelöli, amelyben a SQL Database az optimálisnál rosszabb lekérdezés-végrehajtási tervet használ. A legoptimálisabb terv általában nagyobb lekérdezés-végrehajtást eredményez, ami az aktuális és egyéb lekérdezéseknél több időt vár.

Az SQL Database a lekérdezés végrehajtási tervét határozza meg a lekérdezés végrehajtásának legkevesebb költsége alapján. Ahogy a lekérdezések és a munkaterhelések típusa megváltozik, néha a meglévő csomagok már nem hatékonyak, vagy talán SQL Database nem jó értékelést végeztek. A hibajavítások miatt a lekérdezés-végrehajtási tervek manuálisan is kényszeríthető.

Ez a észlelhető teljesítményi minta a terv regressziójának három különböző esetét ötvözi: az új terv regressziója, a régi terv regressziója és a meglévő csomagok megváltoztak a számítási feladatok. A terv regressziójának adott típusát a diagnosztikai napló *részletek* tulajdonsága adja meg.

Az új terv regressziós állapota olyan állapotra utal, amelyben SQL Database elindítja egy új lekérdezés-végrehajtási terv végrehajtását, amely nem annyira hatékony, mint a régi csomag. A régi terv regressziós állapota arra az állapotra hivatkozik, amikor SQL Database vált a régi csomagra vonatkozó új, hatékonyabb terv használatával, ami nem annyira hatékony, mint az új csomag. A meglévő csomagok megváltoztak a számítási feladatok regressziója arra az állapotra utal, amelyben a régi és az új csomagok folyamatosan váltakoznak, és így az egyenleg nagyobb lesz a gyenge teljesítményű terv felé.

A regressziós csomaggal kapcsolatos további információkért lásd: [Mi a terv regresszió a SQL Serverban?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló a lekérdezési kivonatokat, a jó csomag AZONOSÍTÓját, a rossz csomag AZONOSÍTÓját és a lekérdezési azonosítókat jeleníti meg. Ezeket az információkat a hibaelhárítás alapjául használhatja.

Elemezheti, hogy melyik terv jobban teljesíti az adott lekérdezéseket, amelyeket azonosíthat a megadott lekérdezési kivonatok használatával. Miután eldöntötte, hogy melyik csomag jobban működik a lekérdezéseknél, manuálisan kényszerítheti azt. 

További információ: [how SQL Server meggátolja a terv-regressziók megtervezését](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Tudta, hogy SQL Database beépített intelligenciával automatikusan kezelheti az adatbázisaihoz legjobban teljesítő lekérdezés-végrehajtási terveket?
>
> A SQL Database folyamatos teljesítményének optimalizálása érdekében javasoljuk, hogy engedélyezze [SQL Database automatikus finomhangolását](sql-database-automatic-tuning.md). A SQL Database beépített intelligencia egyedi funkciója folyamatosan figyeli az SQL-adatbázist, és automatikusan összehangolja és létrehozza az adatbázisokhoz legjobban teljesítő lekérdezés-végrehajtási terveket.
>

## <a name="database-scoped-configuration-value-change"></a>Adatbázis hatókörű konfigurációs értékének változása

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményi minta azt a feltételt jelzi, hogy az adatbázis hatókörű konfigurációjának változása a teljesítmény-regressziót okozza, amelyet a rendszer az elmúlt hét napos adatbázis-munkaterhelési viselkedéshez képest észlelt. Ez a minta azt jelzi, hogy az adatbázis-hatókörű konfigurációban történt legutóbbi módosítás nem hasznosnak tűnik az adatbázis teljesítményére nézve.

Az adatbázis-hatókörű konfiguráció módosításait minden egyes adatbázishoz be lehet állítani. Ezt a konfigurációt eseti alapon kell használni az adatbázis egyedi teljesítményének optimalizálása érdekében. Az egyes adatbázisokhoz a következő beállítások konfigurálhatók: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES és CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kiírja az adatbázis hatókörű konfigurációjának módosításait, amelyek a közelmúltban történtek, ami a teljesítmény romlását okozta a munkaterhelések előző hét napján. Visszaállíthatja az előző értékek konfigurációs módosításait. Az érték értéke a kívánt teljesítményszint eléréséig is beállítható. Az adatbázis-hatókör konfigurációs értékeit egy hasonló adatbázisból is átmásolhatja, amely kielégítő teljesítménnyel rendelkezik. Ha nem tudja elhárítani a teljesítményt, térjen át az alapértelmezett SQL Database alapértelmezett értékekre, és próbálja meg az alaptervtől kezdődően finomhangolást elvégezni.

Az adatbázis hatókörű konfigurációjának és a T-SQL szintaxisának a konfiguráció módosítására való optimalizálásával kapcsolatos további információkért lásd: az [adatbázis hatókörű konfigurációjának módosítása (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Lassú ügyfél

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményi minta azt jelzi, hogy az SQL Database-t használó ügyfél nem tudja felhasználni az adatbázis kimenetét olyan gyorsan, ahogy az adatbázis elküldi az eredményeket. Mivel SQL Database nem tárolja a futtatott lekérdezések eredményeit egy pufferben, lelassul, és megvárja, amíg az ügyfél a továbblépés előtt felhasználja a továbbított lekérdezési kimeneteket. Ez az állapot olyan hálózathoz is kapcsolódhat, amely nem elég gyors ahhoz, hogy az SQL-adatbázisból továbbítsa a kimeneteket a fogyasztó ügyfélnek.

Ez az állapot csak akkor jön létre, ha a rendszer a teljesítmény-regressziót észleli az adatbázis az elmúlt hét napi számítási feladatához képest. Ezt a problémát csak akkor észleli a rendszer, ha a teljesítmény statisztikailag jelentős romlása történik a korábbi teljesítmény-viselkedéshez képest.

### <a name="troubleshooting"></a>Hibaelhárítás

Ez az észlelhető teljesítményi minta egy ügyféloldali feltételt jelöl. Hibaelhárításra van szükség az ügyféloldali alkalmazásban vagy az ügyféloldali hálózaton. A diagnosztikai napló kiadja a lekérdezési kivonatokat, és várakozási időt, amely úgy tűnik, hogy az ügyfél a legtöbbet fogja használni az elmúlt két órában. Ezeket az információkat a hibaelhárítás alapjául használhatja.

Optimalizálhatja az alkalmazás teljesítményét a lekérdezések felhasználásához. Az esetleges hálózati késéssel kapcsolatos problémákat is megvizsgálhatja. Mivel a teljesítmény-romlási probléma az elmúlt hét napos teljesítménybeli változáson alapult, megvizsgálhatja, hogy a legutóbbi alkalmazás-vagy hálózati feltétel változása okozta-e a teljesítmény-regressziós eseményt. 

## <a name="pricing-tier-downgrade"></a>Díjszabási szintek visszalépése

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményi minta azt a feltételt jelzi, amelyben a SQL Database-előfizetés díjszabási szintje leállt. Az adatbázis számára elérhető erőforrások (DTU-EK) csökkentése miatt a rendszer a jelenlegi adatbázis-teljesítmény csökkenését észlelte az elmúlt hét napos alaptervhez képest.

Emellett előfordulhat, hogy egy olyan feltételt is felhasználtak, amelyben a SQL Database előfizetésének díjszabási szintje leállt, és rövid időn belül magasabb szintre lett frissítve. Ennek az ideiglenes teljesítménynek az észlelése a diagnosztikai napló részletek szakaszában található, a díjszabási szintek visszalépése és frissítése során.

### <a name="troubleshooting"></a>Hibaelhárítás

Ha csökkentette az árképzési szintet, és így a DTU elérhetővé válik a SQL Database számára, és elégedett a teljesítménnyel, semmit nem kell tennie. Ha csökkentette az árképzési szintet, és nem elégedett az SQL-adatbázis teljesítményével, csökkentse az adatbázis számítási feladatait, vagy növelje magasabb szintre az árképzési szintet.

## <a name="recommended-troubleshooting-flow"></a>Ajánlott hibaelhárítási folyamat

 A teljesítménnyel kapcsolatos problémák Intelligent Insights használatával történő hibaelhárításához kövesse a folyamatábrát.

Intelligent Insights a Azure Portal keresztül érheti el a Azure SQL Analytics. Próbálja meg megkeresni a bejövő teljesítmény riasztását, és jelölje ki. Azonosítsa, hogy mi történik az észlelések lapon. Figyelje meg a probléma okának elemzését, a lekérdezés szövegét, a lekérdezési idő trendjeit és az incidensek evolúcióját. A probléma megoldásához a Intelligent Insights javaslat használatával próbálja megoldani a problémát. 

[![hibaelhárítási folyamatábra](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Válassza ki a folyamatábrát egy PDF-verzió letöltéséhez.

Intelligent Insights általában egy órányi időt kell igénybe vennie a teljesítménnyel kapcsolatos probléma kiváltó okának elemzéséhez. Ha nem találja a problémát Intelligent Insights és kritikus fontosságú az Ön számára, a lekérdezési tároló használatával manuálisan azonosíthatja a teljesítménnyel kapcsolatos probléma okát. (Általában ezek a problémák kevesebb, mint egy órával régebbiek.) További információ: [a teljesítmény figyelése a lekérdezési tároló használatával](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>További lépések
- Ismerkedjen meg [Intelligent Insights](sql-database-intelligent-insights.md) fogalmakkal.
- Használja a [Intelligent Insights Azure SQL Database Performance Diagnostics-naplót](sql-database-intelligent-insights-use-diagnostics-log.md).
- Azure SQL Database figyelése [Azure SQL Analytics használatával](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Ismerje meg, hogyan [gyűjthet és használhat adatokat az Azure-erőforrásokból](../azure-monitor/platform/resource-logs-overview.md).
