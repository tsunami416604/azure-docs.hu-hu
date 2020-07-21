---
title: A Recovery Services-tárolók áttekintése
description: Áttekintés és összehasonlítás Recovery Services-tárolók és Azure Backup-tárolók között.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 11a218badfab141c41430c3f48a5e930bfa1af8b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539040"
---
# <a name="recovery-services-vaults-overview"></a>A helyreállítási tárak áttekintése

Ez a cikk egy Recovery Services-tároló funkcióit ismerteti. A Recovery Services-tároló egy Azure-beli tárolási entitás, amely az adattárolást. Az adatok általában adatok másolatai, vagy a virtuális gépek (VM), a munkaterhelések, a kiszolgálók és a munkaállomások konfigurációs adatai. Recovery Services-tárolók használatával a különböző Azure-szolgáltatások, például a IaaS-alapú virtuális gépek (Linux vagy Windows) és az Azure SQL Database-adatbázisok biztonsági másolatait is megtarthatja. Recovery Services-tárolók támogatják a System Center DPM, a Windows Servert, a Azure Backup Server és egyebeket. A Recovery Services-tárolók leegyszerűsítik a biztonsági mentési adatok szervezését, miközben minimálisra csökkentik a munkaterhelést.

Egy Azure-előfizetésen belül akár 500 Recovery Services-tárolót is létrehozhat régiónként.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Recovery Services-tárolók és mentési tárolók összehasonlítása

Ha továbbra is rendelkezik biztonságimásolat-tárolókkal, azok automatikusan Recovery Services tárba frissülnek. November 2017-ig az összes Backup-tároló frissítve lett Recovery Services-tárolóra.

Recovery Services-tárolók az Azure Azure Resource Manager modelljén alapulnak, de a Backup-tárolók az Azure Service Manager modellen alapulnak. Amikor egy Recovery Services-tárolóra frissít egy biztonságimásolat-tárolót, a biztonsági mentési állapot a frissítési folyamat során és után sértetlen marad. Recovery Services-tárolók olyan funkciókat biztosítanak, amelyek nem érhetők el a Backup-tárolók számára, például:

- **Továbbfejlesztett funkciók a biztonsági mentési adatokat biztonságossá tételéhez**: a Recovery Services-tárolók révén a Azure Backup biztonsági funkciókat biztosít a Felhőbeli biztonsági másolatok védelméhez. A biztonsági funkciók biztosítják, hogy biztonságban legyenek a biztonsági másolatok, és biztonságosan helyreállítsák az adatokat, még akkor is, ha a termelési és a biztonsági mentési kiszolgálók biztonsága sérül. [További információ](backup-azure-security-feature.md)

- **A hibrid informatikai környezet központi monitorozása**: Recovery Services-tárolókkal nem csak az [Azure IaaS virtuális gépeket](backup-azure-manage-vms.md) , hanem a központi portálon található helyszíni [eszközöket](backup-azure-manage-windows-server.md#manage-backup-items) is figyelheti. [További információ](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Szerepköralapú Access Control (RBAC)**: a RBAC részletes hozzáférés-kezelési vezérlést biztosít az Azure-ban. Az [Azure számos beépített szerepkört kínál](../role-based-access-control/built-in-roles.md), és a Azure Backup három [beépített szerepkörrel rendelkezik a helyreállítási pontok kezeléséhez](backup-rbac-rs-vault.md). Recovery Services-tárolók kompatibilisek a RBAC-mel, amely korlátozza a biztonsági mentést, és visszaállítja a felhasználói szerepkörök meghatározott készletéhez való hozzáférést. [További információ](backup-rbac-rs-vault.md)

- **Az Azure Virtual Machines összes konfigurációjának védelme**: Recovery Services-tárolók védik a Resource Manager-alapú virtuális gépeket, beleértve a prémium szintű lemezeket, a Managed Diskseket és a titkosított virtuális gépeket Ha a Backup-tárolót egy Recovery Services-tárolóra frissíti, lehetősége van a Service Manager-alapú virtuális gépeket Resource Manager-alapú virtuális gépekre frissíteni. A tár frissítésekor megőrizheti Service Manager-alapú virtuálisgép-helyreállítási pontjait, és konfigurálhatja a frissített (Resource Manager-kompatibilis) virtuális gépek védelmét. [További információ](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **IaaS virtuális gépek azonnali visszaállítása**: Recovery Services-tárolók használatával visszaállíthatja a fájlokat és mappákat egy IaaS virtuális gépről a teljes virtuális gép visszaállítása nélkül, ami lehetővé teszi a gyorsabb visszaállítási időt. A IaaS virtuális gépek azonnali visszaállítása Windows-és Linux-alapú virtuális gépek esetén is elérhető. [További információ](backup-instant-restore-capability.md)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Tárolási beállítások a Recovery Services-tárolóban

A Recovery Services-tároló egy olyan entitás, amely az idő múlásával létrehozott biztonsági mentéseket és helyreállítási pontokat tárolja. A Recovery Services-tároló a védett virtuális gépekhez társított biztonsági mentési házirendeket is tartalmazza.

A Azure Backup automatikusan kezeli a tároló tárterületét. Megtudhatja, hogyan [módosíthatók a tárolási beállítások](./backup-create-rs-vault.md#set-storage-redundancy).

A tárterület-redundanciával kapcsolatos további tudnivalókért tekintse meg a [geo](../storage/common/storage-redundancy.md) és a [helyi](../storage/common/storage-redundancy.md) redundancia című cikket.

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Recovery Services-tárolók kezelése a portálon

Recovery Services-tárolók létrehozása és kezelése a Azure Portal egyszerű, mert a Backup szolgáltatás integrálható más Azure-szolgáltatásokkal. Ez az integráció azt jelenti, hogy egy Recovery Services *-tárolót a cél szolgáltatás kontextusában*hozhat létre vagy kezelhet. Ha például egy virtuális gép helyreállítási pontjait szeretné megtekinteni, válassza ki a virtuális gépet, majd kattintson a **biztonsági mentés** elemre az Operations (műveletek) menüben.

![Recovery Services tár részletei virtuális gép](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Ha a virtuális gép nem rendelkezik biztonsági mentéssel, akkor a rendszer felszólítja a biztonsági mentés konfigurálására. Ha a biztonsági mentés be van állítva, megjelenik a virtuális gép biztonsági mentési adatai, beleértve a visszaállítási pontok listáját is.  

![Recovery Services-tár részletei VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

Az előző példában a **ContosoVM** a virtuális gép neve. A **ContosoVM-demovault** a Recovery Services-tároló neve. Nem kell megjegyeznünk a helyreállítási pontokat tároló Recovery Services-tár nevét, ezt az információt elérheti a virtuális gépről.  

Ha egy Recovery Services-tár több kiszolgálót is véd, akkor a Recovery Services-tárolóban több logikai érték is megtekinthető. Az előfizetésben megkeresheti az összes Recovery Services-tárolót, és kiválaszthat egyet a listából.

A következő szakaszokban olyan cikkekre mutató hivatkozások találhatók, amelyek elmagyarázzák, hogyan használhat Recovery Services-tárolót az egyes típusú tevékenységekben.

> [!NOTE]
> Recovery Services tároló nem hozható létre ugyanazzal a névvel, ha 24 órán belül törölték. Használjon másik erőforrás-nevet, vagy válasszon másik erőforráscsoportot, vagy próbálkozzon újra 24 óra elteltével.

### <a name="back-up-data"></a>Adatok biztonsági mentése

- [Azure-beli virtuális gép biztonsági mentése](backup-azure-vms-first-look-arm.md)
- [Windows Server-vagy Windows-munkaállomás biztonsági mentése](./backup-windows-with-mars-agent.md)
- [DPM-munkaterhelések biztonsági mentése az Azure-ba](backup-azure-dpm-introduction.md)
- [Munkaterhelések biztonsági mentésének előkészítése Azure Backup Server használatával](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Helyreállítási pontok kezelése

- [Azure-beli virtuális gépek biztonsági mentésének kezelése](backup-azure-manage-vms.md)
- [Fájlok és mappák kezelése](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Adatok visszaállítása a tárból

- [Egyedi fájlok helyreállítása Azure-beli virtuális gépről](backup-azure-restore-files-from-vm.md)
- [Azure-beli virtuális gép visszaállítása](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>A tár védelme

- [A Felhőbeli biztonsági másolatok védelmének biztosítása Recovery Services-tárolókban](backup-azure-security-feature.md)

## <a name="azure-advisor"></a>Azure Advisor

A [Azure Advisor](../advisor/index.yml) egy személyre szabott felhőalapú tanácsadó, amely segít optimalizálni az Azure használatát. Elemzi az Azure-használatot, és időben ajánlásokat nyújt az üzemelő példányok optimalizálásához és biztonságossá tételéhez. Négy kategóriában nyújt javaslatokat: magas rendelkezésre állás, biztonság, teljesítmény és költséghatékonyság.

A Azure Advisor óránként [javaslatokat](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) nyújt a virtuális gépekről, amelyekről nem készül biztonsági mentés, így soha nem marad a fontos virtuális gépek biztonsági mentése. Az ajánlásokat úgy is szabályozhatja, hogy a késleltetéssel megadhatja őket.  Kattintson a javaslatra, és engedélyezze a biztonsági mentést a virtuális gépeken, a tároló (a biztonsági másolatok tárolási helye) és a biztonsági mentési szabályzat (biztonsági mentések ütemezése és biztonsági másolatok megőrzése) megadásával.

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Következő lépések

A következő cikkek használhatók:</br>
[IaaS virtuális gép biztonsági mentése](backup-azure-arm-vms-prepare.md)</br>
[Azure Backup Server biztonsági mentése](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server biztonsági mentése](backup-windows-with-mars-agent.md)
