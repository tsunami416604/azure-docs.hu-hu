---
title: Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása
description: Hibaelhárítási útmutatót biztosít a Azure Backup teljesítményproblémák okának diagnosztizálásához
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 3a39d39412c8b64d1851ea0fc9511d116f3b232a
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465341"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Az Azure Backup-fájlok és -mappák lassú biztonsági mentésének hibaelhárítása
Ez a cikk hibaelhárítási útmutatást nyújt a fájlok és mappák lassú biztonsági mentési teljesítményének okának diagnosztizálásához Azure Backup használatakor. Ha a Azure Backup ügynök használatával készít biztonsági másolatot a fájlokról, a biztonsági mentési folyamat a vártnál több időt vehet igénybe. Ezt a késleltetést a következők egyike okozhatja:

* [A biztonsági mentés alatt álló számítógépen szűk a teljesítmény.](#cause1)
* [Egy másik folyamat vagy víruskereső szoftver zavarja a Azure Backup folyamatot.](#cause2)
* [A biztonságimásolat-készítő ügynök egy Azure-beli virtuális gépen (VM) fut.](#cause3)  
* [Nagy mennyiségű (millió) fájl biztonsági mentését végzi.](#cause4)

A hibaelhárítási problémák megkezdése előtt javasoljuk, hogy töltse le és telepítse a [legújabb Azure Backup](https://aka.ms/azurebackup_agent)-ügynököt. Gyakori frissítéseket végezünk a Backup ügynöknek a különböző problémák megoldásához, funkciók hozzáadásához és a teljesítmény javításához.

Javasoljuk továbbá, hogy tekintse át a [Azure Backup szolgáltatással](backup-azure-backup-faq.md) kapcsolatos gyakori kérdéseket, és győződjön meg arról, hogy nem tapasztalt a gyakori konfigurációs problémák egyikét sem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Ok: A számítógép teljesítményének szűk keresztmetszetei
A biztonsági mentés alatt álló számítógép szűk keresztmetszetei késéseket okozhatnak. Előfordulhat például, hogy a számítógép képes a lemezre írni vagy olvasni, vagy a rendelkezésre álló sávszélességet a hálózaton keresztüli adatküldés esetén szűk keresztmetszetet okozhat.

A Windows olyan beépített eszközt biztosít, amely a [Teljesítményfigyelő](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) használatával ismeri fel ezeket a szűk keresztmetszeteket.

Íme néhány teljesítményszámlálók és tartományok, amelyek hasznosak lehetnek a szűk keresztmetszetek optimális biztonsági mentéshez való diagnosztizálásában.

| Számláló | Állapot |
| --- | --- |
| Logikai lemez (fizikai lemez) –% tétlen |• 100% üresjárati állapot: 50% tétlen = kifogástalan</br>• 49% tétlen – 20% tétlen = figyelmeztetés vagy figyelő</br>• 19% üresjáratban 0% tétlen = kritikus vagy a spec |
| Logikai lemez (fizikai lemez) –% AVG. Lemez (mp) olvasása vagy írása |• 0,001 MS – 0,015 MS = kifogástalan</br>• 0,015 MS – 0,025 MS = figyelmeztetés vagy figyelő</br>• 0,026 MS vagy több = kritikus vagy nem a spec |
| Logikai lemez (fizikai lemez) – a lemez aktuális várólistájának hossza (az összes példány esetében) |80 kérelem 6 percnél hosszabb ideig |
| Memória – nem lapozható készlet (bájt) |• A felhasznált készlet kevesebb mint 60%-a felhasználva = kifogástalan<br>• 61% – 80%-a készlet felhasználva = figyelmeztetés vagy figyelő</br>• Nagyobb, mint 80%-os készlet felhasználva = kritikus vagy a spec |
| Memória – a készlet lapozható bájtjai |• A felhasznált készlet kevesebb mint 60%-a felhasználva = kifogástalan</br>• 61% – 80%-a készlet felhasználva = figyelmeztetés vagy figyelő</br>• Nagyobb, mint 80%-os készlet felhasználva = kritikus vagy a spec |
| Memória – rendelkezésre álló megabájt |• a rendelkezésre álló szabad memória 50%-a vagy több = kifogástalan</br>• a rendelkezésre álló szabad memória 25%-a = figyelő</br>• a rendelkezésre álló szabad memória 10%-a = figyelmeztetés</br>• A rendelkezésre álló szabad memória kevesebb, mint 100 MB vagy 5%-a érhető el = kritikus vagy a spec |
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
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processzor –\`processzoridő (minden példány)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues

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
| Processor--\%Processor Time (all instances) |• Kevesebb, mint 60% felhasznált = kifogástalan</br>• 61% – 90% felhasználva = figyelő vagy figyelmeztetés</br>• 91% – 100% felhasználva = kritikus |

> [!NOTE]
> Ha azt állapítja meg, hogy az infrastruktúra a bűnös, javasoljuk, hogy a lemezeket a jobb teljesítmény érdekében rendszeresen töredezettségmentesíteni.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Ok: Egy másik folyamat vagy víruskereső szoftver zavarja a Azure Backup
Láttuk, hogy a Windows rendszer más folyamatai negatív hatással voltak a Azure Backup ügynök folyamatának teljesítményére. Ha például a Azure Backup-ügynököt és egy másik programot használ az adatbiztonsági mentéshez, vagy ha víruskereső szoftver fut, és zárolva van a fájlok biztonsági mentésére szolgáló zárolás, a fájlok több zárolása is okozhatja a tartalmat. Ebben az esetben előfordulhat, hogy a biztonsági mentés meghiúsul, vagy a feladat a vártnál több időt is igénybe vehet.

Ebben a forgatókönyvben a legjobb javaslat, hogy kikapcsolja a másik biztonsági mentési programot, hogy megtekintse a Azure Backup-ügynök biztonsági mentésének időpontját. Általában ügyeljen arra, hogy a több biztonsági mentési feladat ne fusson egyszerre, hogy ne legyenek hatással egymásra.

A víruskereső programok esetében javasoljuk, hogy zárja ki a következő fájlokat és helyet:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe folyamatként
* C:\Program Files\Microsoft Azure Recovery Services Agent \ mappák
* Üres hely (ha nem a standard helyet használja)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Ok: Azure-beli virtuális gépen futó biztonsági mentési ügynök
Ha a biztonsági mentési ügynököt egy virtuális gépen futtatja, a teljesítmény lassabb lesz, mint amikor egy fizikai gépen futtatja. Ez a IOPS korlátai miatt várható.  Azonban optimalizálhatja a teljesítményt úgy, hogy átváltja az Azure-Premium Storage biztonsági mentés alatt álló adatmeghajtókat. Dolgozunk a probléma javításán, és a javítás egy későbbi kiadásban is elérhető lesz.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Ok: Nagy számú (millió) fájl biztonsági mentése
A nagyméretű adatmennyiség hosszabb időt vesz igénybe, mint a kisebb adatmennyiségek áthelyezése. Bizonyos esetekben a biztonsági mentés ideje nem csupán az adat méretére, hanem a fájlok vagy mappák számára is vonatkozik. Ez különösen akkor igaz, ha több millió kis fájlról (néhány bájtról néhány kilobájtra) készül biztonsági mentés.

Ez a viselkedés azért fordul elő, mert az adatbiztonsági mentés és az Azure-ba való áthelyezés során az Azure egyszerre katalogizálja a fájlokat. Bizonyos ritkán előforduló esetekben a katalógusbeli művelet a vártnál hosszabb időt vesz igénybe.

A következő mutatók segítségével megismerheti a szűk keresztmetszeteket, és ennek megfelelően működik a következő lépésekben:

* **A felhasználói felületen az adatátvitel előrehaladása látható**. Az adatforgalom továbbra is átvitel alatt áll. Előfordulhat, hogy a hálózati sávszélesség vagy az adatméret késést okoz.
* **A felhasználói felület nem mutatja az adatátvitel folyamatát**. Nyissa meg a C:\Program Files\Microsoft Azure Recovery Services Agent\Temp található naplókat, és keresse meg a FileProvider:: EndData bejegyzést a naplókban. Ez a bejegyzés azt jelzi, hogy az adatátvitel befejeződött, és a katalógus-művelet zajlik. Ne szakítsa meg a biztonsági mentési feladatokat. Ehelyett várjon egy kicsit, amíg a katalógus-művelet befejeződik. Ha a probléma továbbra is fennáll, forduljon az [Azure ügyfélszolgálatához](https://portal.azure.com/#create/Microsoft.Support).
