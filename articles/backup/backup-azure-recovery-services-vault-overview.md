---
title: Helyreállítási szolgáltatások – áttekintés tárolók |} Microsoft Docs
description: Áttekintés és Recovery Services-tárolók és az Azure mentési tárolókban összehasonlítása.
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.assetid: 38d4078b-ebc8-41ff-9bc8-47acf256dc80
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/15/2017
ms.author: markgal;arunak;sogup
ms.openlocfilehash: 12ecc7ab33dba2f4f9842a4fbd6491400024a4e5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="recovery-services-vaults-overview"></a>Helyreállítási szolgáltatások tárolók áttekintése

Ez a cikk ismerteti a Recovery Services-tároló funkcióit. Recovery Services-tároló egy tárolási egység, amely az adatok Azure-ban. Az adatok program általában másolja az adatokat, vagy a virtuális gépek (VM), a munkaterhelés, a kiszolgálókra vagy munkaállomásokra konfigurációs adatait. Recovery Services-tárolók használatával a különböző Azure-szolgáltatásokra például IaaS virtuális gépeket (Linux- vagy Windows-) és az Azure SQL-adatbázisok biztonsági mentési adatok tárolására. Helyreállítási szolgáltatások tárolók támogatása a System Center DPM, a Windows Server, Azure Backup Server, és több. A Recovery Services-tárolók leegyszerűsítik a biztonsági mentési adatok szervezését, miközben minimálisra csökkentik a munkaterhelést. 

Azure-előfizetéssel, belül régiónként előfizetésenként legfeljebb 500 Recovery Services-tárolók is létrehozhat.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Összehasonlító Recovery Services-tárolók és a mentési tárolókat

Ha továbbra is mentési tárolókban, folyamatosan automatikusan frissülnek a Recovery Services-tárolók. 2017. November, amelyet az összes biztonsági mentési tárolók frissítette a Recovery Services-tárolók. 

Helyreállítási szolgáltatások tárolók Azure-ban Azure Resource Manager modelljét alapulnak, mivel az Azure Service Manager modell alapuló biztonsági mentési tárolóból. Amikor frissít egy biztonsági mentési tárolót Recovery Services-tároló, a biztonsági mentési adatok sértetlenek maradnak alatt és után a frissítési folyamat. Helyreállítási szolgáltatások tárolók funkciók nem érhető el a biztonsági mentési tárolóból, például a biztosítására:

- **Bővített képességet biztonságos biztonsági mentési adatok segítségével**: A Recovery Services-tárolók, Azure biztonsági mentési képességeket biztosít a biztonsági felhőbeli biztonsági másolatok védelme érdekében. A biztonsági funkciók ellenőrizze a biztonsági másolatok biztonságos, és biztonságosan az adatokat, helyreállítani, még akkor is, ha az éles és a biztonsági mentés integritása sérül. [További információ](backup-azure-security-feature.md)

- **A hibrid informatikai környezetben központi figyelését**: A Recovery Services-tárolók, figyelheti nem csak a [Azure IaaS virtuális gépeket](backup-azure-manage-vms.md) , hanem a [a helyszíni eszközök](backup-azure-manage-windows-server.md#manage-backup-items) központi portálról. [További információ](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Szerepköralapú hozzáférés-vezérlés (RBAC)**: RBAC biztosít a minden részletre kiterjedő felügyeleti hozzáférés az Azure-ban. [Azure biztosít a különféle beépített szerepkörök](../role-based-access-control/built-in-roles.md), és az Azure biztonsági mentés van három [beépített szerepkörök kezelése a helyreállítási pontok](backup-rbac-rs-vault.md). Helyreállítási szolgáltatások tárolók kompatibilisek-e az RBAC, amely korlátozza a biztonsági mentés, és állítsa vissza az elérését a felhasználói szerepkörök adott csoportján. [További információ](backup-rbac-rs-vault.md)

- **Az összes konfiguráció Azure virtuális gépek védelme**: Recovery Services-tárolók a Resource Manager-alapú virtuális gépek többek között a Premium lemezek, a felügyelt lemezek és a titkosított virtuális gépek védelme. A biztonsági másolatok tárolóját frissítése a Recovery Services-tároló lehetőséget nyújt a Service Manager-alapú virtuális gépek frissítése a Resource Manager-alapú virtuális gépeket. A tároló a frissítés során a Service Manager-alapú virtuális gép helyreállítási pontok megőrzése, és konfigurálja a frissített (Resource Manager-kompatibilis) virtuális gépek védelmét. [További információ](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Infrastruktúra-szolgáltatási virtuális gépek azonnali visszaállítási**: használatával Recovery Services-tárolók, állíthatja vissza fájlokat és mappákat az infrastruktúra-szolgáltatási virtuális gép helyreállítása a teljes virtuális gép, amely lehetővé teszi a gyorsabb visszaállítás nélkül. Infrastruktúra-szolgáltatási virtuális gépek azonnali visszaállítási Windows és Linux virtuális gépek érhető el. [További információ](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>A portál a Recovery Services-tárolók kezelése
Létrehozása és kezelése az Azure portálon Recovery Services-tárolók nem egyszerű, mert a biztonsági mentési szolgáltatás integrálva van az Azure-beállítások menüjében. Ez az integráció azt jelenti, hogy hozzon létre, vagy kezelheti a Recovery Services-tároló *a célként megadott szolgáltatás kontextusában*. Például egy virtuális Gépet a helyreállítási pontok megtekintéséhez válassza ki azt, és kattintson **biztonsági mentés** a beállítások menüben. Ezt a virtuális Gépet a biztonsági mentési adatokat jelenik meg. A következő példában **ContosoVM** a virtuális gép neve. **ContosoVM-demovault** a Recovery Services-tároló neve. Ne feledje, hogy a helyreállítási pontokat tárolja a Recovery Services-tároló neve nem szükséges, ez az információ a virtuális gép érheti el.  

![Helyreállítási szolgáltatások tároló tartalmazó virtuális gép részletei](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context.png)

Ha több kiszolgáló védi a azonos Recovery Services-tároló, több logikai közül a Recovery Services-tároló lehet. Keresse meg az előfizetés összes Recovery Services-tárolók, és válasszon egyet a listáról.

A következő szakaszok tartalmazzák a Recovery Services-tároló minden típusú tevékenység használandó cikkek mutató hivatkozásokat tartalmaz.

### <a name="back-up-data"></a>Adatok biztonsági mentése
- [Készítsen biztonsági másolatot az Azure virtuális gép](backup-azure-vms-first-look-arm.md)
- [Biztonsági másolatot készíthet a Windows Server és a Windows munkaállomás](backup-try-azure-backup-in-10-mins.md)
- [A DPM-munkaterhelések biztonsági mentése az Azure-bA](backup-azure-dpm-introduction.md)
- [Felkészülés az Azure Backup Server használatával-munkaterhelések biztonsági mentése](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Helyreállítási pontok kezelése
- [Azure virtuális gép biztonsági mentések kezelése](backup-azure-manage-vms.md)
- [Fájlok és mappák kezelése](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Állítsa vissza az adatokat a tárolóból
- [Egy Azure virtuális gép egyes fájlok helyreállítása](backup-azure-restore-files-from-vm.md)
- [Állítsa vissza az Azure virtuális gép](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>A tároló biztonságos
- [A Recovery Services-tárolók a felhő biztonsági mentési adatok védelme](backup-azure-security-feature.md)



## <a name="next-steps"></a>További lépések
Használja a következő cikkeket:</br>
[Készítsen biztonsági másolatot az infrastruktúra-szolgáltatási virtuális gép](backup-azure-arm-vms-prepare.md)</br>
[Készítsen biztonsági másolatot az Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Készítsen biztonsági másolatot a Windows Server](backup-configure-vault.md)
