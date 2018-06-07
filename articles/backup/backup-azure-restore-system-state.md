---
title: 'Az Azure Backup: Windows Server rendszerállapot visszaállítása'
description: Lépés által lépés magyarázat a Windows Server rendszerállapot helyreállítása egy biztonsági másolatból, az Azure-ban.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: f8011fb3e7e1c5267f259a43f06d605690ffd281
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606154"
---
# <a name="restore-system-state-to-windows-server"></a>Rendszerállapot visszaállítása a Windows Server

Ez a cikk azt ismerteti, hogyan Windows kiszolgáló rendszerállapotának biztonsági másolatok visszaállítása az Azure Recovery Services-tároló. Rendszerállapot visszaállítása, rendelkeznie kell a rendszerállapot (utasításait használatával létrehozott [rendszerállapot biztonsági mentése](backup-azure-system-state.md#back-up-windows-server-system-state), és győződjön meg arról, hogy telepítette a [legújabb verzióját a Microsoft Azure Recovery Services (MARS) ügynök](http://aka.ms/azurebackup_agent). Végezze el az Azure Recovery Services-tároló a Windows Server rendszerállapot-adatok két lépésből áll:

1. Rendszerállapot visszaállítása az Azure Backup-fájlok formájában. Rendszerállapot visszaállítása az Azure Backup-fájlok formájában, lehetőségek közül választhat:
  * Visszaállítás rendszerállapot ugyanarra a kiszolgálóra ahol a biztonsági mentések vették, vagy
  * Rendszerállapot visszaállítása fájlt egy másik kiszolgálón.

2. A Windows Server a visszaállított rendszerállapot-fájlok érvényesek.


## <a name="recover-system-state-files-to-the-same-server"></a>Rendszerállapot helyreállítása fájlok ugyanarra a kiszolgálóra
A következő lépések bemutatják, hogyan szeretné visszaállítani a Windows Server-konfigurációs korábbi állapotába. A kiszolgáló konfigurációjának visszaállítása egy ismert, stabil állapotban, rendkívül fontos lehet. Az alábbi lépéseket a kiszolgáló rendszerállapotának visszaállíthatók a Recovery Services-tároló. 

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, ahová a beépülő modul telepítve van, a számítógép vagy a kiszolgáló keresése **a Microsoft Azure Backup szolgáltatás**.

    Egy asztali alkalmazás meg kell jelennie a keresési eredmények között.

2. Kattintson a **adatok helyreállítása** varázsló elindításához.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

3. Az a **bevezetés** ablaktáblán, az adatok helyreállítását a kiszolgálóhoz vagy a számítógépen, válassza ki **ehhez a kiszolgálóhoz (`<server name>`)** kattintson **következő**.

    ![Állítsa vissza az adatokat ugyanarra a számítógépre a kiszolgáló lehetőség kiválasztása](./media/backup-azure-restore-system-state/samemachine.png)

4. A a **válassza ki a helyreállítási mód** ablaktáblában válassza a **rendszerállapot** , majd **tovább**.

    ![Fájlok tallózása](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. A naptár **mennyiség kiválasztása és a dátum** ablaktáblán válassza ki a megfelelő helyreállítási pont. 

    Bármelyik helyreállítási pontra állíthatja vissza időben. A dátumok **félkövér** jelző legalább egy helyreállítási pont rendelkezésre állását. Miután egy dátumot, ha több helyreállítási pont nem érhető el, válassza ki az adott helyreállítási pontot a **idő** legördülő menü.

    ![Kötet és a dátum](./media/backup-azure-restore-system-state/select-date.png)

6. Miután kiválasztotta a visszaállítani kívánt helyreállítási pontot, kattintson a **következő**.

    Azure biztonsági mentés, a helyi helyreállítási pontot csatlakoztatja, és egy helyreállítási kötet használja.

7. A következő ablaktáblára, adja meg a helyreállított fájlok rendszerállapot célját, és kattintson a **Tallózás** nyissa meg a Windows Intézőt, és keresse meg a fájlokat és mappákat, amelyekről. A beállítás **példányt hoz létre, hogy mindkét változatához**, az egyes fájlok másolatokat készít a meglévő rendszerállapot fájl archiválhatja a másolat a teljes rendszerállapot-archívum létrehozása helyett.

    ![Helyreállítási beállítások](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Ellenőrizze, hogy a helyreállítási részleteit a **megerősítő** kattintson **helyreállítása**.

   ![Kattintson a helyreállítás igazolja a helyreállítás művelet](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Másolás a *WindowsImageBackup* könyvtárhoz, a kiszolgáló nem kritikus kötetre a helyreállítási helyen. A Windows operációs rendszer mennyiségi általában a kritikus kötet.

10. Ha a helyreállítás sikeres, kövesse a szakasz a [alkalmaz rendszerállapot fájlok visszaállítása a Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-files-to-the-windows-server), a rendszerállapot-helyreállítási folyamat befejezéséhez.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Rendszerállapot helyreállítása fájlok egy másodlagos kiszolgálóra

Ha a Windows Server sérült vagy nem érhető el, és visszaállítja azt egy stabil állapotot, ha a Windows Server rendszerállapot helyreállítása, egy másik kiszolgálóról is helyreállíthatja a sérült kiszolgáló rendszerállapotának. Az alábbi lépésekkel a restore rendszerállapot külön kiszolgálón.  

Ezeket a lépéseket a használt terminológiával alábbiakat tartalmazza:

- *Forrásgép* – a biztonsági mentés készült, és ez jelenleg nem érhető el az eredeti gép.
- *Célszámítógép* – a gép, amelyre az adatok helyreállítása.
- *Minta tároló* – a Recovery Services-tároló, amelyhez a *forrásgép* és *célgépen* van regisztrálva. <br/>

> [!NOTE]
> Egyik gépről készített biztonsági másolatokat az operációs rendszer korábbi verzióját futtató gép nem állítható vissza. Például a Windows Server 2016 gépről készített biztonsági másolatok a Windows Server 2012 R2 nem állítható vissza. Inverz azonban lehetőség. Windows Server 2012 R2 készített biztonsági másolat használatával állítsa vissza a Windows Server 2016.
>

1. Nyissa meg a **a Microsoft Azure Backup szolgáltatás** beépülő modult a *célgépen*.
2. Győződjön meg arról, hogy a *célgépen* és a *forrásgép* ugyanazt a Recovery Services-tároló van regisztrálva.
3. Kattintson a **adatok helyreállítása** a munkafolyamat elindítására.
4. Válassza ki **egy másik kiszolgáló**

    ![Egy másik kiszolgáló](./media/backup-azure-restore-system-state/anotherserver.png)

5. Adja meg a tároló hitelesítő adatait tartalmazó fájlt, amely megfelel a *minta tároló*. Ha a tárolói hitelesítő adatok fájlját érvénytelen (vagy lejárt), töltse le az új tárolói hitelesítő adatok fájlját az *minta tároló* az Azure portálon. A tárolói hitelesítő adatok fájlját valósul meg, miután a Recovery Services-tárolónak a tárolói hitelesítő adatok fájlját társított jelenik meg.

6. A biztonsági mentés kiszolgáló kiválasztása ablakban válassza ki a *forrásgép* megjelenített gépet a listából.
7. A helyreállítási mód kiválasztása ablakban válassza ki a **rendszerállapot** kattintson **következő**. 

    ![Keresés](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. A naptár a **mennyiség kiválasztása és a dátum** ablaktáblán válassza ki a megfelelő helyreállítási pont. Bármelyik helyreállítási pontra állíthatja vissza időben. A dátumok **félkövér** jelző legalább egy helyreállítási pont rendelkezésre állását. Miután egy dátumot, ha több helyreállítási pont nem érhető el, válassza ki az adott helyreállítási pontot a **idő** legördülő menü. 

    ![Keresési elemek](./media/backup-azure-restore-system-state/select-date.png)

9. Miután kiválasztotta a visszaállítani kívánt helyreállítási pontot, kattintson a **következő**.

10. A a **válasszon rendszer állapota helyreállítási módban** panelen adja meg a helyet, ahol rendszerállapot fájlok állíthatók helyre, majd kattintson a **következő**.

    ![Titkosítás](./media/backup-azure-restore-system-state/recover-as-files.png)

    A beállítás **példányt hoz létre, hogy mindkét változatához**, az egyes fájlok másolatokat készít a meglévő rendszerállapot fájl archiválhatja a másolat a teljes rendszerállapot-archívum létrehozása helyett.

11. Ellenőrizze a megerősítő panelen helyreállítási részletes adatait, majd kattintson **helyreállítása**. 

    ![a helyreállítás gombra kattintva erősítse meg a helyreállítási folyamat](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Másolás a *WindowsImageBackup* könyvtárból a kiszolgáló nem kritikus kötetet (például D:\). A Windows operációs rendszer mennyiségi általában a kritikus kötet.

13. A helyreállítási folyamat befejezéséhez használja a következő szakaszt [alkalmazni a visszaállított rendszerállapot-fájlok a Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>A visszaállított rendszerállapot alkalmazni a Windows Server

Egyszer helyreállított rendszerállapot-fájlként Azure Recovery Services Agent, a helyreállított rendszerállapot alkalmazása Windows Server a Windows Server biztonsági másolat segédprogram segítségével. A Windows Server biztonsági másolat segédprogram már elérhető a kiszolgálón. A következő lépések bemutatják, hogyan a helyreállított rendszerállapot alkalmazni.

1. A következő parancsok használatával indítsa újra a kiszolgálót a *a címtárszolgáltatás-javító módbeli*. A rendszergazda jogú parancssorból:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. A rendszer újraindítása után a Windows Server biztonsági másolat beépülő modul megnyitásához. Ha nem tudja, ahová a beépülő modul telepítve van, a számítógép vagy a kiszolgáló keresése **Windows Server biztonsági másolat**.

    Egy asztali alkalmazás megjelenik a keresési eredmények között.

3. A beépülő modulban, válassza ki a **helyi biztonsági másolat**.

    ![Válassza ki a helyi biztonsági másolat visszaállítása onnan](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. A helyi biztonsági másolat konzolon a a **műveletek panel**, kattintson a **helyreállítása** a helyreállítási varázsló megnyitásához.

5. A beállítást választja, **más helyen tárolt biztonsági másolat**, és kattintson a **következő**.

   ![egy másik kiszolgáló helyreállítása](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. A tárhely típusának megadása esetén válassza ki a **távoli megosztott mappa** Ha a rendszerállapot biztonsági mentése egy másik kiszolgálóra lett helyreállítva. Ha a rendszerállapot helyileg lett helyreállítva, akkor válasszon **helyi meghajtók**. 

    ![Válassza ki a helyi kiszolgáló vagy egy másik helyreállítás](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Az elérési útját adja meg a *WindowsImageBackup* könyvtár, vagy válassza ki a helyi meghajtót a könyvtárat (például D:\WindowsImageBackup), a rendszerállapot-fájlok az Azure Recovery Services helyreállítási helyre Ügynök, és kattintson **következő**.

    ![a megosztott fájl elérési útja](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Válassza ki a rendszerállapot-verziót, amelyet szeretne visszaállítani, és kattintson a **következő**.

9. A helyreállítási típus kiválasztása párbeszédpanelen válassza ki a **rendszerállapot** kattintson **következő**.

10. A rendszerállapot-helyreállítás helyének, válassza ki a **eredeti helyére**, és kattintson a **következő**.

11. Tekintse át a megerősítő részletes adatait, ellenőrizze a beállításokat a rendszer újraindítása, és kattintson **helyreállítása** applly a visszaállított rendszerállapot a fájlokat.

    ![Indítsa el a visszaállítást rendszerállapot-fájlok](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Különleges szempontok a rendszerállapot-helyreállítást az Active Directory-kiszolgálón

Rendszerállapot biztonsági mentésének Active Directory-adatokat. Tegye a következőket Active Directory tartományi szolgáltatások (AD DS) visszaállítása a jelenlegi állapotában a korábbi állapotába.

1. Indítsa újra a tartományvezérlőt a Címtárszolgáltatások helyreállító módban (DSRM).
2. Kövesse a lépéseket [Itt](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) helyreállítását az Active Directory tartományi szolgáltatások a Windows Server biztonsági másolat segítségével.


## <a name="troubleshoot-failed-system-state-restore"></a>Sikertelen rendszerállapot-visszaállítást hibaelhárítása

Ha az előző folyamat, amely során a rendszerállapot nem sikeres, a Windows helyreállítási környezet (Win RE) segítségével a Windows Server helyreállítása. A következő lépések bemutatják, hogyan helyreállítandó Win RE. Használja ezt a beállítást csak akkor, ha a Windows Server normál esetben ne indítsa a rendszerállapot-visszaállítást követően. A következő folyamat nem rendszerszintű adatot, legyen körültekintő töröl. 

1. A Windows Server indul el a Windows helyreállítási környezet (Windows RE).

2. Válassza ki a hibaelhárítás a három elérhető lehetőségek közül.

    ![a menü megnyitása](./media/backup-azure-restore-system-state/winre-1.png)

3. Az a **speciális beállítások** képernyőn válassza ki **parancssor** , és adja meg a kiszolgálón rendszergazdai jogosultságú felhasználónevet és jelszót.

   ![a menü megnyitása](./media/backup-azure-restore-system-state/winre-2.png)

4. Adja meg a kiszolgálón rendszergazdai jogosultságú felhasználónevet és jelszót.

    ![a menü megnyitása](./media/backup-azure-restore-system-state/winre-3.png)

5. Amikor a rendszergazda módban nyissa meg a parancssort, futtassa a következő parancs használatával beszerezheti a rendszerállapot biztonságimásolat-verziók.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Rendszerállapot biztonságimásolat-verziók beolvasása](./media/backup-azure-restore-system-state/winre-4.png)

6. A következő parancs használatával beszerezheti az összes kötet elérhető a biztonsági mentés futtatása.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Rendszerállapot biztonságimásolat-verziók beolvasása](./media/backup-azure-restore-system-state/winre-5.png)

7. A következő parancsot a rendszerállapot biztonsági mentését részét képező összes kötetet állítja helyre. Vegye figyelembe, hogy a ezt a lépést csak a kritikus kötetek, amelyek részei a rendszerállapot helyreállítása. Nem rendszermeghajtó minden adat törlődik.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Rendszerállapot biztonságimásolat-verziók beolvasása](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>További lépések
* Most, hogy a fájlok és mappák már helyreállítva, akkor [kezelheti a biztonsági másolatok](backup-azure-manage-windows-server.md).
