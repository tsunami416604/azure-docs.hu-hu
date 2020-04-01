---
title: Az Azure Stream Analytics kimeneteinek hibaelhárítása
description: Ez a cikk az Azure Stream Analytics-feladatok kimeneti kapcsolatainak hibaelhárítási technikáit ismerteti.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 305632a0faa1eb7e217e86d36c5159e557df7aaf
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409252"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Az Azure Stream Analytics kimeneteinek hibaelhárítása

Ez a cikk ismerteti az Azure Stream Analytics kimeneti kapcsolatokkal kapcsolatos gyakori problémákat, a kimeneti problémák elhárításának módját és a problémák megoldásának módját. Számos hibaelhárítási lépéshez engedélyezni kell a diagnosztikai naplókat a Stream Analytics-feladathoz. Ha nincs engedélyezve a diagnosztikai naplók, olvassa el [az Azure Stream Analytics hibaelhárítása diagnosztikai naplók használatával című témakört.](stream-analytics-job-diagnostic-logs.md)

## <a name="output-not-produced-by-job"></a>Nem a feladat által előállított kimenet

1.  Ellenőrizze a kimenetekhez való kapcsolódást a **Kapcsolat tesztelése** gombbal az egyes kimenetekhez.

2.  Tekintse meg [**a figyelési metrikákat**](stream-analytics-monitoring.md) a **Figyelő** lapon. Mivel az értékek összesítve vannak, a metrikák néhány percet késnek.
   * Ha a bemeneti események nagyobbak, mint 0, a feladat képes olvasni a bemeneti adatokat. Ha a bemeneti események nem nagyobbak, mint 0, akkor probléma van a feladat bemeneti. A [bemeneti kapcsolatok elhárításáról](stream-analytics-troubleshoot-input.md) a bemeneti kapcsolatokkal kapcsolatos problémák elhárításáról további információt olvashat.
   * Ha az adatkonverziós hibák nagyobbak, mint a 0 és a hegymászás, tekintse meg [az Azure Stream Analytics-adatok hibáit](data-errors.md) az adatkonverziós hibákrészletes információkért.
   * Ha a futásidejű hibák száma 0-nál nagyobb, a feladat adatokat fogadhat, de a lekérdezés feldolgozása közben hibákat generál. A hibák megkereséséhez nyissa meg a [naplókat,](../azure-resource-manager/management/view-activity-logs.md) és szűrje a *Sikertelen* állapotot.
   * Ha az InputEvents nagyobb, mint 0, és a OutputEvents értéke 0, az alábbi adatok egyike igaz:
      * A lekérdezés feldolgozása nulla kimeneti eseményt eredményezett.
      * Az események vagy mezők hibásan formázhatók, így a lekérdezés feldolgozása után nulla kimenet jön létre.
      * A feladat nem tudta lekérni az adatokat a kimeneti fogadó ban kapcsolódási vagy hitelesítési okokból.

   Az összes korábban említett hibaesetén a műveletek naplóüzenetei további részleteket magyaráznak (beleértve a történéseket is), kivéve azokat az eseteket, amikor a lekérdezési logika kiszűrte az összes eseményt. Ha több esemény feldolgozása hibákat generál, a hibák 10 percenként összesítve lesznek.

## <a name="job-output-is-delayed"></a>A feladat kimenete késik

### <a name="first-output-is-delayed"></a>Az első kimenet késik

Stream Analytics-feladat elindításakor a bemeneti eseményeket beolvassa a rendszer, azonban bizonyos helyzetekben késés lehet tapasztalható a kimenet előállításában.

Az időbeli lekérdezési elemek nagy időértékei hozzájárulhatnak a kimeneti késleltetéshez. A nagy időablakok on-ra történő helyes kimenet létrehozásához a streamelési feladat a lehető legutolsó (legfeljebb hét nappal ezelőtti) adatok olvasásával indul el az időablak kitöltéséhez. Ez alatt az idő alatt nem jön létre kimenet, amíg a függőben lévő bemeneti események felzárkózási olvasása be nem fejeződik. Ez a probléma akkor jelentkezhet, amikor a rendszer frissíti a streamelési feladatokat, így újraindítja a feladatot. Az ilyen frissítések általában néhány havonta egyszer történnek.

Ezért a Stream Analytics-lekérdezés tervezésekor körültekintően járjon el. Ha a feladat lekérdezési szintaxisában nagy időablakot (több mint több órát, legfeljebb hét napot) használ a feladat lekérdezési szintaxisában lévő időbeli elemekhez, az az első kimenet késleltetéséhez vezethet a feladat indításakor vagy újraindításakor.  

Az ilyen típusú első kimeneti késleltetés egyik megoldása a lekérdezési párhuzamosítási technikák használata (az adatok particionálása), vagy további streamelési egységek hozzáadása az átviteli teljesítmény javításához, amíg a feladat utoléri.  További információ: [Szempontok a Stream Analytics-feladatok létrehozásakor](stream-analytics-concepts-checkpoint-replay.md)

Ezek a tényezők befolyásolják az első generált kimenet időszerűségét:

1. Ablakos aggregátumok használata (GROUP BY of Tumbling, Hopping és Sliding ablakok)
   - A bukdácsoló vagy ugráló ablak aggregátumok, eredmények jönnek létre a végén az ablak időkeret.
   - Csúszó ablak esetén az eredmények akkor jönnek létre, amikor egy esemény belép a csúszóablakba, vagy kilép a csúszóablakból.
   - Ha nagy ablakméretet szeretne használni (> 1 óra), akkor a legjobb, ha az ugró vagy csúszó ablakot választja, hogy gyakrabban láthassa a kimenetet.

2. Időbeli illesztések használata (JOIN with DATEDIFF)
   - A mérkőzések akkor jönnek létre, amikor az egyező események mindkét oldala megérkezik.
   - Az egyezést nem tartalmazó adatok (LEFT OUTER JOIN) a DATEDIFF ablak végén jönnek létre a bal oldali minden egyes eseményhez képest.

3. Időbeli analitikus függvények használata (ISFIRST, LAST és LAG LIMIT IDŐTARTAM-szal)
   - Az analitikus függvények esetében a kimenet minden eseményhez létrejön, nincs késés.

### <a name="output-falls-behind"></a>A kimenet elmarad

A feladat normál működése során, ha úgy találja, hogy a munka kimenete lemarad (hosszabb és hosszabb késleltetés), az alábbi tényezők vizsgálatával meghatározhatja a kiváltó okokat:
- Azt jelzi, hogy az alsó bb rétegbeli fogadó szabályozott-e
- Azt jelzi, hogy az upstream forrás szabályozott-e
- Azt jelzi, hogy a lekérdezés feldolgozási logikája számításigényes-e

Ezeknek a részleteknek a megtekintéséhez az Azure Portalon válassza ki a streamelési feladatot, és válassza a **Feladatdiagramot.** Minden egyes bemeneti, partíciónkénti hátralék eseménymetrika. Ha a hátralék esemény metrikája folyamatosan növekszik, ez azt jelzi, hogy a rendszer erőforrásai korlátozottak. Potenciálisan ez a kimeneti fogadó szabályozásának vagy a magas CPU-nak köszönhető. A feladatdiagram használatáról további információt az [Adatközpontú hibakeresés a feladatdiagram használatával című témakörben talál.](stream-analytics-job-diagram-with-metrics.md)

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Kulcsmegsértési figyelmeztetés az Azure SQL Database kimenetével

Amikor az Azure SQL-adatbázist kimenetként konfigurálja egy Stream Analytics-feladathoz, tömegesen illeszti be a rekordokat a céltáblába. Általánosságban elmondható, hogy az Azure stream analytics [garantálja legalább egyszer a](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) kimeneti fogadó, az egyik továbbra is [pontosan egyszeri kézbesítés]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) sql kimenet, ha az SQL-tábla egy egyedi megkötés definiálva.

Miután egyedi kulcsmegkötések vannak beállítva az SQL-táblában, és vannak ismétlődő rekordok beszúrása az SQL-táblába, az Azure Stream Analytics eltávolítja a duplikált rekordot. Az adatokat kötegekre osztja fel, és rekurzívmódon beilleszti a kötegeket, amíg egyetlen ismétlődő rekordot nem talál. Ha a streamelési feladat jelentős számú ismétlődő sortal rendelkezik, ez a felosztási és beszúrási folyamat figyelmen kívül hagyja az ismétlődéseket egyenként, ami kevésbé hatékony és időigényes. Ha az elmúlt egy órában több kulcsmegsértési figyelmeztető üzenetet lát a tevékenységnaplóban, akkor valószínű, hogy az SQL-kimenet lelassítja a teljes feladatot.

A probléma megoldásához a IGNORE_DUP_KEY beállítás engedélyezésével [konfigurálnia]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) kell a kulcsmegsértést okozó indexet. Ennek a beállításnak az engedélyezése lehetővé teszi, hogy az SQL figyelmen kívül hagyja az ismétlődő értékeket a tömeges beszúrások során, és az SQL Azure egyszerűen egy figyelmeztető üzenetet hoz létre hiba helyett. Az Azure Stream Analytics már nem okoz elsődleges kulcsmegsértési hibákat.

Vegye figyelembe a következő megfigyeléseket, amikor IGNORE_DUP_KEY többféle indexhez konfigurálja:

* Nem állíthat be IGNORE_DUP_KEY elsődleges kulcsra vagy egyedi megkötésre, amely ALTER INDEX-et használ, el kell dobnia, majd újra létre kell hoznia az indexet.  
* A IGNORE_DUP_KEY beállítás átírása az ALTER INDEX használatával állítható be egy egyedi indexhez, amely eltér az ELSŐDLEGES KULCS/EGYEDI megkötéstől, és a CREATE INDEX vagy INDEX definíció használatával jön létre.  

* IGNORE_DUP_KEY nem vonatkozik az oszloptároló-indexekre, mert az ilyen indexek en nem kényszerítheti az egyediséget.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Az oszlopneveket az Azure Stream Analytics kisbetűsen határozza meg
Az eredeti kompatibilitási szint (1.0) használatakor az Azure Stream Analytics az oszlopnevek kisbetűsre történő módosítását használta. Ezt a viselkedést a későbbi kompatibilitási szinteken javították. Az eset megőrzése érdekében azt tanácsoljuk ügyfeleinknek, hogy az 1.1-es és újabb kompatibilitási szintre lépjenek. Az Azure Stream [Analytics-feladatok kompatibilitási szintjéről](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)további információt talál.

## <a name="get-help"></a>Segítségkérés

További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
