---
title: A Service Map megoldás saját ütemben megtekinthető bemutatója | Microsoft Docs
description: A Service Map az Azure egyik megoldása, amely automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt.  A saját ütemben megtekinthető bemutató a Service Map megoldást használja arra, hogy azonosítsa és diagnosztizálja egy webalkalmazás szimulált hibáját.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: 35fe4e95eae8b63425abc8ed2970c0ad51073883
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28932477"
---
# <a name="self-paced-demo---service-map"></a>Saját ütemben megtekinthető bemutató – Service Map
Ez egy saját ütemben megtekinthető bemutató, amely végigvezeti az Azure [Service Map megoldásának](operations-management-suite-service-map.md) használatán egy webalkalmazás szimulált hibájának azonosításához és diagnosztizálásához.  A Szolgáltatástérkép automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt.  Emellett összevonja a más szolgáltatások és megoldások által gyűjtött adatokat, hogy segítsen a teljesítményelemzésben és a problémák azonosításában.  A [Log Analytics naplóbeli kereséseivel](../log-analytics/log-analytics-log-searches.md) a gyűjtött adatok részletes elemzésén keresztül azonosíthatja a kiindulási problémát.


## <a name="scenario-description"></a>Forgatókönyv leírása
Értesítést kapott, hogy az ACME ügyfélportál-alkalmazásban teljesítményproblémák merültek fel.  Csak annyi információ áll a rendelkezésére, hogy a problémák ma PST idő szerint 4:00-kor kezdődtek.  Néhány webkiszolgálón kívül nem tudja biztosan, hogy a portál milyen összetevőket használ.  

## <a name="components-and-features-used"></a>Használt összetevők és szolgáltatások
- [Szolgáltatástérkép-megoldás](operations-management-suite-service-map.md)
- [Log Analytics-naplókeresések](../log-analytics/log-analytics-log-searches.md)


## <a name="walk-through"></a>Útmutató

### <a name="1-connect-to-the-oms-experience-center"></a>1. Csatlakozás az OMS Experience Centerhez
Ez az útmutató az [Operations Management Suite Experience Centert](https://experience.mms.microsoft.com/) használja, amely egy teljes Log Analytics-környezetet biztosít mintaadatokkal. Először kattintson erre a hivatkozásra, adja meg az adatait, majd válassza az **Insight and Analytics** forgatókönyvet.


### <a name="2-start-service-map"></a>2. Szolgáltatástérkép indítása
Indítsa el a Szolgáltatástérkép-megoldást a **Szolgáltatástérkép** csempére kattintva.

![Szolgáltatástérkép csempe](media/operations-management-suite-walkthrough-servicemap/tile.png)

Megjelenik a Szolgáltatástérkép-konzol.  A bal oldali panelen látható a környezetben lévő számítógépek listája, amelyeken telepítve van a Szolgáltatástérkép-ügynök.  A listából kiválaszthatja a megtekinteni kívánt számítógépet.

![Számítógépek listája](media/operations-management-suite-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. Számítógép megtekintése
Tudjuk, hogy a webkiszolgálók neve AcmeWFE001 és AcmeWFE002, így ez jó kiindulási pont lehet.  Kattintson az **AcmeWFE001** kiszolgálóra.  Ekkor megjelenik az AcmeWFE001 és az összes függőségének térképe.  Megtekintheti, mely folyamatok futnak a kiválasztott számítógépen, és melyik külső szolgáltatásokkal kommunikálnak.

![Webkiszolgáló](media/operations-management-suite-walkthrough-servicemap/web-server.png)

Mivel a webalkalmazás teljesítményét szeretnénk vizsgálni, kattintson az **AcmeAppPool (IIS App Pool)** folyamatra.  Ekkor megjelennek a folyamat részletei és kiemelve a függőségei.  

![Alkalmazáskészlet](media/operations-management-suite-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. Időtartomány módosítása

A probléma hajnali 4:00-kor kezdődött, ezért nézzük meg, mi történt ekkor. Kattintson az **Időtartomány** lehetőségre, és állítsa be a 4:00 PST időpontot (használja az aktuális dátumot, az időpontot pedig módosítsa a helyi időzónának megfelelően) 20 perces időtartammal.

![Időválasztó](./media/operations-management-suite-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. Riasztás megtekintése

Most látjuk, hogy az **acmetomcat** függőségnél egy riasztás jelenik meg, tehát valószínűleg ez lesz a hiba.  Kattintson az **acmetomcat** riasztás ikonjára a riasztás részleteinek megtekintéséhez.  Láthatjuk, hogy a processzor kihasználtsága kritikus, és a riasztást kibontva további részleteket tudhatunk meg.  Valószínűleg ez a lassú teljesítmény oka. 

![Riasztás](./media/operations-management-suite-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. Teljesítmény megtekintése

Vizsgáljuk meg közelebbről az **acmetomcat** elemet.  Kattintson az **acmetomcat** jobb felső részére, és válassza a **Kiszolgálótérkép betöltése** lehetőséget a gép részleteinek és függőségeinek megjelenítéséhez. Ezután részletesebben megvizsgálhatjuk a teljesítményszámlálókat, hogy igazoljuk a feltevésünket.  Válassza a **Teljesítmény** lapot a [Log Analytics által gyűjtött teljesítményszámlálók](../log-analytics/log-analytics-data-sources-performance-counters.md) megjelenítéséhez az időtartományban.  Azt láthatjuk, hogy rendszeres időközönként kiugrások vannak a processzor és a memória kihasználtságában.

![Teljesítmény](./media/operations-management-suite-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. Változáskövetés megtekintése
Lássuk, mi okozza a magas kihasználtságot.  Kattintson az **Összefoglalás** lapra.  Ez megjeleníti a Log Analytics által a számítógépről gyűjtött információkat, például a hibás csatlakozásokat, a kritikus riasztásokat és a szoftverek változásait.  A közelmúltbeli lényeges információkat tartalmazó szakaszok már eleve ki vannak bontva, azonban a többi szakasz is kibontható, ha meg szeretné vizsgálni a bennük lévő információkat.


Ha a **Változáskövetés** nincs megnyitva, bontsa ki.  Ez megjeleníti a [változáskövetési megoldás](../log-analytics/log-analytics-change-tracking.md) által gyűjtött információkat.  Úgy tűnik, hogy az adott időtartományban változás történt a szoftverekben.  Kattintson a **Szoftver** elemre a részletekért.  Nem sokkal 4:00 után egy biztonsági mentési folyamat lett hozzáadva a géphez, és úgy tűnik, hogy ez okozza a túlzott erőforrás-használatot.

![Változások követése](./media/operations-management-suite-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. Részletek megtekintése a naplókeresésben
Ezt ellenőrizhetjük a Log Analytics-munkaterületen gyűjtött részletes teljesítményinformációk vizsgálatával.  Kattintson ismét a **Riasztások** lapra, majd valamelyik **magas fokú processzorhasználattal kapcsolatos** riasztásra.  Kattintson a **Megjelenítés a naplókeresésben** lehetőségre.  Ez megnyitja a Naplókeresés ablakot, ahol [naplókereséseket](../log-analytics/log-analytics-log-searches.md) hajthat végre a munkaterületen tárolt adatokon.  A Service Map már kitöltött egy lekérdezést a megtekinteni kívánt riasztás lekéréséhez.  

![Naplókeresés](./media/operations-management-suite-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. Mentett keresés megnyitása
Lássuk, találunk-e további részleteket a gyűjtött teljesítményadatokban, amelyek ezt a riasztást létrehozták, és tudjuk-e igazolni a feltevésünket, hogy a problémákat a biztonsági mentési folyamat okozza.  Állítsa az időtartományt **6 órára**.  Ezután kattintson a **Kedvencek** lehetőségre, és görgessen le a **Szolgáltatástérkép** mentett kereséseiig.  Ezeket a lekérdezéseket külön ehhez az elemzéshez hoztuk létre.  Kattintson az **Első 5 folyamat processzorhasználat alapján – acmetomcat** elemre.

![Mentett keresés](./media/operations-management-suite-walkthrough-servicemap/saved-search.png)


A lekérdezés az **acmetomcat** 5 legnagyobb processzorigényű folyamatát adja vissza.  Ha megvizsgálja a lekérdezést, megismerkedhet a naplókeresésekhez használt lekérdezési nyelvvel.  Ha a többi számítógépen lévő folyamatok is érdekelik, a lekérdezés módosításával ezek adatait is lekérheti.

Esetünkben láthatjuk, hogy a biztonsági mentési folyamat folyamatosan az alkalmazáskiszolgáló processzorának 60%-át használja.  Egyértelmű, hogy ez az új folyamat a felelős a teljesítményproblémákért.  A megoldás az új biztonsági mentési szoftver eltávolítása az alkalmazáskiszolgálóról.  Az Azure Automation által kezelt célállapot-konfigurálóval (DSC) meghatározhatunk olyan szabályzatokat, amelyek biztosítják, hogy ez a folyamat soha ne fusson kritikus fontosságú rendszereken.


## <a name="summary-points"></a>Összefoglaló pontok
- A [Szolgáltatástérkép](operations-management-suite-service-map.md) megjeleníti a teljes alkalmazás nézetét, még akkor is, ha nem ismeri az összes kiszolgálóját és függőségét.
- A Szolgáltatástérkép más kezelési megoldások által gyűjtött adatok feltárásával segít azonosítani az alkalmazással és az annak alapjául szolgáló infrastruktúrával kapcsolatos problémákat.
- A [naplókeresésekkel](../log-analytics/log-analytics-log-searches.md) részletesen elemezheti a Log Analytics-munkaterületen gyűjtött konkrét adatokat.    

## <a name="next-steps"></a>További lépések
- Tudjon meg többet a [Szolgáltatástérképről](operations-management-suite-service-map.md).
- A Szolgáltatástérkép [üzembe helyezése és konfigurálása](operations-management-suite-service-map-configure.md).
- Tudjon meg többet a [Log Analyticsről](../log-analytics/log-analytics-overview.md), amely szükséges a Szolgáltatástérkép használatához, és amely az ügynökök által gyűjtött működési adatokat tárolja.