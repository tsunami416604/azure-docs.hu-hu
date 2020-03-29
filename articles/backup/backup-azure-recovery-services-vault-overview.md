---
title: A Recovery Services-tárolók áttekintése
description: A Recovery Services-tárolók és az Azure Backup-tárolók áttekintése és összehasonlítása.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 94a3e5a0865bcc8c0a9ecb866ca013f20a558e1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673238"
---
# <a name="recovery-services-vaults-overview"></a>A helyreállítási tárak áttekintése

Ez a cikk a Recovery Services-tároló szolgáltatásait ismerteti. A Recovery Services-tároló egy tárolási entitás az Azure-ban, amely adatokat tárol. Az adatok általában a virtuális gépek, a munkaterhelések, a kiszolgálók vagy a munkaállomások adatainak vagy konfigurációs adatainak másolatai. A Recovery Services-tárolók segítségével biztonsági mentési adatokat tarthat le különböző Azure-szolgáltatásokhoz, például az IaaS virtuális gépekhez (Linux vagy Windows) és az Azure SQL-adatbázisokhoz. A Helyreállítási szolgáltatások tárolói támogatják a System Center DPM, a Windows Server, az Azure Backup Server és egyebek. A Recovery Services-tárolók leegyszerűsítik a biztonsági mentési adatok szervezését, miközben minimálisra csökkentik a munkaterhelést.

Egy Azure-előfizetésen belül régiónként előfizetésenként legfeljebb 500 helyreállítási szolgáltatás-tárolót hozhat létre.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>A Helyreállítási szolgáltatások tárolóinak és biztonsági másolat-tárolóinak összehasonlítása

Ha továbbra is rendelkezik biztonsági mentési tárolókkal, azok automatikus frissítés alatt állnak a Recovery Services-tárolókra. 2017 novemberére az összes biztonsági mentési tároló továbbfejlesztett lett a Helyreállítási szolgáltatások tárolóira.

A Recovery Services-tárolók az Azure Azure Resource Manager-modelljén alapulnak, míg a biztonsági mentési tárolók az Azure Service Manager-modellen alapultak. Amikor egy biztonsági mentési tárolót frissít egy Recovery Services-tárolóba, a biztonsági mentési adatok érintetlenek maradnak a frissítési folyamat során és után. A Helyreállítási szolgáltatások tárolói olyan funkciókat biztosítanak, amelyek nem érhetők el a biztonsági mentési tárolók számára, például:

- **Továbbfejlesztett funkciók a biztonsági mentési adatok védelméhez:** A Recovery Services-tárolókkal az Azure Backup biztonsági funkciókat biztosít a felhőbeli biztonsági mentések védelmére. A biztonsági funkciók biztosítják a biztonsági mentések biztonságossá tétele és az adatok biztonságos helyreállítása, még akkor is, ha az éles és biztonsági mentési kiszolgálók biztonsága sérül. [További információ](backup-azure-security-feature.md)

- **Központi figyelés a hibrid informatikai környezet:** A Recovery Services-tárolók, nem csak az [Azure IaaS virtuális gépek,](backup-azure-manage-vms.md) hanem a [helyszíni eszközök](backup-azure-manage-windows-server.md#manage-backup-items) egy központi portálról. [További információ](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Szerepköralapú hozzáférés-vezérlés (RBAC)**: Az RBAC részletes hozzáférés-vezérlést biztosít az Azure-ban. [Az Azure különböző beépített szerepköröket biztosít,](../role-based-access-control/built-in-roles.md)és az Azure Backup három beépített szerepkörrel rendelkezik [a helyreállítási pontok kezeléséhez.](backup-rbac-rs-vault.md) A Recovery Services-tárolók kompatibilisek az RBAC-kal, amely korlátozza a biztonsági mentést és a felhasználói szerepkörök meghatározott készletéhez való hozzáférést. [További információ](backup-rbac-rs-vault.md)

- **Az Azure virtuális gépek összes konfigurációjának védelme:** A helyreállítási szolgáltatások tárolói védik az Erőforrás-kezelő alapú virtuális gépeket, beleértve a prémium lemezeket, a felügyelt lemezeket és a titkosított virtuális gépeket. A Biztonsági másolat tárolójának a Recovery Services-tárolóra való frissítése lehetővé teszi a Service Manager-alapú virtuális gépek erőforrás-kezelőalapú virtuális gépekre való frissítését. A tároló frissítése közben megtarthatja a Service Manager-alapú virtuálisgép helyreállítási pontokat, és konfigurálhatja a frissített (Erőforrás-kezelő által engedélyezett) virtuális gépek védelmét. [További információ](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **IaaS virtuális gépek azonnali visszaállítása**: A Recovery Services-tárolók használatával visszaállíthatja a fájlokat és mappákat egy IaaS virtuális gépről a teljes virtuális gép visszaállítása nélkül, ami gyorsabb visszaállítást tesz lehetővé. Az IaaS virtuális gépek azonnali visszaállítása Windows és Linux virtuális gépeken is elérhető. [További információ](backup-instant-restore-capability.md)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>A Recovery Services-tárolók kezelése a portálon

A Recovery Services-tárolók létrehozása és kezelése az Azure Portalon egyszerű, mivel a biztonsági mentési szolgáltatás más Azure-szolgáltatásokba integrálódik. Ez az integráció azt jelenti, hogy a *célszolgáltatás környezetében*létrehozhat vagy kezelhet egy Recovery Services-tárolót. Például egy virtuális gép helyreállítási pontjainak megtekintéséhez jelölje ki a virtuális gép, és kattintson a **Biztonsági mentés** parancsra a Műveletek menüben.

![Helyreállítási szolgáltatások tároló részletek VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Ha a virtuális gép nem rendelkezik biztonsági mentés konfigurálva, majd kérni fogja a biztonsági mentés konfigurálását. Ha a biztonsági mentés konfigurálva van, a virtuális gép biztonsági mentési információi jelennek meg, beleértve a visszaállítási pontok listáját is.  

![Helyreállítási szolgáltatások tároló részletek VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

Az előző példában **a ContosoVM** a virtuális gép neve. **ContosoVM-demovault** a recovery services-tároló neve. Nem kell megjegyeznie a helyreállítási pontokat tároló Helyreállítási szolgáltatások tárolójának nevét, és hozzáférhet ezekhez az információkhoz a virtuális gépről.  

Ha egy Helyreállítási szolgáltatások tárolója több kiszolgálót véd, logikusabb lehet a Recovery Services-tárolóban. Megkeresheti az előfizetés összes Helyreállítási szolgáltatás-tárolóját, és választhat egyet a listából.

A következő szakaszok olyan cikkekre mutató hivatkozásokat tartalmaznak, amelyek ismertetik a Recovery Services-tároló használatát az egyes tevékenységtípusokban.

> [!NOTE]
> A Recovery Services-tároló nem hozható létre ugyanazzal a névvel, ha 24 órán belül törölték. Használjon másik erőforrásnevet, vagy válasszon másik erőforráscsoportot, vagy próbálkozzon újra 24 óra elteltével.

### <a name="back-up-data"></a>Adatok biztonsági és biztonsági és biztonsági másolatot

- [Azure-beli virtuális gép biztonsági és biztonsági és biztonsági őbéhez](backup-azure-vms-first-look-arm.md)
- [Biztonsági másolatot a Windows Server vagy a Windows munkaállomásról](backup-try-azure-backup-in-10-mins.md)
- [A DPM-munkaterhelések biztonsági és biztonsági és biztonsági és az Azure-ba való leépítése](backup-azure-dpm-introduction.md)
- [Felkészülés a számítási feladatok biztonsági mentésére az Azure Backup Server használatával](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Helyreállítási pontok kezelése

- [Azure-beli virtuális gépek biztonsági mentésének kezelése](backup-azure-manage-vms.md)
- [Fájlok és mappák kezelése](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Adatok visszaállítása a tárolóból

- [Egyéni fájlok helyreállítása Azure-beli virtuális gépről](backup-azure-restore-files-from-vm.md)
- [Azure-beli virtuális gép visszaállítása](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>A páncélterem biztosítása

- [Felhőalapú biztonsági mentési adatok védelme a Recovery Services-tárolókban](backup-azure-security-feature.md)

## <a name="next-steps"></a>További lépések

A következő cikkeket használja:</br>
[IaaS virtuális gép biztonsági és biztonsági másolatot kell kapnia](backup-azure-arm-vms-prepare.md)</br>
[Azure biztonsági mentési kiszolgáló biztonsági mentése](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server biztonsági másolatot](backup-windows-with-mars-agent.md)
