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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179113"
---
## <a name="azure-backup"></a>Azure Backup

Éles számítási feladatokat futtató Azure-beli virtuális gépek biztonsági mentéséhez használja az Azure Backup szolgáltatást. Az Azure Backup támogatja az alkalmazáskonzisztens biztonsági mentéseket windowsos és Linuxos virtuális gépekhez is. Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak bizonyos fájlokat. 

Az Azure-beli virtuális gépekhez való azure biztonsági mentés egyszerű, gyakorlati bemutatását lásd a [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy Windows "Az Azure virtuális gépek biztonsági mentése" című [oktatóanyagban.](../articles/virtual-machines/windows/tutorial-backup-vms.md)

Az Azure Backup működéséről a [Virtuálisgép biztonsági mentési infrastruktúrájának megtervezése az Azure-ban című témakörben](../articles/backup/backup-azure-vms-introduction.md) talál további információt.


## <a name="azure-site-recovery"></a>Azure Site Recovery

Az Azure Site Recovery megvédi a virtuális gépeket egy jelentős katasztrófa-forgatókönyvtől, amikor egy egész régió súlyos természeti katasztrófa vagy a szolgáltatás széles körű megszakítása miatt kimaradást tapasztal. Konfigurálhatja az Azure Site Recovery a virtuális gépek, így az alkalmazás egyetlen kattintással percek alatt helyreállíthatja. Replikálhatja az Ön által kiválasztott Azure-régióba, nem korlátozódik a párosított régiókra. 

Az igény szerinti tesztelési feladatátvételekkel is futtathat vész-helyreállítási gyakorlatokat anélkül, hogy ez hatással lenne az éles számítási feladatokra vagy a folyamatos replikációra. Hozzon létre helyreállítási terveket a több virtuális gépen futó teljes alkalmazás feladat-átvételének és feladat-visszavételének vezényléséhez. A helyreállítási terv funkció integrálva van az Azure automation runbookok.

A [virtuális gépek replikálásával](https://aka.ms/a2a-getting-started)első lehet. 

## <a name="managed-snapshots"></a>Felügyelt pillanatképek 

A fejlesztési és tesztelési környezetekben a pillanatképek gyors és egyszerű lehetőséget biztosítanak a felügyelt lemezeket használó virtuális gépek biztonsági mentéséhez. A felügyelt pillanatkép egy felügyelt lemez írásvédett teljes másolata. A pillanatképek a forráslemeztől függetlenül léteznek, és új felügyelt lemezek létrehozásához használhatók a virtuális gép újjáépítéséhez. A számlázás a lemez használt része alapján történik. Ha például egy 64 GB-os kiosztott kapacitással és 10 GB tényleges felhasznált adatmérettel rendelkező felügyelt lemez pillanatképét hoz létre, a pillanatkép csak a 10 GB-os használt adatméretért kerül számlázásra.  

A pillanatképek létrehozásáról további információt a következő témakörökben talál:

* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Windows alatt](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt virtuális merevlemez másolatának létrehozása linuxos pillanatképek használatával](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>További lépések
Kipróbálhatja az Azure Backup szolgáltatást a "Windows virtuális gépek ről bemutató" bemutatót [követve Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy [Windows rendszeren.](../articles/virtual-machines/windows/tutorial-backup-vms.md)
