---
title: Intelligent Insights az Azure SQL Database teljesítménnyel kapcsolatos problémáinak elhárítása |} A Microsoft Docs
description: Intelligent Insights segít az Azure SQL Database teljesítménnyel kapcsolatos problémáinak elhárítása.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 49d5e307c51a6527ade63bac0276fa141ecb5c24
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222454"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Intelligent Insights az Azure SQL Database teljesítménnyel kapcsolatos problémáinak elhárítása

Ez az oldal információt nyújt az Azure SQL Database és a felügyelt példány teljesítménnyel kapcsolatos problémákat észlelt a [Intelligent Insights](sql-database-intelligent-insights.md) adatbázis teljesítményének diagnosztikai naplója. A diagnosztikai naplót telemetriát továbbítható legyen [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), vagy egy külső megoldás fejlesztési és üzemeltetési egyéni riasztási és jelentéskészítési képességeit.

> [!NOTE]
> Egy gyors SQL Database teljesítménye – hibaelhárítási útmutató Intelligent Insights használatával, lásd: a [ajánlott a hibaelhárítási folyamat](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) folyamatábra ebben a dokumentumban.
>

## <a name="detectable-database-performance-patterns"></a>Cserélhető eszközként észlelhetőnek adatbázis teljesítmény-minták

Intelligent Insights automatikusan észleli a teljesítményproblémákat lekérdezés végrehajtási várakozási időt, a hibák vagy a várakozási idő alapján az SQL Database és a felügyelt példány adatbázisok. Ez a diagnosztikai napló teljesítmény észlelt minták jelenít meg. Cserélhető eszközként észlelhetőnek teljesítmény-minták az alábbi táblázat foglalja össze.

| Cserélhető eszközként észlelhetőnek teljesítmény minták | Az Azure SQL Database és rugalmas készletek leírása | Felügyelt példány található adatbázisok leírása |
| :------------------- | ------------------- | ------------------- |
| [Ért el erőforráskorlátok](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Elküldött elérhető erőforrások (dtu-k), adatbázis-feldolgozó szálak vagy a figyelt előfizetésekben érhető el az adatbázis-bejelentkezési munkamenetek száma elérte a korlátokat. Ez befolyásolja az SQL Database teljesítményét. | Processzor-erőforrások fogyasztásának eléri-e felügyelt példány korlátok. Ez befolyásolja az adatbázis teljesítményét. |
| [Számítási feladatok növekedése](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Számítási feladatok növekedése vagy az adatbázis a számítási feladatok folyamatos felhalmozódásához volt észlelhető. Ez befolyásolja az SQL Database teljesítményét. | Számítási feladatok növekedése észlelhető. Ez befolyásolja az adatbázis teljesítményét. |
| [Rendelkezésre álló memória mennyisége](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Feldolgozók által kért memória ad. kell várniuk az memórialefoglalások statisztikailag jelentős mennyiségű időt. Vagy nagyobb felhalmozódása a dolgozók által kért memória biztosít létezik. Ez befolyásolja az SQL Database teljesítményét. | Egy statisztikailag jelentős mennyiségű időt a feldolgozót, amelyek memóriát biztosít kért memórialefoglalások várnak. Ez befolyásolja az adatbázis teljesítményét. |
| [Zárolás](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Túl sok adatbázis-zárolást észlelt az SQL Database teljesítményét. | Túl sok adatbázis-zárolást észlelt az adatbázis teljesítményét. |
| [Nagyobb MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | A maximális párhuzamossági fokot (MAXDOP) megváltozott, ez hatással lenne a lekérdezés-végrehajtási hatékonyságát. Ez befolyásolja az SQL Database teljesítményét. | A maximális párhuzamossági fokot (MAXDOP) megváltozott, ez hatással lenne a lekérdezés-végrehajtási hatékonyságát. Ez befolyásolja az adatbázis teljesítményét. |
| [Pagelatch versengés](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Több szálon egyszerre próbál hozzáférni az ugyanazon a memóriában puffer lapok megnövekedett várakozási időt eredményez, és pagelatch versengés okozza. Ez befolyásolja az SQL database teljesítményét. | Több szálon egyszerre próbál hozzáférni az ugyanazon a memóriában puffer lapok megnövekedett várakozási időt eredményez, és pagelatch versengés okozza. Ez befolyásolja az adatbázis teljesítményét. |
| [Hiányzó Index](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Hiányzó index észlelt, amely hatással van az SQL database szolgáltatás teljesítményének. | Hiányzó indexet az adatbázis teljesítményét befolyásoló volt észlelhető. |
| [Új lekérdezés](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Új lekérdezés észlelhető, ez hatással lenne az SQL Database teljesítménye. | Új lekérdezés észlelhető, az általános adatbázis teljesítményét befolyásoló. |
| [Megnövelt várakozási statisztika](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Nagyobb mértékű adatbázis várakozási időt észlelt, amely hatással van az SQL database szolgáltatás teljesítményének. | Nagyobb mértékű adatbázis várakozási időt észlelt az adatbázis teljesítményét. |
| [A TempDB versengés](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Több szálon elérni kívánt szűk keresztmetszetet okoz a TempDB erőforrást. Ez befolyásolja az SQL Database teljesítményét. | Több szálon elérni kívánt szűk keresztmetszetet okoz a TempDB erőforrást. Ez befolyásolja az adatbázis teljesítményét. |
| [Rugalmas készlet dtu-k hiánya](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Kevés a rendelkezésre álló Edtu a rugalmas készlet SQL Database teljesítményét befolyásolja. | Nem érhető el a felügyelt példányt, mert használ Virtuálismag-modell. |
| [Regresszió megtervezése](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Új csomagot, vagy egy meglévő csomagot, a számítási feladat változását észlelte. Ez befolyásolja az SQL Database teljesítményét. | Új csomagot, vagy egy meglévő csomagot, a számítási feladat változását észlelte. Ez befolyásolja az adatbázis teljesítményét. |
| [Adatbázis-specifikus konfigurációs érték módosítása](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Az SQL Database konfigurációváltozás észlelt az adatbázis teljesítményét befolyásoló. | Konfigurációváltozás az adatbázison az adatbázis teljesítményét befolyásoló volt észlelhető. |
| [Lassú ügyféloldali](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Lassú alkalmazás ügyfél nem képes elég gyorsan felhasználásához kimeneti az adatbázisból. Ez befolyásolja az SQL Database teljesítményét. | Lassú alkalmazás ügyfél nem képes elég gyorsan felhasználásához kimeneti az adatbázisból. Ez befolyásolja az adatbázis teljesítményét. |
| [Díjszabási szint alacsonyabb szintű](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Díjszabási szint alacsonyabb szintű művelet csökkent a rendelkezésre álló erőforrásokat. Ez befolyásolja az SQL Database teljesítményét. | Díjszabási szint alacsonyabb szintű művelet csökkent a rendelkezésre álló erőforrásokat. Ez befolyásolja az adatbázis teljesítményét. |

> [!TIP]
> Engedélyezze a folyamatos teljesítményoptimalizálás az SQL Database, [Azure SQL Database automatikus finomhangolása](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning). Ezt az egyedi funkciót az SQL Database beépített intelligenciával folyamatosan figyeli az SQL database, automatikusan hangolja az indexek, és lekérdezés végrehajtási terv javításokat vonatkozik.
>

Az alábbi szakasz ismerteti részletesebben cserélhető eszközként észlelhetőnek teljesítmény mintákat.

## <a name="reaching-resource-limits"></a>Ért el erőforráskorlátok

### <a name="what-is-happening"></a>mi történik

Ez a minta cserélhető eszközként észlelhetőnek teljesítmény egyesíti az elérhető erőforrás-korlátozások, a feldolgozó korlátozások és a munkamenet-korlátok elérése kapcsolatos teljesítményproblémák. Után ezen teljesítményprobléma észlel, egy leírást a diagnosztikai napló azt jelzi, hogy a teljesítménnyel kapcsolatos problémák kapcsolatos erőforrás, a feldolgozó vagy a munkamenet-korlátok.

Az SQL Database erőforrásokat általában hivatkozunk [DTU](https://docs.microsoft.com/azure/sql-database/sql-database-what-is-a-dtu) vagy [virtuális mag](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-service-tiers-vcore) erőforrásokat. Erőforráskorlátok elérése minta észlelésekor elismert lekérdezési teljesítmény romlását okozzák a mért erőforrás-korlátok bármelyikét elérése.

A munkamenet-korlátok erőforrás azt jelzi, hogy az SQL Database elérhető egyidejű bejelentkezések száma. Ez a teljesítmény-minta rendszer ismeri fel, amikor az SQL Database-adatbázisokhoz csatlakozó alkalmazásokat elérte az adatbázis elérhető egyidejű bejelentkezések száma. Alkalmazás próbálja a használatához további előadások, mint amennyi rendelkezésre áll, adatbázis, a lekérdezés teljesítménye érintett.

Feldolgozó korlátok elérése egy adott esetet erőforráskorlátok éri el, mert a rendelkezésre álló feldolgozók nem számít az a dtu-k vagy virtuális mag használata. Feldolgozó korlátai, adatbázis elérése okozhat az erőforrás-specifikus várakozási időt, ami eredményez a lekérdezési teljesítmény romlását márkáik.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló jelenít meg, hogy a teljesítmény- és erőforrás-használat százalékos érintett lekérdezések lekérdezés kivonatokat. Kiindulási pontként az adatbázis-munkaterhelés optimális használhatja ezt az információt. Különösen optimalizálhatja a lekérdezések, amelyek befolyásolják a teljesítményromlást indexek hozzáadásával. Vagy egy további akár számítási feladatok terjesztési alkalmazásokat is optimalizálhatja. Ha nem sikerül, vagy csökkentse a számítási feladatok optimalizálása, érdemes megfontolni a tarifacsomagot az SQL database előfizetés növelheti a rendelkezésre álló erőforrások mennyiségét.

Ha elérte a rendelkezésre álló munkamenet korlátai, optimalizálhatja az alkalmazások csökkentésével az adatbázison végrehajtott bejelentkezések száma. Ha nem sikerül csökkentése érdekében a bejelentkezések száma az adatbázishoz az alkalmazásokból, érdemes megfontolni a tarifacsomagot az adatbázis. Vagy felosztása, és az adatbázis helyezze át a több-több elosztott terhelésű számítási feladatok terjesztési adatbázist.

További javaslatokért feloldásáról a munkamenet-korlátok, lásd: [a korlátokat, az SQL Database legnagyobb bejelentkezések kezelése](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Az előfizetés szintjén elérhető erőforráskorlátok, lásd: [SQL Database erőforrás-korlátozások](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Számítási feladatok növekedése

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény minta problémákat, a munkaterhelés növekedése vagy jogosultságeszkalálást formájában, a számítási feladatok pile-up azonosítja.

Az észlelés több metrikát együttes jön létre. A mért alapszintű metrika van érzékelni a számítási feladatok az elmúlt munkaterhelés alaptervhez képest. Észlelési más formájában növekedésének aktív feldolgozó szálak, amely elég nagy hatással vannak a lekérdezési teljesítmény mérésére alapul.

A szigorúbb formájában a munkaterhelés előfordulhat, hogy folyamatosan egymásra az SQL-adatbázis a nem képes kezelni a számítási feladatok miatt. Ez egy folyamatosan növekvő munkaterhelés méretét, amely a számítási feladatok pile-up feltétel. Ezen állapot miatt a számítási feladatok megvárja, amíg végrehajtási idő növekszik. Ez az állapot jelöli az adatbázis teljesítményét a legsúlyosabb problémák egyike. A probléma a megszakított munkaszálak számának növekedése figyelési észlel. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló jelenít meg a lekérdezések, amelyek végrehajtása emelkedett és a lekérdezés a legnagyobb mértékben a számítási feladatok növekedése a lekérdezés kivonata. Ezt az információt kiindulási pontként használható a számítási feladatok optimalizálása. A lekérdezés azonosította az eseményt a legnagyobb munkaterhelés növekedése közreműködője különösen hasznos kiindulópontként szolgál.

Érdemes lehet a számítási feladatok az adatbázishoz való egyenletesebb elosztása. Fontolja meg a lekérdezést, amely befolyásolja a teljesítményt indexek hozzáadásával optimalizálása. Emellett előfordulhat, hogy terjesztése a számítási feladatok több adatbázis között. Ha ezek a megoldások nem lehetséges, érdemes megfontolni a tarifacsomagot az SQL database előfizetés növelheti a rendelkezésre álló erőforrások mennyiségét.

## <a name="memory-pressure"></a>Rendelkezésre álló memória mennyisége

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény-minta azt jelzi, hogy a az aktuális adatbázis teljesítménye, rendelkezésre álló memória mennyisége, vagy a jogosultságeszkalálást formájában okozott memória pile-up feltétel, az elmúlt hét nap teljesítmény alaptervhez képest.

Rendelkezésre álló memória mennyisége azt jelzi, hogy a teljesítmény feltétel, amelyben nincs sok munkaszálak kérő memóriát biztosít az SQL database-ben. A nagy mennyiségű, amelyben az SQL-adatbázis nem tudja hatékonyan memóriát lefoglalni, hogy az összes Worker magas memória kihasználtsági feltétel okoz. A leggyakoribb okai a problémához kapcsolódik egy kézzel az SQL-adatbázis számára elérhető memória mennyisége. Számítási feladatok növekedése, másrészt növekedését okozza a munkaszálak és a rendelkezésre álló memória mennyisége.

A rendelkezésre álló memória mennyisége jogosultságeszkalálást formátuma a memória pile-up feltétel. Ez az állapot azt jelzi, hogy nagyobb mennyiségű feldolgozó szál a kért memóriát biztosít, mint a memória felszabadítása lekérdezések. A kérelmező memóriát is engedélyezi a munkaszálak számának előfordulhat, hogy kell folyamatosan növekvő (felhalmozódjanak), mert az SQL adatbázis-kezelő nem tudja hatékonyan elegendő memóriát lefoglalni az igény. A memória pile-up feltétel jelöli az adatbázis teljesítményét a legsúlyosabb problémák egyike.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló jelenít meg a memória objektumhoz adattár részletei a nagy mértékű memóriahasználat és a megfelelő időbélyeggel legmagasabb okát megjelölve adminisztrátor (vagyis a munkavégző szál). Ez az információ alapjaként használható hibaelhárítási. 

Optimalizálja, vagy távolítsa el a írnokok a legmagasabb memóriahasználat a kapcsolódó lekérdezések. Akkor is győződjön meg arról is, hogy Ön nem adatlekérdezést, hogy nem szeretné használni. Jó gyakorlat, hogy mindig használja a lekérdezések WHERE záradék. Emellett javasoljuk, hogy hozzon létre nem fürtözött indexek az adatok összegyűjtését, ahelyett hogy vizsgálja.

A számítási feladatok optimalizálása vagy szétosztása több adatbázis is csökkentheti. Vagy juttathatja el a számítási feladatok több adatbázis között. Ha ezek a megoldások nem lehetséges, érdemes megfontolni az SQL adatbázis-előfizetés növeléséhez, az adatbázis számára elérhető memória-erőforrások tarifacsomagja.

További hibaelhárítási javaslatokat, lásd: [memória meditation biztosít: az SQL Server frusztráló memória fogyasztói több névvel](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Zárolás

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény-minta azt jelzi, hogy a az aktuális adatbázis teljesítménye, amelyben túl sok adatbázis-zárolást észlel az elmúlt hét nap teljesítmény alaptervhez képest. 

A modern RDBMS zárolás alapvető fontosságú, amelyben teljesítmény teljes méretű több egyidejű feldolgozók és a párhuzamos adatbázis-tranzakciók futtatásával lehetséges többszálú rendszerek megvalósításához. A beépített hozzáférés mechanizmus, amely csak egyetlen tranzakció kizárólag hozzáférhet a sorokat, lapok, táblák és fájlok, amelyek szükségesek, és a egy másik tranzakció az erőforrások nem befolyásolják hivatkozik zárolása ebben a környezetben. Amikor a tranzakciót, amely a használható erőforrások zárolása velük történik, a erőforrások zárolása akkor szabadul fel, amely lehetővé teszi, hogy más tranzakciók szükséges erőforrások eléréséhez. A zárolás további információkért lásd: [zárolni az adatbázismotor](https://msdn.microsoft.com/library/ms190615.aspx).

Ha hosszabb ideig használható zárolt erőforrások eléréséhez az SQL-kezelő által végrehajtott tranzakciók vár, a várakozási idő hatására a lassulás a számítási feladat végrehajtási teljesítmény. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló zárolási alapjaként használható hibaelhárítási részleteket jelenít meg. A jelentett blokkoló lekérdezéseket, azok a lekérdezések, amelyek bemutatják a zárolási teljesítményromlást elemzése, és távolítsa el őket. Bizonyos esetekben valószínűleg sikeres letiltja a lekérdezések optimalizálását.

A legegyszerűbb és legbiztonságosabb az elhárításukra módja, hogy rövid tranzakciók, és csökkenti a zárolás adatmennyiséget, a legtöbb drága lekérdezések. Kisebb műveletekkel műveletek nagy kötegelt bonthatja. Jó gyakorlat, hogy csökkenti a lekérdezési zárolási adatmennyiséget azáltal, hogy a lekérdezés lehető leghatékonyabb. Csökkenti a nagy vizsgálatok, mert növelése holtpontok esélyét és a kedvezőtlen hatással lehet az általános adatbázis teljesítménye. A zárolást okozó azonosított lekérdezésekhez új indexeket hozhat létre, vagy oszlopok hozzáadása a meglévő index a szekvenciális rekordolvasással elkerülése érdekében. 

További javaslatokért lásd: [blokkoló zárolási eszkalációs az SQL Server által okozott problémák megoldásával](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Nagyobb MAXDOP

### <a name="what-is-happening"></a>mi történik

A cserélhető eszközként észlelhetőnek teljesítmény minta azt jelzi, hogy egy feltételt, amelyben egy kiválasztott lekérdezés végrehajtási terv volt párhuzamosíthatók több mint kellett volna. Az SQL Database lekérdezésoptimalizáló javíthatja a számítási feladatok teljesítményére-lekérdezések végrehajtása párhuzamosan felgyorsításához dolog, ahol csak lehetséges. Bizonyos esetekben a lekérdezés feldolgozása párhuzamosan feldolgozók több időt Várakozás egymással szinkronizálásához, és összefésüli a eredmények ugyanabból a lekérdezés végrehajtása kevesebb párhuzamos feldolgozók használata, vagy akár bizonyos esetekben egy egyetlen munkavégző szál képest képest.

A szakértői rendszer elemzi az aktuális adatbázis-teljesítményt az alapkonfiguráció időszak. Meghatározza, hogy ha egy korábban elindított lekérdezése lassabb, mint korábban, mert a lekérdezés-végrehajtási terv több mint méretezésnek megfelelően kell lennie.

A MAXDOP kiszolgálói konfigurációs beállítás, az SQL Database segítségével szabályozhatja, hogy hány Processzormagok ugyanabból a lekérdezés végrehajtása párhuzamosan használható. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló jelenít meg, amelynek végrehajtási időtartamának magasabb, hiszen ezek is több, mint a kellett volna párhuzamosíthatók lekérdezések kapcsolódó lekérdezés kivonatokat. A naplóban is CXP várakozási időt adja vissza. Ez az idő (0 szál) egyetlen szervező/koordinátor szál várakozik befejezését, mielőtt egyesíti az eredményeket, és előre áthelyezése más futásra idejét jelzi. Emellett a diagnosztikai naplót a várakozási időt, amelyek a gyenge hajt végre lekérdezéseket a teljes végrehajtási jelenít meg. Ez az információ alapjaként használható hibaelhárítási.

Először is optimalizálhatja, vagy egyszerűsítheti a bonyolult lekérdezéseket. Jó gyakorlat, hogy hosszú kötegelt feladatok kisebb részekre. Emellett meg, hogy létrehozott-e a lekérdezések támogatásához indexeket. Manuálisan is kényszeríthető (MAXDOP) párhuzamosság maximális foka egy lekérdezést, amely a gyenge végrehajtása lett megjelölve. Ez a művelet a T-SQL használatával, lásd: [a MAXDOP kiszolgálói konfigurációs beállítás konfigurálása](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

A beállítás a MAXDOP kiszolgáló konfigurációs beállítással, amely nulla (0), mint az alapértelmezett érték azt jelzi, hogy az SQL Database használatával az összes rendelkezésre álló logikai Processzormagok párhuzamosíthatja szálainak egyetlen lekérdezés végrehajtása. Beállítás MAXDOP egy (1) azt jelzi, hogy csak egyetlen mag egy egyetlen lekérdezés-végrehajtás használható. Gyakorlatilag ez azt jelenti, hogy párhuzamosságot ki van kapcsolva. Függően kiszolgálónként eseti alapon elérhető magok száma az adatbázisban, és a diagnosztikai információk naplózása, hangolhassa a MAXDOP beállítást, a párhuzamos lekérdezés-végrehajtáshoz abban az esetben a probléma megoldására használt magok számának.

## <a name="pagelatch-contention"></a>Pagelatch versengés

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény-minta azt jelzi, hogy az aktuális adatbázis számítási teljesítmény romlását pagelatch versengés alapkonfigurációval összevetett az elmúlt 7 napig terhelés miatt.

Zárolás van életben ahhoz, hogy az SQL Database által használt egyszerű szinkronizálási mechanizmus többszálas folyamatokhoz. Indexek, lapok és más belső szerkezetek struktúrák memórián belüli konzisztencia garantálnak.

Nincsenek zárolás van életben számos különböző típusú elérhető az SQL-adatbázison. Az egyszerűség érdekében puffer zárolás van életben védelmére használhatók a pufferkészletben memórián belüli oldalak. I/o-zárolás van életben még nincs betöltve a pufferkészletben a lapok védelmére használhatók. Amikor adatokat írni vagy olvasni a pufferkészletben oldal, az oldal egy puffer zárolás megszerzésére először a munkaszál van szüksége. Minden alkalommal, amikor egy munkavégző szál megpróbál hozzáférni egy oldal, amely nem a memórián belüli pufferkészletben már érhető el, egy i/o-kérelem kérés érkezett a szükséges adatokat betölteni a storage-ból. Az események sorozatát azt jelzi, hogy a teljesítményromlást jogosultságeszkalálást formája.

A lap zárolás van életben a versengés következik be, amikor több szálon egyszerre próbál szerezni az azonos memórián belüli struktúra, amely mutatja be egy nagyobb várakozási idő a lekérdezés végrehajtása a zárolás van életben. Esetén pagelatch IO versengést idéz elő ha adatokat kell érhető el a tárolóból, a várakozási idő még nagyobb. Ez jelentősen befolyásolhatja számítási feladatok teljesítményére. Pagelatch tapasztalható versengés, a leggyakoribb eset szál várakozik egymással, és az erőforrások több Processzor rendszeren versengő.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló pagelatch versengés részleteket jelenít meg. Ez az információ alapjaként használható hibaelhárítási.

Mivel a pagelatch egy belső ellenőrzési mechanizmust, az SQL Database, automatikusan meghatározza, hogy mikor érdemes használni azokat. Alkalmazás döntéseket hozhat, többek között a séma tervezése, hatással lehet a pagelatch viselkedése miatt zárolás van életben determinisztikus viselkedését.

Egy zárolás versengés kezelésére, hogy cserélje le a soros Indexkulcs egyenletes elosztását beszúrásokat egy index tartomány hálója kulccsal. Általában egy vezető oszlop az index osztja el a munkaterhelés arányosan. Egy másik módszert érdemes figyelembe venni, a táblaparticionálást. Túl sok zárolás a versengés csökkentése általánosan használt megközelítés létrehozása a particionálási séma egy számított oszlop egy particionált táblához kivonatot. Esetén pagelatch i/o-versengés indexek bemutatása segít mérsékelni ezen teljesítményprobléma. 

További információkért lásd: [diagnosztizálása és megoldása zárolni az SQL Server-kiszolgálón a versengés](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (letölthető PDF-fájl).

## <a name="missing-index"></a>Hiányzó Index

### <a name="what-is-happening"></a>mi történik

Ez a teljesítmény-minta azt jelzi, hogy az aktuális adatbázis munkaterhelés teljesítménycsökkenés oka egy hiányzó indexet az elmúlt hét nap alaptervhez képest.

Az index szolgál a lekérdezések teljesítményének felgyorsítása érdekében. Táblák adatainak gyors hozzáférést biztosít által meglátogatott vagy beolvasott igénylő adatkészlet oldalak számának csökkentése.

Teljesítményromlást okozó adott lekérdezések azonosítása, amelynek az indexek létrehozása előnyös, ha a teljesítmény lenne az észlelés keresztül.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló jelenít meg a lekérdezések, a számítási feladatok teljesítményére hatással, amelyek meghatározott lekérdezés-kivonatát. A lekérdezések indexeket hozhat létre. Is optimalizálhatja vagy távolítsa el ezeket a lekérdezéseket, ha nem szükségesek. A jó teljesítmény gyakorlat, hogy kerülhetők el adatokat, amelyek nem használják.

> [!TIP]
> Tudta, hogy az SQL Database beépített intelligenciával automatikusan kezelheti a legjobban teljesítő indexeinek kezeléséből?
>
> Folyamatos teljesítményoptimalizálás az SQL Database, javasoljuk, hogy engedélyezze [SQL Database automatikus finomhangolása](sql-database-automatic-tuning.md). Ezt az egyedi funkciót az SQL Database beépített intelligenciával folyamatosan figyeli az SQL-adatbázis, és automatikusan hangolja és indexeinek kezeléséből hoz létre.
>

## <a name="new-query"></a>Új lekérdezés

### <a name="what-is-happening"></a>mi történik

A teljesítmény minta azt jelzi, hogy egy új lekérdezést észlel, amely rosszul végez, és ez hatással lenne a számítási feladatok teljesítményére, 7 napos teljesítmény alapkonfigurációhoz viszonyított száma.

Jó teljesítményű lekérdezés írása néha nehéz feladat lehet. Lekérdezések írásáról további információkért lásd: [írása SQL-lekérdezések](https://msdn.microsoft.com/library/bb264565.aspx). Meglévő lekérdezési teljesítmény optimalizálása érdekében tekintse meg a [lekérdezések hangolása](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplóadatokat kimenetek legfeljebb két új legtöbb CPU-igényes lekérdezéseket, ideértve a lekérdezés kivonatokat. Az észlelt lekérdezés hatással van a számítási feladatok teljesítményére, mivel a lekérdezés is optimalizálhatja. Jó gyakorlat, hogy csak adatokat kell használnia. Javasoljuk továbbá, a WHERE záradék lekérdezésekkel. Azt javasoljuk, hogy Ön egyszerűsítheti a bonyolult lekérdezéseket és az őket kisebb lekérdezésekre. Egy másik jó gyakorlat, hogy nagy kötegelt lekérdezések batch kisebb lekérdezésekre felosztania. Indexek az új lekérdezések bemutatása általában jó megoldás a probléma megoldásához teljesítményét.

Fontolja meg [Azure SQL Database lekérdezési Terheléselemző](sql-database-query-performance.md).

## <a name="increased-wait-statistic"></a>Megnövelt várakozási statisztika

### <a name="what-is-happening"></a>mi történik

A cserélhető eszközként észlelhetőnek teljesítmény minta azt jelzi, hogy a számítási feladatok teljesítménycsökkenés, amelyben a gyenge teljesítményű lekérdezések azonosítják az elmúlt 7 napig terhelés alaptervhez képest.

Ebben az esetben a rendszer a gyenge hajt végre lekérdezéseket a többi cserélhető eszközként észlelhetőnek szabványos teljesítmény-kategóriák nem lehet besorolni, de azt észlelte, hogy a várakozási statisztika a regressziós felelős. Ezért úgy ítéli meg őket, a lekérdezések *várakozási statisztika nőtt*, ahol a felelős a regressziós várakozási statisztika is elérhetővé teszi. 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplóadatokat kimenetek megnövekedett várakozási idő – részletek és a lekérdezés a kivonatok az érintett lekérdezések.

Mivel a rendszer sikeresen nem sikerült azonosítani a gyenge teljesítményű lekérdezések fő okát, a diagnosztikai adatokat, manuális hibaelhárítási jó kiindulási pont. Optimalizálhatja a lekérdezések teljesítményét. Bevált gyakorlat, hogy csak adatokat kell használni, és leegyszerűsítése és az összetett lekérdezések kisebb felosztania beolvasni. 

A lekérdezési teljesítmény optimalizálására vonatkozó további információkért lásd: [lekérdezések hangolása](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>A TempDB versengés

### <a name="what-is-happening"></a>mi történik

A cserélhető eszközként észlelhetőnek teljesítmény minta azt jelzi, hogy egy adatbázis teljesítmény feltételt, amelyben a szálak próbál hozzáférni a tempDB-erőforrásokra szűk keresztmetszetté létezik. (Ez az állapot nem kapcsolódó IO.) Ezen teljesítményprobléma a jellemző forgatókönyv, hogy az összes létrehozását, használatát és majd dobja el a tempDB kis méretű táblák lekérdezést több száz. A rendszer azt észlelte, hogy a használatával a tempDB két tábla egyidejű lekérdezések száma nő, az adatbázis-teljesítményt az elmúlt hét nap meghatározása hatással, amelyek elegendő statisztikai jelentőségét.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplót a tempDB versengés részleteket jelenít meg. Az információk kiindulási pontként használhatja a hibaelhárításhoz. Két dolgot lehet szerezni a versengés az ilyen típusú elmondja, és növelhető a a teljes számítási feladatnak: állítsa le az ideiglenes táblák használata. A memóriaoptimalizált táblák is használhatja. 

További információkért lásd: [Bevezetés a memóriaoptimalizált táblák](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Rugalmas készlet dtu-k hiánya

### <a name="what-is-happening"></a>mi történik

A cserélhető eszközként észlelhetőnek teljesítmény minta azt jelzi, hogy a az aktuális adatbázis számítási feladatok teljesítménye az elmúlt hét nap alaptervhez képest. A rugalmas készletben az előfizetésben elérhető dtu-k hiánya okozza. 

Az SQL Database erőforrásokat általában nevezzük [DTU-erőforrások](sql-database-service-tiers.md#what-are-database-transaction-units-dtus), amely Processzor- és i/o-(adat- és naplózási IO) erőforrások összesített mérésén állnak. [Az Azure rugalmas adatbáziskészlet erőforrásainak](sql-database-elastic-pool.md) rendelkezésre álló eDTU-erőforrások méretezése céljából több adatbázis között megosztott készletét használják. A rugalmas készlet rendelkezésre álló eDTU-erőforrások nem elég nagy a készletben található összes adatbázis támogatására, amikor a rendszer egy rugalmas készlet dtu-k hiánya teljesítménybeli problémát észlel.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai naplót a rugalmas készlet adatokat jelenít meg, láthatók a felső DTU-igényes adatbázisok, illetve a készlet dtu-k a top-igényes adatbázis által használt százalékaként.

Mivel ez a teljesítmény feltétel több adatbázis ugyanazon készlet edtu a rugalmas készlet használatával kapcsolatos, a hibaelhárítási lépéseket a legfontosabb DTU-igényes adatbázisok koncentrálhat. A top-igényes adatbázisok a munkaterhelés, amely tartalmazza a top-igényes lekérdezéseket, ezeket az adatbázisokat a optimalizálása csökkentheti. Akkor is biztosíthatja, hogy Ön nem lekérdezése nem használt adatok számára. Egy másik megoldás, az alkalmazások optimalizálása a felső DTU-igényes adatbázisok használatával, és újra elosztják a terhelést több adatbázis között.

Ha csökkentésére és az első DTU-igényes adatbázisok a jelenlegi terhelés optimalizálása nem lehetséges, érdemes megfontolni a rugalmas készlet tarifacsomagját. Például növelheti a rendelkezésre álló a rugalmas készlet Dtu-növekedés eredményez.

## <a name="plan-regression"></a>Regresszió megtervezése

### <a name="what-is-happening"></a>mi történik

A cserélhető eszközként észlelhetőnek teljesítmény minta azt jelzi, hogy az SQL-adatbázis már használja egy optimálisnál lekérdezés végrehajtási terv feltétel. Az optimálisnál rosszabb terv általában magasabb lekérdezés-végrehajtás, ami már várjon az aktuális és egyéb lekérdezéseket időpontok okoz.

Az SQL database meghatározza, hogy a lekérdezés végrehajtási terv a legkevésbé a lekérdezés-végrehajtás költsége. Lekérdezések és a számítási feladatok módosítás típusát egyes esetekben a meglévő csomagokat nem lesznek hatékony, vagy például végezze el az SQL Database nem a helyes értékelés. Javítás alkot lekérdezések végrehajtási tervét manuálisan kényszeríthető.

A cserélhető eszközként észlelhetőnek teljesítmény minta egyesíti a három különböző eseteit terv regressziós: új terv regressziós, a régi csomag regressziós és a meglévő megváltozott tervek számítási feladatot. Az adott típusú terv regressziós bekövetkezett megtalálható a *részletek* tulajdonság a diagnosztikai naplóban.

Az új csomag regressziós feltétel olyan állapotban, amelyben az SQL Database elindul egy új lekérdezés végrehajtási terv, amely nem annyira hatékony, mint a régi csomagot végrehajtása hivatkozik. A régi csomagot regressziós feltétel hivatkozik az állapot, amikor az SQL Database vált egy új, hatékonyabb tervet a régi csomagot, amely nem annyira hatékony, mint az új csomag használatával. A meglévő megváltozott tervek munkaterhelés regressziós az állam, amelyben a régi és az új csomagok folyamatosan alternatív, fog több a rossz teljesítményű csomag felé egyenleg hivatkozik.

A terv regressziót további információkért lásd: [Mi az az SQL Server terv regressziós?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló jelenít meg a lekérdezés a kivonatok, jó terv azonosítója, rossz csomag azonosítója és lekérdezési azonosítók. Ez az információ alapjaként használható hibaelhárítási.

Melyik csomaggal jobb hajt végre az adott lekérdezések, amelyek a megadott lekérdezés kivonatok azonosíthatja, elemezheti. Miután megállapította, hogy melyik csomaggal jobban működik a lekérdezések, manuálisan kényszerítheti. 

További információkért lásd: [megtudhatja, hogyan megakadályozza az SQL Server a terv regressziót](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Tudta, hogy az SQL Database beépített intelligenciával automatikusan kezelheti a legjobban teljesítő lekérdezések végrehajtási tervét az adatbázisok?
>
> Folyamatos teljesítményoptimalizálás az SQL Database, javasoljuk, hogy engedélyezze [SQL Database automatikus finomhangolása](sql-database-automatic-tuning.md). Ezt az egyedi funkciót az SQL Database beépített intelligenciával folyamatosan figyeli az SQL-adatbázis, és automatikusan hangolja és hoz létre a legjobban teljesítő lekérdezések végrehajtási tervét az adatbázisok.
>

## <a name="database-scoped-configuration-value-change"></a>Adatbázis-specifikus konfigurációs érték módosítása

### <a name="what-is-happening"></a>mi történik

A cserélhető eszközként észlelhetőnek teljesítmény minta azt jelzi, hogy egy feltételt, amelyben az adatbázishoz kötődő konfiguráció módosítása hatására a teljesítmény regressziós észlelt az elmúlt hét nap adatbázis-munkaterhelések viselkedésére képest. Ez a minta azt jelzi, hogy végzett az adatbázis-hatókörű konfiguráció változtatása nem a Microsoft hasznosabb lehet, ha az adatbázis teljesítményét.

Minden egyes adatbázis adatbázis-specifikus konfigurációs módosításokat is állítható be. Ez a konfiguráció szolgál – eseti alapon az adatbázis egyes teljesítményének optimalizálásához. Minden egyes adatbázis a következő beállítások konfigurálhatók: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES és egyértelmű PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai kimenetek adatbázishoz kötődő konfigurációs változások naplózása nemrég elvégzett képest az előző hét nap-munkaterhelések viselkedésére teljesítményromlást okozó. Visszaállíthatja az előző értékek a konfigurációs módosítások. Emellett hangolhassa értékkel a kívánt teljesítményszintet eléréséig. Adatbázis-hatókör-konfigurációs értékeket az elfogadható teljesítmény hasonló adatbázisból másolhatja. Ha Ön nem lehet a teljesítményi hibák elhárítása, visszatérhet az alapértelmezett SQL-adatbázis alapértelmezett értékeket, és próbálja meg finomhangolására, ez a alapvető kezdve.

Adatbázishoz kötődő konfiguráció, és módosítja a konfigurációt a T-SQL-szintaxis optimalizálására vonatkozó további információkért lásd: [Alter database scoped configuration (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Lassú ügyféloldali

### <a name="what-is-happening"></a>mi történik

A cserélhető eszközként észlelhetőnek teljesítmény minta azt jelzi, hogy egy feltételt, amelyben az SQL database használatával az ügyfél nem tudja fogadni az adatbázisból a kimeneti gyors az adatbázis elküldi az eredményeket. SQL-adatbázis nem tárolja a végrehajtott lekérdezések eredményeit a pufferben lévő, mert lelassul, és megvárja, amíg az ügyfél által a továbbított lekérdezés kimeneteket a folytatás előtt. Ez az állapot is előfordulhat, hogy kell egy hálózathoz kapcsolódó, amely nem eléggé elég gyors kimeneteket az SQL database-ből a fogyasztó ügyfél továbbítására.

Ez az állapot akkor jön létre, csak akkor, ha a teljesítmény regresszió észleli az elmúlt hét nap adatbázis-munkaterhelések viselkedésére képest. Ezen teljesítményprobléma észlelt korábbi teljesítménybeli viselkedésének csak akkor, ha egy statisztikailag teljesítménycsökkenés képest.

### <a name="troubleshooting"></a>Hibaelhárítás

A cserélhető eszközként észlelhetőnek teljesítmény minta egy ügyféloldali feltételt jelzi. Hibaelhárítási szükség, az ügyféloldali alkalmazás vagy az ügyféloldali hálózat. A diagnosztikai napló jelenít meg a lekérdezés a kivonatok és a várakozási időt, amely úgy tűnik, hogy az ügyfél felhasználhatják az elmúlt két órában a legtöbb vár. Ez az információ alapjaként használható hibaelhárítási.

Optimalizálhatja a felhasználásához ezeket a lekérdezéseket, az alkalmazás teljesítményét. Emellett érdemes lehet lehetséges hálózati késési problémák. Mivel a teljesítményprobléma teljesítménycsökkenés az elmúlt hét nap meghatározása változása alapul, vizsgálja meg e legutóbbi alkalmazás vagy hálózat feltétel módosítások miatt a teljesítményesemény regressziós. 

## <a name="pricing-tier-downgrade"></a>Díjszabási szint alacsonyabb szintű

### <a name="what-is-happening"></a>mi történik

A cserélhető eszközként észlelhetőnek teljesítmény minta azt jelzi, hogy egy feltételt, amelyben az SQL Database-előfizetés a tarifacsomag visszaminősíteni volt. Az adatbázis számára elérhető erőforrásokat (dtu-k) csökkentését, mert a rendszer észlelt legördülő az aktuális adatbázis teljesítményét, az elmúlt hét nap alaptervhez képest.

Emellett lehet egy feltételt, amelyben az SQL Database-előfizetés a tarifacsomag visszaminősíteni volt, és ezután frissített magasabb szintre rövid időn belül. Az ideiglenes teljesítménycsökkenés észlelését a diagnosztikai napló árképzési szint alacsonyabb szintű és a frissítés részletei szakaszban használt kimeneti adattípus.

### <a name="troubleshooting"></a>Hibaelhárítás

Ha, alacsonyabb tarifacsomagra, ezért az SQL Database-hez elérhető dtu-k, és a teljesítmény elégedett, nincs semmi sem kell tennie. Ha már elégedetlen az SQL database szolgáltatás teljesítményének, alacsonyabb tarifacsomagra, csökkenteni az adatbázis-munkaterhelés, vagy érdemes növelni a magasabb szintű tarifacsomagot.

## <a name="recommended-troubleshooting-flow"></a>Javasolt hibaelhárítási folyamat

 Kövesse a folyamatábra a teljesítménnyel kapcsolatos problémáinak elhárítása Intelligent Insights használatával egy ajánlott módja.

Hozzáférés Intelligent Insights nyissa meg az Azure SQL Analytics az Azure Portalon keresztül. Próbálja meg a bejövő teljesítményriasztás keresse meg, és kattintson rá. Azonosítsa, hogy mi történik az észlelések oldalon. Figyelje meg a megadott kiváltó okok elemzése a probléma, a lekérdezés szövege, a lekérdezési idő trendeket és a incidens fejlődést szem előtt tartva. Próbálja meg a probléma megoldásához az Intelligent Insights ajánlás problémák enyhítését célzó a teljesítményprobléma használatával. 

[![Hibaelhárítási folyamatábrája](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Válassza ki a folyamatábra letöltése PDF-verzió.

Intelligent Insights általában egy órán keresztül, és a teljesítménnyel kapcsolatos problémák okának van szüksége. Ha nem találja a problémát az Intelligent Insights, és fontos, hogy Ön, használja a Query Store kell azonosítania a teljesítményprobléma oka. (Ezek a problémák általában kevesebb mint egy órával korábbiak.) További információkért lásd: [teljesítmény figyelése a Query Store segítségével](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>További lépések
- Ismerje meg, [Intelligent Insights](sql-database-intelligent-insights.md) fogalmakat.
- Használja a [Intelligent Insights az Azure SQL Database teljesítményét diagnosztikai napló](sql-database-intelligent-insights-use-diagnostics-log.md).
- A figyelő [Azure SQL Database az Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Ismerje meg, hogyan [gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
