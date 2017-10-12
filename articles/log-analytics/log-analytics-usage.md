---
title: "1Az adathasználat elemzése a Log Analyticsben | Microsoft Docs"
description: "A Log Analytics használati irányítópultjával megtekintheti, hogy mennyi adatot küld a rendszer a Log Analytics szolgáltatásnak, és elháríthatja a nagy mennyiségű adat küldését okozó hibákat."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/21/2017
ms.author: magoedte
ms.openlocfilehash: 9a4709f298131722e9c473a19f7eee0aebf7e1e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-data-usage-in-log-analytics"></a>Az adathasználat elemzése a Log Analyticsben
A Log Analytics információkat biztosít a gyűjtött adatok mennyiségéről, valamint arról, hogy mely számítógépek küldték az adatokat és milyen típusú adatokat küldtek.  A **Log Analytics használati** irányítópultján megtekintheti, hogy mennyi adatot küld a rendszer a Log Analytics szolgáltatásnak. Az irányítópult megjeleníti, hogy az egyes megoldások mennyi adatot gyűjtenek össze, és a számítógépek mennyi adatot küldenek.

## <a name="understand-the-usage-dashboard"></a>A használati irányítópult bemutatása
A **Log Analytics-használat** irányítópult az alábbi információkat jeleníti meg:

- Adatmennyiség
    - Adatmennyiség az idő függvényében (az aktuális időtartomány alapján)
    - Adatmennyiség megoldásonként
    - Adatok, amelyek nincsenek számítógéphez rendelve
- Számítógépek
    - Adatokat küldő számítógépek
    - Számítógépek, amelyek nem küldtek adatokat az elmúlt 24 órában
- Ajánlatok
    - Insight- és Analytics-csomópontok
    - Automation and Control-csomópontok
    - Biztonsági csomópontok
- Teljesítmény
    - Az adatok gyűjtésével és indexelésével eltelt idő
- Lekérdezések listája

![A Használat irányítópult](./media/log-analytics-usage/usage-dashboard01.png)

### <a name="to-work-with-usage-data"></a>A használati adatok használata
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com) Azure-előfizetésével.
2. A **központi** menüben kattintson a **További szolgáltatások** elemre, majd az erőforrások listájába írja be a következőt: **Log Analytics**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Log Analytics** elemre.  
    ![Azure-központ](./media/log-analytics-usage/hub.png)
3. A **Log Analytics** irányítópultja megjeleníti a munkaterületek listáját. Jelöljön ki egy munkaterületet.
4. A *munkaterületek* irányítópultján kattintson a **Log Analytics-használat** elemre.
5. A **Log Analytics-használat** irányítópulton kattintson az **Idő: Elmúlt 24 óra** elemre az időintervallum módosításához.  
    ![időintervallum](./media/log-analytics-usage/time.png)
6. Tekintse meg a kívánt területeket megjelenítő használatikategória-paneleket. Válasszon ki egy panelt, majd kattintson az egyik elemére további részletek megtekintéséhez a [naplókeresésben](log-analytics-log-searches.md).  
    ![példa adathasználati panelre](./media/log-analytics-usage/blade.png)
7. A Naplók keresése irányítópulton tekintse meg a keresés eredményeit.  
    ![példa használati panelre a naplókeresésben](./media/log-analytics-usage/usage-log-search.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Riasztás létrehozása, amikor az adatgyűjtés szintje a vártnál magasabb
Ez a szakasz ismerteti, hogyan hozhat létre riasztást, ha:
- Az adatmennyiség meghalad egy megadott mennyiséget.
- Az adatmennyiség várhatóan meghalad egy megadott mennyiséget.

A Log Analytics[-riasztások](log-analytics-alerts-creating.md) keresési lekérdezéseket használnak. A következő lekérdezés akkor ad vissza eredményt, ha több mint 100 GB adat lett összegyűjtve az elmúlt 24 órában:

`Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(Quantity,1024)) as DataGB by Type | where DataGB > 100`

A következő lekérdezés egy egyszerű képlettel előrejelzi, mikor fog a rendszer egy nap alatt több mint 100 GB adatot küldeni: 

`Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(mul(Quantity,8),1024)) as EstimatedGB by Type | where EstimatedGB > 100`

Ha más adatmennyiségre szeretne riasztást beállítani, módosítsa a lekérdezésekben a 100 értéket arra a GB mennyiségre, amely esetén riasztást szeretne kapni.

A [riasztási szabályok létrehozásával kapcsolatos](log-analytics-alerts-creating.md#create-an-alert-rule) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha az adatgyűjtés szintje a vártnál magasabb.

Az első lekérdezéshez tartozó riasztás létrehozásakor – amikor több mint 100 GB adat lett összegyűjtve 24 órán belül, állítsa be a következőket:
- A **Név** legyen *Több mint 100 GB adatmennyiség 24 órán belül*
- A **Súlyosság** legyen *Figyelmeztetés*
- A **Keresési lekérdezés** legyen a következő: `Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(Quantity,1024)) as DataGB by Type | where DataGB > 100`
- Az **Időtartomány** legyen *24 óra*.
- A **Riasztási időköz** legyen egy óra, mivel a használati adatok csak óránként egyszer frissülnek.
- A **Riasztások létrehozása a következő alapján:** értéke legyen az *eredmények száma*
- Az **Eredmények száma** legyen *Nagyobb, mint 0*

A [műveletek a riasztási szabályokhoz adásával kapcsolatos](log-analytics-alerts-actions.md) részben leírt lépéseket követve konfigurálhat e-mailt, webhookot, vagy runbook-műveletet a riasztási szabályhoz.

A második lekérdezéshez tartozó riasztás létrehozásakor – amikor több mint 100 GB adat összegyűjtése várható 24 órán belül, állítsa be a következőket:
- A **Név** legyen *Több mint 100 GB várható adatmennyiség 24 órán belül*
- A **Súlyosság** legyen *Figyelmeztetés*
- A **Keresési lekérdezés** legyen a következő: `Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(mul(Quantity,8),1024)) as EstimatedGB by Type | where EstimatedGB > 100`
- Az **Időtartomány** legyen *3 óra*.
- A **Riasztási időköz** legyen egy óra, mivel a használati adatok csak óránként egyszer frissülnek.
- A **Riasztások létrehozása a következő alapján:** értéke legyen az *eredmények száma*
- Az **Eredmények száma** legyen *Nagyobb, mint 0*

Riasztás fogadásakor kövesse a következő szakaszban leírt lépéseket a vártnál magasabb szintű használatot okozó hibák elhárításához.

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>A vártnál magasabb szintű használatot okozó hibák elhárítása
A használati irányítópult segít azonosítani, miért magasabb szintű a használat (és miért nagyobbak a költségek) a vártnál.

A magasabb szintű használatot a következők okozhatják:
- A vártnál több adatot küld a rendszer a Log Analytics számára
- A vártnál több csomópont küld adatokat a Log Analytics számára

### <a name="check-if-there-is-more-data-than-expected"></a>Annak ellenőrzése, hogy a vártnál több adatot küld-e a rendszer 
A használati lap két fő részből áll, amelyek segítségével azonosítható, mi okozza a legtöbb adat gyűjtését.

Az *Adatmennyiség az idő függvényében* diagram megjeleníti az elküldött adatok teljes mennyiségét, valamint azokat a számítógépeket, amelyek a legtöbb adatot küldik. A lap tetején lévő diagramról leolvasható, hogy a teljes adathasználat szintje nő, állandó vagy csökken. A számítógépek listája a 10 legtöbb adatot küldő számítógépet jeleníti meg.

Az *Adatmennyiség megoldásonként* diagram megjeleníti az egyes megoldások által elküldött adatmennyiséget, valamint a legtöbb adatot küldő megoldásokat. A lap tetején lévő diagram megjeleníti az egyes megoldások által küldött teljes adatmennyiséget az idő függvényében. Ez alapján áttekintheti, hogy az egyes megoldások idővel egyre több adatot, azonos mennyiségű adatot vagy egyre kevesebb adatot küldenek. A megoldások listája a 10 legtöbb adatot küldő megoldást jeleníti meg. 

Ezen a két diagramon megjelenik az összes adat. Néhány adat számlázható, mások pedig ingyenesek. Ha csak a számlázható adatokra szeretne összpontosítani, módosítsa a lekérdezést a keresés a következő hozzáadásával: `IsBillable=true`.  

![adatmennyiség-diagramok](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

Tekintse meg az *Adatmennyiség az idő függvényében* diagramot. Azon megoldások és adattípusok megtekintéséhez, amelyek a legtöbb adatot küldik egy adott számítógép esetében kattintson a számítógép nevére. Kattintson a listában szereplő első számítógép nevére.

A következő képernyőképen az látható, hogy a *Log Management / Perf* adattípus küldi a legtöbb adatot a számítógép esetében. 

![adatmennyiség egy számítógép esetében](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)

Ezután lépjen vissza a *Használat* irányítópultra, és tekintse meg az *Adatmennyiség megoldásonként* diagramot. Azon a számítógépek megtekintéséhez, amelyek a legtöbb adatot küldik egy megoldás esetében, kattintson a listában szereplő megoldás nevére. Kattintson a listában szereplő első megoldás nevére. 

A következő képernyőkép megerősíti, hogy az *acmetomcat* számítógép küldi a legtöbb adatot a naplókezelési megoldás esetében.

![adatmennyiség egy megoldás esetében](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)

Szükség esetén végezzen további elemzést a megoldásokban vagy adattípusokban található nagy mennyiségek azonosításához. Példák a lekérdezésekre:

+ **Biztonsági** megoldás
  - `Type=SecurityEvent | measure count() by EventID`
+ **Naplókezelési** megoldás
  - `Type=Usage Solution=LogManagement IsBillable=true | measure count() by DataType`
+ **Perf** adattípus
  - `Type=Perf | measure count() by CounterPath`
  - `Type=Perf | measure count() by CounterName`
+ **Esemény** adattípus
  - `Type=Event | measure count() by EventID`
  - `Type=Event | measure count() by EventLog, EventLevelName`
+ **Rendszernapló** adattípus
  - `Type=Syslog | measure count() by Facility, SeverityLevel`
  - `Type=Syslog | measure count() by ProcessName`
+ **AzureDiagnostics** adattípus
  - `Type=AzureDiagnostics | measure count() by ResourceProvider, ResourceId`

A következő lépésekkel csökkentheti a gyűjtött naplók mennyiségét:

| A nagy adatmennyiség forrása | Az adatmennyiség csökkentésének módja |
| -------------------------- | ------------------------- |
| Biztonsági események            | Válassza a [gyakori vagy minimális biztonsági események](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) lehetőséget <br> Módosítsa a biztonsági naplózási szabályzatot, hogy csak a szükséges eseményeket gyűjtse be. Tekintse át a következőkhöz való eseménygyűjtés szükségességét: <br> - [szűrőplatform naplózása](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [beállításjegyzék naplózása](https://docs.microsoft.com/windows/device-security/auditing/audit-registry)<br> - [fájlrendszer naplózása](https://docs.microsoft.com/windows/device-security/auditing/audit-file-system)<br> - [kernelobjektum naplózása](https://docs.microsoft.com/windows/device-security/auditing/audit-kernel-object)<br> - [leírókezelés naplózása](https://docs.microsoft.com/windows/device-security/auditing/audit-handle-manipulation)<br> - [cserélhető tároló naplózása](https://docs.microsoft.com/windows/device-security/auditing/audit-removable-storage) |
| Teljesítményszámlálók       | Módosítsa a [teljesítményszámlálók konfigurációját](log-analytics-data-sources-performance-counters.md): <br> – Csökkentse a gyűjtés gyakoriságát <br> – Csökkentse a teljesítményszámlálók számát |
| Eseménynaplók                 | Módosítsa az [eseménynaplók konfigurációját](log-analytics-data-sources-windows-events.md): <br> – Csökkentse a gyűjtött eseménynaplók számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* szintű eseményeket |
| Rendszernapló                     | Módosítsa a [rendszernapló konfigurációját](log-analytics-data-sources-syslog.md): <br> – Csökkentse a gyűjtésben részt vevő létesítmények számát <br> – Csak a szükséges eseményszinteket gyűjtse. Ne gyűjtsön például *Tájékoztatás* vagy *Hibakeresés* szintű eseményeket |
| AzureDiagnostics           | Az erőforrásnapló-gyűjtés módosítása a következőre: <br> – Csökkentse a Log Analytics számára naplókat küldő erőforrások számát <br> – Csak a szükséges naplókat gyűjtse |
| Megoldásadatok olyan számítógépekről, amelyeknek nincs szükségük a megoldásra | A [megoldáscélzási](../operations-management-suite/operations-management-suite-solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat. |

### <a name="check-if-there-are-more-nodes-than-expected"></a>Annak ellenőrzése, hogy a vártnál több csomópont küld-e adatokat
Ha a *csomópontonkénti (OMS)* tarifacsomagban van, akkor a díjszabás a használt csomópontok és megoldások számán alapul. A használat adatait megjelenítő irányítópult *Ajánlatok* szakaszában tekintheti meg, hogy az egyes ajánlatok csomópontjaiból mennyi van használatban.

![A Használat irányítópult](./media/log-analytics-usage/log-analytics-usage-offerings.png)

Kattintson **Az összes megjelenítése...** lehetőségre a kiválasztott ajánlat adatait elküldő számítógépek teljes listájának megtekintéséhez.

A [megoldáscélzási](../operations-management-suite/operations-management-suite-solution-targeting.md) funkcióval megadhatja, hogy csak a szükséges számítógépcsoportoktól gyűjtsön adatokat.


## <a name="next-steps"></a>Következő lépések
* A keresési nyelv használatával kapcsolatban tekintse meg a [Log Analytics naplókeresési funkciójával](log-analytics-log-searches.md) kapcsolatos cikket. A keresési lekérdezésekkel további elemzéseket végezhet a használati adatokon.
* A [riasztási szabályok létrehozásával kapcsolatos](log-analytics-alerts-creating.md#create-an-alert-rule) szakaszban leírt lépéseket követve beállíthatja, hogy értesítést kapjon, ha teljesül egy keresési feltétel
* A [megoldáscélzással](../operations-management-suite/operations-management-suite-solution-targeting.md) megadhatja, hogy a rendszer csak a szükséges számítógépcsoportoktól gyűjtsön adatokat
* Válassza a [gyakori vagy minimális biztonsági események](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) lehetőséget
* A [teljesítményszámlálók konfigurációjának](log-analytics-data-sources-performance-counters.md) módosítása
* Az [eseménynaplók konfigurációjának](log-analytics-data-sources-windows-events.md) módosítása
* A [rendszernapló konfigurációjának](log-analytics-data-sources-syslog.md) módosítása
