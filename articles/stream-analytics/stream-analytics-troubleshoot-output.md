---
title: Azure Stream Analytics kimenetek hibáinak megoldása
description: Ez a cikk a Azure Stream Analytics-feladatokban lévő kimeneti kapcsolatok hibaelhárítási módszereit ismerteti.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 5652df0cf142af2ff96590368892530abcb3d667
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133229"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics kimenetek hibáinak megoldása

Ez a cikk a Azure Stream Analytics kimeneti kapcsolataival kapcsolatos gyakori problémákat, a kimeneti problémák elhárítását és a problémák megoldását ismerteti. Számos hibaelhárítási lépés esetén engedélyezni kell az erőforrás-és egyéb diagnosztikai naplókat a Stream Analytics feladatokhoz. Ha nincs engedélyezve az erőforrás-naplók, tekintse meg [a Azure stream Analytics az erőforrás-naplók használatával történő hibakeresését](stream-analytics-job-diagnostic-logs.md)ismertető témakört.

## <a name="output-not-produced-by-job"></a>Nem a feladatok által előállított kimenet

1.  Ellenőrizze a kimenetek kapcsolatát az egyes kimenetek **kapcsolat tesztelése** gombjának használatával.

2.  Tekintse meg a [**figyelési metrikákat**](stream-analytics-monitoring.md) a **figyelés** lapon. Mivel az értékek összesítve vannak, a metrikák néhány perc késéssel lesznek késleltetve.
   * Ha a bemeneti események értéke 0-nál nagyobb, a feladatnak be kell olvasnia a bemeneti adatokat. Ha a bemeneti események nem nagyobbak 0-nál, akkor probléma van a feladathoz megadott bemenettel. A bemeneti kapcsolatokkal kapcsolatos problémák elhárításához tekintse meg a [bemeneti kapcsolatok hibaelhárítása](stream-analytics-troubleshoot-input.md) című témakört.
   * Ha az Adatátalakítási hibák értéke nagyobb, mint 0 és a hegymászás, tekintse meg az Adatátalakítási hibákkal kapcsolatos részletes információkat a [Azure stream Analytics adathibák](data-errors.md) című részben.
   * Ha a futásidejű hibák 0-nál nagyobbak, a feladatok fogadhatnak, de hibákat generálnak a lekérdezés feldolgozásakor. A hibák megkereséséhez nyissa meg a [naplókat](../azure-resource-manager/management/view-activity-logs.md) , és szűrje a *sikertelen* állapotot.
   * Ha a InputEvents nagyobb, mint 0, és a OutputEvents értéke 0, az alábbiak egyike igaz:
      * A lekérdezés feldolgozása nulla kimeneti eseményt eredményezett.
      * Lehet, hogy az események vagy a mezők helytelenek, ami nulla kimenetet eredményez a lekérdezés feldolgozása után.
      * A feladatsor nem tudott adatokat leküldeni a kimeneti fogadónak kapcsolati vagy hitelesítési okokból.

   Az összes korábban említett hiba esetén az Operations log-üzenetek további részleteket (köztük a mi történik) ismertetik, kivéve azokat az eseteket, ahol a lekérdezési logika az összes eseményt kiszűrte. Ha több esemény feldolgozása hibát okoz, a hibák 10 percenként lesznek összesítve.

## <a name="job-output-is-delayed"></a>A feladat kimenete késik

### <a name="first-output-is-delayed"></a>Az első kimenet késleltetve

Stream Analytics-feladat elindításakor a bemeneti eseményeket beolvassa a rendszer, azonban bizonyos helyzetekben késés lehet tapasztalható a kimenet előállításában.

Az időbeli lekérdezési elemek nagy időbeli értékei hozzájárulhatnak a kimeneti késleltetéshez. A nagy idő alatt a Windows megfelelő kimenetének létrehozásához a folyamatos átviteli feladatot a rendszer az adatok a lehető legkésőbbi időpontban történő beolvasásával kezdi meg (akár hét nappal ezelőtt) az időablak kitöltéséhez. Ebben az időszakban nem jön létre kimenet, amíg a függőben lévő bemeneti események beolvasása be nem fejeződik. Ez a probléma akkor is felhasználható, ha a rendszer frissíti a folyamatos átviteli feladatokat, így újraindítja a feladatot. Az ilyen verziófrissítések általában néhány havonta egyszer történnek.

Ezért a Stream Analytics-lekérdezés tervezésekor a mérlegelési jogkörrel kell megtervezni. Ha nagy időintervallumot használ (több, mint több óra, legfeljebb hét nap) a feladatok lekérdezési szintaxisában lévő időbeli elemeknél, a művelet elindításakor vagy újraindításakor késleltetést eredményezhet az első kimenetnél.  

Az ilyen típusú első kimeneti késések egyike a lekérdezési párhuzamos technikák használata (az adatok particionálásával), vagy több folyamatos átviteli egység hozzáadásával növelheti az átviteli sebességet, amíg a feladatok fel nem zárkóznak.  További információ: [stream Analytics feladatok létrehozásakor megfontolandó szempontok](stream-analytics-concepts-checkpoint-replay.md)

Ezek a tényezők befolyásolják az első generált kimenet időszerűségét:

1. Ablakos összesítések használata (kiesési, átugrási és csúszó ablakok CSOPORTOSÍTÁSa)
   - Az ablakos összesítések esetében az eredmények az ablak időkeretének végén jönnek létre.
   - Egy csúszó ablak esetében az eredmények akkor jönnek létre, amikor egy esemény bekerül a csúszó ablakba, vagy kilép onnan.
   - Ha nagyméretű ablakméret használatát tervezi (> 1 órát), a legjobb megoldás a beugró vagy csúszó ablak kiválasztása, hogy a kimenetet gyakrabban lássuk.

2. Időbeli illesztések használata (JOIN with DATEDIFF)
   - A egyezések akkor jönnek létre, amikor az egyeztetett események mindkét oldalán megérkeznek.
   - A egyezést nem tartalmazó adat (a bal oldali külső illesztés) a DATEDIFF ablakának végén jön létre, a bal oldali eseményekre vonatkozóan.

3. Időbeli elemzési függvények használata (ISFIRST, LAST és LAG a korlát IDŐTARTAMával)
   - Az analitikai függvények esetében a kimenet minden eseményhez létrejön, nincs késés.

### <a name="output-falls-behind"></a>A kimenet elesik

Ha úgy találja, hogy a feladatok kimenete (több, mint több késés) miatt a feladatok normál működése alatt áll, a kiváltó okokat a következő tényezők vizsgálatával lehet meghatározni:
- Az alsóbb rétegbeli fogadó szabályozása
- Azt jelzi, hogy a felsőbb rétegbeli forrás szabályozva van-e
- Azt jelzi, hogy a lekérdezés feldolgozási logikája nagy számítási igényű-e

A részletek megtekintéséhez a Azure Portal válassza ki a folyamatos átviteli feladatot, majd válassza ki a **feladathoz tartozó diagramot**. Minden egyes bemenet esetében a partíción várakozó fájlok száma az esemény metrikája. Ha a várakozó események mérőszáma továbbra is növekszik, a rendszer azt jelzi, hogy a rendszererőforrások korlátozottak. Lehetséges, hogy a kimeneti fogadó szabályozása vagy a nagy CPU okozza. A feladatütemezés használatával kapcsolatos további információkért lásd: [adatvezérelt hibakeresés a feladatütemezés használatával](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Kulcs megsértése figyelmeztetés Azure SQL Database kimenettel

Ha az Azure SQL Database-t kimenetként konfigurálja egy Stream Analytics feladatokhoz, a rekordok tömeges beszúrása a céltáblaba. Általánosságban elmondható, hogy az Azure stream Analytics [legalább egyszer](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) megtartja a kimeneti fogadónak való kézbesítést, de az SQL-tábla egyedi korlátozásának meghatározásakor továbbra is [pontosan egyszeri kézbesítést ÉRHET]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) el az SQL-kimenethez.

Miután beállította az egyedi kulcsokra vonatkozó korlátozásokat az SQL-táblán, és az SQL-táblába ismétlődő rekordok vannak beszúrva, Azure Stream Analytics eltávolítja az ismétlődő rekordot. Az adatok kötegekre bontása és a kötegek rekurzív beillesztése, amíg egyetlen duplikált rekord nem található. Ha a folyamatos átviteli feladatokhoz jelentős számú ismétlődő sor tartozik, a felosztási és beillesztési folyamatnak figyelmen kívül kell hagynia az ismétlődéseket, ami kevésbé hatékony és időigényes. Ha az elmúlt órában több kulcs megsértését jelző figyelmeztető üzenet jelenik meg a tevékenység naplójában, akkor valószínű, hogy az SQL-kimenet lelassítja a teljes feladatot.

A probléma megoldásához [konfigurálja azt az indexet]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , amely a kulcs megsértését okozza a IGNORE_DUP_KEY beállítás engedélyezésével. Ha engedélyezi ezt a beállítást, az SQL figyelmen kívül hagyja az ismétlődő értékeket a tömeges beszúrások során, és a SQL Azure egyszerűen figyelmeztető üzenetet állít elő a hiba helyett. A Azure Stream Analytics az elsődleges kulcs megsértésével kapcsolatos hibákat már nem hozza létre.

A IGNORE_DUP_KEY különböző típusú indexekhez való konfigurálásakor vegye figyelembe a következő észrevételeket:

* Az ALTER INDEXet használó elsődleges kulcson vagy egyedi korlátozáson nem állítható be IGNORE_DUP_KEY, ezért el kell dobnia, majd újra létre kell hoznia az indexet.  
* Megadhatja az IGNORE_DUP_KEY beállítást az ALTER INDEX használatával egy egyedi indexhez, amely eltér az elsődleges kulcs/egyedi korlátozástól, és a CREATE INDEX vagy INDEX definition paranccsal hozható létre.  

* A IGNORE_DUP_KEY nem vonatkozik az oszlopok tárolására szolgáló indexekre, mert nem kényszerítheti ki az ilyen indexek egyediségét.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Az oszlopnevek Azure Stream Analytics
Az eredeti kompatibilitási szint (1,0) használatakor Azure Stream Analytics az oszlopnevek kisbetűsre való módosítására szolgál. Ez a viselkedés a későbbi kompatibilitási szinteken lett kijavítva. Az eset megőrzése érdekében javasoljuk, hogy az ügyfelek a 1,1-es és újabb kompatibilitási szintre térjenek át. [Azure stream Analytics-feladatok kompatibilitási szintjéről](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)további információt talál.

## <a name="get-help"></a>Segítségkérés

További segítségért próbálja ki a [Azure stream Analytics fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
