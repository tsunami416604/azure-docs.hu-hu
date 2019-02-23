---
title: Összegyűjtheti és elemezheti az Azure monitorban teljesítményszámlálók |} A Microsoft Docs
description: Az Azure Monitor, elemezheti a teljesítményt, a Windows és Linux-ügynökök által gyűjtött teljesítményszámlálók.  Ez a cikk ismerteti mind a Windows teljesítményszámláló-gyűjtemény konfigurálása és a Linux-ügynökök, azok részleteit a munkaterületet, és hogyan elemezheti őket az Azure Portalon lesznek tárolva.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 93f47529e3be44ff1db4e089bdcdca3eb1b4dea3
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728348"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Az Azure monitorban Windows és Linux rendszerű teljesítmény adatforrások
Teljesítményszámlálók a Windows és Linux cybercrime hardverösszetevők, operációs rendszerek és alkalmazások teljesítményét.  Az Azure Monitor teljesítményszámlálók gyűjthet a teljesítményadatoknak a hosszabb távú elemzésekhez és jelentéskészítési mellett közel valós idejű azokat elemzéshez gyakori időközönként.

![Teljesítményszámlálók](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása
Teljesítményszámlálók konfigurálása a [adatok menü a Speciális beállítások](agent-data-sources.md#configuring-data-sources).

Amikor először konfigurálja egy új munkaterületet Windows vagy Linux-teljesítménye számlálói, akkor gyorsan létrehozhat több gyakran használt számlálót kapják meg.  Ezek mindegyike mellett egy jelölőnégyzet található.  Győződjön meg arról, hogy a rendszer ellenőrzi a kezdetben a létrehozni kívánt számlálókat, és kattintson a **a kijelölt teljesítményszámlálók felvétele**.

Windows-teljesítményszámlálókkal választhat egy adott példányt minden teljesítményszámláló esetében. Linuxos teljesítményszámlálókkal az Ön által választott átlagait példányát az összes gyermek-számlálók a szülő számláló vonatkozik. Az alábbi táblázat a Linux és a Windows-teljesítményszámlálók közös példányig.

| Példány neve | Leírás |
| --- | --- |
| \_Összesen |Összes példány összesen |
| \* |Minden példány |
| (/&#124;/var) |Megegyezik a tagok elnevezése printer0: / vagy /var |

### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók

![Windows-teljesítményszámlálók konfigurálása](media/data-sources-performance-counters/configure-windows.png)

Az alábbi eljárás segítségével adjon hozzá egy új Windows teljesítményszámláló gyűjtése.

1. Írja be a számláló nevét a szövegmezőbe, a következő formátumban *objektum (példányok) \counter*.  Amikor elkezdi beírni, gyakran használt számlálót egyező listája jelenik meg.  Egy számláló kiválaszthatja a listából, vagy írjon be egy saját.  Elemkészlet is visszaadható valamennyi példánya egy adott számlálóra megadásával *object\counter*.  

    Nevesített példány az SQL Server teljesítményszámlálói összegyűjtésekor összes nevű példány számlálók kezdés *MSSQL$* és a példány neve követ.  A napló gyorsítótár találati aránya teljesítményszámláló gyűjtése, minden adatbázis névvel ellátott SQL Database teljesítményét objektumból INST2 példányra vonatkozó, például adja meg a `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Kattintson a **+** vagy nyomja le az **Enter** a Számláló hozzáadása a listához.
3. Amikor hozzáad egy számlálót, használja az alapértelmezett 10 másodperces annak **mintavételi időköze**.  Módosíthatja ezt értéke legfeljebb 1800 másodperc (30 perc), ha azt szeretné, az összegyűjtött teljesítményadatok tárolási követelmények csökkentése érdekében.
4. Számlálók hozzáadása elkészült, kattintson a **mentése** gombra a konfiguráció mentéséhez, a képernyő tetején.

### <a name="linux-performance-counters"></a>Linux-teljesítményszámlálók

![Linux-teljesítményszámlálók konfigurálása](media/data-sources-performance-counters/configure-linux.png)

Az alábbi eljárás segítségével adjon hozzá egy új Linux teljesítményszámláló gyűjtése.

1. Alapértelmezés szerint az összes konfigurációs módosítást automatikusan leküld az összes ügynököt.  Linux-ügynökök a Fluentd adatgyűjtő küld egy konfigurációs fájl.  Ha szeretné módosítani ezt a fájlt minden egyes Linux-ügynök manuálisan, törölje a jelet *alkalmaz az alábbi konfiguráció Linuxos gépeimre* , és kövesse az alábbi útmutatást.
2. Írja be a számláló nevét a szövegmezőbe, a következő formátumban *objektum (példányok) \counter*.  Amikor elkezdi beírni, gyakran használt számlálót egyező listája jelenik meg.  Egy számláló kiválaszthatja a listából, vagy írjon be egy saját.  
3. Kattintson a **+** vagy nyomja le az **Enter** a Számláló hozzáadása a többi számlálók objektum listáját.
4. Az adott objektumhoz tartozó összes számlálók használata azonos **mintavételi időköze**.  Az alapértelmezett érték 10 másodperc.  Módosítsa, értéke legfeljebb 1800 másodperc (30 perc), ha az összegyűjtött teljesítményadatok tárolási követelmények csökkentése érdekében.
5. Számlálók hozzáadása elkészült, kattintson a **mentése** gombra a konfiguráció mentéséhez, a képernyő tetején.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Linuxos teljesítményszámlálókkal konfigurálása a konfigurációs fájlban
Így nem kell beállítani az Azure portal használatával Linux-teljesítményszámlálók, lehetősége van a Linux-ügynök a konfigurációs fájlok szerkesztésével.  Teljesítmény-mérőszámokat gyűjthet konfiguráció által vezérelt **/etc/opt/microsoft/omsagent/\<munkaterület-azonosítót\>/conf/omsagent.conf**.

Minden egyes objektumot, vagy a teljesítmény-mérőszámokat gyűjthet kategóriáját egy konfigurációs fájlban kell definiálni `<source>` elemet. A szintaxist az alábbi mintát követi.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Ez az elem a paramétereket az alábbi táblázatban ismertetett.

| Paraméterek | Leírás |
|:--|:--|
| objektum\_neve | A gyűjtemény objektum neve. |
| példány\_reguláris kifejezés |  A *reguláris kifejezés* gyűjtéséhez előfordulások meghatározása. Az érték: `.*` adja meg az összes példányt. Processzor metrikáinak összegyűjtése csak a \_teljes példány megadhatja `_Total`. Csak a crond segédprogrammal együtt vagy sshd-példányok folyamat metrikák gyűjthetők, megadhatja: `(crond\|sshd)`. |
| a számláló\_neve\_reguláris kifejezés | A *reguláris kifejezés* meghatározása, amely számlálóit (az objektum) gyűjtéséhez. Az objektum számlálói az összes gyűjteni, adja meg: `.*`. Csak lapozófájl-kapacitás terület számlálókat a memória objektumhoz tartozó gyűjthet, például megadhatja: `.+Swap.+` |
| interval | A gyakoriság, amellyel a rendszer az objektum számlálókat gyűjti. |


A következő táblázat felsorolja azokat az objektumokat és a számlálókat is megadhat a konfigurációs fájlban.  Érhetők el további számlálók bizonyos alkalmazások leírtak szerint [Linux-alkalmazások az Azure Monitor teljesítményszámlálók gyűjtése](data-sources-linux-applications.md).

| Objektumnév | Számláló neve |
|:--|:--|
| Logikai lemez | Szabad Inode-OK |
| Logikai lemez | % Szabad terület |
| Logikai lemez | Foglalt Inode-OK % |
| Logikai lemez | Foglalt hely % |
| Logikai lemez | Lemezolvasási sebesség (bájt/s) |
| Logikai lemez | Lemezolvasások/mp |
| Logikai lemez | Átvitel/mp |
| Logikai lemez | Lemezírási sebesség (bájt/s) |
| Logikai lemez | Lemezírások/mp |
| Logikai lemez | Szabad hely MB-ban |
| Logikai lemez | Logikai lemez bájt/mp |
| Memory (Memória) | Rendelkezésre álló memória % |
| Memory (Memória) | Rendelkezésre álló Lapozóterület % |
| Memory (Memória) | Foglalt memória % |
| Memory (Memória) | Foglalt Lapozóterület % |
| Memory (Memória) | Rendelkezésre álló memória |
| Memory (Memória) | Rendelkezésre álló Lapozóterület |
| Memory (Memória) | Olvasott lap/mp |
| Memory (Memória) | Írt lap/mp |
| Memory (Memória) | Lap/mp |
| Memory (Memória) | Használt lapozási memória terület |
| Memory (Memória) | Használt memória (MB) |
| Network (Hálózat) | Küldött bájtok száma összesen |
| Network (Hálózat) | Fogadott bájtok teljes száma |
| Network (Hálózat) | Összes bájt |
| Network (Hálózat) | Az összes csomag továbbított adatok köre |
| Network (Hálózat) | Összes fogadott csomag |
| Network (Hálózat) | Teljes Rx-hibák |
| Network (Hálózat) | Teljes Tx-hibák |
| Network (Hálózat) | Teljes ütközések |
| Fizikai lemez | Átl. Lemez mp/Olvasás |
| Fizikai lemez | Átl. Lemez mp/átvitel |
| Fizikai lemez | Átl. Lemez mp/írás |
| Fizikai lemez | Fizikai lemez bájt/mp |
| Feldolgozás | A PCT kiemelt idő |
| Feldolgozás | A PCT felhasználói idő |
| Feldolgozás | Használt memória mérete kilobájtban |
| Feldolgozás | A megosztott virtuális memória |
| Processzor | DPC idő % |
| Processzor | Inaktivitási idő % |
| Processzor | Megszakítási idő % |
| Processzor | A(z) % IO várakozási idő |
| Processzor | Idő %-ban a processzoron |
| Processzor | Védett módú használat % idő |
| Processzor | Processzoridő |
| Processzor | Felhasználói idő % |
| Rendszer | Szabad fizikai memória |
| Rendszer | Szabad hely a Lapozófájlokban |
| Rendszer | Szabad virtuális memória |
| Rendszer | Folyamatok |
| Rendszer | A Lapozófájlokban tárolt méret |
| Rendszer | Hasznos üzemidő |
| Rendszer | Felhasználók |


Következő teljesítmény-mérőszámok az alapértelmezett konfigurációjának.

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
Az Azure Monitor adatokat gyűjt az összes megadott teljesítményszámlálók a megadott minta időközönként minden számláló telepített rendelkező ügynököknek.  Az adatok nem összesítve, és a nyers adatok érhető el az összes napló lekérdezési nézeteket az előfizetés által megadott időtartama alatt.

## <a name="performance-record-properties"></a>Teljesítmény rekord tulajdonságai
Teljesítményrekordot rendelkezik olyan típusú **Teljesítményoptimalizált** , és a tulajdonságait az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény gyűjtötte a program a számítógép. |
| CounterName |A teljesítményszámláló neve |
| Számláló_elérési_útja |Az űrlap a számláló a teljes elérési útja \\ \\ \<számítógép >\\objektum(példány)\\számlálót. |
| CounterValue |A számláló számérték. |
| Példánynév |Az esemény-példány nevét.  Üres, ha nincsenek példányok. |
| Objektumnév |Teljesítményobjektum neve |
| SourceSystem |Az adatgyűjtés ügynök típusa. <br><br>Csatlakozás OpsManager – Windows-ügynök, közvetlenül vagy SCOM <br> Linux – az összes Linux-ügynökök  <br> AzureStorage – az Azure Diagnostics |
| TimeGenerated |Dátum és idő, az adatok mintavételezése volt. |

## <a name="sizing-estimates"></a>Méretezési becslései
 Egy adott teljesítményszámláló-gyűjtemény 10 másodperces intervallumon durva becslést körülbelül 1 MB / nap példányonként.  Megbecsülheti egy adott számlálóra a következő képlettel tárolási követelményeit.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Teljesítményrekordot log lekérdezéseket.
Az alábbi táblázat példákat különböző teljesítményrekordot lekérő log lekérdezéseket.

| Lekérdezés | Leírás |
|:--- |:--- |
| Perf |Minden teljesítményadat |
| Teljesítményoptimalizált &#124; ahol számítógép == "Sajátgép" |Egy adott számítógép minden teljesítményadat |
| Teljesítményoptimalizált &#124; ahol CounterName == "Lemezvárólista jelenlegi hossza" |Az egy adott számlálóra minden teljesítményadat |
| Teljesítményoptimalizált &#124; ahol ObjectName == "Processzor" és a CounterName == "%-ban a processzoron" és a példánynév == "_Total" &#124; AVGCPU összefoglalója = avg(Average) számítógépenként |Átlagos processzorhasználat az összes számítógép |
| Teljesítményoptimalizált &#124; ahol CounterName == "%-ban a processzoron" &#124; summarize AggregatedValue = max(Max) számítógépenként |Maximális processzorhasználat az összes számítógép |
| Teljesítményoptimalizált &#124; ahol ObjectName == "Logikai lemez" és a CounterName == "Lemezvárólista jelenlegi hossza" és a számítógép == "MyComputerName" &#124; summarize AggregatedValue = avg(Average) InstanceName szerint |Aktuální délka Fronty lemez átlagos egy adott számítógép összes példányra vetítve |
| Teljesítményoptimalizált &#124; ahol CounterName == "DiskTransfers/mp" &#124; summarize AggregatedValue = PERCENTILIS (átlagos, 95) számítógép szerint |95. percentilis az átvitel/mp minden számítógépnél |
| Teljesítményoptimalizált &#124; ahol CounterName == "%-ban a processzoron" és a InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) bin (TimeGenerated, 1 óra), a számítógép által |CPU-használat minden óránkénti átlag |
| Teljesítményoptimalizált &#124; ahol számítógép == "Sajátgép" és a CounterName startswith_cs "%" és a példánynév == "_Total" &#124; summarize AggregatedValue = (Avg, 70) PERCENTILIS szerint bin (TimeGenerated, 1 óra), CounterName | Egy adott számítógép minden % százalékos számláló óránkénti 70 PERCENTILIS |
| Teljesítményoptimalizált &#124; ahol CounterName == "%-ban a processzoron" és a példánynév == "_Total" és a számítógép == "Sajátgép" &#124; összefoglalója ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] PERCENTILIS (Avg, 75) = ["max(CounterValue)"] = max(CounterValue) bin (TimeGenerated, 1 óra), a számítógép szerint |Óránkénti átlagos, minimális, maximális és 75 – PERCENTILIS CPU-használat egy adott számítógép |
| Teljesítményoptimalizált &#124; ahol ObjectName == "MSSQL$ INST2: adatbázisok" és a InstanceName == "master" | Minden teljesítményadat a master adatbázisban az elnevezett SQL Server-példányban INST2 adatbázis-teljesítmény objektumból.  




## <a name="next-steps"></a>További lépések
* [Teljesítményszámlálók gyűjtése Linuxos alkalmazások](data-sources-linux-applications.md) többek között a MySQL és az Apache HTTP Server.
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.  
* Az összegyűjtött adatok exportálása [Power BI](powerbi.md) további Vizualizációk és elemző.
