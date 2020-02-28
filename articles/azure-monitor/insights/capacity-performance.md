---
title: Capacity and Performance megoldás a Azure Monitorban | Microsoft Docs
description: A figyelő Capacity and Performance megoldásával megismerheti a Hyper-V-kiszolgálók kapacitását.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 75c65cf9f76e711a3aeed764de8b92ed619bad2f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666943"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>A Hyper-V virtuális gépek kapacitásának megtervezése a Capacity and Performance megoldással (elavult)

![Kapacitás és teljesítmény szimbólum](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> A kapacitás és teljesítmény-megoldás elavult.  Ügyfelek, akik már telepítették a megoldás továbbra is használhatja, de a kapacitásra és teljesítményre nem vehető új munkaterületek.

A figyelő Capacity and Performance megoldásával könnyebben megismerheti a Hyper-V-kiszolgálók kapacitását. A megoldás biztosítja, hogy a Hyper-V környezetébe insights jeleníti meg a teljes kihasználtság (Processzor, memória és lemezek) a gazdagépek és a Hyper-V gazdagépeken futó virtuális gépek. Metrikákat gyűjt CPU, memória és a lemezek az összes gazdagép és a rajtuk futó virtuális gépek között.

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
| [SCOM felügyeleti csoport](../../azure-monitor/platform/om-agents.md) | Igen |A megoldás egy csatlakoztatott SCOM felügyeleti csoportban lévő ügynököktől származó kapacitás és teljesítmény adatokat gyűjt. Az SCOM-ügynöktől a Log Analyticshez való közvetlen kapcsolat nem kötelező.|
| [Azure Storage-fiók](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nem | Az Azure storage nem tartalmaz a kapacitás-és teljesítményadatokat.|

## <a name="prerequisites"></a>Előfeltételek

- Windows vagy az Operations Manager-ügynököket a Windows Server 2012 vagy újabb Hyper-V gazdagépek, virtuális gépek nem kell telepíteni.


## <a name="configuration"></a>Konfiguráció

Hajtsa végre a következő lépéseket a kapacitás és teljesítmény megoldás hozzáadása a munkaterülethez.

- Adja hozzá a Capacity and Performance megoldást a Log Analytics munkaterülethez az [Solutions Gallery log Analytics-megoldások hozzáadása](../../azure-monitor/insights/solutions.md)című témakörben leírt eljárással.

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

- **Gazdagépek**
    - **GAZDAGÉP CPU-kihasználtsága** Megjeleníti a gazdagépek CPU-kihasználtságának grafikus trendjét, valamint a gazdagépek listáját a kiválasztott időszak alapján. A kurzort a vonaldiagram időben egy adott időpontra részleteinek megtekintéséhez. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely Naplókeresés megnyitásához, ahol megtekintheti a CPU-számláló adatai üzemeltetett virtuális gépek gazdagép nevére.
    - **Gazdagép memóriájának kihasználtsága** A gazdagépek memóriájának kihasználtságát és a gazdagépek listáját jeleníti meg a kiválasztott időszak alapján. A kurzort a vonaldiagram időben egy adott időpontra részleteinek megtekintéséhez. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely Naplókeresés megnyitásához, ahol megtekintheti a memória számláló adatai üzemeltetett virtuális gépek gazdagép nevére.
- **Virtual Machines**
    - **Virtuális gép CPU-kihasználtsága** Megjeleníti a virtuális gépek CPU-kihasználtságának grafikus trendjét, valamint a virtuális gépek listáját a kiválasztott időszak alapján. A kurzort a vonaldiagramot egy adott időpontra részleteinek megtekintése az idő az első 3 virtuális gépek. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely Naplókeresés megnyitásához, ahol megtekintheti a CPU-számláló összesített adatai a virtuális gép virtuális gép nevére.
    - **Virtuális gépek memóriájának kihasználtsága** Megjeleníti a virtuális gépek memóriahasználat és a virtuális gépek listájának grafikus trendjét a kiválasztott időszak alapján. A kurzort a vonaldiagramot egy adott időpontra részleteinek megtekintése az idő az első 3 virtuális gépek. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely virtuális gép neve a Naplókeresés megnyitásához, és a virtuális gép összesített memória számláló részleteinek megtekintéséhez.
    - **Virtuális gép – teljes lemez IOPS** Megjeleníti a virtuális gépek teljes IOPS grafikus trendjeit, valamint a virtuális gépek listáját, amelyekhez a IOPS a kiválasztott időszak alapján. A kurzort a vonaldiagramot egy adott időpontra részleteinek megtekintése az idő az első 3 virtuális gépek. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson az egyik virtuális gép nevére, és nyissa meg a napló keresési és összesített lemez-IOPS részletek számláló a virtuális gép.
    - **Virtuális gép teljes lemezének átviteli sebessége** Megjeleníti a virtuális gépek teljes lemezének átviteli sebességét, valamint a kiválasztott időszak alapján a virtuális gépek teljes lemezes átviteli sebességét tartalmazó listát. A kurzort a vonaldiagramot egy adott időpontra részleteinek megtekintése az idő az első 3 virtuális gépek. Kattintson a diagramra további részletek megtekintése a naplókeresésben. Kattintson bármely Naplókeresés megnyitásához, ahol megtekintheti a összesített lemez teljes átviteli sebesség teljesítményszámláló adatait a virtuális gép virtuális gép nevére.
- **Fürtözött megosztott kötetek**
    - **Teljes átviteli sebesség** A fürtözött megosztott kötetek olvasási és írási összegét jeleníti meg.
    - **Összes IOPS** A fürtözött megosztott köteteken másodpercenkénti bemeneti/kimeneti műveletek összegét jeleníti meg.
    - **Teljes késés** Megjeleníti a fürtözött megosztott kötetek teljes késését.
- **Gazdagép sűrűsége** A felső csempe a megoldás számára elérhető gazdagépek és virtuális gépek teljes számát jeleníti meg. Kattintson a felső csempére a naplókeresésben további részletek megtekintéséhez. Minden gazdagép és a virtuális gépek számát is megjeleníti. Kattintson egy gazdagépet a virtuális gép találatok a Naplókeresés részletes.


![Irányítópult gazdagépek panel](./media/capacity-performance/dashboard-hosts.png)

![Irányítópult (virtuális gépek) panel](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Teljesítmény kiértékelése

Éles környezetekhez nagy mértékben eltérnek egymástól az egyik szervezetből történő egy másikba. Ezenkívül számítási kapacitás és teljesítmény függhet hogyan a virtuális gépek futnak, és mit érdemes normál. Konkrét útmutatást mérték teljesítményt valószínűleg nem alkalmazhatók a környezetre. Így több általánosítva előíró útmutatás a jobban illeszkednek az segítségével. A Microsoft tesz közzé, előírásszerű útmutató cikkek segítségével különböző mérhetik a teljesítményt.

Összefoglalva, az a megoldás kapacitás és teljesítmény adatokat gyűjt különböző forrásokból, beleértve a teljesítményszámlálókat. Használja ezt a kapacitást és teljesítményadatokat, amelyet a megoldás különböző felületeinek mutatnak be, és hasonlítsa össze az eredményeket a [Hyper-V cikk mérési teljesítményének](https://msdn.microsoft.com/library/cc768535.aspx) megfelelő értékekkel. Bár a cikket régebben lett közzétéve, a metrikák, szempontokat és irányelveket még érvényesek. A cikk egyéb hasznos források hivatkozásait tartalmazza.


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


## <a name="next-steps"></a>Következő lépések
* A részletes Capacity and Performance-információk megtekintéséhez használja [a log Analytics a naplóbeli kereséseket](../../azure-monitor/log-query/log-query-overview.md) .
