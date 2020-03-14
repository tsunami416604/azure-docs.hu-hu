---
title: Azure Stream Analytics kimenetek hibáinak megoldása
description: Ez a cikk a Azure Stream Analytics-feladatokban lévő kimeneti kapcsolatok hibaelhárítási módszereit ismerteti.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d40157523a074547885a14a3d92379f8e8b6f351
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254286"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics kimenetek hibáinak megoldása

Ez a lap a kimeneti kapcsolatokkal kapcsolatos gyakori problémákat, valamint a hibaelhárítást és azok megoldását ismerteti.

## <a name="output-not-produced-by-job"></a>Nem a feladatok által előállított kimenet
1.  Ellenőrizze a kimenetek kapcsolatát az egyes kimenetek **kapcsolat tesztelése** gombjának használatával.

2.  Tekintse meg a [**figyelési metrikákat**](stream-analytics-monitoring.md) a **figyelés** lapon. Mivel az értékek összesítve vannak, a metrikák néhány perc késéssel lesznek késleltetve.
    - Ha a bemeneti események > 0, a feladatban beolvashatók a bemeneti adatok. Ha a bemeneti események nem > 0, akkor:
      - Ha szeretné megtekinteni, hogy az adatforrás rendelkezik-e érvényes adattal, tekintse meg a [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)használatával. Ez az ellenőrzési művelet akkor érvényes, ha a feladatban az Event hub bemenetként van használatban.
      - Ellenőrizze, hogy az adatszerializálási formátum és az adatkódolás megegyezik-e a várt értékkel.
      - Ha a feladattípus egy Event hub-t használ, ellenőrizze, hogy az üzenet törzse *null értékű*-e.

    - Ha az Adatátalakítási hibák > 0 és a hegymászás, a következők lehetnek igazak:
      - A kimeneti esemény nem felel meg a cél fogadó sémájának.
      - Előfordulhat, hogy az esemény sémája nem egyezik meg a lekérdezésben szereplő események meghatározott vagy várt sémájával.
      - Előfordulhat, hogy az esemény egyes mezőinek adattípusa nem felel meg az elvárásoknak.

    - Ha a futásidejű hibák értéke > 0, az azt jelenti, hogy a feladatsor képes fogadni az adatok fogadását, de hibákat generál a lekérdezés feldolgozásakor.
      - A hibák megkereséséhez nyissa meg a [naplókat](../azure-resource-manager/management/view-activity-logs.md) , és szűrje a *sikertelen* állapotot.

    - Ha a InputEvents > 0 és a OutputEvents = 0 értéket, az azt jelenti, hogy az alábbiak egyike igaz:
      - A lekérdezés feldolgozása nulla kimeneti eseményt eredményezett.
      - Lehet, hogy az események vagy annak mezői helytelenek, ezért a lekérdezés feldolgozása után nulla kimenetet eredményeznek.
      - A feladatsor nem tudott adatokat leküldeni a kimeneti fogadónak kapcsolati vagy hitelesítési okokból.

    - Az összes korábban említett hiba esetén az Operations log-üzenetek további részleteket (köztük a mi történik) ismertetik, kivéve azokat az eseteket, ahol a lekérdezési logika az összes eseményt kiszűrte. Ha több esemény feldolgozását eredményezi, a Stream Analytics 10 percen belül naplózza az első három hibaüzenetet az operatív naplókba. Ezután letiltja a további azonos hibákat egy olyan üzenettel, amely szerint a "hibák túl gyorsan történnek, ezeket a rendszer letiltotta."

## <a name="job-output-is-delayed"></a>A feladatok kimenete késleltetve

### <a name="first-output-is-delayed"></a>Első kimeneti késleltetve
Stream Analytics-feladat indításakor a bemeneti események olvasása, de lehet késleltetés bizonyos körülmények között előállítása a kimenetben.

A historikus lekérdezések elemek nagy időértékek hozzájárulhat a kimeneti késés. A nagy windows keresztül megfelelő kimenet létrehozására a folyamatos átviteli feladat indítása adatok olvasása a legújabb idő lehetséges (hét napja), adja meg az időtartomány alapján. Ez idő alatt nincs kimenet jön létre az utólagos olvasási a szálankénti függőben lévő bemeneti események befejezéséig. Ez a probléma akkor merülhet fel, amikor a rendszer frissíti a folyamatos átviteli feladatok, így az a feladat újraindítása. Az ilyen frissítések általában egyszer minden néhány hónap során történik.

Ezért járjon el körültekintően, a Stream Analytics-lekérdezés tervezése során. Ha egy nagy időablakban (hét napja akár egynél több órát) historikus elemek a feladat lekérdezési szintaxisban, vezethet, késleltetés az az első kimeneti a feladat indításakor vagy újraindítása.  

Egy megoldás ehhez a kiterjesztéstípushoz első kimeneti késleltetés, hogy a lekérdezés ezerszer eljárások (az adatok particionálása), vagy adja hozzá a további Streamelési egységek, amíg a feladat behozza javítani szeretné.  További információ: [stream Analytics feladatok létrehozásakor megfontolandó szempontok](stream-analytics-concepts-checkpoint-replay.md)

Ezek a tényezők befolyásolják a határidővel, amely akkor jön létre az első kimeneti:

1. Használható az ablakos összesítéseket (csoport által az Átfedésmentes, segítségével tehetjük meg, és késleltetett windows)
   - Átfedésmentes vagy segítségével tehetjük meg ablak összesítések, eredmények ablak időkeretét végén jönnek létre.
   - A csúszóablakban az eredmények jön létre, amikor egy esemény kerül, vagy kilép a csúszóablakban.
   - Ha azt tervezi, nagy méretű ablak méretének (> 1 óra) használata, célszerű gyakran a kimenetben láthatja, hogy válassza ki a szórási vagy mozgó ablak.

2. A historikus illesztések (DATEDIFF csatlakoztatás) használata
   - Egyezések jönnek létre, amint az egyeztetett események mindkét oldalán érkezésekor.
   - A DATEDIFF ablak megállapodást minden esemény a bal oldali végén található adatokat, amely nem rendelkezik (bal oldali külső ILLESZTÉST) egyezést jön létre.

3. A historikus elemzési funkciók (ISFIRST, utolsó és a LIMIT DURATION LAG) használata
   - Az analitikai függvények a kimenet jön létre minden eseményhez, nem lesz késleltetés.

### <a name="output-falls-behind"></a>Kimeneti mögé
A feladat szokásos működés során a feladat kimenetének elmaradásban van mögött (hosszabb és hosszabb késéssel), ha tudja a okát megvizsgálásával ezek a tényezők:
- Hogy a rendszer szabályozza az alsóbb rétegbeli fogadó
- Attól a felsőbb rétegbeli forrás folyamatban van
- Hogy-e a feldolgozási logika, a lekérdezés a nagy számítási igényű

A részletek megtekintéséhez a Azure Portal válassza ki a folyamatos átviteli feladatot, majd válassza ki a **feladathoz tartozó diagramot**. Minden egyes bemenet nincs egy partíció várakozó események metrikánként. A várakozó események metrika is növekszik, ha azt jelzi, hogy a rendszer előrébb. Potenciálisan Ez az oka az, hogy a kimeneti fogadó szabályozás vagy a magas CPU. A feladatütemezés használatával kapcsolatos további információkért lásd: [adatvezérelt hibakeresés a feladatütemezés használatával](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Kulcs megsértése figyelmeztetés Azure SQL Database kimenettel

Azure SQL database egy Stream Analytics-feladat kimeneteként konfigurálásakor azt tömeges rekordok szúr be a céltáblázatban. Általánosságban elmondható, hogy az Azure stream Analytics [legalább egyszer](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) megtartja a kimeneti fogadónak való kézbesítést, de az SQL-tábla egyedi korlátozásának meghatározásakor továbbra is [pontosan egyszeri kézbesítést ÉRHET]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) el az SQL-kimenethez.

Miután egyedi kulcsra vonatkozó megkötések állíthatók be, az SQL-táblát, és SQL-táblába beszúrt ismétlődő rekordok, az Azure Stream Analytics eltávolítja az ismétlődő rekord. Ez felosztja a az adatok kötegek és rekurzív módon szúr be a kötegek, amíg talál egy egyetlen ismétlődő rekordot. Ha a folyamatos átviteli feladat ismétlődő sorok, ez a felosztás jelentős számú rendelkezik, és helyezze be a folyamat figyelmen kívül hagyja az ismétlődő egy kevésbé hatékony és időigényes feladat, amely rendelkezik. Ha több kulcsok protokollmegsértési figyelmeztető üzenetek a tevékenységnaplóban az elmúlt egy órán belül jelenik meg, akkor valószínű, hogy az SQL-kimenet lelassítanunk van-e a teljes feladat.

A probléma megoldásához [konfigurálja azt az indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , amely a kulcs megsértését okozza a IGNORE_DUP_KEY beállítás engedélyezésével. Ez a beállítás engedélyezése lehetővé teszi a duplikált értékek alapján SQL tömeges Beszúrások során figyelmen kívül, és SQL Azure egyszerűen egy figyelmeztető üzenet helyett hibát eredményez. Az Azure Stream Analytics nem eredményez többé elsődleges kulcsok protokollmegsértési hibák.

Vegye figyelembe a következő megfigyeléseken, számos különböző típusú indexeket az IGNORE_DUP_KEY konfigurálásakor:

* Elsődleges kulcs, vagy egy egyedi korlátozás, amely használja az ALTER INDEX az IGNORE_DUP_KEY nem állítható be kell, hogy dobja el és hozza létre újból az indexet.  
* Az IGNORE_DUP_KEY beállítást használja az ALTER INDEX egyedi index, amely nem azonos az elsődleges kulcs vagy UNIQUE megkötést, és a CREATE INDEX vagy az INDEX definícióját használatával létrehozott állíthatja be.  
* Mivel az ilyen indexek egyedisége nem kényszeríti IGNORE_DUP_KEY oszlopcentrikus indexek nem vonatkozik.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Az oszlopnevek Azure Stream Analytics
Az eredeti kompatibilitási szint (1,0) használatakor Azure Stream Analytics az oszlopnevek kisbetűsre való módosítására szolgál. Ez a viselkedés a későbbi kompatibilitási szinteken lett kijavítva. Az eset megőrzése érdekében javasoljuk, hogy az ügyfelek a 1,1-es és újabb kompatibilitási szintre térjenek át. [Azure stream Analytics-feladatok kompatibilitási szintjéről](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)további információt talál.


## <a name="get-help"></a>Segítségkérés

További segítségért próbálja ki a [Azure stream Analytics fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
