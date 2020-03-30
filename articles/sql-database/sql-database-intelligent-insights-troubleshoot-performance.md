---
title: Teljesítményproblémák elhárítása az Intelligent Insights segítségével
description: Az Intelligent Insights segítségével elháríthatja az Azure SQL Database teljesítményével kapcsolatos problémákat.
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
ms.openlocfilehash: 739bba7ed9ab4770a762c08fccc422ce048ae11d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214097"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Az Azure SQL Database teljesítményével kapcsolatos problémák elhárítása az Intelligent Insights segítségével

Ez a lap az Azure SQL Database és a felügyelt példány teljesítményével kapcsolatos problémákat tartalmaz, amelyeket az [Intelligent Insights](sql-database-intelligent-insights.md) erőforrásnapló ban észleltek. Metrikák és erőforrásnaplók streamelhetők az Azure Monitor naplóiba, az Azure Event [Hubsba,](../azure-monitor/insights/azure-sql.md) [az](../azure-monitor/platform/resource-logs-stream-event-hubs.md)Azure [Storageba](sql-database-metrics-diag-logging.md#stream-into-azure-storage)vagy egy külső megoldásba az egyéni DevOps-riasztási és jelentéskészítési képességekhez.

> [!NOTE]
> Az INTELLIGENT Insights használatával az SQL Database teljesítményével kapcsolatos gyors hibaelhárítási útmutatót ebben a dokumentumban az [Ajánlott hibaelhárítási folyamatábra](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) című témakörben találja.

## <a name="detectable-database-performance-patterns"></a>Észlelhető adatbázis-teljesítményminták

Az Intelligent Insights automatikusan észleli az Azure SQL Database adatbázisaival kapcsolatos teljesítményproblémákat a lekérdezés-végrehajtási várakozási idők, hibák vagy időtúlkérdések alapján. Az Intelligent Insights kimenetek teljesítménymintákat észleltek az SQL Database erőforrásnaplójában. A kimutatható teljesítménymintákat az alábbi táblázat foglalja össze.

| Észlelhető teljesítményminták | Az Azure SQL Database és a rugalmas készletek leírása | A felügyelt példányadatbázisainak leírása |
| :------------------- | ------------------- | ------------------- |
| [Erőforráskorlátok elérése](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | A figyelt előfizetésen elérhető erőforrások (DDO-k), adatbázis-feldolgozó szálak vagy a figyelt előfizetésen elérhető adatbázis-bejelentkezési munkamenetek felhasználása elérte a korlátokat. Ez hatással van az SQL Database teljesítményére. | A CPU-erőforrások felhasználása eléri a felügyelt példány korlátait. Ez hatással van az adatbázis teljesítményére. |
| [A munkaterhelés növelése](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | A munkaterhelés növelése vagy a munkaterhelés folyamatos felhalmozódása az adatbázisban észlelt. Ez hatással van az SQL Database teljesítményére. | A rendszer a munkaterhelés növekedését észlelte. Ez hatással van az adatbázis teljesítményére. |
| [Memórianyomás](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | A memóriatámogatást kérő dolgozóknak statisztikailag szignifikáns mennyiségű memóriafoglalásra kell várniuk, vagy a memóriatámogatást kérő dolgozók megnövekedett felhalmozódása létezik. Ez hatással van az SQL Database teljesítményére. | A memóriatámogatást kérő dolgozók statisztikailag szignifikáns ideig várják a memórialefoglalásokat. Ez hatással van az adatbázis teljesítményére. |
| [Zárolás](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | A rendszer az SQL-adatbázis teljesítményét befolyásoló túlzott adatbáziszárolást észlelt. | A rendszer túlzott adatbázis-zárolást észlelt, ami hatással volt az adatbázis teljesítményére. |
| [Megnövekedett MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | A párhuzamosság maximális foka (MAXDOP) megváltozott, ami hatással van a lekérdezés végrehajtási hatékonyságára. Ez hatással van az SQL Database teljesítményére. | A párhuzamosság maximális foka (MAXDOP) megváltozott, ami hatással van a lekérdezés végrehajtási hatékonyságára. Ez hatással van az adatbázis teljesítményére. |
| [Pagelatch versengés](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Több szál egyidejűleg próbál hozzáférni ugyanazokhoz a memórián belüli adatpufferlapokhoz, ami megnövekedett várakozási időt eredményez, és pagelatch versengést okoz. Ez hatással van az SQL-adatbázis teljesítményére. | Több szál egyidejűleg próbál hozzáférni ugyanazokhoz a memórián belüli adatpufferlapokhoz, ami megnövekedett várakozási időt eredményez, és pagelatch versengést okoz. Ez hatással van az adatbázis teljesítményére. |
| [Hiányzó index](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | A rendszer az SQL-adatbázis teljesítményét befolyásoló hiányzó indexet észlelt. | A rendszer hiányzó indexet észlelt, amely hatással volt az adatbázis teljesítményére. |
| [Új lekérdezés](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | A rendszer új lekérdezést észlelt, amely hatással volt az SQL Database általános teljesítményére. | A rendszer új lekérdezést észlelt, amely hatással volt az adatbázis általános teljesítményére. |
| [Megnövekedett várakozási statisztika](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | A rendszer az SQL-adatbázis teljesítményét befolyásoló megnövekedett adatbázis-várakozási időket észlelt. | A rendszer az adatbázis teljesítményét befolyásoló megnövekedett adatbázis-várakozási időket észlelt. |
| [TempDB-versengés](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Több szál próbál hozzáférni ugyanahhoz a TempDB-erőforráshoz, ami szűk keresztmetszetet okoz. Ez hatással van az SQL Database teljesítményére. | Több szál próbál hozzáférni ugyanahhoz a TempDB-erőforráshoz, ami szűk keresztmetszetet okoz. Ez hatással van az adatbázis teljesítményére. |
| [Rugalmas medence DTU hiány](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | A rugalmas készletben rendelkezésre álló eD-k hiánya hatással van az SQL Database teljesítményére. | A felügyelt példány nem érhető el, mert virtuálismag-modellt használ. |
| [Regresszió simára tervezése](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Új terv, vagy egy meglévő terv munkaterhelésének változása történt. Ez hatással van az SQL Database teljesítményére. | Új terv, vagy egy meglévő terv munkaterhelésének változása történt. Ez hatással van az adatbázis teljesítményére. |
| [Adatbázis-hatókörrel kapcsolatos konfigurációs érték módosítása](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | A rendszer az SQL-adatbázis konfigurációs módosítását észlelte, amely hatással volt az adatbázis teljesítményére. | A rendszer az adatbázis teljesítményét befolyásoló konfigurációs módosítást észlelt az adatbázisban. |
| [Lassú ügyfél](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | A lassú alkalmazásügyfél nem tudja elég gyorsan felhasználni az adatbázisból származó kimenetet. Ez hatással van az SQL Database teljesítményére. | A lassú alkalmazásügyfél nem tudja elég gyorsan felhasználni az adatbázisból származó kimenetet. Ez hatással van az adatbázis teljesítményére. |
| [Tarifacsomag visszaminősítése](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Tarifacsomag-visszaminősítési művelet csökkent a rendelkezésre álló erőforrások. Ez hatással van az SQL Database teljesítményére. | Tarifacsomag-visszaminősítési művelet csökkent a rendelkezésre álló erőforrások. Ez hatással van az adatbázis teljesítményére. |

> [!TIP]
> Az SQL Database folyamatos teljesítményoptimalizálásához engedélyezze az [Azure SQL Database automatikus hangolását.](sql-database-automatic-tuning.md) Az SQL Database beépített intelligenciájának ez az egyedülálló funkciója folyamatosan figyeli az SQL-adatbázist, automatikusan beállítja az indexeket, és lekérdezés-végrehajtási terv-javításokat alkalmaz.
>

A következő szakasz részletesebben ismerteti a kimutatható teljesítménymintákat.

## <a name="reaching-resource-limits"></a>Erőforráskorlátok elérése

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményminta egyesíti a teljesítmény problémák, amelyek a rendelkezésre álló erőforrás-korlátok elérése, a dolgozói korlátok és a munkamenet korlátok. A teljesítményprobléma észlelése után a diagnosztikai napló leíró mezője jelzi, hogy a teljesítményprobléma erőforrás-, dolgozói vagy munkamenet-korlátokhoz kapcsolódik-e.

Az SQL Database-erőforrások általában [DTU-](sql-database-what-is-a-dtu.md) vagy [virtuálismag-erőforrásokra](sql-database-service-tiers-vcore.md) hivatkoznak. Az erőforrás-korlátok elérésének mintáját a rendszer felismeri, ha a lekérdezés teljesítményének csökkenését a mért erőforrás-korlátok bármelyikének elérése okozza.

A munkamenet korlátozza az erőforrást az SQL-adatbázisba rendelkezésre álló egyidejű bejelentkezések számát jelöli. Ezt a teljesítménymintát akkor ismeri fel a rendszer, ha az SQL-adatbázisokhoz kapcsolódó alkalmazások elérték az adatbázishoz rendelkezésre álló egyidejű bejelentkezések számát. Ha az alkalmazások az adatbázisban elérhetőnél több munkamenetet próbálnak használni, az hatással van a lekérdezés teljesítményére.

A dolgozói korlátok elérése az erőforráskorlátok elérésének konkrét esete, mivel az elérhető dolgozók nem számítanak a DTU- vagy a virtuálismag-használatban. Az adatbázis dolgozói korlátainak elérése erőforrás-specifikus várakozási idők növekedését okozhatja, ami a lekérdezés teljesítményének csökkenését eredményezheti.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimenetek lekérdezési kikeresés, amely hatással volt a teljesítmény és az erőforrás-felhasználás százalékos. Ezt az információt kiindulási pontként használhatja az adatbázis-munkaterhelés optimalizálásához. Különösen optimalizálhatja a lekérdezéseket, amelyek befolyásolják a teljesítménycsökkenést indexek hozzáadásával. Vagy optimalizálhatja az alkalmazásokat egy egyenletesebb munkaterhelés-elosztással. Ha nem tudja csökkenteni a számítási feladatokat, vagy optimalizálást, fontolja meg az SQL-adatbázis-előfizetés tarifacsomag növelését a rendelkezésre álló erőforrások mennyiségének növelése érdekében.

Ha elérte a rendelkezésre álló munkamenet-korlátokat, optimalizálhatja az alkalmazásokat az adatbázisba bekövetkező bejelentkezések számának csökkentésével. Ha nem tudja csökkenteni az alkalmazásokból az adatbázisba történő bejelentkezések számát, fontolja meg az adatbázis tarifacsomagjának növelését. Vagy feloszthatja és áthelyezheti az adatbázist több adatbázisba a kiegyensúlyozottabb számítási feladatok elosztása érdekében.

A munkamenet-korlátok feloldására vonatkozó további javaslatokról az [SQL Database maximális bejelentkezési korlátainak kezelése című témakörben található.](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/) A kiszolgálói és előfizetési szintekkorlátairól az [SQL Database-kiszolgálón lévő erőforráskorlátok áttekintése](sql-database-resource-limits-database-server.md) című témakörben olvashat.

## <a name="workload-increase"></a>Munkaterhelés növelése

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményminta azonosítja a munkaterhelés növekedése által okozott problémákat, vagy súlyosabb formában a munkaterhelés-halmozás.

Ez az észlelés több metrika kombinációjával történik. A mért alapvető metrika a munkaterhelés növekedését észleli a korábbi számítási feladatok alapértékéhez képest. Az észlelés másik formája az aktív munkaszálak olyan nagy növekedésének mérésén alapul, amely elég nagy ahhoz, hogy befolyásolja a lekérdezés teljesítményét.

A súlyosabb formában a munkaterhelés előfordulhat, hogy folyamatosan felhalmozódnak, mivel az SQL-adatbázis nem képes kezelni a számítási feladatokat. Az eredmény egy folyamatosan növekvő számítási feladatok mérete, amely a munkaterhelés halmozási feltétel. Ennek a feltételnek köszönhetően a számítási feladatok a végrehajtásra vár. Ez a feltétel az egyik legsúlyosabb adatbázis-teljesítménnyel kapcsolatos problémát jelenti. Ezt a problémát a megszakított munkaszálak számának növekedése figyeli.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimeneti a lekérdezések száma, amelyek végrehajtása nőtt, és a lekérdezés kivonata a lekérdezés a legnagyobb mértékben járul hozzá a számítási feladatok növekedését. Ezt az információt kiindulási pontként használhatja a munkaterhelés optimalizálásához. A lekérdezés azonosított, mint a legnagyobb hozzájáruló a munkaterhelés növelése különösen hasznos kiindulási pontként.

Érdemes lehet a számítási feladatok egyenletesebben elosztani az adatbázisba. Fontolja meg a teljesítményt befolyásoló lekérdezés optimalizálását indexek hozzáadásával. Azt is előfordulhat, hogy a számítási feladatok között több adatbázis között. Ha ezek a megoldások nem lehetségesek, fontolja meg az SQL-adatbázis-előfizetés tarifacsomag jának növelését a rendelkezésre álló erőforrások mennyiségének növelése érdekében.

## <a name="memory-pressure"></a>Memóriaterhelés

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményminta a memórianyomás által okozott jelenlegi adatbázis-teljesítmény romlását jelzi, vagy annak súlyosabb formájában memóriahalmozási állapot, az elmúlt hét napos teljesítményalapkonfigurációhoz képest.

A memórianyomás olyan teljesítményfeltételt jelöl, amelyben nagy számú munkavégző szál igényel memóriatámogatást az SQL-adatbázisban. A nagy hangerő magas memóriakihasználtsági feltételt okoz, amelyben az SQL-adatbázis nem tudja hatékonyan lefoglalni a memóriát az azt kérő összes dolgozó számára. A probléma egyik leggyakoribb oka egyrészről az SQL-adatbázis számára rendelkezésre álló memória mennyiségével kapcsolatos. Másrészt a munkaterhelés növekedése okozza a munkaszálak növekedését és a memórianyomást.

A súlyosabb formája a memória nyomás a memória halom-up állapotban. Ez a feltétel azt jelzi, hogy a munkaszálak nagyobb száma igényel memóriatámogatást, mint ahány lekérdezés felszabadítja a memóriát. A memóriatámogatást kérő munkaszálak száma is folyamatosan növekedhet (halmozódhat fel), mert az SQL-adatbázis-motor nem tudja elég hatékonyan lefoglalni a memóriát az igény kielégítéséhez. A memóriahalmozási állapot az adatbázis egyik legsúlyosabb teljesítményproblémája.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló a memóriaobjektum-tároló adatait a magas memóriahasználat és a megfelelő időbélyegzők legmagasabb okaként megjelölt bélyegzővel (munkaszál) adja ki. Ezt az információt használhatja a hibaelhárítás alapjaként.

Optimalizálhatja vagy eltávolíthatja a legmagasabb memóriahasználattal rendelkező ügyintézőkkel kapcsolatos lekérdezéseket. Azt is ellenőrizheti, hogy nem olyan adatokat kérdez-e le, amelyeket nem kíván használni. Helyes gyakorlat, hogy mindig használja a WHERE záradékot a lekérdezések. Emellett azt javasoljuk, hogy hozzon létre nem fürtözött indexek az adatok kereséséhez, nem pedig beszkutat.

A számítási feladatok csökkentése több adatbázison keresztül történő optimalizálásával vagy elosztásával is csökkentheti a munkaterhelést. Vagy eloszthatja a számítási feladatok között több adatbázisok között. Ha ezek a megoldások nem lehetségesek, fontolja meg az SQL-adatbázis-előfizetés tarifacsomagjának növelését az adatbázis számára rendelkezésre álló memória-erőforrások mennyiségének növelése érdekében.

További hibaelhárítási javaslatokat a [Memória a meditáció: A titokzatos SQL Server memóriafogyasztó sok névvel](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994)című témakörben ismer.

## <a name="locking"></a>Zárolás

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményminta az aktuális adatbázis-teljesítmény romlását jelzi, amelyben a rendszer túlzott adatbázis-zárolást észlel az előző hét napos teljesítményalapkonfigurációhoz képest.

A modern RDBMS-ben a zárolás elengedhetetlen a többszálas rendszerek megvalósításához, amelyekben a teljesítményt több egyidejű dolgozó és párhuzamos adatbázis-tranzakciók futtatásával maximalizálják, ahol lehetséges. Ebben a környezetben a zárolás a beépített hozzáférési mechanizmusra utal, amelyben csak egyetlen tranzakció férhet hozzá kizárólag a szükséges sorokhoz, lapokhoz, táblákhoz és fájlokhoz, és nem versenyezhet az erőforrások másik tranzakciójával. Amikor a tranzakció, amely zárolta az erőforrásokat használatra történik velük, a zárolás az erőforrások felszabadulnak, amely lehetővé teszi, hogy más tranzakciók a szükséges erőforrások eléréséhez. A zárolásról további információt [a Zárolás az adatbázis-motorban című témakörben talál.](https://msdn.microsoft.com/library/ms190615.aspx)

Ha az SQL-motor által végrehajtott tranzakciók hosszabb ideig várnak a használatra zárolt erőforrások eléréséhez, ez a várakozási idő a számítási feladatok végrehajtási teljesítményének lassulását okozza.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimenetek zárolási részleteket, amelyek segítségével a hibaelhárítás alapjául szolgáló részleteket. Elemezheti a jelentett blokkoló lekérdezéseket, azaz a zárolási teljesítményromlást okozó lekérdezéseket, és eltávolíthatja azokat. Bizonyos esetekben előfordulhat, hogy sikeresen optimalizálja a blokkoló lekérdezéseket.

A probléma csökkentésének legegyszerűbb és legbiztonságosabb módja a tranzakciók röviden tartása és a legdrágább lekérdezések zárolási lábnyomának csökkentése. A műveletek nagy kötegét kisebb műveletekre bonthatja. Ajánlott eljárás, hogy csökkentse a lekérdezés zárolási lábnyomát azáltal, hogy a lekérdezés a lehető leghatékonyabb. Csökkentse a nagy beolvasásokat, mert növelik a holtpontok esélyét, és hátrányosan befolyásolják az adatbázis általános teljesítményét. A zárolást okozó azonosított lekérdezések esetén új indexeket hozhat létre, vagy oszlopokat adhat a meglévő indexhez, hogy elkerülje a táblázatvizsgálatokat.

További javaslatok: [A zárolások eszkalációja által az SQL Server ben okozott blokkolási problémák megoldása című témakörben található.](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)

## <a name="increased-maxdop"></a>Megnövekedett MAXDOP

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményminta azt a feltételt jelzi, amelyben a kiválasztott lekérdezés-végrehajtási terv párhuzamos, mint kellett volna. Az SQL Database lekérdezés-optimalizáló növelheti a számítási feladatok teljesítményét a lekérdezések párhuzamos végrehajtásával, hogy gyorsítsa fel a dolgokat, ahol lehetséges. Bizonyos esetekben a lekérdezést feldolgozó párhuzamos dolgozók több időt töltenek egymásra az eredmények szinkronizálásával és egyesítésével, mint ha ugyanazt a lekérdezést kevesebb párhuzamos feldolgozóval hajtják végre, vagy akár bizonyos esetekben egyetlen munkaszálhoz képest.

A szakértői rendszer elemzi az aktuális adatbázis teljesítményét az alapkonfigurációs időszakhoz képest. Azt határozza meg, hogy egy korábban futó lekérdezés lassabban fut-e, mint korábban, mert a lekérdezés végrehajtási terve párhuzamosabb, mint amilyennek lennie kellene.

Az SQL Database MAXDOP kiszolgálókonfigurációs beállításával szabályozható, hogy hány processzormag használható ugyanazon lekérdezés párhuzamos végrehajtásához.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimeneti lekérdezési kihágások olyan lekérdezésekhez kapcsolódó, amelyek a végrehajtás időtartama nőtt, mert párhuzamosabbak voltak, mint kellett volna. A napló a CXP várakozási időket is kiadja. Ez az idő azt az időt jelöli, amikor egyetlen szervező/koordinátor szál (0. szál) arra vár, hogy az összes többi szál befejeződzön, mielőtt egyesítené az eredményeket és haladna előre. Emellett a diagnosztikai napló a gyenge teljesítményű lekérdezések által a végrehajtás során összességében várakozó várakozási időket adja ki. Ezt az információt használhatja a hibaelhárítás alapjaként.

Először optimalizálja vagy egyszerűsítse az összetett lekérdezéseket. Jó gyakorlat, hogy a hosszú kötegelt feladatokat kisebbfeladatokra bontják. Emellett győződjön meg arról, hogy a lekérdezések támogatásához létrehozott indexeket. Manuálisan is kényszerítheti a maximális párhuzamosság (MAXDOP) egy lekérdezés, amely meg jelölt e gyenge teljesítő. A művelet T-SQL használatával történő konfigurálásához olvassa [el a MAXDOP-kiszolgáló konfigurációs beállításának konfigurálása című témakört.](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)

A MAXDOP-kiszolgáló konfigurációs beállításának alapértelmezett értékként nullára (0) való beállítása azt jelzi, hogy az SQL Database az összes rendelkezésre álló logikai PROCESSZORmagot használhatja a szálak párhuzamosítására egyetlen lekérdezés végrehajtásához. A MAXDOP beállítása egy (1) értékre azt jelzi, hogy egyetlen lekérdezés végrehajtásához csak egy mag használható. A gyakorlatban ez azt jelenti, hogy a párhuzamosság ki van kapcsolva. Az esetenkénti alapon, az adatbázis rendelkezésre álló magjaitól és a diagnosztikai naplóadataitól függően a MAXDOP-beállítást a párhuzamos lekérdezések végrehajtásához használt magok számára hangolhatja, amelyek megoldhatják a problémát az Ön esetében.

## <a name="pagelatch-contention"></a>Pagelatch versengés

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményminta azt jelzi, hogy az adatbázis aktuális számítási feladatok teljesítményromlása miatt pagelatch versengés az elmúlt hét napos számítási feladatok alapkonfigurációja.

A zárak az SQL Database által a többszálas sápítás engedélyezéséhez használt könnyű szinkronizálási mechanizmusok. Biztosítják az indexeket, adatlapokat és más belső struktúrákat tartalmazó memórián belüli struktúrák konzisztenciáját.

Az SQL adatbázisban sokféle zár található. Az egyszerűség kedvéért a pufferzárak a pufferkészlet ben lévő memórialapok védelmére szolgálnak. Az IO-zárak a pufferkészletbe még be nem töltött oldalak védelmére szolgálnak. Amikor a pufferkészlet egyik oldalára ír vagy olvas adatokat, a munkaszálnak először pufferzárat kell beszereznie az oldalhoz. Amikor egy munkaszál olyan lapelérésére próbál, amely még nem érhető el a memórián belüli pufferkészletben, i/o-kérelem érkezik a szükséges adatok betöltéséhez a tárolóból. Ez az eseménysorozat a teljesítménycsökkenés súlyosabb formáját jelzi.

Az oldalzárak versengése akkor következik be, amikor egyidejűleg több szál is megpróbál zárat szerezni ugyanazon a memórián belüli struktúrán, ami nagyobb várakozási időt eredményez a lekérdezés végrehajtásához. Pagelatch IO-versengés esetén, ha az adatokat a tárolóból kell elérni, ez a várakozási idő még nagyobb. Ez jelentősen befolyásolhatja a számítási feladatok teljesítményét. Pagelatch versengés a leggyakoribb forgatókönyv a szálak vár egymásra, és versenyeznek az erőforrások több CPU-rendszerek.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimenetek pagelatch versengés részleteit. Ezt az információt használhatja a hibaelhárítás alapjaként.

Mivel a pagelatch az SQL Database belső vezérlőmechanizmusa, automatikusan meghatározza, hogy mikor kell használni őket. Az alkalmazásdöntései, beleértve a sématervezést is, befolyásolhatják a lapozat viselkedését a zárak determinisztikus viselkedése miatt.

A zárversengés kezelésének egyik módja, ha egy szekvenciális indexkulcsot nem szekvenciális kulcsra cserél, hogy a lapkák egyenletesen oszlassa el az indextartományt. Általában az index egyik vezető oszlopa arányosan osztja el a munkaterhelést. Egy másik megfontolandó módszer a táblaparticionálás. A kivonatos particionálási séma létrehozása egy particionált tábla számított oszlopával gyakori módszer a túlzott zárversengés csökkentésére. A pagelatch IO-versengés esetén az indexek bevezetése segít a teljesítményprobléma enyhítésében.

További információ: [A zárversengés diagnosztizálása és feloldása az SQL Server kiszolgálón](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (PDF letöltés).

## <a name="missing-index"></a>Hiányzó index

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményminta azt jelzi, hogy az adatbázis aktuális számítási feladatának teljesítményromlása a hiányzó index miatt az elmúlt hét napos alaptervhez képest.

Az index a lekérdezések teljesítményének felgyorsítására szolgál. Gyors hozzáférést biztosít a táblaadatokhoz azáltal, hogy csökkenti a meglátogatott vagy beolvasandó adatkészlet-oldalak számát.

A teljesítményromlást okozó konkrét lekérdezéseket ez az észlelés azonosítja, amelyek esetében az indexek létrehozása előnyös lenne a teljesítmény relevancia.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimeneti lekérdezési kihágásai a számítási feladatok teljesítményét befolyásolólekérdezésekhez. Ezekhez a lekérdezésekhez indexeket hozhat létre. Optimalizálhatja vagy eltávolíthatja ezeket a lekérdezéseket is, ha nincs rájuk szükség. Egy jó teljesítmény gyakorlat, hogy ne kérdezzen adatokat, amelyek et nem használ.

> [!TIP]
> Tudta, hogy az SQL Database beépített intelligenciája automatikusan kezelheti az adatbázisok legjobban teljesítő indexeit?
>
> Az SQL Database folyamatos teljesítményoptimalizálásához azt javasoljuk, hogy engedélyezze [az SQL Database automatikus hangolását.](sql-database-automatic-tuning.md) Az SQL Database beépített intelligenciájának ez az egyedülálló funkciója folyamatosan figyeli az SQL-adatbázist, és automatikusan hangolja és létrehozza az adatbázisok indexeit.
>

## <a name="new-query"></a>Új lekérdezés

### <a name="what-is-happening"></a>mi történik

Ez a teljesítményminta azt jelzi, hogy a rendszer olyan új lekérdezést észlel, amely rosszul teljesít, és befolyásolja a munkaterhelés teljesítményét a hétnapos teljesítményalapkonfigurációhoz képest.

A jól teljesítő lekérdezés írása néha kihívást jelentő feladat lehet. A lekérdezések írásáról további információt az [SQL-lekérdezések írása](https://msdn.microsoft.com/library/bb264565.aspx)című témakörben talál. A lekérdezés teljesítményének optimalizálásához olvassa el a [Lekérdezés hangolása című témakört.](https://msdn.microsoft.com/library/ms176005.aspx)

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló legfeljebb két új, leginkább cpu-fogyasztó lekérdezést, köztük a lekérdezési kihálásokat adja ki. Mivel az észlelt lekérdezés hatással van a számítási feladatok teljesítményére, optimalizálhatja a lekérdezést. Helyes gyakorlat, hogy csak a használandó adatokat olvassa be. Azt is javasoljuk, hogy a lekérdezések egy WHERE záradékkal. Azt is javasoljuk, hogy egyszerűsítse az összetett lekérdezéseket, és bontsa fel őket kisebb lekérdezésekre. Egy másik jó gyakorlat, hogy a nagy köteges lekérdezéseket kisebb kötegelt lekérdezésekre bontja. Az új lekérdezések indexeinek bevezetése általában jó gyakorlat a teljesítményprobléma csökkentésére.

Fontolja meg [az Azure SQL Database Query Performance Insight használatát.](sql-database-query-performance.md)

## <a name="increased-wait-statistic"></a>Megnövekedett várakozási statisztika

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményminta a számítási feladatok teljesítményének csökkenését jelzi, amelyben a gyenge teljesítményű lekérdezéseket azonosítja a rendszer az elmúlt hét napos számítási feladatok alapértékéhez képest.

Ebben az esetben a rendszer nem tudja osztályozni a gyengén teljesítő lekérdezéseket más szabványos észlelhető teljesítménykategóriák alá, de észlelte a regresszióért felelős várakozási statisztikát. Ezért úgy véli, hogy a *lekérdezések megnövekedett várakozási statisztikák*, ahol a várakozási statisztika felelős a regresszió is ki vannak téve.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló az érintett lekérdezések megnövekedett várakozási időre vonatkozó részleteire és lekérdezési kijelentésekre vonatkozó információkat adja ki.

Mivel a rendszer nem tudta azonosítani a gyenge teljesítményű lekérdezések kiváltó okát, a diagnosztikai információk jó kiindulópontot jelentenek a manuális hibaelhárításhoz. Optimalizálhatja ezeknek a lekérdezéseknek a teljesítményét. Egy jó gyakorlat, hogy csak a szükséges adatokat kell letölteni, és egyszerűsíteni és lebontani az összetett lekérdezéseket kisebbekre.

A lekérdezésteljesítmény optimalizálásáról a [Lekérdezés hangolása](https://msdn.microsoft.com/library/ms176005.aspx)című témakörben talál további információt.

## <a name="tempdb-contention"></a>TempDB-versengés

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményminta egy adatbázis-teljesítményfeltételt jelez, amelyben a tempDB-erőforrások elérésére próbáló szálak szűk keresztmetszete áll fenn. (Ez a feltétel nem i/o-val kapcsolatos.) A teljesítményprobléma tipikus forgatókönyve több száz egyidejű lekérdezés, amelyek mindegyike kis tempDB táblákat hoz létre, használ, majd eldob. A rendszer észlelte, hogy az azonos tempDB táblákat használó egyidejű lekérdezések száma nőtt, és elegendő statisztikai szignifikanciával az adatbázis teljesítményének befolyásolásához képest az előző hét napos teljesítményalapkonfigurációhoz képest.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló a tempDB versengés részleteit adja létre. Az információkat a hibaelhárítás kiindulópontjaként használhatja. Két dolgot tehet az ilyen típusú versengés enyhítésére és a teljes munkaterhelés átviteli terhelésének növelésére: Leállíthatja az ideiglenes táblák használatát. A memóriaoptimalizált táblákat is használhatja.

További információt a [Bevezetés a memóriaoptimalizált táblákba című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)

## <a name="elastic-pool-dtu-shortage"></a>Rugalmas medence DTU hiány

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményminta az aktuális adatbázis-számítási feladatok teljesítményének romlását jelzi az előző hét napos alaptervhez képest. Ez annak köszönhető, hogy az előfizetés rugalmas készletében kevés a rendelkezésre álló DRU-k.

Az SQL Database erőforrásait általában [DTU-erőforrásoknak](sql-database-purchase-models.md#dtu-based-purchasing-model)nevezik, amelyek a CPU és az IO (adat- és tranzakciónapló IO) erőforrások kevert mértékéből állnak. [Az Azure rugalmas készlet erőforrásait](sql-database-elastic-pool.md) a rendelkezésre álló eDTU-erőforrások készleteként használják, amelyek et több adatbázis között osztanak meg méretezési célokra. Ha a rugalmas készletben elérhető eDTU-erőforrások nem elég nagyok ahhoz, hogy a készlet összes adatbázisát támogassák, a rendszer rugalmas készlet DTU-hiányteljesítmény-problémát észlel.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló a rugalmas készlet adatait adja ki, felsorolja a legfelső DTU-fogyasztó adatbázisokat, és megadja a készlet a legfelső fogyasztó adatbázis által használt DTU bizonyos százalékát.

Mivel ez a teljesítményfeltétel több adatbázishoz kapcsolódik, amelyek ugyanazt az eDTU-készletet használják a rugalmas készletben, a hibaelhárítási lépések a legfelső DTU-fogyasztó adatbázisokra összpontosítanak. Csökkentheti a munkaterhelést a legjobban fogyasztó adatbázisok, amely magában foglalja az adatbázisok on a legigényesebb lekérdezések optimalizálása. Azt is biztosíthatja, hogy ne kérdezzen le nem használt adatokat. Egy másik megközelítés az alkalmazások optimalizálása a felső DTU-fogyasztó adatbázisok használatával, és a számítási feladatok több adatbázis közötti újraelosztása.

Ha a felső DTU-igényes adatbázisok aktuális számítási feladatának csökkentése és optimalizálása nem lehetséges, fontolja meg a rugalmas készlet tarifacsomag növelését. Ez a növekedés a rugalmas készletben rendelkezésre álló DRU-k növekedését eredményezi.

## <a name="plan-regression"></a>Regresszió simára tervezése

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményminta olyan állapotot jelöl, amelyben az SQL Database egy optimálistól elmaradó lekérdezés-végrehajtási tervet használ. Az optimálistól elmaradó terv általában nagyobb lekérdezés-végrehajtást eredményez, ami hosszabb várakozási időt eredményez az aktuális és egyéb lekérdezések esetében.

Az SQL-adatbázis határozza meg a lekérdezés végrehajtási terv a legkevesebb költséget a lekérdezés végrehajtása. A lekérdezések és a munkaterhelések típusának változásával néha a meglévő tervek már nem hatékonyak, vagy talán az SQL Database nem végzett jó értékelést. A javítás, lekérdezés végrehajtási tervek manuálisan kényszeríthető.

Ez a észlelhető teljesítményminta a terv regressziójának három különböző esetét egyesíti: új terv regresszió, régi tervregresszió és meglévő tervek módosított munkaterhelése. A terv regressziójának adott típusa a diagnosztikai napló *részletes* tulajdonsága tartalmazza.

Az új terv regressziós feltétele olyan állapot, amelyben az SQL Database megkezdi egy új lekérdezés-végrehajtási terv végrehajtását, amely nem olyan hatékony, mint a régi terv. A régi terv regressziós feltétel arra az állapotra utal, amikor az SQL Database egy új, hatékonyabb terv ről a régi tervre vált, ami nem olyan hatékony, mint az új terv. A meglévő tervek megváltozott számítási feladatok regresszióutal, hogy az állapot, amelyben a régi és az új tervek folyamatosan váltakoznak, az egyensúly megy inkább a rosszul teljesítő terv felé.

A terv regresszióiról a [Mi a terv regressziója az SQL Server ben.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/)

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló kimenetele a lekérdezés kimondottak, jó tervazonosító, hibás tervazonosító és lekérdezésazonosítók. Ezt az információt használhatja a hibaelhárítás alapjaként.

Elemezheti, hogy melyik terv jobban teljesít az adott lekérdezésekhez, amelyeket azonosíthat a megadott lekérdezési kibékítményekkel. Miután megállapította, hogy melyik terv működik jobban a lekérdezésekhez, manuálisan kényszerítheti azt.

További [információ: A terv regresszióinak megakadályozása című témakörből megtudhatja, hogy az SQL Server hogyan akadályozza meg a regressziók tervezését.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/)

> [!TIP]
> Tudta, hogy az SQL Database beépített intelligenciája automatikusan képes kezelni az adatbázisok legjobban teljesítő lekérdezés-végrehajtási terveit?
>
> Az SQL Database folyamatos teljesítményoptimalizálásához azt javasoljuk, hogy engedélyezze [az SQL Database automatikus hangolását.](sql-database-automatic-tuning.md) Az SQL Database beépített intelligenciájának ez az egyedülálló funkciója folyamatosan figyeli az SQL-adatbázist, és automatikusan hangolja és létrehozza az adatbázisok legjobban teljesítő lekérdezés-végrehajtási terveit.
>

## <a name="database-scoped-configuration-value-change"></a>Adatbázis-hatókörrel konfigurált érték módosítása

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményminta azt a feltételt jelzi, amelyben az adatbázis-hatókörű konfiguráció változása teljesítmény-regressziót okoz, amely az adatbázis korábbi hét napos számítási feladatának viselkedéséhez képest észlelhető. Ez a minta azt jelzi, hogy az adatbázis-hatókörű konfiguráció legutóbbi módosítása nem tűnik előnyösnek az adatbázis teljesítményére nézve.

Az adatbázis hatókörrel végzett konfigurációs módosításai minden egyes adatbázishoz beállíthatók. Ezt a konfigurációt eseti alapon használjuk az adatbázis egyéni teljesítményének optimalizálásához. Minden egyes adatbázishoz a következő beállítások konfigurálhatók: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES és CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Hibaelhárítás

A diagnosztikai napló olyan adatbázis-hatókörrel végzett konfigurációs módosításokat hajt végre, amelyek a közelmúltban történtek, és amelyek teljesítménycsökkenést okoztak az előző hétnapos számítási feladat viselkedéséhez képest. A konfigurációs módosításokat visszaállíthatja az előző értékekre. Az értéket érték szerint is behangolhatja, amíg el nem éri a kívánt teljesítményszintet. Az adatbázis-hatókör konfigurációs értékeit megfelelő teljesítménnyel másolhatja egy hasonló adatbázisból. Ha nem tudja elhárítani a teljesítményt, térjen vissza az SQL Database alapértelmezett értékeihez, és próbálja meg finomítani az alapkonfigurációtól kezdve.

Az adatbázis-hatókörrel kapcsolatos konfiguráció és a T-SQL szintaxis optimalizálásáról a konfiguráció módosítása című témakörben talál további információt az [Adatbázis-hatókörrel (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx)című témakörben talál.

## <a name="slow-client"></a>Lassú ügyfél

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményminta azt a feltételt jelzi, amelyben az SQL-adatbázist használó ügyfél nem tudja olyan gyorsan felhasználni az adatbázis kimenetét, mint az adatbázis az eredményeket. Mivel az SQL Database nem tárolja a végrehajtott lekérdezések eredményeit egy pufferben, lelassul, és megvárja, amíg az ügyfél felhasználja a továbbított lekérdezéskimeneteket a folytatás előtt. Ez a feltétel is lehet egy olyan hálózathoz, amely nem elég gyors ahhoz, hogy az SQL-adatbázis kimenetek továbbítására a fogyasztó ügyfél.

Ez a feltétel csak akkor jön létre, ha a rendszer teljesítményregressziót észlel az adatbázis korábbi hétnapos számítási feladatának viselkedéséhez képest. Ez a teljesítményprobléma csak akkor észlelhető, ha statisztikailag szignifikáns teljesítménycsökkenés következik be a korábbi teljesítményviselkedéshez képest.

### <a name="troubleshooting"></a>Hibaelhárítás

Ez a észlelhető teljesítményminta ügyféloldali állapotot jelez. Hibaelhárításszükséges az ügyféloldali alkalmazásban vagy az ügyféloldali hálózaton. A diagnosztikai napló kimenetele a lekérdezés kimondott, és a várakozási idő, hogy úgy tűnik, hogy várja a legtöbbet az ügyfél számára, hogy felhasználja őket az elmúlt két órában. Ezt az információt használhatja a hibaelhárítás alapjaként.

Optimalizálhatja az alkalmazás teljesítményét ezeknek a lekérdezéseknek a felhasználásához. A lehetséges hálózati késési problémákat is figyelembe veheti. Mivel a teljesítménycsökkenési probléma az utolsó hétnapos teljesítményalapkonfiguráció változásán alapult, megvizsgálhatja, hogy az alkalmazás vagy a hálózati állapot legutóbbi változásai okozták-e ezt a teljesítményregressziós eseményt.

## <a name="pricing-tier-downgrade"></a>Tarifacsomag visszaminősítése

### <a name="what-is-happening"></a>mi történik

Ez a észlelhető teljesítményminta azt a feltételt jelzi, amelyben az SQL Database-előfizetés tarifacsomagját visszaminősítették. Az adatbázis számára elérhető erőforrások (DDO-k) csökkenése miatt a rendszer az adatbázis aktuális teljesítményének csökkenését észlelte az előző hét napos alapértékhez képest.

Emellett lehet egy olyan feltétel, amelyben az SQL Database-előfizetés tarifacsomagját visszaminősítették, majd rövid időn belül magasabb szintre frissítették. Az ideiglenes teljesítménycsökkenés észlelése a diagnosztikai napló részletes szakaszában történik, mint a tarifacsomag-visszaminősítés és -frissítés.

### <a name="troubleshooting"></a>Hibaelhárítás

Ha csökkentette a tarifacsomagot, és így az SQL Database számára elérhető DIT-eket, és elégedett a teljesítménnyel, semmit sem kell tennie. Ha csökkentette a tarifacsomagot, és elégedetlen az SQL-adatbázis teljesítményével, csökkentse az adatbázis-számítási feladatokat, vagy fontolja meg a tarifacsomag magasabb szintre emelését.

## <a name="recommended-troubleshooting-flow"></a>Ajánlott hibaelhárítási folyamat

 Kövesse a folyamatábrát egy ajánlott megközelítés teljesítményproblémák intelligens insights használatával.

Az Azure SQL Analytics webhelyen keresztül elérheti az Intelligens elemzéseket az Azure SQL Analytics webhelyen keresztül. Próbálja meg megkeresni a bejövő teljesítményriasztást, és jelölje ki. Azonosítsa, hogy mi történik az észlelések oldalon. Figyelje meg a megadott kiváltó oka a probléma, lekérdezés szöveg, lekérdezési idő trendek és az incidensek alakulása. Próbálja meg megoldani a problémát az Intelligent Insights javaslat használatával a teljesítményprobléma csökkentésére.

[![Folyamatábra – hibaelhárítás](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> A FOLYAMATÁBRA kiválasztásával töltse le a PDF-verziót.

Az Intelligens elemzési adatoknak általában egy óra időre van szüksége a teljesítményprobléma kiváltó okainak elemzéséhez. Ha nem találja a problémát az Intelligens elemzési adatok ban, és ez fontos az Ön számára, használja a Lekérdezési tároló segítségével manuálisan azonosítani a probléma kiváltó okát. (Ezek a problémák általában egy óránál rövidebbek.) További információt a [Teljesítmény figyelése a Lekérdezéstároló használatával című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)

## <a name="next-steps"></a>További lépések

- Ismerje meg [az Intelligens elemzési](sql-database-intelligent-insights.md) fogalmakat.
- Használja az [Intelligent Insights Azure SQL Database teljesítménydiagnosztikai naplóját.](sql-database-intelligent-insights-use-diagnostics-log.md)
- Az [Azure SQL Database figyelése az Azure SQL Analytics használatával.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)
- Ismerje meg, hogyan [gyűjthet naplóadatokat az Azure-erőforrásokból.](../azure-monitor/platform/platform-logs-overview.md)
