---
title: Gyakori problémák elhárítása az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti a problémák elhárítása Azure Stream Analytics és lépések több gyakori problémák.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: d3b01e75a9b34ce4e38138816935bdae2e0ea778
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056886"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Gyakori problémák a Stream Analytics és a lépések hibaelhárítása

## <a name="troubleshoot-malformed-input-events"></a>Helytelen formátumú bemeneti események hibaelhárítása

 Szerializálási hibák vannak okozza, ha a Stream Analytics-feladat adatfolyam helytelenül formázott üzeneteket tartalmaz. Például egy helytelenül formázott üzenetet lehet egy hiányzó zárójelek vagy egy hiányzó kapcsos zárójel JSON-objektum által okozott, vagy a helytelen időbélyegző-formátum idő mező idő. 
 
 Ha egy Stream Analytics-feladat bemenete egy helytelenül formázott üzenetet kap, elveti az üzeneteket, és értesíti a felhasználót egy figyelmeztetéssel együtt. Egy figyelmeztető szimbólum jelenik meg a **bemenetek** csempe a Stream Analytics-feladat (Ez a figyelmeztetés bejelentkezési létezik mindaddig, amíg a feladat futó állapotban van):

![Bemenetek csempéjén](media/stream-analytics-malformed-events/inputs_tile.png)

További információk jelennek meg, engedélyezze a diagnosztikai naplók a figyelmeztetést részleteinek megtekintéséhez. Helytelen formátumú bemeneti események, a feladatvégrehajtási naplók tartalmaz bejegyzést a következőhöz hasonló üzenetet: "üzenet: erőforrás bemeneti eseménye(i) nem deszerializálható(k) <blob URI> json-ként". 

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

1. Keresse meg a bemeneti csempét, és kattintson szimbólumra a figyelmeztetések megtekintéséhez.

2. A bemenet részletei csempe megjeleníti a figyelmeztetéseket a probléma részleteivel együtt. Az alábbiakban egy példa figyelmeztető üzenetet a, a figyelmeztető üzenetet jeleníti meg, a partíció, az eltolást és sorozatszámok helytelen formátumú JSON-adatok esetén. 

   ![Az eltolás figyelmeztető üzenet](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Helytelen formátumú JSON-adatok lekéréséhez futtassa a CheckMalformedEvents.cs kódot. Ebben a példában érhető el a [GitHub-mintaadattár](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). A kód olvasást a Partícióazonosító, eltolás és megrendelése adott eltolás található adatok. 

4. Az adatok beolvasása után elemezheti és kijavíthatja a szerializáció formátumát.

5. Emellett [eseményeket olvas az IoT Hub, a Service Bus Explorerrel](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="delayed-output"></a>Késleltetett kimenet

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

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Az Azure SQL Database kimeneti ismétlődő rekordok kezeléséhez

Azure SQL database egy Stream Analytics-feladat kimeneteként konfigurálásakor azt tömeges rekordok szúr be a céltáblázatban. Általánosságban véve az Azure stream analytics biztosítja [legalább egyszer kézbesítési]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) átadni a kimeneti fogadónak is egy [pontosan elérése – egyszer kézbesítési]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) SQL kimeneti, amikor az SQL-tábla meghatározott egyedi korlátozást tartalmaz. 

Miután egyedi kulcsra vonatkozó megkötések állíthatók be, az SQL-táblát, és SQL-táblába beszúrt ismétlődő rekordok, az Azure Stream Analytics eltávolítja az ismétlődő rekord. Ez felosztja a az adatok kötegek és rekurzív módon szúr be a kötegek, amíg talál egy egyetlen ismétlődő rekordot. Ha a folyamatos átviteli feladat ismétlődő sorok, ez a felosztás jelentős számú rendelkezik, és helyezze be a folyamat figyelmen kívül hagyja az ismétlődő egy kevésbé hatékony és időigényes feladat, amely rendelkezik. Ha több kulcsok protokollmegsértési figyelmeztető üzenetek a tevékenységnaplóban az elmúlt egy órán belül jelenik meg, akkor valószínű, hogy az SQL-kimenet lelassítanunk van-e a teljes feladat. 

A probléma megoldásához kell [konfigurálása az index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) okozza, amely a kulcsok protokollmegsértési engedélyezésével az IGNORE_DUP_KEY beállítást. Ez a beállítás engedélyezése lehetővé teszi a duplikált értékek alapján SQL tömeges Beszúrások során figyelmen kívül, és SQL Azure egyszerűen egy figyelmeztető üzenet helyett hibát eredményez. Az Azure Stream Analytics nem eredményez többé elsődleges kulcsok protokollmegsértési hibák.

Vegye figyelembe a következő megfigyeléseken, számos különböző típusú indexeket az IGNORE_DUP_KEY konfigurálásakor:

* Elsődleges kulcs, vagy egy egyedi korlátozás, amely használja az ALTER INDEX az IGNORE_DUP_KEY nem állítható be kell, hogy dobja el és hozza létre újból az indexet.  
* Az IGNORE_DUP_KEY beállítást használja az ALTER INDEX egyedi index, amely nem azonos az elsődleges kulcs vagy UNIQUE megkötést, és a CREATE INDEX vagy az INDEX definícióját használatával létrehozott állíthatja be.  
* Mivel az ilyen indexek egyedisége nem kényszeríti IGNORE_DUP_KEY oszlopcentrikus indexek nem vonatkozik.  

## <a name="next-steps"></a>További lépések
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
