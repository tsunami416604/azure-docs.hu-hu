---
title: Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása
description: Hibaelhárítási útmutató segít az Azure Backup teljesítménybeli problémák okának biztosít
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 592a46077bb9e3469f3a42a95173af1b6db93510
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704937"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása
Ez a cikk segítségével diagnosztizálhatja a fájlok és mappák lassú biztonsági mentési teljesítmény okának, amikor az Azure Backup használata a hibaelhárítási útmutatót. Fájlok biztonsági mentése az Azure Backup-ügynök használatakor a biztonsági mentési folyamat a vártnál hosszabb vehet igénybe. Ez a késleltetés oka lehet egy vagy több, a következők:

* [Nincsenek a teljesítmény szűk azon a számítógépen, a másolat készül.](#cause1)
* [Egy másik folyamat vagy víruskereső zavarja az Azure biztonsági mentési folyamat.](#cause2)
* [A Backup szolgáltatás ügynökének futása egy Azure virtuális gépen (VM).](#cause3)  
* [Biztonsági másolatot egy nagy mennyiségű (több millió) fájlt.](#cause4)

Mielőtt elkezdené kapcsolatos hibák elhárítása, azt javasoljuk, hogy töltse le és telepítse a [legújabb Azure Backup szolgáltatás ügynökének](https://aka.ms/azurebackup_agent). Gyakori frissítések különböző kapcsolatos problémák megoldása, a szolgáltatások hozzáadása és a teljesítmény javítása a biztonsági mentési ügynök VÁLLALUNK.

Emellett javasoljuk, hogy tekintse át a [Azure Backup szolgáltatás – gyakori kérdések](backup-azure-backup-faq.md) , hogy nem oldják a gyakori konfigurációs hibáinak bármelyikét.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Ok: A számítógépen a teljesítmény szűk
Azon a számítógépen, a másolat készül a szűk keresztmetszetek késéseket okozhat. Például a számítógép képes olvasni vagy írni lemezre vagy adatokat küldeni a hálózaton keresztül a rendelkezésre álló sávszélesség az szűk keresztmetszeteket okozhat.

Windows beépített eszközt, amelynek a neve biztosít [Teljesítményfigyelő](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Teljesítményfigyelő) észleli ezeket a szűk keresztmetszeteket.

Íme, néhány teljesítményszámlálók és az optimális biztonsági mentésekhez szűk diagnosztizálásakor hasznos lehet,-címtartományokat.

| A számláló | Állapot |
| --- | --- |
| Logikai lemez (a fizikai lemez) – üresjárati % |• 100 %-os üresjárati 50 %-os üresjárati = kifogástalan</br>• 49 % 20 %-os üresjárati üresjárati = figyelmeztetés vagy a figyelő</br>• 19: % 0 % üresjárati üresjárati = kritikus vagy kívüli specifikációja |
| Logikai lemez (a fizikai lemez) – % átlagos száma Lemez mp Olvasás vagy írás |• 0,015 MS 0,001 ms = kifogástalan</br>• 0,025 MS 0,015 ms = figyelmeztetés vagy a figyelő</br>• 0.026 ms vagy kritikus vagy kívüli specifikációja már = |
| Logikai lemez (a fizikai lemez) – a Lemezvárólista jelenlegi hossza (az összes példányra) |80 kérelem 6 percnél hosszabb ideig |
| Memória – nem készlet mérete (bájt) |• Legfeljebb 60 %-ban felhasznált készlet = kifogástalan<br>• 61 % és 80 %-készlet felhasznált = figyelmeztetés vagy a figyelő</br>• Nagyobb, mint 80 %-os készlet felhasznált = kritikus vagy kívüli specifikációja |
| Memória – készlet mérete (bájt) |• Legfeljebb 60 %-ban felhasznált készlet = kifogástalan</br>• 61 % és 80 %-készlet felhasznált = figyelmeztetés vagy a figyelő</br>• Nagyobb, mint 80 %-os készlet felhasznált = kritikus vagy kívüli specifikációja |
| Memória – rendelkezésre álló hely MB-ban |• 50 %-a rendelkezésre álló szabad memória vagy további = kifogástalan</br>• 25 %-a rendelkezésre álló szabad memória = figyelője</br>• 10 %-a rendelkezésre álló szabad memória figyelmeztetés =</br>• Kevesebb mint 100 MB-os vagy 5 %-a rendelkezésre álló szabad memória = kritikus vagy kívüli specifikációja |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processzor –\`processzor kihasználtsága (összes)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• 60 %-nál kisebb felhasznált = kifogástalan</br>• 61 90 %-felhasznált = figyelő vagy figyelmeztetés</br>• 91 – 100 % felhasznált kritikus = |

> [!NOTE]
> Ha azt állapítja meg, hogy az infrastruktúra képes legyen a probléma oka, azt javasoljuk, hogy a lemezek, a jobb teljesítmény érdekében rendszeresen töredezettségmentesítése.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Ok: Egy másik folyamat vagy víruskereső zavarja a folyamatot az Azure Backup szolgáltatással
Megtudtuk, hogy több példányt, ahol más folyamatok, a Windows rendszerben rendelkezik az zavarhatja az Azure Backup ügynök folyamat teljesítményét. Például ha az Azure Backup ügynök és a egy másik program használja, a biztonsági másolatokat, vagy ha víruskereső fut, és zárolta a fájlok biztonsági mentése, az a többszörös zárolja a fájlok okozhat a versengés. Ebben a helyzetben a biztonsági mentés sikertelen lehet, vagy a feladat a vártnál hosszabb ideig tarthat.

Ebben a forgatókönyvben a legjobb javaslat, hogy kapcsolja ki a másik biztonsági mentési program meg, hogy az Azure Backup-ügynök biztonsági mentésének ideje változik. Általában gondoskodik róla, hogy több biztonsági mentési feladat nem fut egyszerre is használhatók, nehogy azok egymással.

A víruskeresők azt javasoljuk, hogy a következő fájlok és helyek kizárása:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe folyamatként
* C:\Program Files\Microsoft Azure Recovery Services Agent\ mappák
* Ideiglenes hely (Ha nem használ a szokásos helyre)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Ok: Egy Azure virtuális gépen futó biztonsági mentési ügynök
A Backup szolgáltatás ügynökét a virtuális gép futtatja, ha a teljesítmény lassabb, mint ha is futtat, a fizikai gép lesz. Ez várható IOPS-korlátozások miatt.  Azonban optimalizálhatja a teljesítményt az adatmeghajtók, amely éppen készül biztonsági másolat az Azure Premium Storage között. Dolgozunk a probléma javítása, és a javítás egy későbbi kiadásban elérhető lesz.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Ok: Nagyszámú (több millió) fájlok biztonsági mentésével
Nagy mennyiségű adat áthelyezését-nál kisebb mennyiségű adatot áthelyezése hosszabb ideig tart. Bizonyos esetekben a biztonsági mentés időpontja nem csupán az adatok, hanem a fájlok és mappák száma méretének kapcsolódik. Ez akkor különösen igaz olyankor, amikor a kisméretű fájlok (és a néhány kilobájt néhány bájt) több millió éppen készül biztonsági másolat.

Ez az oka, hogy közben az adatok biztonsági mentése és áthelyezi az Azure-ba, Azure egyidejűleg katalogizálni a fájlokat. Egyes ritka esetekben a katalógus műveletet is igénybe vehet a vártnál hosszabb.

A következő mutatók segítségével megismerheti a szűk keresztmetszetet, és ennek megfelelően működjön a következő lépéseket:

* **Felhasználói felület folyamatban láthatók az adatátvitel**. Az adatok átkerülnek még mindig folyamatban van. A hálózati sávszélességet vagy az adatok mérete, előfordulhat, hogy lehet késedelmet okoz.
* **Felhasználói felület folyamat nem jelenik a adatátvitel**. Nyissa meg a C:\Program Files\Microsoft Azure Recovery Services Agent\Temp található naplókat, és ellenőrizze a naplókat a FileProvider::EndData bejegyzést. Ez a bejegyzés azt jelzi, hogy, hogy az adatátvitel befejeződött, és a katalógus művelet történik. A biztonsági mentési feladatok ne szakítsa meg. Inkább várja meg, már egy kicsit a katalógus művelet befejeződik. Ha a probléma tartósan fennáll, forduljon a [az Azure-támogatás](https://portal.azure.com/#create/Microsoft.Support).
