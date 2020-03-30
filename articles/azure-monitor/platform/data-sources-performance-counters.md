---
title: Teljesítményszámlálók gyűjtése és elemzése az Azure Monitorban | Microsoft dokumentumok
description: A teljesítményszámlálókat az Azure Monitor gyűjti a Windows és Linux ügynökök teljesítményének elemzéséhez.  Ez a cikk ismerteti, hogyan konfigurálhatja a teljesítményszámlálók gyűjteménye a Windows és linuxos ügynökök, azok részleteit a munkaterületen tárolja, és hogyan elemezheti őket az Azure Portalon.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: d1a972a1d89066b961f2dcc28fba830e3a04ebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274761"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Windows- és Linux-teljesítményadatforrások az Azure Monitorban
A Windows és linuxos teljesítményszámlálók betekintést nyújtanak a hardverösszetevők, operációs rendszerek és alkalmazások teljesítményébe.  Az Azure Monitor a teljesítményadatok hosszú távú elemzéshez és jelentéskészítéshez való összesítése mellett gyakori időközönként teljesítményszámlálókat gyűjthet a közel-real idejű (NRT) elemzéshez.

![Teljesítményszámlálók](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása
Teljesítményszámlálók konfigurálása a [Speciális beállítások párbeszédpanel Adatok menüjéből.](agent-data-sources.md#configuring-data-sources)

Amikor először konfigurálja a Windows vagy Linux performance számlálókat egy új munkaterülethez, lehetősége van arra, hogy gyorsan hozzon létre több gyakori számlálót.  Ezek mindegyike mellett egy jelölőnégyzet található.  Győződjön meg arról, hogy az eredetileg létrehozni kívánt számlálók be vannak jelölve, majd kattintson **a Kijelölt teljesítményszámlálók hozzáadása gombra**.

A Windows teljesítményszámlálók esetében minden teljesítményszámlálóhoz választhat egy adott példányt. Linux teljesítményszámlálók esetén a kiválasztott számlálók példánya a szülőszámláló összes gyermekszámlálójára vonatkozik. Az alábbi táblázat a Linux és a Windows teljesítményszámlálói számára elérhető általános példányokat mutatja be.

| Példány neve | Leírás |
| --- | --- |
| \_Összesen |Az összes példány összege |
| \* |Minden példány |
| (/&#124;/var) |Egyezik a következő nek nevezett példányokkal: / vagy /var |

### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók

![A Windows teljesítményszámlálóinak konfigurálása](media/data-sources-performance-counters/configure-windows.png)

Az alábbi eljárással új Windows teljesítményszámlálót adhat hozzá a gyűjtéshez.

1. Írja be a számláló nevét a *formátumobjektum(példány)\számláló szövegmezőjébe.*  Amikor elkezd gépelni, megjelenik a közös számlálók megfelelő listája.  Választhat egy számlálót a listából, vagy beírhatja a sajátját.  Egy adott számláló összes példányát visszaadhatja *az object\counter*kapcsoló megadásával.  

    Az SQL Server teljesítményszámlálóinak névvel ellátott példányokból történő gyűjtésekor az összes elnevezett példányszámláló *az MSSQL$* programmal kezdődik, majd a példány neve követi.  Ha például az ÖSSZES adatbázis naplógyorsítótár-találati arányszámlálóját szeretné összegyűjteni az INST2 nevű SQL-példány adatbázis-teljesítményobjektumából, adja meg a ( log cache hit ratio) számlálót az ADATBÁZIS teljesítményobjektumából az INST2 nevű példányhoz, adja meg a . `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`

2. Az **+** Enter gombra kattintva vagy az **Enter** billentyű lenyomásával vegye fel a számlálót a listára.
3. Amikor hozzáad egy számlálót, az alapértelmezett 10 másodpercet használja a **mintavételi időközhöz.**  Ezt módosíthatja akár 1800 másodperc (30 perc) magasabb értékre, ha csökkenteni szeretné az összegyűjtött teljesítményadatok tárolási követelményeit.
4. Ha végzett a számlálók hozzáadásával, a képernyő tetején található **Mentés** gombra kattintva mentse a konfigurációt.

### <a name="linux-performance-counters"></a>Linux teljesítményszámlálók

![Linux teljesítményszámlálók konfigurálása](media/data-sources-performance-counters/configure-linux.png)

Kövesse ezt az eljárást, hogy egy új Linux teljesítményszámlálót adjon hozzá a gyűjtéshez.

1. Alapértelmezés szerint az összes konfigurációs módosítás automatikusan lekerül az összes ügyintézőre.  Linux-ügynökök esetében a rendszer konfigurációs fájlt küld a Fluentd adatgyűjtőnek.  Ha manuálisan szeretné módosítani ezt a fájlt minden Linux-ügynökön, akkor törölje a jelet az *Alábbi konfiguráció alkalmazása a Linux-gépeimre,* és kövesse az alábbi útmutatást.
2. Írja be a számláló nevét a *formátumobjektum(példány)\számláló szövegmezőjébe.*  Amikor elkezd gépelni, megjelenik a közös számlálók megfelelő listája.  Választhat egy számlálót a listából, vagy beírhatja a sajátját.  
3. Az **+** Enter gombra kattintva vagy az **Enter** billentyű lenyomásával vegye fel a számlálót az objektum többi számlálójának listájára.
4. Az objektum összes számlálója ugyanazt a **mintavételi időközt**használja.  Az alapértelmezett érték 10 másodperc.  Ha csökkenteni szeretné az összegyűjtött teljesítményadatok tárolási igényeit, akkor ezt magasabb, akár 1800 másodperces (30 perces) értékre is módosíthatja.
5. Ha végzett a számlálók hozzáadásával, a képernyő tetején található **Mentés** gombra kattintva mentse a konfigurációt.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Linux teljesítményszámlálók konfigurálása a konfigurációs fájlban
A Linux teljesítményszámlálók konfigurálása az Azure Portalhasználatával, lehetősége van a konfigurációs fájlok szerkesztése a Linux-ügynök.  Az összegyűjtésre begyűjthető teljesítménymutatókat az **/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf**.

Az összegyűjtött teljesítménymutatók minden objektumát vagy kategóriáját egyetlen `<source>` elemként kell definiálni a konfigurációs fájlban. A szintaxis az alábbi mintát követi.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Az elem paramétereit az alábbi táblázat ismerteti.

| Paraméterek | Leírás |
|:--|:--|
| objektum\_neve | A gyűjtemény objektumneve. |
| példa\_regex |  Reguláris *kifejezés,* amely meghatározza, hogy mely példányokat kell összegyűjteni. Az érték: `.*` megadja az összes példányt. Ha csak az \_Összes példány processzormetrikáját szeretné gyűjteni, megadhatja a értéket. `_Total` Ha csak a crond vagy sshd példányok folyamatmetrikákat szeretne gyűjteni, megadhatja a következőket: `(crond\|sshd)`. |
| számláló\_\_neve regex | Reguláris *kifejezés,* amely meghatározza, hogy mely számlálókat (az objektumhoz) kell gyűjteni. Az objektum összes számlálójának összegyűjtéséhez adja meg a következőket: `.*`. Ha például csak a memóriaobjektum hoz a lapozóterület-számlálókat szeretné gyűjteni, megadhatja a következőket:`.+Swap.+` |
| interval | Az objektum számlálóinak összegyűjtésének gyakorisága. |


Az alábbi táblázat felsorolja a konfigurációs fájlban megadható objektumokat és számlálókat.  Bizonyos alkalmazásokhoz további számlálók érhetők el az [Azure Monitor Linux-alkalmazások teljesítményszámlálóinak gyűjtése című részben leírtak szerint.](data-sources-linux-applications.md)

| Objektum neve | Számláló neve |
|:--|:--|
| Logikai lemez | % szabad inodák |
| Logikai lemez | Szabad terület %-os |
| Logikai lemez | Használt inodák %-a |
| Logikai lemez | % használt terület |
| Logikai lemez | Lemez olvasási bájtjai/mp |
| Logikai lemez | Lemezolvasás/mp |
| Logikai lemez | Lemezátvitel/mp |
| Logikai lemez | Lemezírási bájtok/mp |
| Logikai lemez | Lemezírás/mp |
| Logikai lemez | Ingyenes Megabájt |
| Logikai lemez | Logikai lemezbájt/mp |
| Memory (Memória) | Rendelkezésre álló memória %-a |
| Memory (Memória) | A rendelkezésre álló csereterület %-a |
| Memory (Memória) | Felhasznált memória %-a |
| Memory (Memória) | % Használt csereterület |
| Memory (Memória) | Elérhető MByte memória |
| Memory (Memória) | Elérhető MByteswap |
| Memory (Memória) | Oldalolvasások/mp |
| Memory (Memória) | Lapírások/mp |
| Memory (Memória) | Oldalak/mp |
| Memory (Memória) | Használt MBytes swap tér |
| Memory (Memória) | Használt memória MBytes |
| Network (Hálózat) | Átküldött bájtok összesen |
| Network (Hálózat) | Fogadott bájtok összesen |
| Network (Hálózat) | Összes bájt |
| Network (Hálózat) | Átküldött csomagok összesen |
| Network (Hálózat) | Fogadott csomagok összesen |
| Network (Hálózat) | Rx hibák összesen |
| Network (Hálózat) | Összes Tx hiba |
| Network (Hálózat) | Ütközések összesen |
| Fizikai lemez | Átlagos lemezmásodperc/olvasás |
| Fizikai lemez | Átlagos lemezmásodperc/átvitel |
| Fizikai lemez | Átlagos lemez másodperc/írás |
| Fizikai lemez | Fizikai lemezbájt/mp |
| Folyamat | Pct privilegizált idő |
| Folyamat | Pct felhasználói idő |
| Folyamat | Használt memóriakbyte-ok |
| Folyamat | Virtuális megosztott memória |
| Processzor | DPC-idő %-os ideje |
| Processzor | Üresjáratban eltöltött időhányad (%) |
| Processzor | Megszakítási idő %-os ideje |
| Processzor | %-os IO várakozási idő |
| Processzor | % Szép idő |
| Processzor | Jogosultsági idő %-os százaléka |
| Processzor | A processzor kihasználtsága (%) |
| Processzor | %felhasználói idő |
| Rendszer | Szabad fizikai memória |
| Rendszer | Szabad terület a lapozófájlokban |
| Rendszer | Ingyenes virtuális memória |
| Rendszer | Folyamatok |
| Rendszer | Lapozófájlokban tárolt méret |
| Rendszer | Uptime |
| Rendszer | Felhasználók |


A következő a teljesítménymutatók alapértelmezett konfigurációja.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Adatgyűjtés
Az Azure Monitor összegyűjti az összes megadott teljesítményszámlálók a megadott mintavételi időközminden olyan ügynökök, amelyek a számláló telepítve van.  Az adatok nincsenek összesítve, és a nyers adatok az előfizetés által megadott időtartamú összes naplólekérdezési nézetben elérhetők.

## <a name="performance-record-properties"></a>Teljesítményrekord tulajdonságai
A teljesítményrekordok **perf** típusúak, és az alábbi táblázatban található tulajdonságokkal rendelkeznek.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Számítógép, amelyből az eseményt gyűjtötték. |
| Ellennév |A teljesítményszámláló neve |
| CounterPath (Ellengörbe) |A számláló teljes elérési \\ \\ \<útja \\a Számítógép\\>objektum(példány) számláló formájában. |
| Ellenérték |A számláló numerikus értéke. |
| Példánynév |Az eseménypéldány neve.  Üres, ha nincs példány. |
| ObjectName |A teljesítményobjektum neve |
| SourceSystem |Az adatokból gyűjtött ügynök típusa. <br><br>OpsManager – Windows ügynök, közvetlen csatlakozás vagy SCOM <br> Linux – Minden Linux ügynök  <br> AzureStorage – Azure diagnosztika |
| TimeGenerated |Az adatok mintavételének dátuma és időpontja. |

## <a name="sizing-estimates"></a>Méretezési becslések
 Egy adott számláló 10 másodperces időközönkénti gyűjtésének durva becslése példányonként körülbelül 1 MB naponta.  Egy adott számláló tárolási követelményeit a következő képlettel becsülheti meg.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Lekérdezések naplózása teljesítményrekordokkal
Az alábbi táblázat különböző példákat tartalmaz a teljesítményrekordokat lekérdező naplólekérdezésekre.

| Lekérdezés | Leírás |
|:--- |:--- |
| Teljesítmény |Minden teljesítményadat |
| Perf &#124; ahol computer == "MyComputer" |Az adott számítógép összes teljesítményadata |
| Perf &#124; ahol countername == "Aktuális lemezvárólista hossza" |Egy adott számláló összes teljesítményadata |
| Perf &#124; ahol ObjectName == "Processzor" és CounterName == "% processzoridő" és InstanceName == "_Total" &#124; összegző AVGCPU = avg(CounterValue) a számítógép |Átlagos CPU-kihasználtság az összes számítógépen |
| Perf &#124; ahol a CounterName == "processzoridő%" &#124; összegezi az AggregatedValue = max(CounterValue) számítógép szerinti értékét |Maximális cpu-kihasználtság az összes számítógépen |
| Perf &#124; ahol az ObjectName == "LogicalDisk" és a CounterName == "Current Disk Queue Length" és a Computer == "MyComputerName" &#124; summarize AggregatedValue = avg(CounterValue) a InstanceName szerint |Az aktuális lemezvárólista átlagos hossza egy adott számítógép összes példányán |
| Perf &#124; ahol a CounterName == "Disk Transfers/sec" &#124; összegzi az AggregatedValue = percentilis(CounterValue, 95) számítógép szerint |A lemezátvitel/mp 95. |
| Perf &#124; ahol a CounterName == "% processzoridő" és a Példánynév == "_Total" &#124; összegző AggregatedValue = avg(CounterValue) a raktárhely(TimeGenerated, 1h), Számítógép |A processzorhasználat óránkénti átlaga az összes számítógépen |
| Perf &#124; ahol a Computer == "MyComputer" és a CounterName startswith_cs "%" és a InstanceName == "_Total" &#124; összegző AggregatedValue = percentilis(CounterValue, 70) raktárhely(TimeGenerated, 1h), CounterName | Egy adott számítógép minden százalékának óránkénti 70%-a |
| Perf &#124; ahol a CounterName == "processzoridő% és a instancename == "_Total" és a Számítógép == "Saját számítógép" &#124; összegző ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = átlag(CounterValue), ["percentile75(CounterValue)"] = percentilis(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) raktárhely(TimeGenerated, 1h), Számítógép |Óránkénti átlagos, minimális, maximális és 75 százalékos CPU-használat egy adott számítógéphez |
| Perf &#124; ahol ObjectName == "MSSQL$INST2:Databases" és InstanceName == "master" | A főadatbázis összes teljesítményadata a megnevezett SQL Server-példány INST2-ből.  




## <a name="next-steps"></a>További lépések
* [Teljesítményszámlálók gyűjtése Linux-alkalmazásokból,](data-sources-linux-applications.md) köztük a MySQL-ből és az Apache HTTP Serverből.
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez.  
* Az összegyűjtött adatok exportálása a [Power BI-ba](powerbi.md) további vizualizációk és elemzések érdekében.
