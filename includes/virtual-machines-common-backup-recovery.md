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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160051"
---
## <a name="azure-backup"></a>Azure Backup

Azure virtuális gépek biztonsági mentésének éles számítási feladatokat futtatnak, az Azure Backup használata. Az Azure Backup a Windows és Linux rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentést támogatja. Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak bizonyos fájlokat. 

Lásd az "Azure virtuális gépek biztonsági mentése" oktatóanyag az egyszerű, gyakorlati Bevezetés az Azure Backup szolgáltatásban, az Azure virtuális gépek, [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Azure Backup működésével kapcsolatos további információkért lásd: [az Azure-beli virtuális gép biztonsági infrastruktúrájának megtervezése](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Az Azure Site Recovery vészhelyzetek forgatókönyv, védi a virtuális gépek, amikor egy teljes régió kimaradás jelentős természeti katasztrófa vagy széles körű szolgáltatás megszakadása miatt. A virtuális gépek Azure Site Recovery úgy, hogy helyre tudja állítani az alkalmazás, függetlenül attól, hogy egyetlen kattintással perc konfigurálható. A választott Azure-régióba replikálhatja, nem korlátozott a társított két régió bármelyikén. 

Az igény szerinti feladatátvételi teszteket, vészhelyreállítási próbákat anélkül, hogy ez hatással lenne az éles számítási feladatokra vagy a folyamatban lévő replikáció futtathatja. Feladatátvétele és feladat-visszavétel a teljes alkalmazás több virtuális gépen futó helyreállítási terveket hozhat létre. A helyreállítási terv funkció integrálva van az Azure automation-runbookokkal.

Úgy kezdheti [a virtuális gépek replikálásához](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Felügyelt pillanatképek 

Fejlesztési-tesztelési környezetet pillanatképek egy felügyelt lemezeket használó virtuális gépek biztonsági mentésének gyors és egyszerű lehetőséget biztosítanak. Egy felügyelt pillanatkép egy csak olvasható teljes másolatát egy felügyelt lemez. Pillanatképek létezik a forráslemez független, és a virtuális gép újjáépítését új felügyelt lemezek létrehozására használható. Ezek díjszabása a felhasznált alapján a lemez. Ha például létrehoz egy pillanatképet egy felügyelt lemezről 64 GB kiosztott kapacitással és 10 GB tényleges felhasznált adatmérettel, a pillanatképért csak a felhasznált 10 GB adatméret alapján számolunk fel díjat.  

Pillanatképek létrehozásával kapcsolatos további információkért lásd:

* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Windows alatt](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Linux alatt](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>További lépések
Kipróbálhatja az Azure Backup a következő, a "biztonságimásolat-Windows virtual machines oktatóanyagban" a [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
