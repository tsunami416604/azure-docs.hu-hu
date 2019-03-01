---
title: Kapacitás és teljesítmény megoldás az Azure Monitor |} A Microsoft Docs
description: A kapacitásra és teljesítményre megoldást használni a Monitor segítségével megismerheti a kapacitás, a Hyper-V-kiszolgálók.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: fcf71bf144b559c4867303988d4c1f08b7aa5605
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008627"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>A kapacitásra és teljesítményre megoldás (elavult) a Hyper-V virtuális gép kapacitásának megtervezése

![Kapacitás és teljesítmény szimbólum](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> A kapacitás és teljesítmény-megoldás elavult.  Ügyfelek, akik már telepítették a megoldás továbbra is használhatja, de a kapacitásra és teljesítményre nem vehető új munkaterületek.

A figyelő a kapacitás és teljesítmény megoldás használatával segítségével megismerheti a kapacitás, a Hyper-V-kiszolgálók. A megoldás biztosítja, hogy a Hyper-V környezetébe insights jeleníti meg a teljes kihasználtság (Processzor, memória és lemezek) a gazdagépek és a Hyper-V gazdagépeken futó virtuális gépek. Metrikákat gyűjt CPU, memória és a lemezek az összes gazdagép és a rajtuk futó virtuális gépek között.

A megoldás:

-   A legmagasabb és legalacsonyabb CPU és memória kihasználtságáról gazdagépei láthatók
-   Virtuális gépek látható, legmagasabb és legalacsonyabb CPU és memória kihasználtságáról
-   Virtuális gépek látható, legmagasabb és legalacsonyabb IOPS és átviteli kihasználtság
-   Jeleníti meg, amely a virtuális gépek mely állomásokon futnak
-   A felső lemezek nagy átviteli sebesség, IOPS és a késés jeleníti meg a fürt megosztott kötete
- Lehetővé teszi, hogy testre szabhatja és szűrése csoportok alapján

> [!NOTE]
> A kapacitásra és teljesítményre megoldást a kapacitáskezelés előző verziója szükséges a System Center Operations Manager és a System Center Virtual Machine Manager. A frissített megoldás ezeket a függőségeket nem rendelkezik.


## <a name="connected-sources"></a>Összekapcsolt források

Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Leírás |
|---|---|---|
| [Windows-ügynökök](../../azure-monitor/platform/agent-windows.md) | Igen | A megoldás kapacitás és teljesítmény adatokkal kapcsolatos információk Windows-ügynököktől gyűjti. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem    | A megoldás nem kapacitás és teljesítmény adatok adatainak gyűjtését a Linux-ügynökök közvetlen.|
| [Az SCOM felügyeleti csoport](../../azure-monitor/platform/om-agents.md) | Igen |A megoldás egy csatlakoztatott SCOM felügyeleti csoportban lévő ügynököktől származó kapacitás és teljesítmény adatokat gyűjt. Az SCOM-ügynöktől a Log Analyticshez való közvetlen kapcsolat nem kötelező.|
| [Azure Storage-fiók](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nem | Az Azure storage nem tartalmaz a kapacitás-és teljesítményadatokat.|

## <a name="prerequisites"></a>Előfeltételek

- Windows vagy az Operations Manager-ügynököket a Windows Server 2012 vagy újabb Hyper-V gazdagépek, virtuális gépek nem kell telepíteni.


## <a name="configuration"></a>Konfiguráció

Hajtsa végre a következő lépéseket a kapacitás és teljesítmény megoldás hozzáadása a munkaterülethez.

- Adja hozzá a kapacitás és teljesítmény megoldás a Log Analytics-munkaterülethez ismertetett folyamatot [adja hozzá a Log Analytics solutions kövesse a megoldástárban](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Felügyeleti csomagok

Ha az SCOM felügyeleti csoport a Log Analytics-munkaterülethez van csatlakoztatva, majd a következő felügyeleti csomagokat telepíti az SCOM Ez a megoldás hozzáadásakor. Ezek a felügyeleti csomagok nem igényelnek további konfigurációs vagy karbantartási feladatokat.

- Microsoft.IntelligencePacks.CapacityPerformance

A 1201 esemény a következőhöz hasonló:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Ha a kapacitás és teljesítmény megoldás frissül, a verziószám változik.

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>A megoldás használata

A kapacitásra és teljesítményre megoldás ad hozzá a munkaterülethez, ha a kapacitás és teljesítmény hozzáadódik az áttekintő irányítópult. Ez a csempe jelenleg aktív Hyper-V-gazdagépek számát jeleníti meg, és is figyeli az adott időszakban aktív virtuális gépek számát kiválasztva.

![Kapacitás és teljesítmény csempe](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Tekintse át a kihasználtság

Kattintson a kapacitás és teljesítmény csempére a kapacitás és teljesítmény irányítópultjának megnyitásához. Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlopban legfeljebb tíz olyan elem jelenik meg, amely megfelel a megadott hatóköri és időtartományi kritériumoknak. Az oszlop alján található **Az összes megtekintése** elemre vagy az oszlop fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

- **Hosts**
    - **Gazdagép CPU-kihasználtság** egy tendenciagrafikont hoz létre a számítógépcsoporthoz CPU-kihasználtság és a gazdagépek, a kijelölt időszak alapján listáját jeleníti meg. A kurzort a vonaldiagram időben egy adott időpontra részleteinek megtekintéséhez. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely Naplókeresés megnyitásához, ahol megtekintheti a CPU-számláló adatai üzemeltetett virtuális gépek gazdagép nevére.
    - **Gazdagép memóriahasználata** egy tendenciagrafikont hoz memóriahasználata gazdagépet számítógépek és a gazdagépek, a kijelölt időszak alapján listáját jeleníti meg. A kurzort a vonaldiagram időben egy adott időpontra részleteinek megtekintéséhez. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely Naplókeresés megnyitásához, ahol megtekintheti a memória számláló adatai üzemeltetett virtuális gépek gazdagép nevére.
- **Virtuális gépek**
    - **Virtuális gép CPU-kihasználtság** egy tendenciagrafikont hoz létre a CPU-kihasználtság virtuális gépek és virtuális gépek, a kijelölt időszak alapján listáját jeleníti meg. A kurzort a vonaldiagramot egy adott időpontra részleteinek megtekintése az idő az első 3 virtuális gépek. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely Naplókeresés megnyitásához, ahol megtekintheti a CPU-számláló összesített adatai a virtuális gép virtuális gép nevére.
    - **Virtuális gép memóriahasználata** egy tendenciagrafikont hoz létre virtuális gépek memóriahasználata és virtuális gépek, a kijelölt időszak alapján listáját jeleníti meg. A kurzort a vonaldiagramot egy adott időpontra részleteinek megtekintése az idő az első 3 virtuális gépek. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely virtuális gép neve a Naplókeresés megnyitásához, és a virtuális gép összesített memória számláló részleteinek megtekintéséhez.
    - **Virtuális gép teljes lemez IOPS** egy tendenciagrafikont hoz létre a teljes lemez iops-t a virtuális gépek és az iops, a virtuális gépek listáját jeleníti meg a kijelölt időszak alapján. A kurzort a vonaldiagramot egy adott időpontra részleteinek megtekintése az idő az első 3 virtuális gépek. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson az egyik virtuális gép nevére, és nyissa meg a napló keresési és összesített lemez-IOPS részletek számláló a virtuális gép.
    - **Virtuális lemezek teljes átviteli sebességének** egy tendenciagrafikont hoz létre a virtuális gépek a lemez teljes átviteli sebesség és a lemez teljes átviteli sebessége, a virtuális gépek listáját jeleníti meg a kijelölt időszak alapján. A kurzort a vonaldiagramot egy adott időpontra részleteinek megtekintése az idő az első 3 virtuális gépek. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely Naplókeresés megnyitásához, ahol megtekintheti a összesített lemez teljes átviteli sebesség teljesítményszámláló adatait a virtuális gép virtuális gép nevére.
- **Fürtözött megosztott kötetek**
    - **Teljes átviteli sebesség** fürtözött megosztott kötetek írási és az olvasásokat összegét mutatja.
    - **Teljes IOPS** bemeneti/kimeneti műveletek száma másodpercenként az összegét mutatja a fürt megosztott kötetei.
    - **Teljes késés** fürtözött megosztott köteteket jeleníti meg a teljes késést.
- **Gazdagép sűrűségű** a felső csempe gazdagépek és a megoldás számára elérhető virtuális gépek teljes számát jeleníti meg. Kattintson a felső csempére a naplókeresésben további részletek megtekintéséhez. Minden gazdagép és a virtuális gépek számát is megjeleníti. Kattintson egy gazdagépet a virtuális gép találatok a Naplókeresés részletes.


![Irányítópult gazdagépek panel](./media/capacity-performance/dashboard-hosts.png)

![Irányítópult (virtuális gépek) panel](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Teljesítmény kiértékelése

Éles környezetekhez nagy mértékben eltérnek egymástól az egyik szervezetből történő egy másikba. Ezenkívül számítási kapacitás és teljesítmény függhet hogyan a virtuális gépek futnak, és mit érdemes normál. Konkrét útmutatást mérték teljesítményt valószínűleg nem alkalmazhatók a környezetre. Így több általánosítva előíró útmutatás a jobban illeszkednek az segítségével. A Microsoft tesz közzé, előírásszerű útmutató cikkek segítségével különböző mérhetik a teljesítményt.

Összefoglalva, az a megoldás kapacitás és teljesítmény adatokat gyűjt különböző forrásokból, beleértve a teljesítményszámlálókat. Az, hogy a megoldás különböző felületek megjelenő kapacitás és teljesítmény adatokat, és hasonlítsa össze azokat, az eredményeket a [teljesítmény méréséhez, Hyper-v](https://msdn.microsoft.com/library/cc768535.aspx) cikk. Bár a cikket régebben lett közzétéve, a metrikák, szempontokat és irányelveket még érvényesek. A cikk egyéb hasznos források hivatkozásait tartalmazza.


## <a name="sample-log-searches"></a>Naplókeresési minták

Az alábbi táblázat a kapacitás és teljesítmény adatokat gyűjteni, és a megoldás által számított naplókeresési mintákat tartalmazza.


| Lekérdezés | Leírás |
|:--- |:--- |
| Az összes gazdagép-memóriakonfiguráció | Teljesítményoptimalizált &#124; ahol ObjectName == "Kapacitás és teljesítmény" és a CounterName == "Gazdagép hozzárendelt memória (MB)" &#124; MB összefoglalója avg(CounterValue) által InstanceName = |
| Az összes Virtuálisgép-memóriakonfiguráció | Teljesítményoptimalizált &#124; ahol ObjectName == "Kapacitás és teljesítmény" és a CounterName == "Virtuális Géphez rendelt memória (MB)" &#124; MB összefoglalója avg(CounterValue) által InstanceName = |
| Minden virtuális gép teljes lemez IOPS-művelet részletezése | Teljesítményoptimalizált &#124; ahol ObjectName == "Kapacitás és teljesítmény" és a (CounterName == "VHD olvasás/mp" vagy CounterName == "VHD írási műveletek/s") &#124; summarize AggregatedValue = avg(CounterValue) bin (TimeGenerated, 1 óra), amelyet CounterName, InstanceName |
| Minden virtuális gép teljes lemez átviteli sebességének részletezése | Teljesítményoptimalizált &#124; ahol ObjectName == "Kapacitás és teljesítmény" és a (CounterName == "VHD olvasási MB/s" vagy CounterName == "VHD Write MB/s") &#124; summarize AggregatedValue = avg(CounterValue) bin (TimeGenerated, 1 óra), amelyet CounterName, InstanceName |
| Az összes CSV-k összes i/o-művelet részletezése | Teljesítményoptimalizált &#124; ahol ObjectName == "Kapacitás és teljesítmény" és a (CounterName == "CSV olvasás/mp" vagy CounterName == "CSV-írási műveletek/s") &#124; summarize AggregatedValue = avg(CounterValue) bin (TimeGenerated, 1 óra), amelyet CounterName, InstanceName |
| Minden CSV-n teljes átviteli sebességének részletezése | Teljesítményoptimalizált &#124; ahol ObjectName == "Kapacitás és teljesítmény" és a (CounterName == "CSV olvasás/mp" vagy CounterName == "CSV-írási műveletek/s") &#124; summarize AggregatedValue = avg(CounterValue) bin (TimeGenerated, 1 óra), amelyet CounterName, InstanceName |
| Minden CSV-n teljes késés részletezése | Teljesítményoptimalizált &#124; ahol ObjectName == "Kapacitás és teljesítmény" és a (CounterName == "CSV olvasási késés" vagy CounterName == "CSV írási késés") &#124; summarize AggregatedValue = avg(CounterValue) bin (TimeGenerated, 1 óra), amelyet CounterName, InstanceName |


## <a name="next-steps"></a>További lépések
* Használat [Log Analytics naplóbeli kereséseivel](../../azure-monitor/log-query/log-query-overview.md) részletes kapacitásra és teljesítményre vonatkozó adatok megtekintéséhez.
