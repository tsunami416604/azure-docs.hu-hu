---
title: "Telepítse az Azure biztonságimásolat-kiszolgáló v2 |} Microsoft Docs"
description: "Az Azure Backup Server v2 lehetővé teszi a virtuális gép, fájlok és mappák, munkaterhelések és több védelméhez továbbfejlesztett biztonsági mentési lehetőségeket. Megtudhatja, hogyan telepítse, vagy frissítsen az Azure Backup Server v2."
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
ms.openlocfilehash: 1bbb16afef7940933b4c3ae23873f212770137e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-azure-backup-server-v2"></a>Azure biztonságimásolat-kiszolgáló v2 telepítése

Az Azure Backup Server a virtuális gépek (VM) munkaterhelések, fájlok és mappák és több védi. Az Azure Backup Server v2 Azure Backup Server v1 épül, és lehetővé teszi az új szolgáltatások nem érhetők el az 1-es verzió. A szolgáltatások közötti v1 és v2, lásd: [Azure Backup Server védelmi mátrix](backup-mabs-protection-matrix.md). 

A biztonsági mentés kiszolgáló v2 további funkciók a biztonsági mentés kiszolgáló v1 frissítés. Biztonsági mentés kiszolgáló v1 viszont nem a biztonsági mentés kiszolgáló v2 telepítésének előfeltétele. Ha szeretné frissíteni a biztonsági mentés kiszolgáló v1 Backup Server v2, telepítse a biztonsági mentés kiszolgáló v2-kiszolgálón a biztonsági mentés védelmi. A meglévő biztonsági mentés beállításait változatlanok maradnak.

Biztonsági mentés kiszolgáló v2 telepíthető Windows Server 2012 R2 vagy Windows Server 2016. Új szolgáltatások, mint a System Center 2016 adatok Protection Manager Modern Backup-tárhelyre előnyeit, telepítenie kell biztonsági mentés kiszolgáló v2 Windows Server 2016. Előtt, frissítsen, vagy telepítse a biztonsági mentés kiszolgáló v2, olvassa el a [telepítésének előfeltételei](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Az Azure Backup Server alap, a System Center Data Protection Manager ugyanazt a kódot tartalmaz. Tartalék kiszolgáló v1 megegyezik a Data Protection Manager 2012 R2-re, és biztonsági mentés kiszolgáló v2 megegyezik a Data Protection Manager 2016-os. Ez a cikk alkalmanként a Data Protection Manager dokumentációs hivatkozik.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Tartalék kiszolgáló frissítsen v2
A biztonsági mentés kiszolgáló v1 frissítsen Backup Server v2, győződjön meg a telepítést a szükséges frissítéseket:

- [A védelmi ügynökök frissítésének](backup-mabs-upgrade-to-v2.md#update-the-dpm-protection-agent) a védett kiszolgálókon.
- Windows Server 2012 R2, Windows Server 2016 váltson.
- Azure biztonsági mentés kiszolgáló távoli felügyeleti frissítse az összes üzemi kiszolgálón.
- Győződjön meg arról, hogy a biztonsági mentés van beállítva az üzemi kiszolgáló újraindítása nélkül szeretné folytatni.


### <a name="upgrade-steps-for-backup-server-v2"></a>Biztonsági mentés kiszolgáló 2-es verzió frissítési lépések

1. A letöltőközpontban [töltse le a frissítési telepítő](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Miután a telepítő varázsló bontsa ki, győződjön meg arról, hogy **hajtható végre a setup.exe** kiválasztva, majd jelölje ki **Befejezés**.

  ![A telepítő installer - futtassa a telepítő](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. A Microsoft Azure Backup Server varázslóban a **telepítése**, jelölje be **Microsoft Azure Backup Server**.

  ![A telepítő installer - válassza telepítése](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Az a **üdvözlő** lapon olvassa el a figyelmeztetéseket, és válassza ki **következő**.

  ![A telepítő installer - kezdőlap](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. A telepítővarázsló előfeltétel-ellenőrzéseket győződjön meg arról, hogy a környezet frissíthető hajt végre. Az a **előfeltételek ellenőrzésének** lapon jelölje be **ellenőrizze**.

  ![A telepítő telepítő – Előfeltételek ellenőrzése lap](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. A környezet meg kell felelnie az előfeltétel-ellenőrzések. Ha a környezet nem feleltek meg a során, vegye figyelembe a problémákat, és kijavíthatja azokat. Ezt követően válassza **ellenőrizze újra**. Amikor az előfeltétel-ellenőrzést, válassza ki **következő**.

  ![A telepítő installer - ellenőrizze újra gomb](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Az a **SQL-beállítások** lapon, a megfelelő beállítást az SQL-telepítés, majd válassza ki és **ellenőrzés és telepítés**.

  ![A telepítő installer - SQL-beállítások lap](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  Az ellenőrzések néhány percig is eltarthat. Az ellenőrzések befejezésekor válassza **következő**.

  ![A telepítő installer - SQL ellenőrizze és telepítése gombra](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. Az a **telepítési beállítások** lapon, a helyre, ahol a biztonsági mentés Server telepítve van, vagy az ideiglenes helyre végezze el a módosításokat. Válassza ki **következő**.

  ![A telepítő installer - telepítési beállítások lapon](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. A telepítő varázsló befejezéséhez válassza ki a **Befejezés**.

  ![A telepítő telepítő - a befejezési](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Tároló hozzáadása a Modern Backup-tárhelyre

Biztonsági mentési tároló-hatékonyságot biztosít javítása érdekében tartalék kiszolgáló v2 támogatást nyújt a köteteket. Biztonsági mentés kiszolgáló v1, például a biztonsági mentés kiszolgáló v2 lemezeit támogatja.

### <a name="add-volumes-and-disks"></a>Kötetek és a lemezek hozzáadása
Ha Windows Server 2016 tartalék kiszolgáló v2 futtatja, használhatja a kötetek biztonsági mentési adatok tárolására. Kötet nyújtja a tárhely-megtakarítást és gyorsabb biztonsági mentéseket. Mivel kötetek új biztonsági másolat kiszolgálóhoz, hozzá kell adnia őket. 

A kötet biztonsági mentés kiszolgáló hozzáadásakor adhat egy rövid nevet a kötetet. Kattintson a **rövid név** nevet szeretne oszlopban. Később bármikor módosíthatja a nevet, ha szükséges. Is használhatja PowerShell hozzáadása vagy módosítása kötetek rövid nevét.

A kötet hozzáadása a felügyeleti konzol:

1. Válassza ki a Azure Backup Server felügyeleti konzol **felügyeleti** > **lemezegységet** > **Hozzáadás**.

    ![Nyissa meg a lemezes tárolás hozzáadása varázsló](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    Ekkor megnyílik a lemezes tárolás hozzáadása varázsló.

2. A a **adja hozzá a lemezes tárolás** lap a **rendelkezésre álló köteteken** mezőben, válassza ki a kötet, majd **Hozzáadás**.
3. Az a **kijelölt kötetek** mezőbe, adjon meg egy rövid nevet a kötet, és válassza ki **OK**.

      ![Adja hozzá a lemezes tárolás varázsló – a kötet hozzáadása](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Ha hozzá szeretne adni egy lemezt, a lemez egy örökölt tárolási rendelkező védelmi csoporthoz kell tartoznia. Ezek a lemezek csak a védelmi csoportok használható. Ha a biztonsági kiszolgáló nem kapcsolódik az adatforrásokat, amelyek az örökölt védelmet, a lemez nem szerepel a listában.

  Lemezek hozzáadásával kapcsolatos további információkért lásd: [örökölt tárolási növelje a lemezek hozzáadása a](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). A lemez nem adjon egy rövid nevet.


### <a name="assign-workloads-to-volumes"></a>Munkaterhelések hozzárendelése kötetek

A kiszolgáló biztonsági mentése megadhatja, mely munkaterhelések kötetek vannak rendelve. Például beállíthat költséges kötetek, amelyek támogatják a bemeneti/kimeneti műveletek száma másodpercenként (IOPS) túl magas száma csak azok a szolgáltatások, amelyek rendszeres, nagy mennyiségű biztonsági másolatok tárolásához. Példa: SQL Server, a tranzakciós naplók.

#### <a name="update-dpmdiskstorage"></a>Frissítés-DPMDiskStorage

Frissítés-DPMDiskStorage PowerShell-parancsmag segítségével egy kötetet a biztonsági mentés Server tárolókészletben tulajdonságainak frissítéséhez.

Szintaxis:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Az összes végrehajtott módosításokat a PowerShell használatával is megjelennek a felhasználói felület.


## <a name="protect-data-sources"></a>Adatforrások védelméhez
Az adatforrások védelmét, létre kell hoznia egy védelmi csoportot. Az alábbi lépéseket kiemelése módosításait és kiegészítéseit az új védelmi csoport létrehozása varázsló.

A védelmi csoport létrehozása:

1. Válassza ki a biztonsági mentés Server felügyeleti konzol **védelmi**.

2. Válassza ki az eszközök menüszalagján **új**.

    Ekkor megnyílik az új védelmi csoport létrehozása varázsló.

  ![Új védelmi csoport létrehozása varázsló](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Az a **üdvözlő** lapon jelölje be **következő**.
4. Az a **védelmi csoport típusának kiválasztása** lapon, válassza ki a védelmi csoport létrehozásához, és válassza ki a kívánt **következő**.

  ![Válassza ki a védelmi csoport típusa lap](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Az a **csoporttagok kiválasztása** lap a **választható tagok** ablaktáblán, a tagokat a védelmi ügynökök találhatók. Ehhez a példához válassza ki a kötetet a D:\ és E:\, és hozzáadhatja a **kijelölt tagok** ablaktáblán. Válassza ki **következő**.

  ![Csoport kijelölése tagok lap](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. A a **adatvédelmi módszer kiválasztása** lapján adjon meg egy **védelmi csoport neve**, a védelmi módszert, majd válassza ki és **következő**. Ha azt szeretné, hogy a rövid távú védelem, ki kell választania a **lemez** metódus biztonsági mentését.

  ![Adatvédelmi módszer kiválasztása lap](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Az a **rövid távú célok megadása** lapon, válassza ki a részletes **megőrzési időtartam** és **szinkronizálási gyakoriság**. Ezt követően válassza **következő**. Ha szükséges, amikor készít a helyreállítási pontok ütemezésének módosításához válassza **módosítás**.

  ![Adja meg a rövid távú célok lapja](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Az a **lemezterület-foglalás tekintse át** lapon tekintse át adatforrásokra vonatkozó beállítást jelölte ki, a mérete és részletes a kiépítendő terület és a célként megadott tárolási kötet értékeit.

  ![Áttekintés lemezterület-foglalás lap](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Tárolási köteteket a munkaterhelés kötet foglalási (beállítása a PowerShell használatával) és a rendelkezésre álló tár alapulnak. A tároló kötetek más kötetek kiválasztja a legördülő menüben módosíthatja. Ha megváltoztatja a **célként megadott**, értéke **szabad tárhely** dinamikusan megfelelően változik értékeket az **szabad terület** és **Underprovisioned terület**.

  Ha adatforrások nő, ahogy a tervezett, értékét a **Underprovisioned terület** oszlopa **szabad tárhely** tükrözi a további szükséges tárolókapacitást. Ez az érték a segítségével zökkenőmentes biztonsági másolatok tárolási igényeinek tervezése. Ha az érték nulla, nincsenek nem tárolási érintő lehetséges problémákat a közeljövőben. Ha az érték egy számot a nullától eltérő, nincs elegendő tárhely lefoglalt (a védelmi házirend és az adatok mérete a védett tagok alapján).

  ![Szabad kapacitással rendelkező erőforrásokkal lemezes tárolás](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   A védelmi csoport létrehozásának befejezéséhez fejezze be a varázslót.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Modern Backup-tárhelyre örökölt tárolójának áttelepítése
Vagy a biztonsági mentés kiszolgáló v2 telepítéséhez, és frissítse az operációs rendszert a Windows Server 2016 frissítése után frissítse a Modern biztonsági mentési tárolót használjanak a védelmi csoportok. Alapértelmezés szerint a védelmi csoportok nem változik. Ezek működésének fenntartása mellett azokat eredetileg állított be. 

Védelmi csoportok Modern Backup-tárhelyre használandó frissítése nem kötelező megadni. A védelmi csoport frissítésére, állítsa le az összes adatforrás védelmét az adatmegőrzési opció használatával. Ezt követően vegye fel az adatforrásokat egy új védelmi csoportba.

1. A felügyeleti konzolon válassza ki a **védelmi** szolgáltatás. Az a **védelmi csoport tagjához** listában kattintson a jobb gombbal a tagja, és válassza ki **tag védelmének kikapcsolása**.

  ![Tag védelmének kikapcsolása](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Az a **távolítsa el a csoportból** párbeszédpanelen tekintse át a felhasznált lemezterület és a rendelkezésre álló szabad területet a tárolókészlethez. Az alapértelmezett érték a helyreállítási pontok a lemezen, és lehetővé teszik a kapcsolódó adatmegőrzési / lejár. Kattintson az **OK** gombra.

  Ha azt szeretné, azonnal vissza foglalni a szabad lemezterület, válassza a **lemezen tárolt replika törlése** melletti jelölőnégyzetet, hogy törölje a biztonsági mentési adatok (és a helyreállítási pontok) társított tag.

  ![Távolítsa el a csoport párbeszédpanel](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Hozzon létre egy védelmi csoportot, amely Modern biztonsági mentési tárolót használ. A nem védett adatforrások tartalmazza.


## <a name="add-disks-to-increase-legacy-storage"></a>Vegyen fel lemezeket a hagyományos tárolási növelése

Ha azt szeretné, hogy örökölt tárhelyet használ a biztonsági mentés kiszolgáló, szükség lehet vegyen fel lemezeket a hagyományos tárolási növelése érdekében. 

Lemezes tárolás hozzáadása:

1. Válassza ki a felügyeleti konzol **felügyeleti** > **lemezegységet** > **Hozzáadás**.

    ![Adja hozzá a lemezes tárolás párbeszédpanel](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. Az a **adja hozzá a lemezes tárolás** párbeszédablakban válassza **vegyen fel lemezeket**.

5. A rendelkezésre álló lemezek, jelölje ki szeretné hozzáadni, válassza a lemezek **Hozzáadás**, majd válassza ki **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>A Data Protection Manager védelmi ügynök frissítése

Helykiszolgáló biztonsági mentése a System Center Data Protection Manager védelmi ügynök használja a frissítéseket. Ha frissíti a védelmi ügynök, amely nem kapcsolódik a hálózathoz, a Data Protection Manager rendszergazdai konzol nem használhat a csatlakoztatott ügynökök frissítési befejezéséhez. Frissítenie kell a védelmi ügynök egy nem aktív tartomány környezetében. Az ügyfélszámítógép csatlakozik a hálózathoz, amíg a Data Protection Manager rendszergazdai konzol jeleníti meg, hogy a védelmi ügynök frissítése függőben.

Az alábbi szakaszok azt ismertetik, hogyan frissíteni a védelmi ügynökök csatlakozó ügyfélszámítógépek és a nem csatlakozó ügyfélszámítógépekről.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Frissítse a védelmi ügynököt a csatlakoztatott ügyfélszámítógépen

1. Válassza ki a biztonsági mentés Server felügyeleti konzol **felügyeleti** > **ügynökök**.

2. A megjelenítési ablaktáblán jelölje ki azon ügyfélszámítógépeket, amelyekhez a védelmi ügynök frissítése.

  > [!NOTE]
  > A **Ügynökfrissítéssel** oszlop azt jelzi, ha a védelmi ügynök frissítése a védett számítógépekhez. Az a **műveletek** ablaktáblán, a **frissítés** művelet áll rendelkezésre, csak ha egy védett számítógép van kiválasztva, és frissítések érhetők el.
  >
  >

3. A kijelölt számítógépen, a frissített védelmi ügynök telepítése a **műveletek** ablaktáblán válassza előbb **frissítés**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Egy ügyfélszámítógépen, amely nem csatlakozik a védelmi ügynök frissítése

1. Válassza ki a biztonsági mentés Server felügyeleti konzol **felügyeleti** > **ügynökök**.

2. A megjelenítési ablaktáblán jelölje ki azon ügyfélszámítógépeket, amelyekhez a védelmi ügynök frissítése.

  > [!NOTE]
   > A **Ügynökfrissítéssel** oszlop azt jelzi, ha a védelmi ügynök frissítése a védett számítógépekhez. Az a **műveletek** ablaktáblán, a **frissítés** művelet nem érhető el, ha egy védett számítógép van kiválasztva, kivéve, ha frissítések érhetők el.
  >
  >

3. A frissített védelmi ügynök telepítéséhez a kijelölt számítógépen, válassza ki a **frissítés**.

4. Az ügyfélszámítógép, amely nem csatlakozik a hálózathoz, amíg a számítógép kapcsolódik a hálózathoz a **ügynök állapota** az oszlopban látható a állapotának **frissítés függőben**.

  Miután egy ügyfélszámítógép csatlakozik a hálózathoz, a **Ügynökfrissítéssel** az ügyfélszámítógép az oszlopban látható a állapotának **Frissítéskísérleti**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Örökölt védelmi csoportok áthelyezése régi verziója, és szinkronizálja az új verziót az Azure-ral

Amennyiben az Azure Backup Server és az operációs rendszer is frissítve lett, készen áll a Modern biztonsági mentési tárolót használó új adatforrások védelméhez. Azonban már védett adatforrások továbbra is az Azure Backup Server, de minden új védelmi voltak a hagyományos módon kell védeni fogja használni a Modern Backup-tárhelyre.

Következő lépések telepítsen át adatforrásokat védelmi örökölt üzemmódú Modern biztonságimásolat-tároláshoz.

• Adja hozzá az új kötetek a DPM-tárolókészletben, és rendelje hozzá a rövid nevek és adatok forrás, ha szükséges.
• Minden adatforrás, amely a hagyományos módban van, az adatforrások és a "Védett adatok megőrzése" állítsa le a védelmet.  Ez lehetővé teszi a helyreállítás a régi helyreállítási pontok áttelepítés után.

• Hozzon létre egy új PG, és válassza ki az adatforrásokat, amelyek a új formátum használatával kell tárolni.
• A DPM mindent replika másolatot a korábbi biztonsági mentési tárolóból be helyileg a Modern biztonsági másolatokat tároló kötetet.
Megjegyzés: Ez fogja látni, a helyreállítás után a művelet feladat • minden új szinkronizálási és helyreállítási pontok majd tárolódnak a Modern Backup-tárhelyre.
• A régi helyreállítási pontok lesz kiürítve kimenő lejár, és végül a lemezterület felszabadítása.
• A hagyományos köteteket a régi tárterületet, a lemez a törölt távolíthatók el az Azure biztonsági mentésre és a rendszer.
• A Azure a dpmdb biztonsági mentés készítése.

2. lépés:-fontos elemek > az új kiszolgáló neve lehet azonos az eredeti Azure Backup-kiszolgálóként kell. Az új Azure biztonsági mentési kiszolgáló neve nem módosítható, ha szeretné megőrizni a régi tárolókészlet és a DPMDB használni kívánt helyreállítási pontok - kell rendelkeznie a dpmdb biztonsági másolat, el kell állítani

1) Az eredeti Azure leállítása biztonsági mentése a kiszolgáló, vagy ki a vezeték-EK.
2) Alaphelyzetbe állítja a számítógépfiókot az active Directoryban.
3) Server 2016 telepítse az új számítógépen, és nevezze el a számítógép neve megegyezik az eredeti kiszolgáló Azure Backup szolgáltatásnál.
4) A tartományhoz
5) Telepítse az Azure Backup server V2 (helyezze át a DPM Tárolókészletének lemezeit a régi kiszolgáló és az importálás)
6) A 2. rész oldalától végrehajtott DPMDB visszaállítása
7) A tároló csatlakoztatása az eredeti biztonsági mentési kiszolgálóról az új kiszolgálóra.
8) A DPMDB SQL visszaállításból
9) A Microsoft Azure Backup szolgáltatás új server CD-n rendszergazdai parancssorból telepítése helyét és a bin mappa

Példa az útvonalra: C:\windows\system32 > cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
az Azure biztonsági mentés futtassa a DPMSYNC-SYNC

10) Futtassa a DPMSYNC-SYNC Megjegyzés felvett új lemezt a DPM tárolókészletéhez helyett a régiek áthelyezése futtassa a DPMSYNC - Reallocatereplica

## <a name="new-powershell-cmdlets-in-v2"></a>A v2 új PowerShell-parancsmagok

Ha telepíti az Azure Backup Server v2, a két új parancsmagok érhetők el: 
* [Csatlakoztatási-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan készíti elő a kiszolgálót, vagy a munkaterhelések védelmének megkezdéséhez:
- [A kiszolgálói biztonsági mentési feladatok előkészítése](backup-azure-microsoft-azure-backup.md)
- [Készítsen biztonsági másolatot a VMware server Backup Server használatával](backup-azure-backup-server-vmware.md)
- [Készítsen biztonsági másolatot az SQL Server biztonsági másolat-kiszolgáló használatával](backup-azure-sql-mabs.md)
- [Modern biztonsági mentési tárhelyet használ a helykiszolgáló biztonsági mentése](backup-mabs-add-storage.md)

