---
title: Kapacitás- és teljesítménymegoldás az Azure Monitorban | Microsoft dokumentumok
description: Használja a Monitor kapacitás- és teljesítménymegoldását a Hyper-V-kiszolgálók kapacitásának megértéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 75c65cf9f76e711a3aeed764de8b92ed619bad2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666943"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>A Hyper-V virtuálisgép kapacitásának megtervezése a Kapacitás és teljesítmény megoldással (elavult)

![Kapacitás és teljesítmény szimbólum](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> A Kapacitás és teljesítmény megoldás elavult.  Azok az ügyfelek, akik már telepítették a megoldást, továbbra is használhatják, de a Kapacitás és a Teljesítmény nem adható hozzá új munkaterületekhez.

A Monitor kapacitás- és teljesítménymegoldásával megismerheti a Hyper-V-kiszolgálók kapacitását. A megoldás betekintést nyújt a Hyper-V környezetbe azáltal, hogy bemutatja a gazdagépek és a Hyper-V gazdagépeken futó virtuális gépek általános kihasználtságát (CPU, memória és lemez). Metrikák a cpu, a memória és a lemezek az összes gazdagép és a rajtuk futó virtuális gépek között.

A megoldás:

-   A legmagasabb és a legalacsonyabb processzorral és memóriakihasználtsú állomások megjelenítése
-   A legmagasabb és a legalacsonyabb processzorral és memóriakihasználtsú virtuális gépeket jeleníti meg
-   A legmagasabb és a legalacsonyabb IOPS-értékkel és átviteli kihasználtsult megjelenítő virtuális gépeket jeleníti meg
-   Megmutatja, hogy mely virtuális gépek futnak, mely gazdagépeken
-   A nagy átviteli sebességgel, IOPS-szal és késéssel rendelkező legfelső lemezek megjelenítése a fürt megosztott kötetein
- Lehetővé teszi csoportok alapján testre szabható és szűrhet

> [!NOTE]
> A Kapacitás- és teljesítménymegoldás korábbi verziójához, a Capacity Managementhez a System Center Operations Manager és a System Center Virtual Machine Manager is szükséges. Ez a frissített megoldás nem rendelkezik ezekkel a függőségekkel.


## <a name="connected-sources"></a>Összekapcsolt források

Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Leírás |
|---|---|---|
| [Windows-ügynökök](../../azure-monitor/platform/agent-windows.md) | Igen | A megoldás kapacitás- és teljesítményadatokat gyűjt a Windows-ügynököktől. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem    | A megoldás nem gyűjt kapacitás- és teljesítményadatokat a közvetlen Linux-ügynököktől.|
| [SCOM felügyeleti csoport](../../azure-monitor/platform/om-agents.md) | Igen |A megoldás kapacitás- és teljesítményadatokat gyűjt egy csatlakoztatott SCOM felügyeleti csoport ügynökeitől. Az SCOM-ügynök közvetlen kapcsolata a Log Analytics szolgáltatással nem szükséges.|
| [Azure tárfiók](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nem | Az Azure storage nem tartalmazza a kapacitás és a teljesítmény adatokat.|

## <a name="prerequisites"></a>Előfeltételek

- A Windows vagy az Operations Manager ügynököket windows Server 2012 vagy újabb Hyper-V állomásokra kell telepíteni, nem pedig virtuális gépekre.


## <a name="configuration"></a>Konfiguráció

Hajtsa végre a következő lépést a Kapacitás és teljesítmény megoldás munkaterülethez való hozzáadásához.

- Adja hozzá a Kapacitás és teljesítmény megoldást a Log Analytics-munkaterülethez a [Megoldások tárból származó LogAnalytics-megoldások hozzáadása](../../azure-monitor/insights/solutions.md)című részben ismertetett folyamat tal.

## <a name="management-packs"></a>Felügyeleti csomagok

Ha az SCOM felügyeleti csoport csatlakozik a Log Analytics-munkaterülethez, akkor a következő felügyeleti csomagok lesznek telepítve az SCOM-ban a megoldás hozzáadásakor. Ezek a felügyeleti csomagok nem igényelnek további konfigurációs vagy karbantartási feladatokat.

- Microsoft.IntelligencePacks.CapacityPerformance

Az 1201-es esemény a következőkhöz hasonlít:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

A Kapacitás és teljesítmény megoldás frissítésekor a verziószám megváltozik.

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>A megoldás használata

Amikor hozzáadja a Kapacitás és teljesítmény megoldást a munkaterülethez, a Kapacitás és teljesítmény hozzáadódik az Áttekintés irányítópulthoz. Ez a csempe az aktuálisan aktív Hyper-V-állomások számát és a figyelt aktív virtuális gépek számát jeleníti meg a kiválasztott időszakban.

![Kapacitás és teljesítmény csempe](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Használat áttekintése

Kattintson a Kapacitás és teljesítmény csempére a Kapacitás és teljesítmény irányítópult megnyitásához. Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlopban legfeljebb tíz olyan elem jelenik meg, amely megfelel a megadott hatóköri és időtartományi kritériumoknak. Az oszlop alján található **Az összes megtekintése** elemre vagy az oszlop fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

- **Házigazdák**
    - **Állomás CPU-kihasználtsága** A gazdaszámítógépek cpu-kihasználtságának grafikus trendjét és az állomások listáját jeleníti meg a kiválasztott időszak alapján. Mutasson a vonaldiagramra egy adott időpont részleteinek megtekintéséhez. Kattintson a diagramra a további részletek megtekintéséhez a naplókeresésben. Kattintson bármelyik állomásnévre a naplókeresés megnyitásához és a üzemeltetett virtuális gépek CPU-számlálójának részleteinek megtekintéséhez.
    - **Gazdamemória kihasználtsága** A gazdaszámítógépek memóriafelhasználásának grafikus trendjét és az állomások listáját jeleníti meg a kiválasztott időszak alapján. Mutasson a vonaldiagramra egy adott időpont részleteinek megtekintéséhez. Kattintson a diagramra a további részletek megtekintéséhez a naplókeresésben. Kattintson bármelyik állomásnévre a naplókeresés megnyitásához és a tárolt virtuális gépek memóriaszámláló-részleteinek megtekintéséhez.
- **Virtuális gépek**
    - **Virtuális gép cpu-kihasználtsága** A virtuális gépek CPU-kihasználtságának grafikus trendjét és a virtuális gépek listáját jeleníti meg a kiválasztott időszak alapján. Vigye az egérmutatót a vonaldiagram fölé a felső 3 virtuális gép adott időpontjának részleteinek megtekintéséhez. Kattintson a diagramra a további részletek megtekintéséhez a naplókeresésben. Kattintson bármelyik virtuális gép nevére a naplókeresés megnyitásához és a virtuális gép összesített CPU-számlálórészleteinek megtekintéséhez.
    - **Virtuális gép memóriakihasználtsága** A virtuális gépek memóriakihasználtságának grafikus trendjét és a virtuális gépek listáját jeleníti meg a kiválasztott időszak alapján. Vigye az egérmutatót a vonaldiagram fölé a felső 3 virtuális gép adott időpontjának részleteinek megtekintéséhez. Kattintson a diagramra a további részletek megtekintéséhez a naplókeresésben. Kattintson bármelyik virtuális gép nevére a naplókeresés megnyitásához és a virtuális gép összesített memóriaszámláló-részleteinek megtekintéséhez.
    - **Virtuális gép teljes lemezi iOPS-a** A virtuális gépek teljes lemezIOPS-azonosítójának grafikus trendjét jeleníti meg, és a kiválasztott időszak alapján az egyes eszközökre vonatkozó IOPS-t tartalmazó virtuális gépek listáját jeleníti meg. Vigye az egérmutatót a vonaldiagram fölé a felső 3 virtuális gép adott időpontjának részleteinek megtekintéséhez. Kattintson a diagramra a további részletek megtekintéséhez a naplókeresésben. Kattintson bármelyik virtuális gép nevét a naplókeresés megnyitásához és a virtuális gép összesített lemez IOPS-számláló részleteinek megtekintéséhez.
    - **Virtuális gép teljes lemezátviteli-átata** A virtuális gépek teljes lemezátviteli-állásának grafikus trendjét és az egyes gépek teljes lemezátviteli-átmenő virtuális gépeinek listáját jeleníti meg a kiválasztott időszak alapján. Vigye az egérmutatót a vonaldiagram fölé a felső 3 virtuális gép adott időpontjának részleteinek megtekintéséhez. Kattintson a diagramra a további részletek megtekintéséhez a naplókeresésben. Kattintson bármelyik virtuális gép nevére a naplókeresés megnyitásához és a virtuális gép összesített teljes lemezátviteli számlálójának részleteinek megtekintéséhez.
- **Fürtözött megosztott kötetek**
    - **Teljes átviteli** A fürtözött megosztott kötetekolvasásek és írások összegzését jeleníti meg.
    - **IOPS összesen** A fürtözött megosztott köteteken másodpercenkénti bemeneti/kimeneti műveletek összegét jeleníti meg.
    - **Teljes késés** A fürtözött megosztott kötetek teljes késését jeleníti meg.
- **Állomás sűrűsége** A legfelső csempén a megoldás számára elérhető állomások és virtuális gépek teljes száma látható. A naplókeresésben a legfelső csempére kattintva megtekintheti a további részleteket. Felsorolja az összes állomást és a üzemeltetett virtuális gépek számát is. Kattintson egy állomásra a virtuális gép eredményeinek naplókeresésben történő részletezéséhez.


![műszerfal hosts panel](./media/capacity-performance/dashboard-hosts.png)

![műszerfal virtuális gépek blade](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Teljesítmény kiértékelése

A termelési számítástechnikai környezetek nagymértékben különböznek szervezetenként. Emellett a kapacitás és a teljesítmény számítási feladatok függhet a ttól, hogy a virtuális gépek futnak, és mit tekint normálisnak. A teljesítmény mérését segítő speciális eljárások valószínűleg nem vonatkoznak a környezetre. Így az általánosabb előíró útmutatás alkalmasabb a segítségre. A Microsoft számos előíró útmutatócikket tesz közzé a teljesítmény mérése érdekében.

Összefoglalva, a megoldás kapacitás- és teljesítményadatokat gyűjt különböző forrásokból, beleértve a teljesítményszámlálókat is. Használja ki a megoldás különböző felületein megjelenő kapacitás- és teljesítményadatokat, és hasonlítsa össze az eredményeket a [Hyper-V mérési teljesítmény című](https://msdn.microsoft.com/library/cc768535.aspx) cikkében szereplő adatokkal. Bár a cikk néhány évvel ezelőtt jelent meg, a mérőszámok, szempontok és irányelvek továbbra is érvényesek. A cikk más hasznos forrásokra mutató hivatkozásokat tartalmaz.


## <a name="sample-log-searches"></a>Naplókeresési minták

Az alábbi táblázat mintanapló-kereséseket tartalmaz az ezzel a megoldással gyűjtött és kiszámított kapacitás- és teljesítményadatokra vonatkozóan.


| Lekérdezés | Leírás |
|:--- |:--- |
| Az összes állomásmemória-konfiguráció | Perf &#124; ahol az ObjectName == "Kapacitás és teljesítmény" és a CounterName == "Állomás hozzárendelt memória MB" &#124; összegzi az MB = átlagot(CounterValue) a InstanceName szerint |
| Az összes virtuálisgép-memória konfigurációja | Perf &#124; ahol az ObjectName == "Kapacitás és teljesítmény" és a CounterName == "VM hozzárendelt memória MB" &#124; összegzi az MB = átlagot(CounterValue) a InstanceName szerint |
| A teljes lemez IOPS-ának lebontása az összes virtuális gép között | Perf &#124; ahol az ObjectName == "Kapacitás és teljesítmény" és (CounterName == "VHD olvasás/s" vagy CounterName == "VHD writes/s") &#124; összegző összesített érték = átlag(CounterValue) bin(TimeGenerated, 1h), CounterName, InstanceName |
| A teljes lemezátviteli-rendszer lebontása az összes virtuális gép között | Perf &#124; ahol az ObjectName == "Kapacitás és teljesítmény" és (CounterName == "VHD Read MB/s" vagy CounterName == "VHD Write MB/s") &#124; összegződik az AggregatedValue = avg(CounterValue) tárolóval(TimeGenerated, 1h), CounterName, InstanceName |
| Az összes IOPS bontása az összes CSV-n | Perf &#124; ahol az ObjectName == "Kapacitás és teljesítmény" és (CounterName == "CSV olvasás/s" vagy CounterName == "CSV írások/s") &#124; összegződik az AggregatedValue = avg(CounterValue) raktárhely(TimeGenerated, 1h), CounterName, InstanceName szerint |
| A teljes átviteli érték bontása az összes csv-n | Perf &#124; ahol az ObjectName == "Kapacitás és teljesítmény" és (CounterName == "CSV olvasás/s" vagy CounterName == "CSV írások/s") &#124; összegződik az AggregatedValue = avg(CounterValue) raktárhely(TimeGenerated, 1h), CounterName, InstanceName szerint |
| A teljes késés bontása az összes csv-n | Perf &#124; ahol az ObjectName == "Kapacitás és teljesítmény" és (CounterName == "CSV olvasási késés" vagy countername == "CSV írási késés") &#124; összegezi az AggregatedValue = avg(CounterValue) raktárhely(TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>További lépések
* A [Log Analytics naplókeresései](../../azure-monitor/log-query/log-query-overview.md) vel részletes kapacitás- és teljesítményadatokat tekinthet meg.
