---
title: A Recovery Services-tárolók áttekintése
description: Áttekintése és összehasonlítása az Azure Backup-tárolók és a Recovery Services-tárolók.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/10/2018
ms.author: markgal
ms.openlocfilehash: 04b755d7ca1ec72b075ecaa4b32a35f5009a938b
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "42060810"
---
# <a name="recovery-services-vaults-overview"></a>Recovery Services tárolók áttekintése

Ez a cikk a Recovery Services-tároló funkcióját ismerteti. Recovery Services-tárolót egy tárolóentitás az Azure-beli adat-környezetet tároló. Az adatok általában a másolatait adatokat, illetve a virtuális gépek (VM), a számítási feladatok, a kiszolgálókra vagy munkaállomásokra konfigurációs adatait. Recovery Services-tárolók segítségével különböző Azure-szolgáltatások például az Azure SQL-adatbázisok és az IaaS virtuális gépek (Linux vagy Windows) a biztonsági mentési adatok tárolásához. Helyreállítási szolgáltatások tárolók támogatása a System Center DPM, a Windows Server, az Azure Backup Server és több. A Recovery Services-tárolók leegyszerűsítik a biztonsági mentési adatok szervezését, miközben minimálisra csökkentik a munkaterhelést. 

Egy Azure-előfizetés keretében előfizetésenként és régiónként legfeljebb 500 Recovery Services-tárolót hozhat létre.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Összehasonlító Recovery Services-tárolók és a Backup-tárolók

Ha biztonsági mentési tárak továbbra is rendelkezik, azok folyamatban van az automatikus frissítése Recovery Services-tárolókra. 2017 November szerint az összes biztonsági mentési tárak is frissülnek a Recovery Services-tárolók. 

Recovery Services-tárolók az Azure-ban, az Azure Resource Manager modellel alapulnak, mivel a Backup-tárolók az Azure Service Manager modellel is alapján. Amikor frissít egy biztonsági mentési tárolót egy Recovery Services-tároló, a biztonsági mentési adatok sértetlenek maradnak alatt és a frissítési folyamat után. Recovery Services-tárolók adja meg a funkciók nem érhető el a Backup-tárolók, például:

- **Bővített képességet a biztonsági mentési adatainak védelme**: Recovery Services-tárolók, Azure Backup biztonsági képességeket védelme érdekében a felhőbeli biztonsági mentést biztosít. A biztonsági funkciók ellenőrizze, hogy a biztonsági mentések biztonságos, és biztonságosan az adatokat, helyreállítani, még akkor is, ha az éles és a biztonsági mentés integritása sérül. [További információ](backup-azure-security-feature.md)

- **A hibrid informatikai környezet központi figyelését**: Recovery Services-tárolók, figyelheti nem csupán a [Azure IaaS virtuális gépek](backup-azure-manage-vms.md) , hanem a [a helyszíni eszközök](backup-azure-manage-windows-server.md#manage-backup-items) egy központi portálról. [További információ](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Szerepköralapú hozzáférés-vezérlés (RBAC)**: RBAC biztosítja a hozzáférés részletes felügyeleti vezérlés az Azure-ban. [Az Azure különféle beépített szerepkört biztosít](../role-based-access-control/built-in-roles.md), és az Azure Backup három [beépített szerepkörök kezelése a helyreállítási pontok](backup-rbac-rs-vault.md). Recovery Services-tárolók kompatibilisek az RBAC, amely korlátozza a biztonsági mentés, és állítsa vissza a felhasználói szerepkörök meghatározott készletével való hozzáférést. [További információ](backup-rbac-rs-vault.md)

- **Az összes konfiguráció az Azure Virtual Machines védelme**: Recovery Services-tárolók Resource Manager-alapú virtuális gépet is, beleértve a felügyelt lemezek prémium szintű Managed Disks és titkosított virtuális gépek védelmét. Egy biztonsági mentési tároló frissítése Recovery Services-tároló lehetővé teszi, hogy a Service Manager-alapú virtuális gépek frissítése a Resource Manager-alapú virtuális gépeket. A tárolót a frissítés során a Service Manager-alapú virtuális gép helyreállítási pontjai, és a frissített (Resource Manager-kompatibilis) virtuális gépek védelme nem konfigurálható. [További információ](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **IaaS virtuális gépekhez azonnali helyreállítás**: használatával Recovery Services-tárolók, visszaállíthatja fájlokat és mappákat az IaaS virtuális gépek visszaállítása a teljes virtuális Gépet, amely lehetővé teszi a gyorsabb helyreállítás nélkül. Azonnali helyreállítás IaaS virtuális gépekhez Windows és Linux rendszerű virtuális gépeken érhető el. [További információ](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>A portálon a Recovery Services-tárolók kezelése
Létrehozása és kezelése az Azure Portalon a Recovery Services-tárolók oka egyszerűen az egyéb Azure-szolgáltatásokkal integrálható a Backup szolgáltatás. Ez az integráció azt jelenti, létrehozhat vagy kezelheti a Recovery Services-tároló *a célként megadott szolgáltatás kontextusában*. Ha például egy virtuális Gépet a helyreállítási pontok megtekintéséhez válassza ki a virtuális Gépet, majd kattintson **Backup** a Műveletek menü. 

![Recovery services tár adatait VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Ha a virtuális gép nem rendelkezik a biztonsági mentés konfigurálva, majd felszólítja, hogy a biztonsági mentés konfigurálása. Ha a biztonsági mentés konfigurálva van, látni fogja a biztonsági mentési információ a virtuális gép, beleértve a visszaállítási pontok listáját.  

![Recovery services tár adatait VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

Az előző példában **ContosoVM** a virtuális gép neve. **ContosoVM-demovault** a Recovery Services-tároló neve. Nem kell jegyezni a nevét, a Recovery Services-tároló, amely tárolja a helyreállítási pontok, ezeket az információkat a virtuális gép érheti el.  

Ha egy Recovery Services-tároló védi azokat több kiszolgálóra, és tekintse meg a Recovery Services-tároló több logikai lehet. Keresse meg az összes, az előfizetés Recovery Services-tárolók, és válassza ki azt a listából.

Az alábbi szakaszok azt ismertetik, hogyan használja a Recovery Services-tároló minden típusú tevékenység található hivatkozásokat.

### <a name="back-up-data"></a>Adatok biztonsági mentése
- [Készítsen biztonsági másolatot egy Azure virtuális Gépen](backup-azure-vms-first-look-arm.md)
- [Készítsen biztonsági másolatot a Windows Server vagy Windows-munkaállomás](backup-try-azure-backup-in-10-mins.md)
- [A DPM-munkaterhelések biztonsági mentéséhez az Azure-bA](backup-azure-dpm-introduction.md)
- [Használja az Azure Backup Server számítási feladatok biztonsági mentésének előkészítése](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Helyreállítási pontok kezelése
- [Azure virtuális gépek biztonsági másolatainak kezelése](backup-azure-manage-vms.md)
- [Fájlok és mappák kezelése](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Adatok visszaállítása a tárból
- [Az egyes fájlok helyreállítása Azure virtuális gép](backup-azure-restore-files-from-vm.md)
- [Egy Azure virtuális gép visszaállítása](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>A tároló biztonságossá tétele
- [A Recovery Services-tárolók felhőbeli biztonsági mentési adatok védelme](backup-azure-security-feature.md)



## <a name="next-steps"></a>További lépések
Használja a következő cikkeket:</br>
[Készítsen biztonsági másolatot egy IaaS-beli virtuális Gépen](backup-azure-arm-vms-prepare.md)</br>
[Készítsen biztonsági másolatot az Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[A Windows Server biztonsági mentése](backup-configure-vault.md)
