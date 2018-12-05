---
title: Adatok betöltési ideje az Azure Log Analyticsben |} A Microsoft Docs
description: Ismerteti a különböző tényező befolyásolja az Azure Log Analytics adatokat gyűjt a késés.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: bwren
ms.openlocfilehash: d8d8e344ce9ee317a7f864492514162b1dc085f9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882688"
---
# <a name="data-ingestion-time-in-log-analytics"></a>A Log Analytics Data betöltési idő
Az Azure Log Analytics egy nagy méretű szolgáltatás az Azure monitorban ellátó – több ezer ügyfelünk egyre bővülő ütemben havonta terabájtnyi adatot küld a. Gyakran előfordulnak az adatokat a begyűjtésük után válnak elérhetővé a Log Analytics szükséges idő kapcsolatos kérdésekre. Ez a cikk ismerteti a különböző tényező befolyásolja, a késés.

## <a name="typical-latency"></a>Átlagos késés
Késés a létrehozott adatok a figyelt rendszeren és az idő, elemezhetők a Log Analytics előre hivatkozik. A tipikus késés betölteni az adatokat a Log Analytics az 2 és 5 perc között van. A megadott késése az adott adatok számos tényezőtől alábbi leírásban függően változhat.


## <a name="factors-affecting-latency"></a>Késés befolyásoló tényezők
A teljes feldolgozási idő egy adott adatkészlet is kell bontani a következő magas szintű területeken. 

- Ügynök ideje - esemény észlelése, gyűjteni, és elküldi azokat a Log Analytics Adatbetöltési pont, egy naplórekord ideje. A legtöbb esetben ez a folyamat egy ügynök kezeli.
- Folyamat idő – az idő a betöltési folyamat naplórekord feldolgozásához. Ez magában foglalja az esemény tulajdonságainak elemzés és a lehetséges hozzáadni számított információkat.
- Indexelési idő – egy naplórekord Log Analytics Big Data store-ba, hogy az idő.

A különböző késést, a folyamat részletei az alábbiakban tekintheti át.

### <a name="agent-collection-latency"></a>Az ügynök gyűjtemény késés
Az ügynökök és felügyeleti megoldásokat különböző stratégiák segítségével adatokat gyűjteni a virtuális gép, amelyek befolyásolhatják a várakozási. Néhány konkrét példa a következők:

- Windows-események, a syslog-események és a teljesítmény-mérőszámokat a rendszer azonnal gyűjti. Linuxos teljesítményszámlálókkal, 30 másodperces időközönként kérdezi le azt.
- IIS-naplók és egyéni naplók összegyűjtése után időbélyegzőik változik. Az IIS-naplók, ez befolyásolja a [átvitel ütemezése az IIS-kiszolgálón konfigurált](../../azure-monitor/platform/data-sources-iis-logs.md). 
- Active Directory replikáció-megoldás a kiértékelés öt naponta, hajt végre, amíg az Active Directory Assessment megoldás hajt végre az Active Directory infrastruktúrával heti értékelése. Az ügynök összegyűjti ezeket a naplókat, csak ha értékelés befejeződött.

### <a name="agent-upload-frequency"></a>Az ügynök feltöltési gyakoriságának
Annak érdekében, hogy a Log Analytics-ügynök egy egyszerűsített, az ügynök puffereli a naplókat, és rendszeres időközönként feltölt azokat a Log Analytics szolgáltatásba. Töltse fel gyakorisága 30 másodperc és az adatok típusától függően 2 perc közé esik. A legtöbb adatfeltöltés 1 perc alatt. Hálózati körülmények hátrányosan befolyásolhatják a késést az adatok Log Analytics Adatbetöltési pont eléréséhez.

### <a name="azure-logs-and-metrics"></a>Az Azure-naplók és mérőszámok 
Tevékenységnapló adatainak elérhetővé a Log Analytics körülbelül 5 percet igénybe vehet. Diagnosztikai naplók és a metrikák adatait a válnak elérhetővé, attól függően, az Azure-szolgáltatás 1 – 5 percet is igénybe vehet. Ezután egy további 30 – 60 másodpercre naplók és mérőszámok az adatok 3 percenként, a Log Analytics Adatbetöltési pont küldendő vesz igénybe.

### <a name="management-solutions-collection"></a>Felügyeleti megoldások gyűjtemény
Egyes megoldások ne gyűjtsön adataikat az ügynök, és előfordulhat, hogy a módszert egy gyűjtemény, amely további késleltetést. Egyes megoldások adatgyűjtést rendszeres időközönként közel valós idejű gyűjtemény kísérlet nélkül. Példák a következők:

- Az Office 365-megoldás tevékenységeket tartalmazó naplók az Office 365 felügyeleti tevékenység API, amely jelenleg nem biztosít semmilyen közel valós idejű késési garancia használatával kérdezi le.
- Windows Analytics-megoldások (például frissítési megfelelőség szempontjából) adatait gyűjti a megoldás a napi gyakorisággal.

Tekintse meg az egyes megoldások annak gyűjtési gyakoriságát dokumentációját.

### <a name="pipeline-process-time"></a>Pipeline-feldolgozási idő
Ha a rekordok naplózása a Log Analytics-folyamat be vannak töltve, íródtak egy ideiglenes tárolóra annak biztosítása érdekében a bérlők elszigetelésére, és győződjön meg arról, hogy az adatok nem vesznek el. Ez a folyamat általában 5 – 15 másodperc hozzáadja. Egyes felügyeleti megoldások megvalósítása nehezebb algoritmusokat összesített adatokat, és hozzájut, az adatok a streamelés a. Például a hálózati teljesítmény figyelése összesíti a bejövő adatok keresztül 3 perces időközönként, hatékonyan hozzáadása a 3 perces késéssel. Egy másik folyamat, amely hozzáadja a késés az a, amely kezeli az egyéni naplókat. Bizonyos esetekben ez a folyamat néhány percet a késés előfordulhat, hogy hozzá-fájlokból az ügynök által gyűjtött naplók.

### <a name="new-custom-data-types-provisioning"></a>Kiépítés új egyéni adattípusok
Az egyéni adatokat egy új típusú létrehozásakor egy [egyéni napló](data-sources-custom-logs.md) vagy a [adatgyűjtő API](data-collector-api.md), a rendszer létrehoz egy dedikált storage-tárolóba. Ez az egy egyszeri terhelést, amely csak az első megjelenítési módja, ez az adattípus megy végbe.

### <a name="surge-protection"></a>Védelem
Gondoskodjon arról, hogy nincsenek felhasználói adatok elvesznek, ezért a rendszer az adatok megnövekedésénél beépített védelemmel rendelkezik a Log Analytics kiemelt prioritása az. Ez magában foglalja a pufferek ellenőrizze, hogy még terhelés környezetéről, a rendszer fog tartani működik. Normál terhelés alatt ezek a vezérlők hozzáadása a kevesebb mint egy percet, de a feltételek és a hibák, lehetőség van felvenni jelentős mennyiségű időt, miközben biztosítja az adatok biztonságos-e.

### <a name="indexing-time"></a>Indexelési idő
Nincs közötti analytics és a fejlett keresési képességek figyelésekor az adatokhoz való azonnali hozzáférés biztosítása minden Big Data platform beépített egyensúlyt. Az Azure Log Analytics lehetővé teszi nagy teljesítményű lekérdezések futtatása több milliárd rekordon, és néhány másodpercen belül eredményeinek beolvasása. Ez lehetséges legyen, mivel az infrastruktúra során az Adatbetöltési jelentősen alakítja át az adatokat, és tárolja azokat az egyedi kompakt struktúrák. A rendszer puffereli az adatokat, amíg elegendő az, hogy nem áll rendelkezésre ezen szerkezetek létrehozásához. Ez a keresési eredmények között megjelenik a naplórekord előtt kell elvégezni.

Ez a folyamat jelenleg körülbelül 5 percet vesz igénybe esetén adatok mennyisége alacsony, de kevesebb idő magasabb adatok alapján. Ez a megszokottnál counterintuitive, de ez a folyamat lehetővé teszi, hogy a késés nagy mennyiségű éles számítási feladatokra optimalizálása.



## <a name="checking-ingestion-time"></a>Betöltési idő ellenőrzése
Használhatja a **szívverés** becsléséhez késés, adatok ügynököktől származó tábla. Mivel szívverés egyszer küld egy perc, a különbség az aktuális idő és az utolsó szívverés rekord ideális lesz egy percet, minél közelebb.

Használja a következő lekérdezést a legmagasabb késéssel rendelkező azon számítógépek listázásához.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | top 50 by IngestionTime asc

 
Használja a következő lekérdezést a nagy méretű környezetekben a késés, az összes számítógép eltérő százalékos foglalják össze.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | summarize percentiles(IngestionTime, 50,95,99)



## <a name="next-steps"></a>További lépések
* Olvassa el a [szolgáltatói szerződést (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) Log Analytics.

