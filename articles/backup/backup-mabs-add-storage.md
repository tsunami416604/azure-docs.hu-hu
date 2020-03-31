---
title: Modern biztonsági mentési tároló használata az Azure Backup Server rel
description: Ismerje meg az Azure Backup Server új funkcióit. Ez a cikk a biztonsági másolat kiszolgáló telepítésének frissítését ismerteti.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: c6346d7b0275a00271c1787b378a63b8365edf2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172380"
---
# <a name="add-storage-to-azure-backup-server"></a>Tárterület hozzáadása az Azure Backup Serverhez

Az Azure Backup Server V2 és újabb verziói támogatják a Modern Backup Storage szolgáltatást, amely 50 százalékos tárhelymegtakarítást, háromszor gyorsabb biztonsági mentéseket és hatékonyabb tárolást kínál. Munkaterhelés-érzékeny tárolást is kínál.

> [!NOTE]
> A Modern Backup Storage használatához a Backup Server V2 vagy V3 rendszert Windows Server 2016 vagy V3 rendszeren kell futtatnia Windows Server 2019 rendszeren.
> Ha a Backup Server V2-t a Windows Server egy korábbi verzióján futtatja, az Azure Backup Server nem tudja kihasználni a Modern Backup Storage előnyeit. Ehelyett úgy védi a számítási feladatokat, mint a Backup Server V1 esetén. További információt a Biztonsági másolat kiszolgáló [verzióvédelmi mátrixában talál.](backup-mabs-protection-matrix.md)
>
> A jobb biztonsági mentési teljesítmény elérése érdekében javasoljuk, hogy a MABS v3-as csomagszintű tárolóval a Windows Server 2019 rendszeren telepítse. A rétegzett tárterület konfigurálásának lépéseit lásd a DPM "[Az MBS beállítása rétegzett tárral](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-2019#set-up-mbs-with-tiered-storage)" című cikkében.

## <a name="volumes-in-backup-server"></a>Kötetek a biztonsági másolat készítő kiszolgálón

A Backup Server V2 vagy újabb kiszolgáló elfogadja a tárolóköteteket. Kötet hozzáadásakor a Biztonsági másolat kiszolgáló a kötetet rugalmas fájlrendszerre (ReFS) formázza, amelyre a Modern Backup Storage-nak szüksége van. Ha kötetet szeretne hozzáadni, és szükség esetén később ki szeretné bővíteni, javasoljuk, hogy használja ezt a munkafolyamatot:

1. Állítsa be a biztonsági mentési kiszolgálót egy virtuális számítógépen.
2. Kötet létrehozása tárolókészletben lévő virtuális lemezen:
    1. Lemez hozzáadása a tárolókészlethez, és egyszerű elrendezésű virtuális lemez létrehozása.
    2. Adja hozzá a további lemezeket, és bővítse ki a virtuális lemezt.
    3. Kötetek létrehozása a virtuális lemezen.
3. Adja hozzá a köteteket a biztonsági másolat kiszolgálóhoz.
4. Számítási feladatokat tároló tároló konfigurálása.

## <a name="create-a-volume-for-modern-backup-storage"></a>Kötet létrehozása a modern biztonsági mentési tárolóhoz

Ha a Backup Server V2-t vagy újabb kiszolgálót kötetekkel lemeztárolóként használja, akkor a tárhely felett is szabályozhatja a rendszert. A kötet lehet egyetlen lemez is. Ha azonban a jövőben ki szeretné terjeszteni a tárhelyet, hozzon létre egy kötetet a tárolóhelyek használatával létrehozott lemezből. Ez segíthet, ha bővíteni szeretné a biztonsági mentési tároló kötetét. Ez a szakasz gyakorlati tanácsokat ad a kötetek létrehozásához ezzel a beállítással.

1. A Kiszolgálókezelőben válassza a **Fájl- és tárolási szolgáltatások** > **tárolókészleteit.****Volumes** >  A **FIZIKAI LEMEZEK csoportban**válassza az **Új tárolókészlet lehetőséget.**

    ![Új tárolókészlet létrehozása](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. A **FELADATOK** legördülő lista válassza az **Új virtuális lemez**lehetőséget.

    ![Virtuális lemez hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Jelölje ki a tárolókészletet, majd kattintson **a Fizikai lemez hozzáadása gombra.**

    ![Fizikai lemez hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Jelölje ki a fizikai lemezt, majd válassza **a Virtuális lemez kiterjesztése**lehetőséget .

    ![A virtuális lemez bővítése](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Jelölje ki a virtuális lemezt, majd válassza az **Új kötet**lehetőséget.

    ![Új kötet létrehozása](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. A **Kiszolgáló és lemez kiválasztása** párbeszédpanelen jelölje ki a kiszolgálót és az új lemezt. Ezután válassza a **Tovább gombot.**

    ![A kiszolgáló és a lemez kijelölése](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Kötetek hozzáadása a Biztonsági másolat kiszolgáló lemeztárolójához

> [!NOTE]
>
> - Csak egy lemezt adjon a készlethez, hogy az oszlopok száma 1 legyen. Ezután szükség szerint hozzáadhat lemezeket.
> - Ha egyszerre több lemezt ad hozzá a tárolókészlethez, a lemezek száma oszlopszámként lesz tárolva. Ha több lemezt ad hozzá, azok csak az oszlopok számának többszörösei lehetnek.

Ha kötetet szeretne hozzáadni a biztonsági másolat kiszolgálóhoz, a **Kezelés** ablaktáblán próbálja át a tárolót, és válassza a **Hozzáadás**lehetőséget. Megjelenik a Biztonságimásolat-kiszolgáló tárolóhoz hozzáadandó kötetek listája. Miután a rendelkezésre álló köteteket hozzáadta a kijelölt kötetek listájához, rövid nevet adhat nekik, hogy segítsen kezelni őket. Ha ezeket a köteteket reFS formátumba szeretné formázni, hogy a Biztonsági másolat kiszolgáló javunkra használhassa a Modern Backup Storage előnyeit, válassza az **OK gombot.**

![Elérhető kötetek hozzáadása](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Munkaterhelés-barát tárolás beállítása

A munkaterhelés-tudatos tárolással kiválaszthatja azokat a köteteket, amelyek előnyben részesítik bizonyos típusú számítási feladatok tárolását. Például beállíthat drága kötetek, amelyek támogatják a nagy számú bemeneti/kimeneti műveletek másodpercenkénti (IOPS) csak a gyakori, nagy mennyiségű biztonsági mentést igénylő számítási feladatok tárolására. Erre példa az SQL Server tranzakciós naplókkal. Más számítási feladatok, amelyek ritkábban biztonsági másolatot, például a virtuális gépek, alacsony költségű kötetek biztonsági másolatot lehet kapnia.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

A számítási feladatokat támogató tárolást az Update-DPMDiskStorage powerShell-parancsmag használatával állíthatja be, amely frissíti az Azure Backup Server tárolókészletében lévő kötet tulajdonságait.

Szintaxis:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Az alábbi képernyőképen az Update-DPMDiskStorage parancsmag látható a PowerShell ablakban.

![Az Update-DPMDiskStorage parancs a PowerShell ablakban](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

A PowerShell használatával végzett módosítások a Biztonsági másolat kiszolgáló felügyeleti konzolján jelennek meg.

![Lemezek és kötetek a felügyeleti konzolon](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Régi tárhely áttelepítése a modern biztonsági mentési tárolóba

Miután frissítette vagy telepítette a Backup Server V2 rendszert, és frissítette az operációs rendszert Windows Server 2016-ra, frissítse a védelmi csoportokat a Modern Backup Storage használatára. Alapértelmezés szerint a védelmi csoportok nem változnak. Továbbra is úgy működnek, ahogy eredetileg létrehozták őket.

A védelmi csoportokat nem kötelező átállítani a Modern Backup Storage használatára. A védelmi csoport frissítéséhez állítsa le az összes adatforrás védelmét az adatmegőrzési beállítás sal. Ezután adja hozzá az adatforrásokat egy új védelmi csoporthoz.

1. A Rendszergazda konzolon válassza a **Védelem** szolgáltatást. A **Védelmi csoport taglistájában** kattintson a jobb gombbal a tagra, majd válassza **a Tag védelmének leállítása parancsot.**

   ![A tagok védelmének leállítása](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Az Eltávolítás a **csoportból** párbeszédpanelen tekintse át a tárolókészlet számára rendelkezésre álló lemezterületet és szabad területet. Az alapértelmezés ilyenkor a helyreállítási pontok meghagyása a lemezen, hagyva, hogy lejárjanak a megőrzési szabályzatuk szerint. Kattintson az **OK** gombra.

   Ha azonnal vissza szeretné küldeni a felhasznált lemezterületet a szabad tárolókészletbe, jelölje be a **Kópia törlése a lemezen** jelölőnégyzetet az adott taghoz társított biztonsági mentési adatok (és helyreállítási pontok) törléséhez.

   ![Eltávolítás a csoportból párbeszédpanel](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Hozzon létre egy védelmi csoportot, amely a Modern Backup Storage-ot használja. A nem védett adatforrások felvétele.

## <a name="add-disks-to-increase-legacy-storage"></a>Lemezek hozzáadása az örökölt tárterület növeléséhez

Ha örökölt tárhelyet szeretne használni a Biztonsági másolat kiszolgálóval, előfordulhat, hogy lemezeket kell hozzáadnia az örökölt tárterület növeléséhez.

Lemezterület növelése:

1. A Rendszergazda konzolon válassza a **Felügyeleti** > **lemeztároló** > **hozzáadása lehetőséget.**

    ![Lemeztároló hozzáadása párbeszédpanel](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. A **Lemeztárolás hozzáadása** párbeszédpanelen válassza **a Lemezek hozzáadása**lehetőséget.

3. Az elérhető lemezek listájában jelölje ki a hozzáadni kívánt lemezeket, válassza a **Hozzáadás**lehetőséget, majd kattintson az **OK gombra.**

## <a name="next-steps"></a>További lépések

A Biztonsági másolat kiszolgáló telepítése után ismerje meg, hogyan készítheti elő a kiszolgálót, vagy hogyan védheti meg a munkaterhelést.

- [Biztonságimásolat-kiszolgáló munkaterhelésének előkészítése](backup-azure-microsoft-azure-backup.md)
- [VMware-kiszolgáló biztonsági mentése a Biztonsági másolat kiszolgálójával](backup-azure-backup-server-vmware.md)
- [Az SQL Server biztonsági mentése a Biztonsági másolat kiszolgálójával](backup-azure-sql-mabs.md)
