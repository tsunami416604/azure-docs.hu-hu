---
title: A Naplókeresési portál használata az Azure Log Analyticsben |} A Microsoft Docs
description: Ez a cikk egy oktatóanyag, amely azt ismerteti, hogyan hozhatók létre naplóbeli keresések, és a Naplókeresési portál használata a Log Analytics-munkaterület-ban tárolt adatok elemzését tartalmazza.  Az oktatóanyag részeként néhány mintalekérdezést is futtatni fog különböző adattípusok visszaadásához és az eredmények elemzéséhez.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 532df20a7639f42d8ba1c840a5fd19f0ad0e4042
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43246333"
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Az Azure Log Analytics Naplókeresési portál hozhatók létre naplóbeli keresések

Ez a cikk egy oktatóanyag, amely azt ismerteti, hogyan hozhatók létre naplóbeli keresések, és a Naplókeresési portál használata a Log Analytics-munkaterület-ban tárolt adatok elemzését tartalmazza.  Az oktatóanyag részeként néhány mintalekérdezést is futtatni fog különböző adattípusok visszaadásához és az eredmények elemzéséhez.  A Naplókeresési portálon módosítani a lekérdezést, hanem hogy közvetlenül módosítaná azt szolgáltatásokra összpontosít.  Közvetlenül módosítsa a lekérdezést a részletekért lásd: a [lekérdezési nyelv leírása](https://go.microsoft.com/fwlink/?linkid=856079).

A bővített analitika portált a Naplókeresési portál helyett a keresések létrehozásához lásd: [– első lépések az Analytics-portál](https://go.microsoft.com/fwlink/?linkid=856587).  Mindkét portálok ugyanazokat az adatokat a Log Analytics-munkaterület eléréséhez használja ugyanazt a lekérdezési nyelvet.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy van legalább egy csatlakoztatott forrás, amely létrehozza a elemezheti a lekérdezések adatait a Log Analytics-munkaterületet.  

- Ha nem rendelkezik egy munkaterületet, létrehozhat egy ingyenes, a következő eljárás használatával egy [Ismerkedés a Log Analytics-munkaterület](log-analytics-get-started.md).
- Csatlakozás legalább egy [Windows-ügynök](log-analytics-windows-agent.md) vagy egy [Linux-ügynök](log-analytics-linux-agents.md) a munkaterülethez.  

## <a name="open-the-log-search-portal"></a>A naplókeresési portál megnyitása
Először nyissa meg a naplókeresési portált. 

1. Nyissa meg az Azure Portalt.
2. Keresse meg a Log Analyticshez, és válassza ki a munkaterületet.
3. Válassza a **Naplókat**.


## <a name="create-a-simple-search"></a>Egyszerű keresés létrehozása
A feldolgozható adatok lekérdezésének leggyorsabb módja az egyszerű lekérdezés, amely egy tábla összes rekordját visszaadja.  Ha Windows vagy Linux rendszerű ügyfelek vannak csatlakoztatva a munkaterülethez, akkor az adatok az Event (Esemény, Windows) vagy a Syslog (Linux) táblában találhatók.

Írja be a következő lekérdezések egyikét a keresőmezőbe, és kattintson a keresés gombra.  

```
Event
```
```
Syslog
```

A rendszer az alapértelmezett listanézetben adja vissza az adatokat, és megjeleníti, hogy összesen hány rekord lett visszaadva.

![Egyszerű lekérdezés](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Az egyes rekordoknak csak az első néhány tulajdonsága jelenik meg.  Kattintson a **részletek megjelenítése** gombra egy adott rekord összes tulajdonságának megjelenítéséhez.

![Rekordok részletei](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Az időtartomány beállítása
A Log Analytics által gyűjtött minden rekord tartalmazza-e egy **TimeGenerated** tulajdonságot, amely tartalmazza a dátum és a rekord létrehozásának időpontja.  A Naplókeresési portál egy lekérdezés csak rekordokat ad vissza egy **TimeGenerated** a képernyő bal oldalán megjelenő idő hatókörébe.  

Az Időszűrő módosításával a csúszka vagy kiválasztásával a legördülő menüben módosíthatja.  A csúszka sáv diagramját, amelyek a tartományon belüli idő szegmensekhez rekordok relatív számát jeleníti meg.  Ez a szakasz a tartománytól függően változnak.

Az alapértelmezett időt hatókör **1 nap**.  Módosítsa ezt az értéket **7 nap**, és növelje a rekordok teljes számát.

![Dátum-idő hatókör](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>A lekérdezés eredményeinek szűrése
A képernyő bal oldalán található a szűrő ablaktábla, amellyel szűrőt adhat a lekérdezéshez anélkül, hogy közvetlenül módosítaná azt.  A visszaadott rekordok számos tulajdonságát a rekord száma az első tíz értékekre jelennek meg.

Ha az **Event** táblával dolgozik, jelölje be az **EVENTLEVELNAME** területen lévő **Error** (Hiba) elem melletti jelölőnégyzetet.   Ha a **Syslog** táblával dolgozik, jelölje be a **SEVERITYLEVEL** területen lévő **err** elem melletti jelölőnégyzetet.  Így a lekérdezés a következőképpen módosul, és csak a hibaeseményeket jeleníti meg.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Szűrés](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Ha tulajdonságokat szeretne adni a szűrő ablaktáblához, akkor az egyik rekord tulajdonságmenüjében válassza a **Hozzáadás a szűrőkhöz** elemet.

![Hozzáadás a szűrőhöz menü](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

Ugyanazon szűrő beállításához válassza ki egy olyan rekord tulajdonságmenüjéből a **Szűrő** elemet, amely rendelkezik a szűrendő értékkel.  

Csak a **szűrő** beállítást, amelyek neve kéken.  Ezek a *kereshető* mezők, amelyek keresési feltételekhez vannak indexelve.  A szürke mezők *kereshető szabad szöveges* mezők, amelyeknél csak a **Hivatkozások megjelenítése** beállítás aktív.  Ez a beállítás azokat a rekordokat adja vissza, amelyeknek bármely tulajdonságában megtalálható a keresett érték.

![Szűrés menün](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Egyetlen tulajdonság alapján csoportosíthatja az eredményeket, ha kiválasztja a rekord menüjének **Csoportosítási szempont** elemét.  Ez egy [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) (összegző) operátort ad a lekérdezéshez, amely egy diagramban jeleníti meg az eredményeket.  Több tulajdonság alapján is csoportosíthat, de ehhez közvetlenül kell szerkesztenie a lekérdezést.  Válassza ki a rekord menüjét a **Computer** (Számítógép) tulajdonság mellett, és válassza a **Csoportosítási szempont: „Computer”** (Számítógép) lehetőséget.  

![Csoportosítás számítógép alapján](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Munka az eredményül kapott adatokkal
A naplókeresési portál számos funkcióval rendelkezik a lekérdezések eredményeinek feldolgozásához.  Rendezheti, szűrheti és csoportosíthatja az eredményeket az adatok elemzése érdekében a tényleges lekérdezés módosítása nélkül.  A lekérdezések eredményei alapértelmezés szerint nincsenek rendezve.

Ha táblázatos formában szeretné megtekinteni az adatokat, ami további lehetőségeket nyújt a szűréshez és a rendezéshez, kattintson a **Tábla** gombra.  

![Tábla nézet](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Kattintson az egyik rekordnál található nyílra a rekord részleteinek megtekintéséhez.

![Az eredmények rendezése](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Bármely mező alapján rendezhet, ha az oszlopfejlécére kattint.

![Az eredmények rendezése](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Az oszlopban található valamelyik érték alapján szűrni is tudja az eredményeket, ha a szűrő gombra kattint, és megadja a szűrőfeltételt.

![Szűrés eredményei](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Egy oszlop alapján úgy csoportosíthat, ha az oszlopfejlécet az eredmények tetejére húzza.  Több mező alapján úgy csoportosíthat, ha több oszlopot húz felülre.

![Az eredmények csoportosítása](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Munka a teljesítményadatokkal
A rendszer a Windows- és a Linux-ügynökök teljesítményadatait is a Log Analytics-munkaterületen tárolja, a **Perf** táblában.  Néznek ki, mint a többi rekord, és a egy egyszerű lekérdezést, amely visszaadja az összes teljesítményrekordot az eseményekhez hasonlóan is írunk.

```
Perf
```

![Teljesítményadatok](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

Nem szerencsés azonban, ha a rendszer az összes teljesítményobjektum és számláló több millió rekordját visszaadja.  A fent használt módszerekkel szűrheti a kapott adatokat, vagy azt is megteheti, hogy a következő lekérdezést közvetlenül beírja a napló keresőmezőjébe.  Ez csak a Windows és Linux rendszerű számítógépek processzorhasználati rekordjait adja vissza.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Processzorhasználat](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

Ezzel egy adott számlálóra korlátozza az adatokat, de nem rendezi őket olyan formátumba, amely megkönnyítené a használatukat.  Megjelenítheti az adatokat egy vonaldiagramban, de először csoportosítania kell őket a Computer (Számítógép) és a TimeGenerated (Létrehozás időpontja) érték alapján.  Ha több mező alapján szeretne csoportosítani, közvetlenül kell módosítania a lekérdezést a következők szerint.  Ez a **CounterValue** tulajdonság [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) függvényével számítja ki órás lebontásban az átlagértéket.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Teljesítményadatok diagram](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Most, hogy az adatok megfelelően vannak csoportosítva, megjelenítheti őket egy vizuális diagramban a [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) operátor hozzáadásával.  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Vonaldiagram](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>További lépések

- További információ a Log Analytics lekérdezési nyelvet a [– első lépések az Analytics-portál](https://go.microsoft.com/fwlink/?linkid=856079).
- Oktatóanyag segítségével végigvezetik a [bővített analitika portál](https://go.microsoft.com/fwlink/?linkid=856587) amely lehetővé teszi, hogy az ugyanazon lekérdezéseket futtathat, és ugyanazokat az adatokat a Naplókeresési portál eléréséhez.
