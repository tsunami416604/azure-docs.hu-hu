---
title: "Összegyűjti és elemzi az Azure Naplóelemzés teljesítményszámlálók |} Microsoft Docs"
description: "Log Analytics teljesítménye a Windows és Linux-ügynökök elemzését által gyűjtött teljesítményszámlálók.  Ez a cikk mindkét Windows teljesítményszámlálók gyűjteményét beállításának módját ismerteti, és a Linux-ügynököt, azok adatait tárolja a munkaterületet, és elemezheti őket az Azure portálon."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2017
ms.author: magoedte
ms.openlocfilehash: 0f7119f280f2eb51222ade2ea7984b560a02f667
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>A Naplóelemzési Windows és Linux teljesítmény adatforrások
A Windows és Linux teljesítményszámlálók Észreveheti az olyan hardverösszetevők, operációs rendszerek és alkalmazások teljesítményét.  A Naplóelemzési össze tudják gyűjteni a teljesítményszámlálók gyakori elemzésre közel valós idejű (NRT) hosszabb távú elemzés teljesítményadatait összesítése és jelentéskészítési mellett.

![Teljesítményszámlálók](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása
Konfigurálja a teljesítményszámlálók a [Naplóelemzés beállításai adatok menüben](log-analytics-data-sources.md#configuring-data-sources).

Amikor először konfigurálja egy új Naplóelemzési munkaterület Windows vagy Linux teljesítmény számlálói, lehetősége van gyorsan létrehozhat több általános jellegű számlálót.  Ezek mindegyike mellett egy jelölőnégyzet található.  Győződjön meg arról, hogy kezdetben létrehozandó számlálókat a rendszer ellenőrzi, és kattintson a **adja hozzá a kijelölt teljesítményszámlálók**.

Windows-teljesítményszámlálókat kiválaszthatja az egyes teljesítményszámlálókhoz bizonyos példányainak. Linux teljesítményszámlálókkal az egyes számlálóit az Ön által példányát a szülő számláló összes gyermek számláló vonatkozik. Az alábbi táblázat a Linux és a Windows a teljesítményszámlálók rendelkezésre közös példányok.

| Példány neve | Leírás |
| --- | --- |
| \_Összesen |A példányok száma |
| \* |Minden példány |
| (/ &#124; / var) |Példány neve megegyezik: / vagy /var |

### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók

![Konfigurálja a Windows-teljesítményszámlálók](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Az alábbi eljárás segítségével adja hozzá egy új Windows teljesítményszámláló gyűjtése.

1. A szövegmezőben formátumban írja be a számláló nevét *objektum (példány) \counter*.  Amikor elkezdi beírni, lehetősége lesz egyező listáját általános jellegű számlálót.  Kiválaszthatja a számláló a listából, vagy írja be egy saját.  A számláló az összes példányát adja vissza megadásával *object\counter*.  

    Elnevezett példányok az SQL Server teljesítményszámlálói összegyűjtésekor összes nevű példány számlálók útmutató *MSSQL$* , a példány neve követ.  A napló-gyorsítótári találati aránya teljesítményszámláló gyűjtése, minden adatbázisok elnevezett SQL adatbázis-teljesítmény objektumból INST2 példányra vonatkozó, például adja meg a `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Kattintson a  **+**  vagy nyomja le az ENTER **Enter** a Számláló hozzáadása a listához.
3. Amikor Számláló hozzáadása, akkor használja, a rendszer az alapértelmezett 10 másodperces a **mintavételi időköze**.  Módosíthatja a értéke legfeljebb 1800 másodperc (30 perc), ha azt szeretné, hogy az összegyűjtött teljesítményadatok a tárolási követelmények csökkentése érdekében.
4. Amikor elkészült a számlálók hozzáadását, kattintson a **mentése** gombra a konfiguráció mentéséhez, a képernyő tetején.

### <a name="linux-performance-counters"></a>Linux-teljesítményszámlálók

![Linux-teljesítményszámlálók konfigurálása](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Az alábbi eljárás segítségével adja hozzá egy új Linux teljesítményszámláló gyűjtése.

1. Összes konfigurációs módosításhoz alapértelmezés szerint automatikusan leküldéssel az összes ügynököt.  Linux-ügynökök, a konfigurációs fájlt a Fluentd adatgyűjtő küld.  Ha manuálisan minden egyes Linux-ügynök a következő fájl módosításához, törölje a jelet *alkalmaz az alábbi konfiguráció a Linuxos gépeimre* és kövesse az alábbi útmutatást.
2. A szövegmezőben formátumban írja be a számláló nevét *objektum (példány) \counter*.  Amikor elkezdi beírni, lehetősége lesz egyező listáját általános jellegű számlálót.  Kiválaszthatja a számláló a listából, vagy írja be egy saját.  
3. Kattintson a  **+**  vagy nyomja le az ENTER **Enter** a Számláló hozzáadása az objektum más számlálóinak listája.
4. Egy objektum számlálók használata azonos **mintavételi időköze**.  Az alapértelmezett érték 10 másodperc.  Módosítja ezt értéke legfeljebb 1800 másodperc (30 perc), ha azt szeretné, hogy az összegyűjtött teljesítményadatok a tárolási követelmények csökkentése érdekében.
5. Amikor elkészült a számlálók hozzáadását, kattintson a **mentése** gombra a konfiguráció mentéséhez, a képernyő tetején.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>A konfigurációs fájlban Linux teljesítményszámlálók konfigurálása
Az Azure portál használatával Linux teljesítményszámlálók konfigurálására, lehetősége van a Linux-ügynök a konfigurációs fájlok szerkesztésével.  Teljesítménymutatók gyűjtéséhez a konfiguráció által vezérelt **/etc/opt/microsoft/omsagent/\<munkaterület azonosítója\>/conf/omsagent.conf**.

Minden objektumot, vagy a kategória Teljesítményelemzési mutatón gyűjtéséhez definiálni kell egy konfigurációs fájlban `<source>` elemet. A szintaxis a következő mintát követi.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


A paraméterek az elem a következő táblázat ismerteti.

| Paraméterek | Leírás |
|:--|:--|
| objektum\_neve | A gyűjtemény objektum nevét. |
| példány\_regex |  A *reguláris kifejezés* gyűjtéséhez előfordulások meghatározása. Az érték: `.*` határozza meg az összes példányát. Gyűjtéséhez processzor a csak a \_teljes példányt kell megadni `_Total`. Csak a crond vagy sshd példány folyamat gyűjtéséhez kell megadni: "(crond\|sshd) ". |
| a számláló\_neve\_regex | A *reguláris kifejezés* meghatározása, amely számlálóit (az az objektum) gyűjtéséhez. Adja meg az objektum összes számlálóját gyűjteni kívánt: `.*`. Csak swap terület számlálókat a memória objektumhoz tartozó gyűjthet, például kell megadni:`.+Swap.+` |
| interval | A gyakoriság, amellyel az objektumot számlálók összegyűjtése. |


A következő táblázat az objektumokat és számlálók megadása a konfigurációs fájlban.  Nincsenek további számlálók bizonyos alkalmazások leírtak [Linux Log Analytics-alkalmazások a teljesítményszámlálók adatainak összegyűjtése](log-analytics-data-sources-linux-applications.md).

| Objektumnév | Számláló neve |
|:--|:--|
| Logikai lemez | % Szabad Inode-OK |
| Logikai lemez | % Szabad terület |
| Logikai lemez | Foglalt Inode-OK % |
| Logikai lemez | Foglalt hely % |
| Logikai lemez | Lemezolvasási sebesség (bájt/s) |
| Logikai lemez | Lemezolvasások/mp |
| Logikai lemez | Átvitel/mp |
| Logikai lemez | Lemezírási sebesség (bájt/s) |
| Logikai lemez | Lemezírás/mp |
| Logikai lemez | Szabad hely MB-ban |
| Logikai lemez | Logikai lemez bájtok/s |
| Memory (Memória) | Rendelkezésre álló memória %-ban |
| Memory (Memória) | Rendelkezésre álló Lapozóterület % |
| Memory (Memória) | Foglalt memória % |
| Memory (Memória) | Foglalt Lapozóterület % |
| Memory (Memória) | Rendelkezésre álló memória |
| Memory (Memória) | Rendelkezésre álló Lapozóterület |
| Memory (Memória) | Olvasott lap/mp |
| Memory (Memória) | Írt lap/mp |
| Memory (Memória) | Lap/mp |
| Memory (Memória) | Használt memória (MB) Lapozóterület |
| Memory (Memória) | Használt memória (MB) |
| Network (Hálózat) | Küldött bájtok teljes száma |
| Network (Hálózat) | Fogadott bájtok száma összesen |
| Network (Hálózat) | Az összes bájt |
| Network (Hálózat) | Továbbított csomagok száma összesen |
| Network (Hálózat) | Fogadott csomagok száma összesen |
| Network (Hálózat) | A Rx teljes hibák |
| Network (Hálózat) | Teljes Tx hibák |
| Network (Hálózat) | Teljes ütközések |
| Fizikai lemez | Átlagos Lemez mp/Olvasás |
| Fizikai lemez | Átlagos Lemez mp/átvitel |
| Fizikai lemez | Átlagos Lemez mp/írás |
| Fizikai lemez | Fizikai lemez bájtok/s |
| Feldolgozás | A PCT kiemelt idő |
| Feldolgozás | A PCT felhasználói módú használatának aránya |
| Feldolgozás | Használt memória KB |
| Feldolgozás | Megosztott virtuális memória |
| Processzor | DPC idő % |
| Processzor | Inaktivitási idő % |
| Processzor | Megszakítási idő % |
| Processzor | IO várakozási idő % |
| Processzor | Feladatok futtatásával töltött idő %-át |
| Processzor | % Védett módú használatának aránya |
| Processzor | Processzor kihasználtsága |
| Processzor | Felhasználói idő % |
| Rendszer | Szabad fizikai memória |
| Rendszer | Szabad hely a Lapozófájlokban |
| Rendszer | Virtuális memória |
| Rendszer | Folyamatok |
| Rendszer | A Lapozófájlokban tárolt adatok mérete |
| Rendszer | Hasznos üzemidő |
| Rendszer | Felhasználók |


Az alábbiakban olvashatja a metrikák alapértelmezett konfigurációja.

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
A Naplóelemzési megadott teljesítményszámlálók gyűjti. a megadott minta időközönként minden számláló telepített rendelkező ügynököknek.  Az adatok nem összesített értéket, és a nyers adatok érhető el az összes naplófájl-keresési nézetben az előfizetés által megadott időtartama alatt.

## <a name="performance-record-properties"></a>Teljesítmény rekord tulajdonságai
Teljesítmény rekordok típusa lehet **telj** , és a tulajdonságok az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény gyűjtötte a program a számítógép. |
| CounterName |A teljesítményszámláló neve |
| Számláló_elérési_útja |A képernyőn a számláló elérési útját \\ \\ \<számítógép >\\objektum(példány)\\számláló. |
| Ellenértéknek |A számláló numerikus értéket. |
| Példánynév |Az esemény-példány nevét.  Üres, ha egyetlen példánya. |
| ObjectName |Teljesítményobjektum neve |
| SourceSystem |Az adatgyűjtés az ügynök típusa. <br><br>OpsManager – Windows-ügynök, vagy közvetlen kapcsolódás vagy SCOM <br> Linux – az összes Linux-ügynökök  <br> AzureStorage – az Azure Diagnostics |
| TimeGenerated |Dátum és idő, az adatok lett mintát venni. |

## <a name="sizing-estimates"></a>Méretezési becslése
 Egy durva becslést gyűjtemény egy adott számláló 10 másodperces időközönként körülbelül 1 MB naponkénti egy példány.  A számláló a következő képlettel tárolási követelményeinek megbecsülheti.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Teljesítmény rekordot tartalmazó napló-keresések
Az alábbi táblázat példákat különböző teljesítmény lehívása napló kereséseket.

| Lekérdezés | Leírás |
|:--- |:--- |
| A Teljesítményfigyelő |Minden teljesítményadat |
| A Teljesítményfigyelő &#124; Ha számítógép == "Sajátgép" |Egy adott számítógép minden teljesítményadat |
| A Teljesítményfigyelő &#124; Ha CounterName == "Lemezvárólista jelenlegi hossza" |Egy adott számláló minden teljesítményadat |
| A Teljesítményfigyelő &#124; Ha ObjectName == "Processzor" és a CounterName == "kihasználtsága (%)" és a példánynév == "_Total" &#124; AVGCPU összefoglalója = avg(Average) számítógépenként |Minden átlagos CPU-felhasználás |
| A Teljesítményfigyelő &#124; Ha CounterName == "kihasználtsága (%)" &#124; AggregatedValue összefoglalója = max(Max) számítógépenként |Minden maximális CPU-felhasználás |
| A Teljesítményfigyelő &#124; Ha ObjectName == "Logikai lemez" és a CounterName == "Lemezvárólista jelenlegi hossza" és a számítógép == "MyComputerName" &#124; AggregatedValue összefoglalója által példánynév avg(Average) = |Egy adott számítógép minden példányára átlagos aktuális lemez-várólista hossza |
| A Teljesítményfigyelő &#124; Ha CounterName == "DiskTransfers/mp" &#124; AggregatedValue összefoglalója = (átlagos, 95) PERCENTILIS számítógépenként |95. percentilis az átvitel/mp minden |
| A Teljesítményfigyelő &#124; Ha CounterName == "kihasználtsága (%)" és a példánynév == "_Total" &#124; AggregatedValue összefoglalója bin (TimeGenerated, 1 óra), a számítógép által avg(CounterValue) = |CPU-használat minden óránkénti átlaga |
| A Teljesítményfigyelő &#124; Ha számítógép == "Sajátgép" és a CounterName startswith_cs "%" és a példánynév == "_Total" &#124; AggregatedValue összefoglalója (ellenértéknek, 70) PERCENTILIS szerint bin (TimeGenerated, 1 óra), a CounterName = | Egy adott számítógépen minden % százalékos számláló óránkénti 70 százalékos érték |
| A Teljesítményfigyelő &#124; Ha CounterName == "kihasználtsága (%)" és a példánynév == "_Total" és a számítógép == "Sajátgép" &#124; ["min(CounterValue)"] összefoglalója min(CounterValue), = ["avg(CounterValue)"] avg(CounterValue), = ["percentile75(CounterValue)"] PERCENTILIS (ellenértéknek, 75), = ["max(CounterValue)"] bin (TimeGenerated, 1 óra), a számítógép által max(CounterValue) = |Óránkénti átlag, minimális, maximális és 75-PERCENTILIS CPU-használat egy adott számítógépen |
| A Teljesítményfigyelő &#124; Ha ObjectName == "MSSQL$ INST2: adatbázisok" és a példánynév == "master" | Minden teljesítményadat elnevezett SQL Server-példány INST2 a master adatbázis adatbázis-teljesítmény objektumból.  




## <a name="next-steps"></a>További lépések
* [A teljesítményszámlálók adatainak összegyűjtése Linux alkalmazásokból](log-analytics-data-sources-linux-applications.md) többek között a MySQL és az Apache HTTP Server.
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére.  
* Összegyűjtött adatok exportálása az [Power BI](log-analytics-powerbi.md) további képi megjelenítések és elemzésére.
