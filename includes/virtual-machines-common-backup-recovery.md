---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179113"
---
## <a name="azure-backup"></a>Azure Backup

Éles számítási feladatokat futtató Azure-beli virtuális gépek biztonsági mentéséhez használja a Azure Backup. A Azure Backup támogatja a Windows és a Linux rendszerű virtuális gépekhez készült alkalmazás-konzisztens biztonsági mentéseket. Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak bizonyos fájlokat. 

Az Azure-beli virtuális gépek Azure Backupének egyszerű, gyakorlati bevezetését az Azure Virtual Machines biztonsági mentése című oktatóanyagban tekintheti meg [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md)rendszeren.

A Azure Backup működéséről a [virtuális gépek biztonsági mentési infrastruktúrájának megtervezése az Azure-ban](../articles/backup/backup-azure-vms-introduction.md) című témakörben olvashat bővebben.


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery a virtuális gépeket jelentős katasztrófa esetén védi, amikor egy egész régió jelentős természeti katasztrófák vagy széleskörű szolgáltatás-megszakítás miatt kiesést tapasztal. A virtuális gépekhez Azure Site Recovery konfigurálhatja, hogy az alkalmazás a percek alatt egyetlen kattintással helyreállítható legyen. Egy tetszőleges Azure-régióba replikálhat, és nem korlátozódik a párosított régiókra. 

A vész-helyreállítási gyakorlatokat igény szerinti tesztelési feladatátvételsel futtathatja anélkül, hogy az hatással lenne az éles számítási feladatokra vagy a folyamatos replikálásra. Helyreállítási tervek létrehozása a több virtuális gépen futó teljes alkalmazás feladatátvételének és feladat-visszavételének előkészítéséhez. A helyreállítási terv szolgáltatás integrálva van az Azure Automation runbookok.

[A virtuális gépek replikálásával](https://aka.ms/a2a-getting-started)kezdheti meg a lépéseket. 

## <a name="managed-snapshots"></a>Felügyelt Pillanatképek 

A fejlesztési és tesztelési környezetekben a pillanatképek gyors és egyszerű lehetőséget biztosítanak a Managed Disks használó virtuális gépek biztonsági mentéséhez. A felügyelt pillanatkép egy felügyelt lemez írásvédett teljes másolata. A pillanatképek függetlenek a forrás lemeztől, és felhasználhatók új felügyelt lemezek létrehozására a virtuális gépek újraépítése érdekében. A számlázás a lemez használt része alapján történik. Ha például létrehoz egy pillanatképet egy felügyelt lemezről, amely 64 GB kiosztott kapacitással rendelkezik, és a tényleges felhasznált adatok mérete 10 GB, a pillanatképet csak a 10 GB-os adatméretre számítjuk fel.  

A pillanatképek létrehozásával kapcsolatos további információkért lásd:

* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Windows alatt](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt VHD másolatának létrehozása Pillanatképek használatával Linuxon](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>További lépések
A Azure Backup kipróbálásához kövesse a "Windows rendszerű virtuális gépek biztonsági mentése" oktatóanyagot a [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy a [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md)rendszerhez.
