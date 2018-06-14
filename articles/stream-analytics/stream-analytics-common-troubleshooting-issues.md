---
title: Gyakori problémák elhárítása az Azure Stream Analytics
description: Ez a cikk ismerteti az Azure Stream Analytics és a lépéseket és a hibakeresést számos gyakori problémákat.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: e04d1072acee635235b0a5bd8465ca38c861017b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523523"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Gyakori problémák hibaelhárítása a Stream Analytics és a lépéseket a

## <a name="troubleshoot-malformed-input-events"></a>Helytelen formátumú bemeneti események hibaelhárítása

 Szerializálási problémák vannak az okozza, ha a bemeneti adatfolyam a Stream Analytics-feladat nem megfelelő alakú üzenetek tartalmazza. Például egy helytelenül formázott üzenetet lehet egy hiányzó zárójel vagy egy hiányzó zárójelet egy JSON-objektum által okozott, vagy nem megfelelő idő stamp formátum idő mezőben. 
 
 A Stream Analytics-feladat egy bemeneti helytelenül formázott üzenetet kap, amikor elutasítja azokat az üzenetet, és értesíti a felhasználót egy figyelmeztetést. Figyelmeztetés szimbólum jelenik meg a **bemenetek** csempe a Stream Analytics-feladat (Ez a figyelmeztetés bejelentkezési létezik mindaddig, amíg a feladat nem fut,):

![Bemenetek csempe](media/stream-analytics-malformed-events/inputs_tile.png)

További információ, engedélyezze a diagnosztikai naplókat a figyelmeztetés részletes adatainak megtekintéséhez. Helytelen formátumú bemeneti események, a végrehajtási naplót tartalmaz bejegyzést, amely a következőképpen néz. üzenet: "üzenet: nem sikerült deszerializálni az erőforrás a bemeneti esemény <blob URI> json-ként". 

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

1. Keresse meg a bemeneti csempe, és kattintson ide a figyelmeztetéseket.

2. A bemeneti részletek csempe információkhoz juthat a problémáról a figyelmeztetések megjelenítése. Az alábbiakban látható egy példa figyelmeztető üzenet, a figyelmeztető üzenetet jeleníti meg, a partíció, az eltolás és a sorozatszámok nem megfelelően formázott JSON-adatok esetén. 

   ![Figyelmeztető üzenet eltolású](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Ahhoz, hogy a JSON-adatokat, amelyek formátuma nem megfelelő, a kódra CheckMalformedEvents.cs. Ebben a példában a érhető el a [minták GitHub-tárházban](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). A kód olvasások a Partícióazonosító, eltolás és megrendelése adott eltolás található adatokat. 

4. Az adatok beolvasása után elemezheti és kijavíthatja a szerializáció formátumát. 

## <a name="delayed-output"></a>Késleltetett kimeneti

### <a name="first-output-is-delayed"></a>Első kimeneti késleltetve
A Stream Analytics-feladat indítása, ha a bemeneti események olvasása, de lehet egy késleltetést az éppen létrehozott bizonyos körülmények között kimenet.

A kimeneti késleltetés hozzájárulhat a historikus lekérdezés elemek nagy időértékeket. Megfelelő kimeneti eredményezett a nagy idő windows keresztül, a folyamatos átviteli feladat indítása adatok olvasása a legújabb időpontja (hét napja) lehetséges kitöltse a időszak szerint. Ebben az időszakban amíg be nem fejeződik a függőben lévő bemeneti események utólagos olvasás nincs kimenet hozott létre. Ez a probléma is surface, amikor a rendszer frissíti a folyamatos átviteli feladat, így az a feladat újraindítása. Ilyen frissítések általában egyszer minden néhány hónappal történik. 

Ezért járjon el körültekintően, a Stream Analytics lekérdezési tervezésekor. Ha egy olyan nagy időkeretet (hét napja akár egynél több órát) historikus elemek a lekérdezés szintaxisa a feladat, eredményezhet késleltetés az első kimeneti meg a feladat indításakor vagy újraindítása.  

Az ilyen típusú első kimeneti késleltetés egy megoldás lekérdezési párhuzamos folyamatkezelést biztosítja módszerek (az adatok particionálása), vagy adja hozzá a további folyamatos átviteli egységet javítására az átviteli sebesség, amíg a feladat ki.  További információkért lásd: [Stream Analytics-feladatok létrehozásának szempontjai](stream-analytics-concepts-checkpoint-replay.md)

Ezek a tényezők hatással lehet az első kimeneti, amely akkor jön létre, a megoldásukhoz:

1. Az ablakolt aggregátumok (csoport által az Átfedésmentes, Hopping, és a késleltetett windows)
   - Az átfedésmentes vagy hopping ablak összesítések, az ablak időtartam végén eredmények jönnek létre. 
   - A csúszóablak az eredmények jön létre, amikor egy esemény kerül, vagy a csúszóablak kilép. 
   - Ha azt tervezi, hogy nagy ablakméret (> 1 óra) használja, érdemes szórási vagy mozgó ablakban válasszon, hogy gyakrabban megtekintheti a kimeneti.

2. Ideiglenes táblákra (DATEDIFF csatlakoztatás) használata
   - Egyezések jönnek létre, amint a megfelelő események mindkét oldalán érkezésekor.
   - A DATEDIFF ablak bal oldalán mindegyik esemény tekintetében végén előállított, amely nem rendelkezik (bal oldali külső ILLESZTÉST) egyező adatokat.

3. A historikus analitikai függvények (ISFIRST, LAST és a LIMIT DURATION LAG) használata
   - Az analitikai függvények a kimenet jön létre minden eseményhez, késlekedés nélkül.

### <a name="output-falls-behind"></a>Kimeneti korábbiak
A feladat normál működés során, ha a feladat kimenetére esik (hosszabb és hosszabb késéssel), mögött tudja az alapvető okok ezek a tényezők megvizsgálásával:
- Hogy az alsóbb rétegbeli fogadó szabályozva van
- Hogy a felsőbb rétegbeli forrás szabályozva van
- A feldolgozó logika a lekérdezésben-e számítási igényű

Adatai, az Azure portálon, válassza ki az adatfolyam-feladatot, majd válassza ki a **feladat ábra**. Minden egyes bemeneti van egy partíció várakozó esemény metrika száma. A várakozó események metrika tartja növekszik, akkor azt jelzi, hogy a rendszer-erőforrások csak korlátozottan. Potenciálisan Ez oka az, hogy a kimeneti fogadó sávszélesség-szabályozás és magas CPU. A feladat ábra használatával kapcsolatos további információkért lásd: [adatvezérelt hibakeresést a feladat ábra keresztül](stream-analytics-job-diagram-with-metrics.md).

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Az Azure SQL Database kimenet ismétlődő rekordok kezeléséhez

Azure SQL-adatbázis egy Stream Analytics-feladat kimeneti konfigurálásakor tömeges beilleszti rögzíti a céltáblába történő. Általában az Azure stream analytics biztosítja, hogy az [legalább egyszer kézbesítési]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) a kimeneti fogadóját egy is még [pontosan elérése-egyszer kézbesítési]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) SQL kimeneti Ha SQL táblázat rendelkezik egy egyedi korlátozást. 

Ha az SQL-tábla egyedi kulcsra vonatkozó megkötések állíthatók be, és SQL táblába beszúrt ismétlődő rekordok, Azure Stream Analytics eltávolítja az ismétlődő rekord. Az adatok kötegek és a kötegek beszúrása, amíg meg nem találja a duplikált rekord rekurzív módon felosztja azt. Ha a folyamatos átviteli feladatok ismétlődő sorok, a felosztás jelentős számú, és helyezze be a folyamat figyelmen kívül hagyja a rendszer a duplikált egy kevésbé hatékony és időigényes rendelkezik. Ha több kulcs megsértése figyelmeztető üzenetek a tevékenységnapló az elmúlt egy órában belül jelenik meg, valószínű, hogy az SQL kimeneti lelassult van-e a teljes feladat. 

A probléma megoldásához, akkor [konfigurálása az index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) okozza, hogy a kulcs megsértése azáltal, hogy az IGNORE_DUP_KEY beállítást. Ez a beállítás lehetővé teszi, hogy által SQL tömeges Beszúrások során figyelmen kívül az ismétlődő értékek, és SQL Azure egyszerűen hoz létre egy figyelmeztető üzenet hiba helyett. Az Azure Stream Analytics nem eredményez már elsődleges kulcs megsértése hibák.

Vegye figyelembe a következő megfigyelések, számos különböző típusú indexeket az IGNORE_DUP_KEY konfigurálásakor:

* Az elsődleges kulcs vagy egyedi megkötésen, amely használja az ALTER INDEX IGNORE_DUP_KEY nem állítható be, meg kell dobja el és hozza létre újból az indexet.  
* Beállíthatja az ALTER INDEX használatával, amely nem azonos az elsődleges kulcs vagy UNIQUE megkötést, és a CREATE INDEX vagy az INDEX definícióját használatával létrehozott egyedi index IGNORE_DUP_KEY beállítást.  
* IGNORE_DUP_KEY nem vonatkozik az oszlopcentrikus indexek, mert az ilyen indexek egyedisége nem érvényesíthetők.  

## <a name="next-steps"></a>További lépések
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
