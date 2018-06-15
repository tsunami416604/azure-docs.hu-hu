---
title: Az Azure Naplóelemzés kapacitást és teljesítményt megoldás |} Microsoft Docs
description: Log Analytics a kapacitást és teljesítményt megoldás segítségével megismerheti a kapacitás, a Hyper-V-kiszolgálók.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: db38678a05afbc764dec20f2a475e00856a1aeee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170033"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Hyper-V virtuális gép Kapacitástervezés a kapacitást és teljesítményt megoldás (előzetes verzió)

![Kapacitást és teljesítményt szimbólum](./media/log-analytics-capacity/capacity-solution.png)

> [!NOTE]
> A kapacitást és teljesítményt megoldás elavult.  Az ügyfelek, akik már telepítették a megoldás továbbra is használhatja, de kapacitást és teljesítményt is nem vehető fel bármely új munkaterületek.

A Naplóelemzési a kapacitást és teljesítményt megoldás segítségével megismerheti a kapacitás, a Hyper-V-kiszolgálók. A megoldás jelenít meg a teljes kihasználtság (Processzor, memória és lemez) a gazdagépek és azokat Hyper-V gazdagépeken futó virtuális gépeket a Hyper-V környezetben betekintést nyújt. Metrikák Processzor, memória és a lemezek gyűjtése történt a gazdagépek és a rajtuk futó virtuális gépek között.

A megoldás:

-   A legkisebb és legnagyobb Processzor- és memóriafelhasználását gazdagépei láthatók
-   A legkisebb és legnagyobb Processzor- és memóriafelhasználását jeleníti meg a virtuális gépek
-   Virtuális gépek biztosítanak legnagyobb és legkisebb iops-érték és az átvitel mellett látható
-   Jeleníti meg, amely mely állomásokon futó virtuális gépek
-   A felső lemez is van a magas teljesítmény, a iops-érték és a késleltetés jeleníti meg a fürt megosztott kötete
- Lehetővé teszi testre szabhatja, és a csoportok szűrése

> [!NOTE]
> A kapacitást és teljesítményt megoldást kínál kapacitás Management korábbi verziója szükséges a System Center Operations Manager és a System Center Virtual Machine Manager. A frissített megoldás azok nem rendelkezik.


## <a name="connected-sources"></a>Összekapcsolt források

Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Leírás |
|---|---|---|
| [Windows-ügynökök](log-analytics-windows-agent.md) | Igen | A megoldás kapacitást és teljesítményt adatok információt gyűjt a Windows-ügynökök. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem    | A megoldás nem kapacitást és teljesítményt adatok információkat gyűjtsön a közvetlen Linux-ügynököt.|
| [SCOM felügyeleti csoport](log-analytics-om-agents.md) | Igen |A megoldás kapacitás és teljesítményadatokat gyűjt az ügynökök a csatlakoztatott SCOM felügyeleti csoport. Naplóelemzési az SCOM-ügynököt a közvetlen kapcsolatra szükség.|
| [Azure Storage-fiók](log-analytics-azure-storage.md) | Nem | Az Azure storage nem tartalmazza a kapacitást és teljesítményt adatait.|

## <a name="prerequisites"></a>Előfeltételek

- A Windows vagy az Operations Manager-ügynökök telepítenie kell a Windows Server 2012 vagy újabb Hyper-V gazdagépen, a nem a virtuális gépek.


## <a name="configuration"></a>Konfiguráció

Hajtsa végre a következő lépés a kapacitást és teljesítményt megoldás hozzáadása a munkaterületen.

- A kapacitást és teljesítményt megoldás hozzáadni a Naplóelemzési munkaterület ismertetett eljárással [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).

## <a name="management-packs"></a>Felügyeleti csomagok

Ha az SCOM felügyeleti csoport csatlakoztatva van a Naplóelemzési munkaterület, majd a következő felügyeleti csomagokat telepíti scom ebben a megoldásban hozzáadásakor. Ezek a felügyeleti csomagok nem igényelnek további konfigurációs vagy karbantartási feladatokat.

- Microsoft.IntelligencePacks.CapacityPerformance

A 1201 esemény hasonlít:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Amikor a kapacitást és teljesítményt megoldás frissül, a verziószám változik.

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](log-analytics-om-agents.md).

## <a name="using-the-solution"></a>A megoldás használata

A kapacitást és teljesítményt megoldás a munkaterülethez való hozzáadásakor a kapacitást és teljesítményt hozzáadódik az áttekintő irányítópulthoz. Ez a csempe megjeleníti a jelenleg aktív Hyper-V-gazdagépek számát, és volt figyeli az adott időszakban aktív virtuális gépek száma a kijelölt.

![Kapacitást és teljesítményt csempe](./media/log-analytics-capacity/capacity-tile.png)


### <a name="review-utilization"></a>Tekintse át a kihasználtság

Kattintson a kapacitást és teljesítményt csempére a kapacitást és teljesítményt irányítópult megnyitásához. Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlopban legfeljebb tíz olyan elem jelenik meg, amely megfelel a megadott hatóköri és időtartományi kritériumoknak. Az oszlop alján található **Az összes megtekintése** elemre vagy az oszlop fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

- **Hosts**
    - **A gazdagép CPU-felhasználás** egy tendenciagrafikont hoz létre a CPU-felhasználást a számítógépek és a gazdagépet, a kijelölt időszakot listáját jeleníti meg. Mutasson a vonaldiagram időben adott részleteinek megtekintése. Kattintson a diagram megtekintheti annak további részleteit a napló keresési. Kattintson a napló keresési megnyithatja és megtekintheti a CPU teljesítményszámláló adatait üzemeltetett virtuális gépek egyetlen állomás nevét.
    - **Gazdagép memóriahasználata** egy tendenciagrafikont hoz létre a számítógépek memóriahasználata és a gazdagépet, a kijelölt időszakot listáját jeleníti meg. Mutasson a vonaldiagram időben adott részleteinek megtekintése. Kattintson a diagram megtekintheti annak további részleteit a napló keresési. Kattintson a napló keresési megnyithatja és megtekintheti a memória teljesítményszámláló adatait üzemeltetett virtuális gépek egyetlen állomás nevét.
- **Virtuális gépek**
    - **VM CPU-felhasználás** egy tendenciagrafikont hoz létre a processzorkihasználtság, virtuális gépek és virtuális gépek, a kijelölt időszakot alapuló listáját jeleníti meg. Mutasson a vonaldiagram meg az adott időben a felső 3 virtuális gépek számára. Kattintson a diagram megtekintheti annak további részleteit a napló keresési. Kattintson a napló keresési megnyithatja és megtekintheti a összesített CPU teljesítményszámláló adatait a virtuális gép egyetlen virtuális gép nevét.
    - **Virtuális gép memória-felhasználás** egy tendenciagrafikont hoz létre a virtuális gépek memóriahasználata és a virtuális gépek, a kijelölt időszakot alapuló listáját jeleníti meg. Mutasson a vonaldiagram meg az adott időben a felső 3 virtuális gépek számára. Kattintson a diagram megtekintheti annak további részleteit a napló keresési. Kattintson az egyik virtuális gép nevére, és nyissa meg a keresési napló és a virtuális gép összesített memória számláló részleteinek megtekintése.
    - **Virtuális gép összes lemez IOPS** egy tendenciagrafikont hoz létre a teljes lemez a virtuális gépek IOPS és az IOPS, az egyes, a virtuális gépek listáját jeleníti meg a kijelölt időszakot alapján. Mutasson a vonaldiagram meg az adott időben a felső 3 virtuális gépek számára. Kattintson a diagram megtekintheti annak további részleteit a napló keresési. Kattintson az egyik virtuális gép nevére, és nyissa meg a naplófájl-keresési és összesített lemez IOPS számláló a virtuális gép részletei.
    - **Virtuális gép teljes lemez átviteli sebesség** egy tendenciagrafikont hoz létre az a lemez teljes átviteli sebesség a virtuális gépek és a virtuális gépek minden egyes, a lemez teljes átviteli sebesség a listáját jeleníti meg a kijelölt időszakot alapján. Mutasson a vonaldiagram meg az adott időben a felső 3 virtuális gépek számára. Kattintson a diagram megtekintheti annak további részleteit a napló keresési. Kattintson a napló keresési megnyithatja és megtekintheti a összesített a lemez teljes átviteli sebesség teljesítményszámláló adatait a virtuális gép egyetlen virtuális gép nevét.
- **A fürt megosztott kötetei**
    - **Teljes átviteli sebesség** mindkét olvasások összegét mutatja, és a fürt megosztott kötetei azt.
    - **Teljes IOPS** a fürtözött megosztott kötetek bemeneti/kimeneti műveletek száma másodpercenként összegzése látható.
    - **Teljes késést** mutatja be a teljes késést fürtözött megosztott köteteket.
- **Gazdagép sűrűség** az első csempe a gazdagépek és virtuális gépek érhető el, a megoldásra mutató teljes számát mutatja. Kattintson a felső csempe a naplófájl-keresési további részletek megtekintéséhez. Minden gazdagép és virtuális gépek számát is tartalmazza. Kattintson egy gazdagépet a virtuális gép eredményezi, a naplófájl-keresési elemezze a.


![Irányítópult állomások panel](./media/log-analytics-capacity/dashboard-hosts.png)

![Irányítópult virtuális gépek panelje](./media/log-analytics-capacity/dashboard-vms.png)


### <a name="evaluate-performance"></a>Teljesítmény kiértékelése

A termelési számítási környezetek eltérőek jelentősen egy szervezet. Emellett kapacitást és teljesítményt munkaterhelések függhet hogyan a virtuális gépek futnak, és Ön normál érdemes. Speciális eljárásokat segítséget mérték teljesítmény akkor valószínűleg nem vonatkozik a környezetben. Így több általánosítva előíró útmutatás a jobban illeszkedik segítségével. A Microsoft tesz közzé az előírásoknak megfelelő útmutató cikkek segítségével különböző mérni a teljesítményt.

Összefoglalva, a megoldás kapacitás és teljesítményadatokat gyűjt a különböző forrásokból, beleértve a teljesítményszámlálókat. Az, hogy a különböző felületek, a megoldás bemutatott kapacitást és teljesítményt adatokat, és hasonlítsa össze azokat a az eredményeket a [teljesítménye méri a Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) cikk. A cikk régebben lett közzétéve, bár a metrikákat, szempontok és irányelveket még érvényesek. Egyéb hasznos források mutató hivatkozásokat tartalmaz.


## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a kapacitást és teljesítményt adatokat gyűjt, és ez a megoldás által kiszámított minta napló keres.


| Lekérdezés | Leírás |
|:--- |:--- |
| Az összes állomás tárolómemória beállításai | A Teljesítményfigyelő &#124; ahol ObjectName == "Kapacitást és teljesítményt" és a CounterName == "Állomás hozzárendelt memória (MB)" &#124; MB összefoglalója által példánynév avg(CounterValue) = |
| Az összes virtuális gép memória konfigurációja | A Teljesítményfigyelő &#124; ahol ObjectName == "Kapacitást és teljesítményt" és a CounterName == "Virtuális gép hozzárendelt memória (MB)" &#124; MB összefoglalója által példánynév avg(CounterValue) = |
| Minden virtuális gép közötti összes lemez IOPS bontása | A Teljesítményfigyelő &#124; ahol ObjectName == "Kapacitást és teljesítményt" és (CounterName == "VHD olvasási műveletek/mp" vagy a CounterName == "VHD írási műveletek/mp") &#124; AggregatedValue összefoglalója bin (TimeGenerated, 1 óra), amelyet avg(CounterValue) = CounterName, az InstanceName |
| Minden virtuális gépek között a teljes lemez átviteli lebontása | A Teljesítményfigyelő &#124; ahol ObjectName == "Kapacitást és teljesítményt" és (CounterName == "VHD olvasási MB/s" vagy a CounterName == "VHD írás MB/s") &#124; AggregatedValue összefoglalója bin (TimeGenerated, 1 óra), amelyet avg(CounterValue) = CounterName, az InstanceName |
| Minden CSV-k között teljes IOPS bontása | A Teljesítményfigyelő &#124; ahol ObjectName == "Kapacitást és teljesítményt" és (CounterName == "CSV olvasási műveletek/mp" vagy a CounterName == "CSV írási műveletek/mp") &#124; AggregatedValue összefoglalója bin (TimeGenerated, 1 óra), amelyet avg(CounterValue) = CounterName, az InstanceName |
| Teljes átviteli sebesség minden CSV-k között bontása | A Teljesítményfigyelő &#124; ahol ObjectName == "Kapacitást és teljesítményt" és (CounterName == "CSV olvasási műveletek/mp" vagy a CounterName == "CSV írási műveletek/mp") &#124; AggregatedValue összefoglalója bin (TimeGenerated, 1 óra), amelyet avg(CounterValue) = CounterName, az InstanceName |
| Minden CSV-k között teljes késést bontása | A Teljesítményfigyelő &#124; ahol ObjectName == "Kapacitást és teljesítményt" és (CounterName == "CSV olvasási késése" vagy a CounterName == "CSV írási késése") &#124; AggregatedValue összefoglalója bin (TimeGenerated, 1 óra), amelyet avg(CounterValue) = CounterName, az InstanceName |


## <a name="next-steps"></a>További lépések
* Használjon [Log Analytics-e jelentkezni a keresések](log-analytics-log-search.md) kapacitást és teljesítményt részletes adatainak megtekintéséhez.
