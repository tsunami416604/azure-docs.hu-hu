---
title: A PerfInsights Linux használata a Microsoft Azure-ban| Microsoft dokumentumok
description: Megtudhatja, hogyan használhatja a PerfInsights segítségével a Linux-virtuális gépek teljesítményével kapcsolatos problémák elhárításához.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266987"
---
# <a name="how-to-use-perfinsights"></a>A PerfInsights használata

[A PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) egy önsegítő diagnosztikai eszköz, amely összegyűjti és elemzi a diagnosztikai adatokat, és jelentést készít a Linux virtuális gépek teljesítményproblémáinak elhárításához az Azure-ban. A PerfInsights önálló eszközként vagy közvetlenül a portálról futtatható a támogatott virtuális gépeken az [Azure virtuális gépek teljesítménydiagnosztika](performance-diagnostics.md)használatával.

Ha teljesítményproblémákat tapasztal a virtuális gépekkel kapcsolatban, mielőtt kapcsolatba lépne az ügyfélszolgálattal, futtassa ezt az eszközt.

## <a name="supported-troubleshooting-scenarios"></a>Támogatott hibaelhárítási forgatókönyvek

A PerfInsights többféle információt gyűjthet és elemezhet. A következő szakaszok a gyakori forgatókönyveket ismertetik.

### <a name="quick-performance-analysis"></a>Gyors teljesítményelemzés

Ez a forgatókönyv olyan alapvető információkat gyűjt, mint például a virtuális gép tárolása és hardverkonfigurációja, különböző naplók, többek között:

- Az operációs rendszer adatai

- PCI-eszköz adatai

- Általános vendég operációs rendszer naplók

- Konfigurációs fájlok

- Tárolási adatok

- Azure virtuálisgép-konfiguráció [(az Azure-példány metaadatszolgáltatása használatával gyűjtve)](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

- A futó folyamatok, a lemez-, a memória- és a processzorhasználat listája

- Hálózati információk

Ez egy passzív információgyűjtemény, amely nem befolyásolhatja a rendszert.

>[!Note]
>A gyorsteljesítmény-elemzési forgatókönyv automatikusan bekerül az alábbi esetek mindegyikébe:

### <a name="performance-analysis"></a>Teljesítményelemzés

Ez a forgatókönyv hasonló a gyors teljesítményelemzéshez, de lehetővé teszi a diagnosztikai információk hosszabb ideig történő rögzítését.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Milyen információkat gyűjt a PerfInsights?

A Linux virtuális gép, az operációs rendszer, a blokk eszközök, a nagy erőforrás-fogyasztók, a konfiguráció és a különböző naplók gyűjtése. További részletek:

- Operációs rendszer
  - Linux disztribúció és verzió
  - Kernel információ
  - Járművezetői információk

- Hardver
  - PCI-eszközök`*`[ ]

- Folyamatok és memória
  - Folyamatok listája (feladat neve, használt memória, megnyitott fájlok)
  - Összes, rendelkezésre álló és szabad fizikai memória
  - Összes, rendelkezésre álló és szabad lapozómemória
  - A CPU és a CPU-használat profilkészítési rögzítése 5 másodperces időközzel
  - A folyamatok memóriahasználatának profilkészítési rögzítése 5 másodperces időközzel

- Hálózat  
  - Adapterekkel rendelkező hálózati adapterek statisztikáinak listája
  - Hálózati útválasztási tábla
  - Megnyitott portok és állapot

- Storage
  - Eszközök listájának letiltása
  - Partíciók listája
  - Pontok csatlakoztatása
  - MDADM kötetinformáció
  - LVM-kötet adatai
  - Profilkészítési rögzítés az összes lemezen 5 másodperces időközzel

- Naplók
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[kiterjesztésmappa]/\*napló\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Az elmúlt öt nap naplóctl-kibocsátása

- [Az Azure virtuálisgép-példány metaadatai](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] PCI-adatokat még nem gyűjtenek a Debian és az SLES disztribúciókról

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Futtassa a PerfInsights Linuxot a virtuális gépen

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Mit kell tudnom, mielőtt futtatom az eszközt

#### <a name="tool-requirements"></a>A szerszámra vonatkozó követelmények

- Ezt az eszközt a teljesítményproblémát okozó virtuális számítógépen kell futtatni.
- A Python 2.7-et telepíteni kell a virtuális gépre

- A következő disztribúciók jelenleg támogatottak:

    | Disztribúció               | Verzió                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux szerver        | 6.10`*`[ ], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8 piactér imázsa)|
    | CentOS                     | 6,5`*`[ ], 7,6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4`*`[ ]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Kérjük, olvassa el [az Ismert problémák](#known-issues) című részt

### <a name="known-issues"></a>Ismert problémák

- Az RHEL 8 alapértelmezés szerint nincs telepítve a Python. A PerfInsights Linux futtatásához először telepítenie kell a Python 2.7-et

- A vendégügynök adatainak gyűjtése sikertelen lehet a CentOS 6.x rendszeren

- A PCI-eszközök adatait nem gyűjtjük a Debian alapú disztribúciókon

- Az LVM-adatokat részben gyűjtik egyes disztribúciók

### <a name="how-do-i-run-perfinsights"></a>Hogyan futtathatom a PerfInsights-et?

A PerfInsights virtuális gépen futtatható az Azure Performance Diagnostics azure-portálról történő telepítésével. Önálló eszközként is futtathatja.

>[!Note]
>A PerfInsights egyszerűen gyűjti és elemzi az adatokat. Nem módosítja a rendszert.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>A PerfInsights telepítése és futtatása az Azure Portalról

Erről a beállításról az [Azure performance diagnostics](performance-diagnostics.md)című témakörben talál további információt.  

#### <a name="run-perfinsights-in-standalone-mode"></a>PerfInsights futtatása önálló módban

A PerfInsights eszköz futtatásához hajtsa végre az alábbi lépéseket:

1. Töltse le [a PerfInsights.tar.gz fájlt](https://aka.ms/perfinsightslinuxdownload) a virtuális gép egy mappájába, és bontsa ki a tartalmát a terminál alábbi parancsaival.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Nyissa meg a `perfinsights.py` fájlt tartalmazó mappát, majd futtassa `perfinsights.py` az elérhető parancssori paraméterek megtekintéséhez.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Képernyőkép a PerfInsights Linux parancssori kimenetéről](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    A PerfInsights-forgatókönyvek futtatásának alapvető szintaxisa a következő:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Az alábbi példában 1 percig futtathatja a Gyors teljesítményelemzési forgatókönyvet, és létrehozhatja az eredményeket a /tmp/output mappában:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Az alábbi példában 5 napig futtathatja a teljesítményelemzési forgatókönyvet, és feltöltheti az eredményt a tárfiókba:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >A perfInsights egy forgatókönyv futtatása előtt felszólítja a felhasználót, hogy egyezzen bele a diagnosztikai információk megosztásába, és fogadja el a végfelhasználói licencszerződés elfogadását. Az **-a vagy --accept-disclaimer-and-share-diagnostics** kapcsolóval kihagyhatja ezeket az utasításokat.
    >
    >Ha aktív támogatási jegye van a Microsoftnál, és a PerfInsights futtatása a támogatási szakember kérésére, akivel együtt dolgozik, győződjön meg arról, hogy megadja a támogatási jegy számát az **-s vagy a --support-request** kapcsoló használatával.

A futtatás befejezéseután egy új kátrányfájl jelenik meg ugyanabban a mappában, mint a PerfInsights, kivéve, ha nincs megadva kimeneti mappa. A fájl neve **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz.** Ezt a fájlt elküldheti a támogatási ügynöknek elemzésre, vagy megnyithatja a jelentést a fájlon belül a megállapítások és javaslatok áttekintéséhez.

## <a name="review-the-diagnostics-report"></a>A diagnosztikai jelentés áttekintése

A **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** fájlban talál egy HTML-jelentést, amely részletezi a PerfInsights megállapításait. A jelentés áttekintéséhez **bontsa\_ki a PerformanceDiagnostics\_yyyy-MM-dd hh-mm-ss-fff.tar.gz** fájlt, majd nyissa meg a **PerfInsights Report.html** fájlt.

### <a name="overview-tab"></a>Áttekintés lap

Az **Áttekintés** lap alapvető futtatási részleteket és virtuális gépadatokat tartalmaz. A **Megállapítások** lapon a PerfInsights-jelentés összes különböző szakaszának ajánlásait tartalmazza.

![Képernyőkép a PerfInsights-jelentésről](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Képernyőkép a PerfInsights-jelentésről](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> A magasként kategorizált megállapítások olyan ismert problémák, amelyek teljesítményproblémákat okozhatnak. A közepesként kategorizált megállapítások nem optimális konfigurációkat képviselnek, amelyek nem feltétlenül okoznak teljesítményproblémákat. Az alacsonyként kategorizált megállapítások csak tájékoztató jellegű megállapítások.

Tekintse át az ajánlásokat és a linkeket az összes magas és közepes megállapításokat. Ismerje meg, hogyan befolyásolhatják a teljesítményt, valamint a teljesítményre optimalizált konfigurációkra vonatkozó gyakorlati tanácsok.

### <a name="cpu-tab"></a>CPU lap

**A CPU** lap a PerfInsights futtatása során a rendszerszintű processzorfogyasztásról nyújt tájékoztatást. A magas processzorhasználati időszakokról és a felső, hosszú ideig futó CPU-fogyasztókról szóló információk hasznosak lehetnek a magas CPU-val kapcsolatos problémák elhárításához.

![Képernyőkép a PerfInsights jelentés PROCESSZORlapjáról](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Tárolás lap

A **Megállapítások** szakasz a tárolással kapcsolatos különböző megállapításokat és ajánlásokat jeleníti meg.

Az **Eszközök blokkolása** és más kapcsolódó szakaszok, például **a partíciók,** **az LVM**és az **MDADM** lapok azt írják le, hogy a blokkeszközök hogyan vannak konfigurálva és kapcsolódnak egymáshoz.

![Képernyőkép a Tárhely lapról](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Képernyőkép az MDADM lapról](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Linux lap

A **Linux** lap információkat tartalmaz a hardver és az operációs rendszer fut a virtuális gép. A részletek közé tartozik a futó folyamatok listája és a vendégügynökre, a PCI-re, a PROCESSZORRA, az illesztőprogramokra és a LIS-illesztőprogramokra vonatkozó információk.

![Képernyőkép a Linux lapról](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>További lépések

További ellenőrzés céljából diagnosztikai naplókat és jelentéseket tölthet fel a Microsoft támogatási szolgálatának. Amikor a Microsoft támogatási munkatársaival dolgozik, kérhetik, hogy továbbítsa a PerfInsights által létrehozott kimenetet, hogy segítse a hibaelhárítási folyamatot.

A következő képernyőképen a kapotthoz hasonló üzenet jelenik meg:

![Képernyőkép a Microsoft támogatási szolgálatától származó mintaüzenetről](media/how-to-use-perfinsights-linux/support-email.png)

A fájlátviteli munkaterület eléréséhez kövesse az üzenetutasításait. A nagyobb biztonság érdekében az első használatkor meg kell változtatnia a jelszavát.

Miután bejelentkezett, talál egy párbeszédpanelt a PerfInsights által gyűjtött **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** fájl feltöltéséhez.
