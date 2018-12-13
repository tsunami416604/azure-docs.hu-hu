---
title: Az Azure Stream Analytics kimenetek hibaelhárítása
description: Ez a cikk ismerteti a kimenő kapcsolatokat, az Azure Stream Analytics-feladatok hibaelhárításához technikák.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 92cb427149e6e6cbddfb96c6e4488017641e6482
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164908"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Az Azure Stream Analytics kimenetek hibaelhárítása

Ezen a lapon a kimenő kapcsolatok és hibaelhárítása, és oldja meg, hogyan lehet gyakori problémákat ismerteti.

## <a name="output-not-produced-by-job"></a>Nincs feladat által létrehozott kimenet 
1.  Ellenőrizze a kapcsolatot a kimenetek használatával a **kapcsolat tesztelése** egyes kimeneti gombra.

2.  Tekintse meg [ **figyelési metrikákat** ](stream-analytics-monitoring.md) a a **figyelő** fülre. Összesítjük az értékeket, mert a metrikák késnek, néhány perc alatt.
    - Ha a bemeneti események > 0, a feladat a bemeneti adatok olvashatja. Ha bemeneti események nem > 0, majd:
      - Hogy az adatforrás tartozik-e érvényes adatokat, ellenőrizze az azt [Service Bus Explorerrel](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Ez az ellenőrzés érvényes, ha a feladat használ Event Hub bemeneti adatként.
      - Ellenőrizze, hogy az adatok szerializálási formátum és az adatok kódolásának vannak-e a várt módon.
      - Ha a feladat egy Eseményközpontba, ellenőrizze, hogy van-e az üzenet törzse *Null*.
      
    - Ha az Adatátalakítási hibák > 0 nagyobb és emelkedik, a következő okai lehetnek:
      - A cél fogadó sémája nem felel meg a kimeneti esemény. 
      - Az esemény sémája nem egyeznek a meghatározott vagy a várt séma a lekérdezésben az események.
      - Az egyes mezőit a adattípusokat az lehet, hogy nem egyezik meg elvárásainak.
      
    - Ha a futásidejű hibák > 0, az azt jelenti, hogy a feladat fogadhat az adatokat, de hibáinak generáló a lekérdezés feldolgozása során.
      - A hibák megkereséséhez nyissa meg a [Auditnaplók](../azure-resource-manager/resource-group-audit.md) és szűrheti a *sikertelen* állapotát.
      
    - Ha InputEvents outputevents értéke pedig > 0 = 0, az azt jelenti, hogy az alábbiak egyike igaz:
      - A lekérdezés feldolgozása nulla kimeneti eseményt eredményezett.
      - Események vagy a helytelen formátumú, nulla kimenetet eredményez a lekérdezés feldolgozása után lehet.
      - A feladat nem tudta adatok leküldése a kimeneti fogadó csatlakozási vagy hitelesítési okokból.
      
    - A korábban említett hiba esetekben a műveletnaplók üzenetei további részleteket (beleértve a Mi történik), kivéve azokban az esetekben, ahol a lekérdezés logikája minden eseményt kiszűrt. Több esemény feldolgozása hibákat okoz, ha a Stream Analytics az első három hibaüzenetek azonos típusú naplók 10 percen belül. Ezután elrejti a további hibákat jelenik meg: "Hiba történik túl gyorsan ezek elrejtéséről."
    
## <a name="job-output-is-delayed"></a>Feladat kimenetének késleltetve

### <a name="first-output-is-delayed"></a>Első kimeneti késleltetve
Stream Analytics-feladat indításakor a bemeneti események olvasása, de lehet késleltetés bizonyos körülmények között előállítása a kimenetben.

A historikus lekérdezések elemek nagy időértékek hozzájárulhat a kimeneti késés. A nagy windows keresztül megfelelő kimenet létrehozására a folyamatos átviteli feladat indítása adatok olvasása a legújabb idő lehetséges (hét napja), adja meg az időtartomány alapján. Ez idő alatt nincs kimenet jön létre az utólagos olvasási a szálankénti függőben lévő bemeneti események befejezéséig. Ez a probléma akkor merülhet fel, amikor a rendszer frissíti a folyamatos átviteli feladatok, így az a feladat újraindítása. Az ilyen frissítések általában egyszer minden néhány hónap során történik. 

Ezért járjon el körültekintően, a Stream Analytics-lekérdezés tervezése során. Ha egy nagy időablakban (hét napja akár egynél több órát) historikus elemek a feladat lekérdezési szintaxisban, vezethet, késleltetés az az első kimeneti a feladat indításakor vagy újraindítása.  

Egy megoldás ehhez a kiterjesztéstípushoz első kimeneti késleltetés, hogy a lekérdezés ezerszer eljárások (az adatok particionálása), vagy adja hozzá a további Streamelési egységek, amíg a feladat behozza javítani szeretné.  További információkért lásd: [Stream Analytics-feladatok létrehozásának szempontjai](stream-analytics-concepts-checkpoint-replay.md)

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

Ezeket az adatokat, az Azure Portalon válassza ki a folyamatos átviteli feladat, és válassza a **feladatábra**. Minden egyes bemenet nincs egy partíció várakozó események metrikánként. A várakozó események metrika is növekszik, ha azt jelzi, hogy a rendszer előrébb. Potenciálisan Ez az oka az, hogy a kimeneti fogadó szabályozás vagy a magas CPU. A feladatdiagramról használatáról további információkért lásd: [feladatábrában használatával adatvezérelt hibakeresés](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Az Azure SQL Database kimeneti kulcsok protokollmegsértési figyelmeztetés

Azure SQL database egy Stream Analytics-feladat kimeneteként konfigurálásakor azt tömeges rekordok szúr be a céltáblázatban. Általánosságban véve az Azure stream analytics biztosítja [legalább egyszer kézbesítési]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) átadni a kimeneti fogadónak is egy [pontosan elérése – egyszer kézbesítési]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) SQL kimeneti, amikor az SQL-tábla meghatározott egyedi korlátozást tartalmaz. 

Miután egyedi kulcsra vonatkozó megkötések állíthatók be, az SQL-táblát, és SQL-táblába beszúrt ismétlődő rekordok, az Azure Stream Analytics eltávolítja az ismétlődő rekord. Ez felosztja a az adatok kötegek és rekurzív módon szúr be a kötegek, amíg talál egy egyetlen ismétlődő rekordot. Ha a folyamatos átviteli feladat ismétlődő sorok, ez a felosztás jelentős számú rendelkezik, és helyezze be a folyamat figyelmen kívül hagyja az ismétlődő egy kevésbé hatékony és időigényes feladat, amely rendelkezik. Ha több kulcsok protokollmegsértési figyelmeztető üzenetek a tevékenységnaplóban az elmúlt egy órán belül jelenik meg, akkor valószínű, hogy az SQL-kimenet lelassítanunk van-e a teljes feladat. 

A probléma megoldásához kell [konfigurálása az index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) okozza, amely a kulcsok protokollmegsértési engedélyezésével az IGNORE_DUP_KEY beállítást. Ez a beállítás engedélyezése lehetővé teszi a duplikált értékek alapján SQL tömeges Beszúrások során figyelmen kívül, és SQL Azure egyszerűen egy figyelmeztető üzenet helyett hibát eredményez. Az Azure Stream Analytics nem eredményez többé elsődleges kulcsok protokollmegsértési hibák.

Vegye figyelembe a következő megfigyeléseken, számos különböző típusú indexeket az IGNORE_DUP_KEY konfigurálásakor:

* Elsődleges kulcs, vagy egy egyedi korlátozás, amely használja az ALTER INDEX az IGNORE_DUP_KEY nem állítható be kell, hogy dobja el és hozza létre újból az indexet.  
* Az IGNORE_DUP_KEY beállítást használja az ALTER INDEX egyedi index, amely nem azonos az elsődleges kulcs vagy UNIQUE megkötést, és a CREATE INDEX vagy az INDEX definícióját használatával létrehozott állíthatja be.  
* Mivel az ilyen indexek egyedisége nem kényszeríti IGNORE_DUP_KEY oszlopcentrikus indexek nem vonatkozik.  

## <a name="get-help"></a>Segítségkérés

További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
