---
title: modern biztonsági másolati tárhely használata a Azure Backup Server
description: Ismerkedjen meg a Azure Backup Server új szolgáltatásaival. Ez a cikk a biztonsági mentési kiszolgáló telepítésének frissítését ismerteti.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 1a265579593c8eb39f0df4037f8ec39c788ddbce
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465127"
---
# <a name="add-storage-to-azure-backup-server"></a>Tárterület hozzáadása az Azure Backup Serverhez

A Azure Backup Server v2 és újabb verziók olyan modern biztonsági másolati tárhelyokat is támogatnak, amelyek 50%-os tárterület-megtakarítás, valamint háromszor gyorsabb biztonsági mentést és hatékonyabb tárolást biztosítanak. Emellett munkaterhelés-kompatibilis tárhelyet is biztosít.

> [!NOTE]
> A modern biztonsági másolati tárhely használatához a Backup Server v2 vagy v3 verzióját kell futtatnia a Windows Server 2016-es vagy v3-es verziójával a Windows Server 2019 rendszeren.
> Ha a Backup Server v2-t a Windows Server egy korábbi verziójára futtatja, Azure Backup Server nem tudja kihasználni modern biztonsági másolati tárhely. Ehelyett a biztonsági mentési kiszolgáló v1-es verziójának megfelelően védi a munkaterheléseket. További információt a biztonsági mentési kiszolgáló verziójának [védelmi mátrixa](backup-mabs-protection-matrix.md)című témakörben talál.

## <a name="volumes-in-backup-server"></a>Kötetek a biztonsági mentési kiszolgálón

A Backup Server v2 vagy újabb verziója fogadja a tárolási köteteket. Kötet hozzáadásakor a biztonsági mentési kiszolgáló a kötetet a rugalmas fájlrendszerre (ReFS) formázza, amelyhez modern biztonsági másolati tárhely szükséges. Ha egy kötetet szeretne hozzáadni, és később szeretné kibontani, javasoljuk, hogy használja ezt a munkafolyamatot:

1.  Állítsa be a biztonsági mentési kiszolgálót egy virtuális gépen.
2.  Kötet létrehozása egy virtuális lemezen egy tárolási készletben:
    1.  Adjon hozzá egy lemezt egy tárolóhoz, és hozzon létre egy egyszerű elrendezésű virtuális lemezt.
    2.  Vegyen fel további lemezeket, és terjessze ki a virtuális lemezt.
    3.  Hozzon létre köteteket a virtuális lemezen.
3.  Adja hozzá a köteteket a biztonsági mentési kiszolgálóhoz.
4.  A munkaterhelés-kompatibilis tároló konfigurálása.

## <a name="create-a-volume-for-modern-backup-storage"></a>Kötet létrehozása modern biztonsági másolati tárhelyhoz

A Backup Server v2 vagy újabb verziókkal, a lemezes tárolással rendelkező kötetek használatával kezelheti a tárhely feletti irányítást. Egy kötet lehet egyetlen lemez. Ha azonban később szeretné bővíteni a tárterületet, hozzon létre egy kötetet a tárolóhelyek használatával létrehozott lemezből. Ez segíthet, ha ki szeretné bővíteni a kötetet a biztonsági mentési tárolóban. Ez a szakasz ajánlott eljárásokat tartalmaz a kötet létrehozásához ezzel a telepítővel.

1. A Kiszolgálókezelőben válassza a **fájl-és tárolási szolgáltatások** > **kötetek** > **tárolási készletek**elemet. A **fizikai lemezek**területen válassza az **új tárolási készlet**lehetőséget.

    ![Új tárolási készlet létrehozása](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. A **feladatok** legördülő listában válassza az **új virtuális lemez**lehetőséget.

    ![Virtuális lemez hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Válassza ki a tárolási készletet, majd válassza a **fizikai lemez hozzáadása**lehetőséget.

    ![Fizikai lemez hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Válassza ki a fizikai lemezt, majd válassza a **virtuális lemez kiterjesztése**lehetőséget.

    ![A virtuális lemez kiterjesztése](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Válassza ki a virtuális lemezt, majd válassza az **új kötet**lehetőséget.

    ![Új kötet létrehozása](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. A **kiszolgáló és lemez kiválasztása** párbeszédpanelen válassza ki a kiszolgálót és az új lemezt. Ezután válassza a **Tovább** lehetőséget.

    ![A kiszolgáló és a lemez kiválasztása](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Kötetek hozzáadása a biztonsági mentési kiszolgáló lemezes tárolásához

Ha kötetet szeretne hozzáadni a biztonsági mentési kiszolgálóhoz, a **felügyelet** ablaktáblán Ellenőrizze újra a tárolót, majd válassza a **Hozzáadás**lehetőséget. Megjelenik a Backup Server-tárolóhoz hozzáadandó összes kötet listája. Miután hozzáadta a rendelkezésre álló köteteket a kiválasztott kötetek listájához, egy felhasználóbarát nevet adhat nekik, amely segít a kezelésben. Ha ezeket a köteteket ReFS szeretné formázni, hogy a biztonsági mentési kiszolgáló használhassa a modern biztonsági másolati tárhely előnyeit, kattintson az **OK gombra**.

![Elérhető kötetek hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Munkaterhelés-kompatibilis tároló beállítása

A munkaterhelés-kompatibilis tárolással kiválaszthatja azokat a köteteket, amelyek a bizonyos típusú számítási feladatok kedvezményes tárolási lehetőségeit tárolják. Beállíthat például olyan költséges köteteket, amelyek nagy mennyiségű bemeneti/kimeneti műveletet támogatnak másodpercenként (IOPS) a gyakori, nagy mennyiségű biztonsági mentést igénylő munkaterhelések tárolásához. Ilyen például a tranzakciónaplók SQL Server. A ritkábban biztonsági mentést végző egyéb munkaterhelések, például a virtuális gépek, biztonsági mentést készíthetnek az alacsony díjszabású kötetekre.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Az Update-DPMDiskStorage PowerShell-parancsmag használatával beállíthatja a munkaterhelés-kompatibilis tárolót, amely egy Azure Backup Server a tárolóban lévő kötet tulajdonságait frissíti. 

Szintaxis

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
A következő képernyőképen az Update-DPMDiskStorage parancsmag látható a PowerShell-ablakban.

![Az Update-DPMDiskStorage parancs a PowerShell-ablakban](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

A PowerShell használatával végrehajtott módosítások megjelennek a biztonsági mentési kiszolgáló felügyeleti konzolban.

![Lemezek és kötetek a felügyeleti konzol](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Örökölt tároló migrálása modern biztonsági másolati tárhelyre
Miután frissítette vagy telepítette a Backup Server v2-t, és az operációs rendszert a Windows Server 2016-re frissíti, frissítse a védelmi csoportokat modern biztonsági másolati tárhely használatára. Alapértelmezés szerint a védelmi csoportok nem változnak. Továbbra is ugyanúgy működnek, mint az eredetileg beállítottak.

A védelmi csoportok modern biztonsági másolati tárhely használatára való frissítése nem kötelező. A védelmi csoport frissítéséhez állítsa le az összes adatforrás védelmét az adat megőrzése lehetőség használatával. Ezután adja hozzá az adatforrásokat egy új védelmi csoporthoz.

1. A felügyeleti konzol válassza ki a **védelmi** funkciót. A **védelmi csoport tagja** listában kattintson a jobb gombbal a tagra, majd válassza a **tag védelmének leállítása**lehetőséget.

   ![Tag védelmének leállítása](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. A csoportból való **Eltávolítás** párbeszédpanelen tekintse át a felhasznált lemezterületet és a tárterület rendelkezésre álló szabad területét. Az alapértelmezett érték a helyreállítási pontok elhagyása a lemezen, és a hozzájuk tartozó adatmegőrzési házirend érvényességének engedélyezése. Kattintson az **OK** gombra.

   Ha azonnal vissza kívánja adni a felhasznált lemezterületet az ingyenes tárterületre, jelölje be a **replika törlése a lemezen** jelölőnégyzetet az ehhez a taghoz tartozó biztonsági mentési információk (és helyreállítási pontok) törléséhez.

   ![Eltávolítás a csoportból párbeszédpanel](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Hozzon létre egy modern biztonsági másolati tárhely-t használó védelmi csoportot. Adja meg a nem védett adatforrásokat.

## <a name="add-disks-to-increase-legacy-storage"></a>Lemezek hozzáadása a régi tárolók növeléséhez

Ha az örökölt tárolót a Backup Serverrel szeretné használni, lehetséges, hogy lemezeket kell hozzáadnia a régi tárterület növeléséhez.

Lemezes tár hozzáadása:

1. A felügyeleti konzol válassza a **felügyeleti** > **Disk Storage** > **Hozzáadás**elemet.

    ![Disk Storage hozzáadása párbeszédpanel](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. A **Disk Storage hozzáadása** párbeszédpanelen válassza a **lemezek hozzáadása**lehetőséget.

5. Az elérhető lemezek listájában jelölje ki a hozzáadni kívánt lemezeket, válassza a **Hozzáadás**lehetőséget, majd kattintson **az OK gombra**.

## <a name="next-steps"></a>További lépések
A Backup Server telepítése után megtudhatja, hogyan készítheti elő a kiszolgálót, vagy megkezdheti a munkaterhelések védelmét.

- [A biztonsági mentési kiszolgáló munkaterhelésének előkészítése](backup-azure-microsoft-azure-backup.md)
- [Egy VMware-kiszolgáló biztonsági mentése a Backup Server használatával](backup-azure-backup-server-vmware.md)
- [Biztonsági másolat készítése a Backup Server használatával SQL Server](backup-azure-sql-mabs.md)
