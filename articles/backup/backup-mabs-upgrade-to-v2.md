---
title: Telepítse az Azure Backup Server v2
description: Az Azure Backup Server v2 biztosít védelme érdekében a virtuális gép, fájlok és mappák, számítási feladatok és további fejlett biztonsági funkciókat. Ismerje meg, hogyan kell telepíteni vagy frissíteni az Azure Backup Server v2.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: adigan
ms.openlocfilehash: fdf69003566f704354a17335b1f46fc3077aedbc
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598390"
---
# <a name="install-azure-backup-server-v2"></a>Telepítse az Azure Backup Server v2

Az Azure Backup Server a virtuális gépek (VM), számítási feladatok, fájlok és mappák és több védi. Az Azure Backup Server v2 az Azure Backup Server v1 épül, és új funkciók, amelyek nem érhetők el a v1 biztosít. Szolgáltatások közötti v1 és v2 összehasonlításáért lásd: [Azure Backup Server védelmi mátrix](backup-mabs-protection-matrix.md). 

A Backup Server v2-ben a további funkciók a Backup Server 1-es frissítés. Backup-kiszolgáló v1 viszont nem v2 Backup Server telepítésének előfeltétele. Ha azt szeretné, frissítsen a Backup Server v1 Backup Server v2, a Backup Server v2 telepíthető a Backup Server védelmi kiszolgáló. A meglévő Backup-kiszolgáló beállításai változatlanok maradnak.

Backup Server v2 telepítheti a Windows Server 2012 R2 vagy Windows Server 2016-ban. Új funkciók, például a System Center 2016 Data Protection Manager Modern biztonsági mentési tár előnyeit, telepítenie kell Backup Server v2 Windows Server 2016-on. Mielőtt frissíteni vagy v2 Backup Server telepítése, olvassa el a [telepítésének előfeltételei](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Az Azure Backup Server rendelkezik alap, a System Center Data Protection Manager ugyanazt a kódot. Biztonsági mentési kiszolgáló v1 megegyezik a Data Protection Manager 2012 R2-re, és Backup Server v2 megegyezik a Data Protection Manager 2016-ra. Ez a cikk alkalmanként a Data Protection Manager dokumentációs hivatkozik.
>
>

## <a name="upgrade-backup-server-to-v2"></a>V2 Backup Server frissítése
Frissítse a Backup Server v1 a Backup Server v2, győződjön meg arról, hogy a telepítés rendelkezik a szükséges frissítéseket:

- [A védelmi ügynökök frissítése](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent) a védett kiszolgálókon.
- Windows Server 2012 R2 frissítése a Windows Server 2016-ban.
- Az Azure Backup Server Távfelügyelet frissítse az összes éles kiszolgálón.
- Győződjön meg arról, hogy biztonsági mentések beállítása továbbra is az üzemi kiszolgáló újraindítása nélkül.


### <a name="upgrade-steps-for-backup-server-v2"></a>A Backup Server v2-frissítési lépések

1. A letöltőközpontban [töltse le a frissítési](https://go.microsoft.com/fwlink/?LinkId=626082).

2. A telepítővarázsló kicsomagolása, után ellenőrizze, hogy **hajtsa végre a setup.exe** kiválasztott, és adja meg a **Befejezés**.

  ![A telepítő telepítő - futtassa a telepítőprogramot](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. A Microsoft Azure Backup Server varázsló alatt **telepítése**válassza **Microsoft Azure Backup Server**.

   ![A telepítő telepítő – válassza a telepítés](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Az a **üdvözlő** lapon tekintse át a figyelmeztetéseket, és válassza ki **tovább**.

   ![A telepítő telepítő – kezdőlap](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. A varázsló végez annak biztosításához, hogy a környezet verzióra frissítés előfeltétel-ellenőrzések. Az a **előfeltétel-ellenőrzési** lapon jelölje be **ellenőrizze**.

   ![A telepítő telepítő - előfeltétel-ellenőrzési lap](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. A környezet át kell adnia az előfeltétel-ellenőrzések. Ha a környezet nem adhatók át az ellenőrzések, vegye figyelembe a problémákat, és kijavíthatja azokat. Ezután válassza ki **újbóli ellenőrzés**. Után át kell adnia az előfeltétel-ellenőrzések, jelölje be az **tovább**.

  ![A telepítő telepítő - újbóli ellenőrzés gombra](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Az a **SQL-beállítások** lapon válassza ki a megfelelő beállítást az SQL telepítéséhez, és válassza ki **ellenőrzés és telepítés**.

   ![A telepítő telepítő - SQL-beállítások lap](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  Az ellenőrzések pár percet is igénybe vehet. Az ellenőrzés befejezésekor válassza **tovább**.

   ![A telepítő telepítő - SQL-beállítások ellenőrzése és telepítés gombra](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and-fix-settings.png)

8. Az a **telepítési beállítások** lapon, a hely, ahol a Backup Server telepítve van-e, vagy az ideiglenes helyre végezze el a módosításokat. Kattintson a **Tovább** gombra.

  ![A telepítő telepítő - telepítési beállítások lapon](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. A telepítő varázsló befejezéséhez válassza **Befejezés**.

  ![A telepítő telepítő – Befejezés](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>A Modern Backup Storage-tároló felvétele

A biztonsági mentési tár hatékonyság növelése érdekében, a biztonsági mentési kiszolgálóra v2 hozzáadja a kötetek támogatása. Például a Backup Server v1 a Backup Server v2 támogatja a lemezek használatát.

### <a name="add-volumes-and-disks"></a>Kötetek és lemezek hozzáadása
Ha a biztonsági mentési kiszolgálóra v2 Windows Server 2016 rendszeren futtatja, használhatja a kötetek biztonsági mentési adatok tárolására. Kötetek kínálnak bánhat és gyorsabb biztonsági mentést. Mivel a kötetek új biztonsági mentési kiszolgálóra, hozzá kell adnia őket. 

A kötet hozzáadásakor a Backup Server adhat egy rövid nevet a kötetet. Kattintson a **rövid név** oszlop a kötet nevét. A név később módosíthatja, ha szükséges. Is használhatja PowerShell hozzáadásához vagy kötetek valódi nevének módosításához.

Kötet hozzáadása a felügyeleti konzol:

1. Válassza ki az Azure Backup Server felügyeleti konzol **felügyeleti** > **Disk Storage** > **Hozzáadás**.

    ![Nyissa meg a lemezterület hozzáadása varázsló](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    Ekkor megnyílik a lemezterület hozzáadása varázsló.

2. Az a **lemezterület hozzáadása** lap a **elérhető kötetek** mezőbe, válasszon olyan kötetet, majd válassza ki **Hozzáadás**.
3. Az a **kijelölt kötetek** mezőbe, majd válassza ki és adjon egy rövid nevet a kötet **OK**.

      ![Hozzáadása varázsló lemezterület – kötet hozzáadása](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Ha azt szeretné, adjon hozzá egy lemezt, a lemez egy védelmi csoportot, amely rendelkezik a régi típusú tárhely kell tartoznia. Ezek a lemezek csak akkor használható, a védelmi csoportokban. Ha a Backup Server nem rendelkezik a régi típusú védelemmel rendelkező források, a lemez nem szerepel a listán.

  Lemezek hozzáadásával kapcsolatos további információkért lásd: [lemezek hozzáadása a régi típusú tárhely bővítése érdekében](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Nem adhat egy lemezt egy rövid nevet.


### <a name="assign-workloads-to-volumes"></a>Számítási feladatok rendelése a kötetekhez

A Backup Server megadhatja, mely számítási feladatok kötetek vannak rendelve. Például beállíthatja a költséges köteteket, amelyek támogatják a nagy számú bemeneti/kimeneti műveletek száma másodpercenként (IOPS) csak igénylő számítási feladatok gyakori, nagy mennyiségű biztonsági másolatok tárolásához. Ilyen például az SQL Server, a tranzakciós naplókhoz.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

A Backup Server a tárolókészletben található kötetek tulajdonságainak frissítéséhez használja az Update-DPMDiskStorage PowerShell-parancsmagot.

Szintaxis:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

A felhasználói felület módosításokat, hogy a PowerShell használatával is megjelennek.


## <a name="protect-data-sources"></a>Adatforrások védelméhez
Az adatforrások védelméhez hozzon létre egy védelmi csoportot. Az alábbi lépéseket a módosítása vagy bővítése az új védelmi csoport varázsló jelöljön ki.

Védelmi csoport létrehozása:

1. A Backup Server felügyeleti konzolon válassza ki a **védelmi**.

2. Az eszközök menüszalagján válassza **új**.

    Ekkor megnyílik az új védelmi csoport létrehozása varázsló.

  ![Új védelmi csoport létrehozása varázsló](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
4. Az a **védelmi csoport típusának kiválasztása** lapon, válassza ki a védelmi csoport létrehozása, és válassza ki a kívánt **tovább**.

  ![Válassza ki a védelmi csoport típusának lapon](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Az a **csoporttagok kiválasztása** lap a **választható tagok** panelen kattintson a jobb a tagokat a védelmi ügynökök szerepelnek. Ebben a példában válassza a D:\ kötetre és E:\, és adja hozzá őket a **kiválasztott tagok** ablaktáblán. Kattintson a **Tovább** gombra.

  ![Csoport kiválasztása a tagok lap](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Az a **adatvédelmi módszer kiválasztása** lap, adja meg egy **védelmi csoport neve**, válassza ki az adatvédelmi módszert, és válassza **tovább**. Ha azt szeretné, hogy rövid távú védelem, válassza a **lemez** biztonsági mentési módszer.

  ![Adatvédelmi módszer kiválasztása lap](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Az a **rövid távú célok megadása** lapra, jelölje be a részletei **megőrzési** és **szinkronizálási gyakoriság**. Ezután válassza a **Tovább** lehetőséget. Igény szerint, amikor készít a helyreállítási pontok ütemezésének módosításához válassza **módosítás**.

  ![Adja meg a rövid távú célok lapja](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Az a **lemezterület-foglalás tekintse át** lapon tekintse át részleteit az adatforrások választotta, mérete és a kiépítendő terület és a cél tárolókötet értékeit.

  ![Tekintse át a lemezterület-foglalás lap](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Tárolóköteteket a számítási feladatok kötet foglalási (beállítása a PowerShell használatával) és a rendelkezésre álló tár alapulnak. A tárolóköteteket Ehhez válasszon más köteteket a legördülő menüben módosíthatja. Ha módosítja az értéket **cél tárterület**, értéke **elérhető lemezterület** dinamikusan változik, hogy a értékekhez **szabad terület** és  **Hely underprovisioned**.

  Ha a adatforrások terveknek megfelelően növekedhessenek, az érték a **Underprovisioned terület** oszlopa **elérhető lemezterület** tükrözi a további tárhely szükséges. Ez az érték a segítségével tervezze meg a szükséges tárhelyet a biztonsági mentések zökkenőmentes lefutását. Ha az érték nulla, nincsenek nem kell problémákra a storage belátható. Ha az érték nem nulla számnak, nincs lefoglalva elegendő tárhely (a védelmi házirend és a védett tagok adatainak mérete alapján).

  ![Lemeztárhely](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   Az a védelmi csoport létrehozásának befejezéséhez, a varázsló befejezése.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Régi típusú tárhely áttelepítése a Modern Backup Storage
Vagy a v2 Backup Server telepítése, és frissítse az operációs rendszert Windows Server 2016-ra frissít, frissítse a védelmi csoportok, a Modern Backup Storage használatára. Alapértelmezés szerint a védelmi csoportok nem változnak. Ezek továbbra is működni, először voltak beállítva. 

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

## <a name="update-the-data-protection-manager-protection-agent"></a>A Data Protection Manager védelmi ügynök frissítése

Biztonsági mentési kiszolgálóra frissítések a System Center Data Protection Manager védelmi ügynököt használ. Ha frissíti a védelmi ügynök, amely nem csatlakozik a hálózathoz, a Data Protection Manager rendszergazdai konzol nem használhat egy csatlakoztatott ügynökök frissítési folyamat befejezéséhez. A védelmi ügynök egy nem aktív tartomány környezetében kell frissítenie. Mindaddig, amíg az ügyfélszámítógép csatlakozik a hálózathoz, a Data Protection Manager rendszergazdai konzol jeleníti meg, hogy a védelmi ügynök frissítése függőben.

A következő szakaszok ismertetik a csatlakozó ügyfélszámítógépek és az ügyfélszámítógépek, amelyek nem csatlakoznak a védelmi ügynökök frissítése.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Frissítse a védelmi ügynököt egy csatlakoztatott ügyfélszámítógépen

1. A Backup Server felügyeleti konzolon válassza ki a **felügyeleti** > **ügynökök**.

2. A kijelzőpanelen válassza ki az ügyfélszámítógépek, amelynek meg szeretné frissíteni a védelmi ügynök.

  > [!NOTE]
  > A **Ügynökfrissítések** oszlopban látható, ha a védelmi ügynök frissítése elérhető minden védett számítógépen. Az a **műveletek** panelen a **frissítés** művelet érhető el, csak ha egy védett számítógép van kiválasztva, és frissítések érhetők el.
  >
  >

3. A kiválasztott számítógépeken a frissített védelmi ügynök telepítése a **műveletek** ablaktáblán válassza előbb **frissítés**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Frissítse a védelmi ügynököt nem csatlakoztatott ügyfélszámítógépen

1. A Backup Server felügyeleti konzolon válassza ki a **felügyeleti** > **ügynökök**.

2. A kijelzőpanelen válassza ki az ügyfélszámítógépek, amelynek meg szeretné frissíteni a védelmi ügynök.

  > [!NOTE]
   > A **Ügynökfrissítések** oszlopban látható, ha a védelmi ügynök frissítése elérhető minden védett számítógépen. Az a **műveletek** panelen a **frissítés** művelet nem érhető el, ha egy védett számítógép van kiválasztva, kivéve, ha frissítések érhetők el.
  >
  >

3. A frissített védelmi ügynök telepítéséhez a kijelölt számítógépen, válassza ki a **frissítés**.

4. Az ügyfélszámítógép, amely nem csatlakozik a hálózathoz, amíg a számítógép csatlakozik a hálózathoz a **ügynök állapota** az oszlopban látható egy állapotát **frissítés függőben**.

  Miután egy ügyfélszámítógép csatlakozik a hálózathoz, a **Ügynökfrissítések** az ügyfélszámítógép az oszlopban látható egy állapotát **Updating**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Helyezze át a régi védelmi csoportok a régi verziót, és az Azure-ral az új verzió szinkronizálása

Miután az Azure Backup Server és az operációs rendszer is frissítve lett, készen áll a Modern Backup Storage használatával új adatforrások védelméhez. Azonban már védett adatforrások továbbra is a hagyományos módon védeni az Azure Backup Server, de minden új védelmet a Modern Backup Storage fogja használni.

Örökölt mód védelmet a Modern backup storage-adatforrások át az alábbi lépéseket.

• Az új (ek) ad hozzá a DPM-tárolókészletben, és felhasználóbarát nevek és adatok forrás címkék hozzárendeléséhez, ha szükséges.
• Az egyes adatforrások, amelyek az örökölt módban van, állítsa le az adatforrások és a "Védett adatok megőrzése" védelmét.  Ez lehetővé teszi a helyreállítási a régi helyreállítási pontok áttelepítés után.

• Hozzon létre egy új OLDALRA, és válassza ki azokat az adatforrásokat, az új formátum használatával tárolni kívánja.
• A DPM mindent replika másolatot a régi biztonsági mentési tárolóból, a Modern Backup Storage kötet helyileg.
Megjegyzés: Ez lesz látható, a helyreállítás utáni művelet feladat • a Modern Backup Storage majd minden új szinkronizálási és helyreállítási pontok lesz tárolva.
• A régi helyreállítási pontok törlődnek ki, hamarosan lejár, és végül szabadítson fel lemezterületet.
• A régi köteteket a régi tárhelyet, a lemez a törölt eltávolíthatók az Azure backup és a rendszer.
• Készítsen biztonsági mentést az Azure a dpmdb.

2. rész:-fontos elemek > az új kiszolgáló neve lehet ugyanaz, mint az eredeti Azure Backup server kell. Az új az Azure backup server neve nem módosítható, ha megőrzi a régi tárolókészlet és a DPMDB használni kívánt helyreállítási pontok – rendelkeznie kell a dpmdb biztonsági másolat, el kell állítani

1) Leállítása az eredeti Azure backup server, vagy ki az átviteli állapotúra.
2) Állítsa vissza a számítógépfiókot az active Directoryban.
3) Telepítse a Server 2016 új gépen, és nevezze el a gép neve megegyezik az eredeti Azure Backup server.
4) A tartományhoz
5) Telepítse az Azure Backup server V2 (helyezze át a DPM tárolókészlet-lemezeit a régi kiszolgáló és az importálás)
6) Állítsa vissza a dpmdb-ből származó 2. rész vége
7) A tároló csatlakoztatása az eredeti biztonsági mentési kiszolgálóról az új kiszolgálóra.
8) Az SQL állítsa vissza a dpmdb-t
9) A rendszergazdai parancssorból az új kiszolgáló cd, a Microsoft Azure Backup telepítése a hely és a bin mappa

Elérési út példa: C:\windows\system32 > cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
az Azure backup futtassa a DPMSYNC-SYNC

10) Futtassa a DPMSYNC-SYNC Megjegyzés felvett új lemezeket a DPM tárolókészlethez áthelyezése a régiek helyett majd futtassa a DPMSYNC - reallocatereplica parancsot

## <a name="new-powershell-cmdlets-in-v2"></a>Új PowerShell-parancsmagok a v2-ben

Az Azure Backup Server v2 telepítésekor két új parancsmagok érhetők el: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan készíti elő a kiszolgálót, vagy a munkaterhelések védelmének megkezdése:
- [Backup Server számítási feladatainak előkészítése](backup-azure-microsoft-azure-backup.md)
- [VMware-kiszolgáló biztonsági mentése Backup Server használatával](backup-azure-backup-server-vmware.md)
- [SQL Server biztonsági mentése Backup Server használatával](backup-azure-sql-mabs.md)
- [Modern Backup Storage használata biztonsági mentési kiszolgálóra](backup-mabs-add-storage.md)

