---
title: "Modern biztonsági mentési tárhelyet használ az Azure Backup Server v2 |} Microsoft Docs"
description: "További tudnivalók az Azure Backup Server v2 új funkciókkal. A cikkből megtudhatja, hogyan lehet frissíteni a biztonsági mentés Server telepítését."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-storage-to-azure-backup-server-v2"></a>Azure Backup Server v2 tároló hozzáadása

Az Azure Backup Server v2 tartalmaz a System Center 2016 adatok Protection Manager Modern Backup-tárhelyre. Modern Backup-tárhelyre 50 %-, a biztonsági mentések, amelyek háromszor gyorsabb és hatékonyabb tárolási a tárhely-megtakarítást nyújt. Munkaterhelés-kompatibilis tárterületet is biztosít. 

> [!NOTE]
> Modern biztonsági mentési tárolót használni, a Windows Server 2016 Backup Server v2 kell futtatnia. Ha a Windows Server korábbi verzióján futtatja a biztonsági mentés kiszolgáló v2, Azure Backup Server tudják kihasználni a Modern Backup-tárhelyre. Ehelyett azt védi a munkaterheléseket mint a biztonsági mentés kiszolgáló v1. További információkért lásd: a biztonsági mentés kiszolgálóverzió [védelmi mátrix](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server-v2"></a>A helykiszolgáló biztonsági mentése v2 kötetek

Tartalék kiszolgáló v2 tárolókötetek fogad el. Ha hozzáad egy köteten, a biztonsági mentés Server formázza a kötetet a refs fájlrendszer (ReFS), amelyek Modern Backup-tárhelyre van szükség. Kötet hozzáadása, és később kibontásához, ha szeretné, javasoljuk, hogy a munkafolyamat használja:

1.  A virtuális gép biztonsági mentése kiszolgáló v2 beállítása.
2.  Kötet létrehozása a virtuális lemez a tárolókészlethez:
    1.  Adjon hozzá egy lemezt a tárolókészletbe, és hozzon létre egy virtuális lemezt egyszerű elrendezés.
    2.  Vegyen fel minden további lemezeket, és a virtuális lemez.
    3.  A virtuális lemezen található köteteket hozhat létre.
3.  A kötetek hozzá a biztonsági mentés kiszolgálóhoz.
4.  Munkaterhelés-t támogató tároló konfigurálásához.

## <a name="create-a-volume-for-modern-backup-storage"></a>Kötet létrehozása a Modern Backup-tárhelyre

Biztonsági mentés kiszolgáló v2 használatával kötetekkel, mint a lemezes tárolás segíthetnek ellenőrzése alatt tartja a tároló karbantartása. A kötet lehet egyetlen lemezre. Ha azt szeretné, a későbbiekben kibővítheti a tárolási, azonban a tárolóhelyek használatával létrehozott lemezterülete kötet létrehozása. Ez segít, ha azt szeretné, bontsa ki a kötetet a biztonsági másolatok tárolására. Ez a rész nyújt gyakorlati tanácsok a kötet létrehozása ezzel a beállítással.

1. A Kiszolgálókezelő ablakában válassza **fájl- és tárolási szolgáltatások** > **kötetek** > **Tárolókészletek**. A **fizikai lemezek**, jelölje be **új tárolókészlet**. 

    ![Új tárolókészlet létrehozása](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Az a **feladatok** legördülő mezőben válassza **új virtuális lemez**.

    ![Virtuális lemez hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. A tárolókészlet, majd válassza ki és **fizikai lemez hozzáadása**.

    ![Fizikai lemez hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. A fizikai lemezt, majd válassza ki és **virtuális lemez bővítése**.

    ![A virtuális lemez](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. A virtuális lemezre, majd válassza ki és **új kötet**.

    ![Hozzon létre egy új kötetet](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Az a **válassza ki a kiszolgálót és a lemez** párbeszédpanelen válassza ki a kiszolgálót és az új lemez. Ezt követően válassza **következő**.

    ![Válassza ki a kiszolgáló és lemez](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>A biztonsági mentés lemezre tárhelyéhez kötetek hozzáadása

Kiszolgáló biztonsági mentése, a kötet hozzáadása a **felügyeleti** ablaktáblában ellenőrizze újra a tárolót, és válassza **Hozzáadás**. Az összes biztonsági mentés tárolására adható hozzá köteteket jelenik meg. Után rendelkezésre álló köteteken hozzáadódnak a kijelölt kötetek listáját, azokat egy rövid nevet adhat, hogyan kezelheti azokat. Ezeket a köteteket a refs fájlrendszer fájlformátumba biztonsági mentés kiszolgáló használatával a Modern Backup-tárhelyre előnyeit, jelölje be **OK**.

![Adja hozzá a rendelkezésre álló köteteken](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Munkaterhelés-kompatibilis tárolás beállítása

Munkaterhelés-kompatibilis tárolóval kiválaszthatja a bizonyos típusú munkaterhelések lehetőleg tárolására szolgáló köteteknél. Például beállíthat költséges kötetek, amelyek támogatják a bemeneti/kimeneti műveletek száma másodpercenként (IOPS) túl magas száma csak a szolgáltatások, amelyek rendszeres, nagy mennyiségű biztonsági másolatok tárolásához. Példa: SQL Server, a tranzakciós naplók. Egyéb munkaterhelések, amelyekről a ritkábban, például a virtuális gépek, alacsony költségű kötetek készíthető.

### <a name="update-dpmdiskstorage"></a>Frissítés-DPMDiskStorage

Munkaterhelés-t támogató tárolási állíthat be egy kötetet a tárolókészletből a Data Protection Manager-kiszolgáló tulajdonságait frissítve frissítés-DPMDiskStorage PowerShell-parancsmag használatával.

Szintaxis:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Az alábbi képernyőfelvételen látható a frissítés-DPMDiskStorage parancsmag a PowerShell-ablakban.

![A frissítés-DPMDiskStorage parancsot a PowerShell-ablakban](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

A PowerShell használatával a módosítások megjelennek a biztonsági mentés Server felügyeleti konzol.

![A lemezek és kötetek a felügyeleti konzol](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>Következő lépések
Biztonsági kiszolgáló telepítése után megtudhatja, hogyan készíti elő a kiszolgálót, vagy indítsa el a védelmet a munkaterhelés.

- [A kiszolgálói biztonsági mentési feladatok előkészítése](backup-azure-microsoft-azure-backup.md)
- [Készítsen biztonsági másolatot a VMware server Backup Server használatával](backup-azure-backup-server-vmware.md)
- [Készítsen biztonsági másolatot az SQL Server biztonsági másolat-kiszolgáló használatával](backup-azure-sql-mabs.md)

