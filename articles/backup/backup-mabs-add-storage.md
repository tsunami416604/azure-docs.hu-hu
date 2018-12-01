---
title: Modern Backup Storage használata az Azure Backup Server
description: Az Azure Backup Server új funkcióinak megismerése. Ez a cikk ismerteti a Backup Server-telepítés frissítése.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 74940c29c4c6c623da569c70a3c0f1ed7194e185
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727426"
---
# <a name="add-storage-to-azure-backup-server"></a>Tárterület hozzáadása az Azure Backup Serverhez

Az Azure Backup Server V2, és később a System Center 2016 Data Protection Manager Modern biztonsági mentési tár. Modern Backup Storage kínál bánhat 50 %-os, háromszor gyorsabb és hatékonyabb tárolási biztonsági mentéseket. Munkaterhelés-támogató tárhely is kínál.

> [!NOTE]
> Modern Backup Storage használatára, futtatnia kell Backup Server V2 és V3 a Windows Server 2016 vagy Windows Server verzióját 2019 V3.
> Ha Backup Server V2 Windows Server korábbi verzióján futtatja, az Azure Backup Server nem tudja kihasználni a Modern Backup Storage. Ehelyett azt védi a munkaterheléseket, mint a Backup Server V1. További információkért lásd: a biztonsági mentés kiszolgálóverzió [védelmi mátrix](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server"></a>Kötetek a biztonsági mentési kiszolgálóra

Biztonsági mentés kiszolgáló V2, vagy később a tárolási köteteket is elfogad. Amikor hozzáad egy köteten, a Backup Server formázza a kötetet, a Resilient File System (ReFS) esetén, amelyhez szükség van a Modern Backup Storage. Kötet hozzáadása, és később kibontásához, ha szeretné, javasoljuk, hogy használja a következő munkafolyamatot:

1.  Állítsa be a virtuális gép biztonsági mentési kiszolgálót.
2.  Hozzon létre egy virtuális lemezt a tárolókészletben kötetet:
    1.  Adjon hozzá egy lemezt a tárolókészletbe, és hozzon létre egy virtuális lemezt egyszerű elrendezéssel.
    2.  Adjon hozzá további lemezeket, és a virtuális lemezek kiterjesztése.
    3.  Hozzon létre köteteket a virtuális lemezen.
3.  Adja hozzá a kötetek biztonsági mentési kiszolgálóra.
4.  Munkaterhelés-támogató tárhely konfigurálása.

## <a name="create-a-volume-for-modern-backup-storage"></a>Kötet létrehozása a Modern Backup Storage

Használja a Backup Server V2 vagy újabb, a köteteket, a lemezes tárolás segítségével szabályozhatja a tárolási. A kötet lehet egyetlen lemez. Ha kívánja kiterjeszteni a jövőben a storage, azonban a tárolóhelyek használatával létrehozott lemezből kötetet létrehozni. Ez segíthet, ha azt szeretné, bontsa ki a biztonsági mentési tárterület a köteten. Ez a rész ezzel a beállítással a kötet létrehozásának ajánlott eljárásai nyújt.

1. A Kiszolgálókezelőben válasza **fájl- és tárolási szolgáltatások** > **kötetek** > **Tárolókészletek**. A **fizikai lemezek**válassza **új tárolókészlet**.

    ![Új tárolókészlet létrehozása](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Az a **feladatok** legördülő mezőben válassza ki **új virtuális lemez**.

    ![Egy virtuális lemez hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Válassza ki a tárolókészletet, és válassza **fizikai lemez hozzáadása**.

    ![Fizikai lemez hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. A fizikai lemezt, majd válassza ki és **virtuális lemez bővítése**.

    ![A virtuális lemezek kiterjesztése](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. A virtuális lemezre, majd válassza ki és **új kötet**.

    ![Hozzon létre egy új kötetet](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Az a **válassza ki a kiszolgálót és a lemez** párbeszédpanelen válassza ki a kiszolgálót és az új lemezt. Ezután válassza a **Tovább** lehetőséget.

    ![Válassza ki a kiszolgálót és a lemez](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Kötetek hozzáadása a Backup Server lemezes tárolás

A Backup Server, a kötet hozzáadása a **felügyeleti** ablaktáblán, a tároló ismételt vizsgálata, és válassza **Hozzáadás**. Megjelenik egy lista a biztonsági mentési tárolót hozzáadható összes kötetről. Miután az elérhető kötetek vesznek fel a kiválasztott kötetek listájához, átadásával, amelyek segítségével kezelheti őket egy rövid nevet. Ezek a kötetek refs formázásához, így Backup Server használatával is a Modern Backup Storage előnyeit, válassza ki a **OK**.

![Elérhető kötetek hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Állítsa be a munkaterhelés-támogató tárhellyel

Munkaterhelés-támogató tárhellyel kiválaszthatja a köteteket, amelyek részesítsenek bizonyos típusú számítási feladatokat. Például beállíthatja a költséges köteteket, amelyek támogatják a nagy számú bemeneti/kimeneti műveletek száma másodpercenként (IOPS) csak a számítási feladatokat igénylő gyakori, nagy mennyiségű biztonsági másolatok tárolásához. Ilyen például az SQL Server, a tranzakciós naplókhoz. Egyéb munkaterhelések, amelyekről ritkábban, például a virtuális gépek, az alacsony költségű köteteken készíthető.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Munkaterhelés-támogató tárhely beállítása a PowerShell-parancsmaggal Update-DPMDiskStorage parancsmag frissíti a a kötet tulajdonságait a Data Protection Manager-kiszolgálón a tárolókészletben.

Szintaxis:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Az alábbi képernyőfelvételen az Update-DPMDiskStorage parancsmag a PowerShell-ablakban.

![Az Update-DPMDiskStorage parancsot a PowerShell-ablakban](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

PowerShell-lel a módosításokat a Backup Server felügyeleti konzol is megjelennek.

![Lemezek és kötetek a felügyeleti konzol](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Régi típusú tárhely áttelepítése a Modern Backup Storage
Vagy telepítse a Backup Server V2, és frissítse az operációs rendszert Windows Server 2016-ra frissít, frissítse a védelmi csoportok, a Modern Backup Storage használatára. Alapértelmezés szerint a védelmi csoportok nem változnak. Ezek továbbra is működni, először voltak beállítva.

Védelmi csoportok Modern Backup Storage használatára való frissítése nem kötelező. A védelmi csoport frissítéséhez állítsa le az adatbázis megőrzése beállítás használatával összes adatforrás védelmét. Ezután vegye fel az adatforrásokat egy új védelmi csoportba.

1. A felügyeleti konzolon válassza a **védelmi** funkció. Az a **védelmi csoport tagjához** listában kattintson a jobb gombbal a tagja, és válassza ki **tag védelmének kikapcsolása**.

  ![Tag védelmének kikapcsolása](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Az a **eltávolítása a csoportból** párbeszédpanelen tekintse át a felhasznált lemezterület és a rendelkezésre álló szabad területet a tárolókészlethez. Az alapértelmezett érték a helyreállítási pontok meghagyása a lemezen, és lehetővé teszik számukra megőrzési / hamarosan lejár. Kattintson az **OK** gombra.

  Ha szeretné azonnal a felhasznált lemezterület térjen vissza a szabad tárolókészletbe, válassza ki a **lemezen tárolt replika törlése** taghoz tartozó melletti jelölőnégyzetet, hogy törölje a biztonsági mentési adatok (és a helyreállítási pontok).

  ![Távolítsa el a csoport párbeszédpanel](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Hozzon létre egy védelmi csoportot, amely a Modern Backup Storage használja. A nem védett adatforrásokat tartalmaznak.

## <a name="add-disks-to-increase-legacy-storage"></a>Adjon hozzá lemezeket a régi típusú tárhely bővítése

Ha azt szeretné, régi típusú tárhely használata a Backup Server, szüksége lehet adjon hozzá lemezeket a régi típusú tárhely bővítése érdekében.

Lemezterület növelése:

1. A felügyeleti konzolon válassza ki a **felügyeleti** > **Disk Storage** > **Hozzáadás**.

    ![Adja hozzá a lemezes tárolás párbeszédpanel](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. Az a **lemezterület hozzáadása** párbeszédablakban válassza **lemezekkel Bővíthetem a gépemet**.

5. Az elérhető lemezek listája, válassza ki a lemezeket, válassza ki a hozzáadni kívánt **Hozzáadás**, majd válassza ki **OK**.

## <a name="next-steps"></a>További lépések
Miután telepítette a Backup Server, megtudhatja, hogyan készíti elő a kiszolgálót, vagy a munkaterhelések védelmének megkezdése.

- [Backup Server számítási feladatainak előkészítése](backup-azure-microsoft-azure-backup.md)
- [VMware-kiszolgáló biztonsági mentése Backup Server használatával](backup-azure-backup-server-vmware.md)
- [SQL Server biztonsági mentése Backup Server használatával](backup-azure-sql-mabs.md)
