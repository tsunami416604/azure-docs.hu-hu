---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3dfc72ff0347a93c6c6dce0e7ec763dd8241c55b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
## <a name="azure-backup"></a>Azure Backup

Termelési számítási feladatokhoz futtató Azure virtuális gépek biztonsági mentéséről, használja az Azure Backup szolgáltatásnál. Azure biztonsági mentés a Windows és a Linux virtuális gépek alkalmazáskonzisztens biztonsági mentést támogatja. Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak bizonyos fájlokat. 

Az egyszerű, gyakorlati megismerkedhet az Azure Backup Azure virtuális gépekhez, tekintse meg az "Azure virtuális gépek biztonsági mentése" a [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Azure biztonsági mentési működéséről további információkért lásd: [tervezze meg a virtuális gép biztonsági mentési infrastruktúra az Azure-ban](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Az Azure Site Recovery jelentős katasztrófa esetben megakadályozza a virtuális gépek, amikor egy teljes régió miatt jelentős természeti katasztrófa vagy szélesebb körben szolgáltatáskiesést kimaradás. Azure Site Recovery konfigurálhatja a virtuális gépek, így helyreállíthatja az alkalmazás, függetlenül attól, hogy az egyetlen kattintással perc alatt. Az Ön által választott Azure-régióhoz replikálhatja, a nem párhuzamos régiók korlátozni. 

Az igény szerinti feladatátvételi teszteket, anélkül, hogy befolyásolná a termelési számítási feladatokhoz vagy a folyamatban lévő replikáció futtathatja vész-helyreállítási gyakorlatokat. Hozzon létre helyreállítási terv feladatátvételi és a teljes alkalmazás több virtuális gépeken futó feladat-visszavétel számít. A helyreállítási terv funkció integrálva van az Azure automation-forgatókönyveket.

Elkezdheti által [a virtuális gépek replikálásához](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Felügyelt pillanatképek 

Fejlesztési és tesztelési környezetben pillanatképek adjon meg egy gyors és egyszerű beállítása, a felügyelt lemezeket használó virtuális gépek biztonsági mentéséről. Felügyelt pillanatképet egy csak olvasható, teljes másolata egy felügyelt lemezes. A pillanatképek független a forrás lemez létezik, és új kezelt lemezek újbóli létrehozása a virtuális gépek létrehozásához használható. Azok a lemez használt része alapján számlázzuk. Ha például létrehoz egy pillanatképet egy felügyelt lemezről 64 GB kiosztott kapacitással és 10 GB tényleges felhasznált adatmérettel, a pillanatképért csak a felhasznált 10 GB adatméret alapján számolunk fel díjat.  

Pillanatképeinek további információkért lásd:

* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Windows alatt](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Linux alatt](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>További lépések
Módszer kipróbálásához Azure Backup a "biztonságimásolat-Windows virtuális gépek oktatóanyag" követve a [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
