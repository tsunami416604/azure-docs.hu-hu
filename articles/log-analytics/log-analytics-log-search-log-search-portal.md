---
title: "A naplófájl-keresési portál használata az Azure Naplóelemzés |} Microsoft Docs"
description: "A cikk azt ismerteti, hogyan napló keresések létrehozása és a keresési napló portálon Naplóelemzési munkaterületet tárolt adatok elemzése webalkalmazással tartalmaz.  Az oktatóanyag magában foglalja a különböző típusú adatok és elemzésekor eredmények vissza egyszerű lekérdezések futtatásának."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 3a2e8803d51d81ab0eda3dc814d01822e17bc14e
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Napló keresések létrehozása az Azure Naplóelemzés a naplófájl-keresési portál használatával

> [!NOTE]
> Ez a cikk ismerteti a naplófájl-keresési portálra, az Azure Naplóelemzés az új lekérdezés nyelven.  További információ az új nyelv, és a frissítés a következő munkaterület eljárás első [Azure Naplóelemzési munkaterület frissítése új naplófájl-keresési](log-analytics-log-search-upgrade.md).  
>
> Ha a munkaterületet még nem lett frissítve az új lekérdezési nyelv, tekintse át [található adatokat, és napló kereséseket a Naplóelemzési](log-analytics-log-searches.md) információt a naplófájl-keresési portál jelenlegi verziója.

A cikk azt ismerteti, hogyan napló keresések létrehozása és a keresési napló portálon Naplóelemzési munkaterületet tárolt adatok elemzése webalkalmazással tartalmaz.  Az oktatóanyag magában foglalja a különböző típusú adatok és elemzésekor eredmények vissza egyszerű lekérdezések futtatásának.  A naplófájl-keresési portálon módosítani a lekérdezést, nem pedig közvetlenül módosítsák a szolgáltatásokra összpontosít.  A lekérdezés közvetlenül szerkesztési részletekért lásd: a [lekérdezés nyelvi referencia](https://go.microsoft.com/fwlink/?linkid=856079).

A keresések a Advanced Analytics portál helyett a naplófájl-keresési portál létrehozásához lásd: [az Analytics portál első lépések](https://go.microsoft.com/fwlink/?linkid=856587).  Mindkét portálok ugyanabban a lekérdezési nyelv használatával a Naplóelemzési munkaterület ugyanazon adatokat érik el.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy már rendelkezik a Naplóelemzési munkaterület legalább egy csatlakoztatott forrás, amely a lekérdezések elemzéséhez adatokat állít elő.  

- Ha a munkaterületet nincs, létrehozhat egy ingyenes egy, a következő eljárás segítségével [Ismerkedjen meg a Naplóelemzési munkaterület](log-analytics-get-started.md).
- Csatlakozás legalább egy [Windows-ügynök](log-analytics-windows-agent.md) vagy egy [Linux-ügynök](log-analytics-linux-agents.md) a munkaterületre.  

## <a name="open-the-log-search-portal"></a>Nyissa meg a keresési napló portált
Indítsa el a napló keresési portál megnyitásával. 

1. Nyissa meg az Azure Portalt.
2. A Naplóelemzési váltson, és jelölje ki a munkaterületen.
3. Válassza ki **keresési jelentkezzen**.

![Napló Keresés gomb](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>Hozzon létre egy egyszerű keresés
A leggyorsabban beolvasása néhány adatokra egy egyszerű lekérdezést, amely visszaadja az összes rekord tábla.  Ha olyan Windows vagy Linux rendszerű ügyfelek csatlakoztatva a munkaterület, majd konfigurálnia kell az esemény (Windows) vagy a Syslog (Linux) tábla adatait.

Írjon be egyet az alábbi lekérdezéseket a keresési mezőbe, és kattintson a Keresés gombra.  

```
Event
```
```
Syslog
```

Az alapértelmezett listanézetben adat, és láthatja, hogy hány teljes rekord lett visszaadva.

![Egyszerű lekérdezés](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Csak az első néhány tulajdonságok mindegyik rekorddal jelennek meg.  Kattintson a **megjelenítése további** egy adott bejegyzés minden tulajdonságok megjelenítése.

![Rekord részletei](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Az idő hatókör megadása
Minden Naplóelemzési által gyűjtött rekord tartalmazza-e egy **TimeGenerated** tulajdonság, amely tartalmazza a dátum és idő, hogy a rekord létrejött.  A naplófájl-keresési portálon lekérdezés csak rekordokat adja vissza egy **TimeGenerated** a képernyő bal oldalán megjelenő idő hatókörén belül.  

Módosíthatja a időszűrője; ehhez válassza a legördülő menüből, vagy a csúszka módosításával.  A csúszka sávjának diagramját, amelyek a tartományon belüli idő szegmensekhez rekordok relatív számát jeleníti meg.  Ezt a szegmenst a tartomány függ.

Az alapértelmezett időt hatókör **1 nap**.  Módosítsa az értéket **7 nap**, és növelje a rekordok teljes száma.

![Dátum idő hatókör](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>A lekérdezés eredményeit szűrni
A képernyő bal oldalán van a keresőablak, amely lehetővé teszi a lekérdezésbe felvenni kívánt szűrése a közvetlenül a módosítása nélkül.  A visszaadott rekordok számos tulajdonság az a rekordok száma az első tíz értékükön jelennek meg.

Ha dolgozunk **esemény**, jelölje be a jelölőnégyzetet a **hiba** alatt **EVENTLEVELNAME**.   Ha dolgozunk **Syslog**, jelölje be a jelölőnégyzetet a **err** alatt **súlyossági szint**.  A lekérdezés egy, a következő szeretné szűkíteni a hibaesemények értékre változik.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Szűrés](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

A keresőablak kiválasztásával tulajdonságok hozzáadása **szűrők hozzáadása** egyik rekord tulajdonság menüjéből.

![Szűrő menü hozzáadása](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

Az azonos szűrő kiválasztásával állíthatja be **szűrő** egy rekord, a szűrni kívánt érték tulajdonság menüjéből.  

Csak a **szűrő** kék a nevükkel tulajdonságok beállítása.  Ezek a *kereshető* mezők, amely az indexelt keresési feltételeket.  A szürke színnel mezők *szabad kereshető szöveg* mezők, amelyek csak a **hivatkozások megjelenítése** lehetőséget.  Ez a beállítás azt jelzi, hogy az egyik tulajdonságnak sem lehet ezt az értéket adja vissza.

![Szűrő menü](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Az eredmények egyetlen tulajdonság alapján csoportosíthatja kiválasztásával a **szerint kell csoportosítani a** beállítást a rekord menüből.  Ez hozzáadja egy [összefoglalója](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) operátor a lekérdezéshez, amely megjeleníti az eredményeket a diagramon.  Egynél több tulajdonság a csoportosíthatja, de a lekérdezés szerkesztéséhez közvetlenül kell.  Ezután válassza ki a rekord menüt a a **számítógép** tulajdonság, és válassza **"Számítógép" csoport**.  

![Számítógép szerint kell csoportosítani](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Eredmények használata
A naplófájl-keresési portál számos a lekérdezés eredményeinek való munkához funkcióval rendelkezik.  Rendezheti, szűrő és az eredmények az adatok elemzése a tényleges lekérdezés módosítása nélkül.  A lekérdezés nem alapértelmezés szerint vannak rendezve.

Az adatok táblázatos formában, amely további lehetőségeket nyújt a szűrési és rendezési megtekintéséhez kattintson **tábla**.  

![Tábla megtekintése](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Kattintson a nyílra által egy rekordot a rekord a részletek megtekintéséhez.

![Rendezés eredmények](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Az oszlop fejlécére kattintva a mező rendezése.

![Rendezés eredmények](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

A szűrő gombra kattint, és egy szűrési feltételt megadásával az oszlopot a megadott értéket az eredmények szűréséhez.

![Szűrés eredménye](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Egy oszlop csoportosítás oszlop fejlécére húzva az eredmények tetején.  Több oszlop húzva felső csoportosíthatja több mező.

![Az eredmények csoportosításához](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Teljesítményadatok használata
Windows- és Linux-ügynökök teljesítményadatait tárolva van a Naplóelemzési munkaterület a **telj** tábla.  Teljesítmény rekordok csakúgy, mint bármilyen más rekordéval. Keresse meg, és azt írhat, amely visszaadja az összes teljesítmény rekord ugyanúgy, mint az események egyszerű lekérdezést.

```
Perf
```

![Teljesítményadatok](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

Ad vissza, ha több millió rekordot a teljesítményobjektumok és a teljesítményszámlálók nem nagyon hasznosak.  A használt fent szűrje az adatokat, vagy csak írjon be a következő lekérdezés közvetlenül a napló keresőmezőbe ugyanazokat a módszereket is használhatja.  Ez adja vissza csak a processzor kihasználtsága rögzíti a Windows és Linux számítógépek.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Processzorhasználat](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

Ez korlátozza, hogy a számláló az adatokat, de az még nem elhelyezi egy űrlapon, amelyek különösen hasznosak.  Az adatok megjelenítése egy vonaldiagramot, de először kell csoportosítás számítógép és a TimeGenerated.  Csoport több mező, kell közvetlenül módosítsa a lekérdezést, így módosíthatja a lekérdezés a következő.  Használja a [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) működnek a **ellenértéknek** tulajdonság keresztül minden órában az átlagos érték kiszámításához.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Adatok teljesítménydiagramban](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Most, hogy az adatok megfelelően vannak csoportosítva, meg lehet jeleníteni visual diagram hozzáadásával a [leképezési](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) operátor.  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Vonaldiagram](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>További lépések

- További információ a Log Analytics lekérdezési nyelv [az Analytics portál első lépések](https://go.microsoft.com/fwlink/?linkid=856079).
- Végezze el a oktatóanyag segítségével a [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587) amely lehetővé teszi az ugyanazon lekérdezések futtatását, illetve a napló keresési portál ugyanazon adatokat érik el.
