---
title: A PerfInsights Linux használata a Microsoft Azureban | Microsoft Docs
description: Megtudhatja, hogyan használható a PerfInsights a linuxos virtuális gépek teljesítményével kapcsolatos problémák elhárításához.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266987"
---
# <a name="how-to-use-perfinsights"></a>A PerfInsights használata

A [PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) egy önsegítő diagnosztikai eszköz, amely a diagnosztikai adatok gyűjtésére és elemzésére szolgál, és jelentést nyújt a linuxos virtuális gépek teljesítményével kapcsolatos problémák megoldásához az Azure-ban. A PerfInsights futtathatók a támogatott virtuális gépeken önálló eszközként, vagy közvetlenül a portálról az [Azure Virtual Machines teljesítmény-diagnosztika](performance-diagnostics.md)használatával.

Ha teljesítménnyel kapcsolatos problémákat tapasztal a virtuális gépekkel kapcsolatban, akkor a támogatáshoz való kapcsolatfelvétel előtt futtassa ezt az eszközt.

## <a name="supported-troubleshooting-scenarios"></a>Támogatott hibaelhárítási forgatókönyvek

A PerfInsights több fajta információt gyűjthet és elemez. A következő szakaszban a gyakori forgatókönyvek jelennek meg.

### <a name="quick-performance-analysis"></a>Gyors teljesítmény elemzése

Ez a forgatókönyv olyan alapszintű adatokat gyűjt, mint például a virtuális gép tárolója és hardveres konfigurációja, különböző naplók, beleértve a következőket:

- Operációs rendszer adatai

- PCI-eszköz adatai

- A vendég operációs rendszer általános naplói

- Konfigurációs fájlok

- Szolgáltatás adatai

- Azure-beli virtuális gépek konfigurálása (az [azure instance metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)használatával gyűjtöttük össze)

- Futó folyamatok, lemezek, memória és CPU-használat listája

- Hálózatkezelési információk

Ez az információ passzív gyűjteménye, amely nem érinti a rendszerét.

>[!Note]
>A gyors teljesítmény-elemzési forgatókönyv a következő esetekben automatikusan szerepel:

### <a name="performance-analysis"></a>Teljesítmény elemzése

Ez a forgatókönyv hasonlít a gyors teljesítmény elemzéséhez, de lehetővé teszi a diagnosztikai adatok hosszabb időtartamra történő rögzítését.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Milyen típusú információkat gyűjt a PerfInsights

A Linux rendszerű virtuális gépre, az operációs rendszerre, az eszközök letiltására, a magas erőforrás-felhasználókra, a konfigurációra és a különböző naplókra vonatkozó információk gyűjtése. További részletek:

- Operációs rendszer
  - Linux-disztribúció és-verzió
  - Kernel-információk
  - Illesztőprogram-információk

- Hardver
  - PCI-eszközök [`*`]

- Folyamatok és memória
  - Folyamatok listája (feladat neve, felhasznált memória, megnyitott fájlok)
  - Teljes, elérhető és szabad fizikai memória
  - Teljes, elérhető és szabad swap memória
  - Profilkészítési rögzítés a PROCESSZORon és a CPU-használat feldolgozása 5 másodperces intervallumban
  - Profilkészítés – a folyamatok memória-használatának 5 másodperces intervallumon belüli rögzítése

- Hálózat  
  - Adapterek statisztikáit tartalmazó hálózati adapterek listája
  - Hálózati útválasztási táblázat
  - Nyitott portok és állapot

- Tárterület
  - Eszközök listájának letiltása
  - Partíciók listája
  - Csatlakoztatási pontok listája
  - MDADM-kötet adatai
  - LVM kötet adatai
  - Profilkészítés rögzítése az összes lemezen 5 másodperces intervallumban

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
  - /var/log/Azure/[kiterjesztés mappája]/\*log\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - A journalctl kimenete az elmúlt öt napban

- [Azure virtuálisgép-példány metaadatainak](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] A PCI-információk gyűjtése még nem történt meg a Debian és a SLES disztribúcióban

## <a name="run-the-perfinsights-linux-on-your-vm"></a>A PerfInsights Linux futtatása a virtuális gépen

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Tudnivalók az eszköz futtatása előtt

#### <a name="tool-requirements"></a>Eszközre vonatkozó követelmények

- Ezt az eszközt a teljesítménnyel kapcsolatos problémát biztosító virtuális gépen kell futtatni.
- A Python 2,7-et telepíteni kell a virtuális gépre.

- A következő eloszlások jelenleg támogatottak:

    | Disztribúció               | Verzió                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux kiszolgáló        | 6,10 [`*`], 7,3, 7,6, 7,5 (Oracle-Database-EE 13,8 Marketplace-rendszerkép)|
    | CentOS                     | 6,5 [`*`], 7,6                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4 [`*`]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Tekintse meg az [ismert problémák](#known-issues) szakaszt.

### <a name="known-issues"></a>Ismert problémák

- A RHEL 8 alapértelmezés szerint nem telepítette a Pythont. A PerfInsights Linux futtatásához először telepítenie kell a Python 2,7

- A vendég ügynök információinak gyűjtése sikertelen lehet a CentOS 6. x-ben

- A PCI-eszközök adatait a rendszer nem gyűjti a Debian-alapú disztribúciókban

- Az LVM-adatokat részben gyűjti a rendszer egyes disztribúciókban

### <a name="how-do-i-run-perfinsights"></a>PerfInsights Hogyan futtatása

A PerfInsights-et virtuális gépen is futtathatja az Azure Performance Diagnostics Azure Portalból történő telepítésével. Önálló eszközként is futtatható.

>[!Note]
>A PerfInsights egyszerűen összegyűjti és elemzi az adatokat. Nem végez módosításokat a rendszeren.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>A PerfInsights telepítése és futtatása a Azure Portal

További információ erről a lehetőségről: [Azure Performance Diagnostics](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>PerfInsights futtatása önálló módban

A PerfInsights eszköz futtatásához kövesse az alábbi lépéseket:

1. Töltse le a [PerfInsights. tar. gz](https://aka.ms/perfinsightslinuxdownload) mappát a virtuális gép egyik mappájába, és bontsa ki a tartalmat a terminálon az alábbi parancsokkal.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Navigáljon a `perfinsights.py` fájlt tartalmazó mappához, majd a `perfinsights.py` futtatásával tekintse meg az elérhető parancssori paramétereket.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Képernyőkép a PerfInsights Linux parancssori kimenetről](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    A PerfInsights-forgatókönyvek futtatásának alapvető szintaxisa a következő:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Az alábbi példát követve futtathat gyors teljesítmény-elemzési forgatókönyvet 1 percig, és létrehozhatja az eredményeket a/tmp/output mappában:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Az alábbi példát követve futtasson teljesítmény-elemzési forgatókönyvet 5 percen keresztül, és töltse fel az eredmény-tar labdát a Storage-fiókba:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Egy forgatókönyv futtatása előtt a PerfInsights felszólítja a felhasználót, hogy fogadja el a diagnosztikai adatok megosztását, és fogadja el a végfelhasználói licencszerződést. Használja az **-a vagy az--Accept-Disclaimer-and-share-Diagnostics** lehetőséget az ilyen kérések kihagyásához.
    >
    >Ha aktív támogatási jegyet használ a Microsofttal, és PerfInsights-t futtat a támogatási szakember kérelmére, akkor ügyeljen arra, hogy a támogatási jegy számát a **-s vagy a--support-Request** kapcsoló használatával adja meg.

Ha a Futtatás befejeződött, egy új tar-fájl ugyanabban a mappában jelenik meg, mint a PerfInsights, kivéve, ha nincs megadva kimeneti mappa. A fájl neve **PerformanceDiagnostics\_éééé-hh-nn\_hh-mm-SS-fff. tar. gz.** Ezt a fájlt elküldheti a támogatási ügynöknek elemzésre, vagy megnyithatja a jelentést a fájlon belül a megállapítások és javaslatok áttekintéséhez.

## <a name="review-the-diagnostics-report"></a>A diagnosztikai jelentés áttekintése

A **PerformanceDiagnostics\_éééé-hh-nn\_hh-mm-SS-fff. tar. gz** fájlon belül egy HTML-jelentést talál, amely a PerfInsights eredményeit részletezi. A jelentés áttekintéséhez bontsa ki a **PerformanceDiagnostics\_éééé-hh-nn\_hh-mm-SS-fff. tar. gz** fájlt, majd nyissa meg a **PerfInsights report. html** fájlt.

### <a name="overview-tab"></a>Áttekintés lap

Az **Áttekintés** lapon az alapszintű Futtatás részletei és a virtuális gép adatai láthatók. Az **eredmények** lapon a PerfInsights-jelentés különböző részeiből származó javaslatok összegzése látható.

![Képernyőkép a PerfInsights-jelentésről](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Képernyőkép a PerfInsights-jelentésről](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> A magas kategóriába sorolt megállapítások olyan ismert problémák, amelyek teljesítménnyel kapcsolatos problémákat okozhatnak. A közepesként kategorizált eredmények olyan nem optimális konfigurációkat jelentenek, amelyek nem szükségszerűen okoznak teljesítménnyel kapcsolatos problémákat. Az alacsonyként kategorizált eredmények csak tájékoztató utasítások.

Tekintse át az ajánlásokat és a hivatkozásokat az összes magas és közepes eredményhez. Ismerje meg, hogy miként befolyásolhatják a teljesítményt és az ajánlott eljárásokat a teljesítményre optimalizált konfigurációk esetében.

### <a name="cpu-tab"></a>CPU lap

A **CPU** lap a PerfInsights futtatása során a rendszerszintű CPU-használatról nyújt információkat. A magas CPU-használati időszakokra és a nagy teljesítményű CPU-felhasználókra vonatkozó információk hasznosak lehetnek a PROCESSZORral kapcsolatos problémák elhárításában.

![Képernyőfelvétel a PerfInsights jelentés CPU lapjáról](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Tároló lap

Az **eredmények** szakasz a tárolással kapcsolatos különböző eredményeket és javaslatokat jeleníti meg.

Az eszközök és egyéb kapcsolódó szakaszok, például a **Partitions**, az **LVM**és a **MDADM** lapok **letiltják** , hogy az eszközök hogyan vannak konfigurálva és hogyan kapcsolódnak egymáshoz.

![Képernyőfelvétel a Storage lapról](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Képernyőkép a MDADM lapról](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Linux lap

A **Linux** lapon a virtuális gépen futó hardverre és operációs rendszerre vonatkozó információk találhatók. A részletek tartalmazzák a futó folyamatok listáját és a vendég ügynök, a PCI, a CPU, az illesztőprogramok és a LIS illesztőprogramok információit.

![A Linux lap képernyőképe](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Következő lépések

A diagnosztikai naplókat és jelentéseket feltöltheti Microsoft ügyfélszolgálata további áttekintés céljából. Ha a Microsoft ügyfélszolgálata munkatársaival dolgozik, kérheti, hogy továbbítsa a PerfInsights által generált kimenetet, hogy segítséget nyújtson a hibaelhárítási folyamathoz.

Az alábbi képernyőképen egy, a következőhöz hasonló üzenet látható:

![Képernyőkép a Microsoft ügyfélszolgálataról](media/how-to-use-perfinsights-linux/support-email.png)

Az üzenetben található utasításokat követve férhet hozzá a fájlátviteli munkaterülethez. A további biztonság érdekében meg kell változtatnia a jelszavát az első használatkor.

A bejelentkezést követően egy párbeszédpanel jelenik meg, amelyen feltöltheti a PerfInsights által gyűjtött **PerformanceDiagnostics\_éééé-hh-nn\_hh-mm-SS-fff. tar. gz** fájlt.
