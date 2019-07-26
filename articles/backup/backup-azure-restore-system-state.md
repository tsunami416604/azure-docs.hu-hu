---
title: 'Azure Backup: Rendszerállapot visszaállítása Windows Serverre'
description: Részletes útmutató a Windows Server rendszerállapotának az Azure-beli biztonsági másolatból történő visszaállításához.
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: 48eb9fce7b9863c84518546270a6566bb0cec711
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465655"
---
# <a name="restore-system-state-to-windows-server"></a>Rendszerállapot visszaállítása a Windows Server rendszerre

Ez a cikk azt ismerteti, hogyan lehet visszaállítani a Windows Server rendszerállapotának biztonsági másolatait egy Azure Recovery Services-tárolóból. A rendszerállapot visszaállításához a rendszerállapot biztonsági mentésére van szükség (amelyet a rendszerállapot biztonsági mentése című részben [talál, és](backup-azure-system-state.md#back-up-windows-server-system-state)ellenőrizze, hogy telepítve van- [e a Microsoft Azure Recovery Services (MARS) ügynök legújabb verziója](https://aka.ms/azurebackup_agent). A Windows Server rendszerállapot-adatainak egy Azure Recovery Services-tárolóból történő helyreállítása kétlépéses folyamat:

1. A rendszer állapotának visszaállítása Azure Backup fájlból. Ha a rendszerállapotot fájlok Azure Backupból való visszaállításakor, a következőkre van lehetősége:
   * Állítsa vissza a rendszerállapotot ugyanarra a kiszolgálóra, ahol a biztonsági másolatok készültek, vagy
   * Rendszerállapot-fájl visszaállítása egy másik kiszolgálóra.

2. Alkalmazza a visszaállított rendszerállapot-fájlokat egy Windows Serverre.


## <a name="recover-system-state-files-to-the-same-server"></a>Rendszerállapot-fájlok helyreállítása ugyanarra a kiszolgálóra
A következő lépések bemutatják, hogyan állíthatja vissza a Windows Server konfigurációját egy korábbi állapotba. A kiszolgáló konfigurációjának egy ismert, stabil állapotba történő visszaállítása rendkívül értékes lehet. A következő lépések a kiszolgáló rendszerállapotának visszaállítását Recovery Services-tárolóból.

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, hol telepítette a beépülő modult, keresse meg **Microsoft Azure Backup**a számítógépet vagy a kiszolgálót.

    Az asztali alkalmazásnak meg kell jelennie a keresési eredmények között.

2. A varázsló elindításához kattintson az **adatok helyreállítása** elemre.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

3. Az **első lépések** ablaktáblán, ha az adott kiszolgálóra vagy számítógépre szeretné visszaállítani az adatvisszaállítást, válassza ki **ezt a kiszolgálót`<server name>`()** , majd kattintson a **tovább**gombra.

    ![Válassza ezt a lehetőséget, ha az adott gépre szeretné visszaállítani az adatvisszaállítást](./media/backup-azure-restore-system-state/samemachine.png)

4. A **helyreállítási mód kiválasztása** panelen válassza a rendszerállapot lehetőséget, majd kattintson a  **tovább**gombra.

    ![Fájlok tallózása](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. A **kötet és dátum kiválasztása** ablaktáblán válassza ki a kívánt helyreállítási pontot a naptárban.

    Bármely helyreállítási pontról visszaállítható. A **félkövérrel szedett** dátumok azt jelzik, hogy legalább egy helyreállítási pont rendelkezésre áll. Ha kiválasztott egy dátumot, ha több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az **idő** legördülő menüből.

    ![Kötet és dátum](./media/backup-azure-restore-system-state/select-date.png)

6. Miután kiválasztotta a visszaállítani kívánt helyreállítási pontot, kattintson a **tovább**gombra.

    Azure Backup csatlakoztatja a helyi helyreállítási pontot, és helyreállítási kötetként használja.

7. A következő ablaktáblán adja meg a helyreállított rendszerállapot-fájlok célhelyét, és a **Tallózás** gombra kattintva nyissa meg a Windows Intézőt, és keresse meg a kívánt fájlokat és mappákat. A példányok **létrehozása, hogy mindkét verzióval rendelkezzen**, másolatot készít az egyes fájlokról egy meglévő rendszerállapot-archívumban a teljes rendszerállapot-Archívum másolatának létrehozása helyett.

    ![Helyreállítási beállítások](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Ellenőrizze a helyreállítás részleteit a megerősítő ablaktáblán, és kattintson a **helyreállítás**gombra.

   ![kattintson a helyreállítás gombra a helyreállítási művelet visszaigazolásához](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Másolja a helyreállítási célhelyen található *WindowsImageBackup* könyvtárat a kiszolgáló nem kritikus kötetére. A Windows operációsrendszer-kötet általában a kritikus kötet.

10. A helyreállítás sikeres végrehajtása után kövesse a rendszerállapot-helyreállítási folyamat befejezéséhez szükséges, a visszaállított rendszerállapot- [fájlok alkalmazása a Windows Serverre](backup-azure-restore-system-state.md)című szakasz lépéseit.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Rendszerállapot-fájlok helyreállítása egy másik kiszolgálóra

Ha a Windows Server sérült vagy nem érhető el, és a Windows Server rendszerállapotának helyreállításával szeretné visszaállítani a stabil állapotot, visszaállíthatja a sérült kiszolgáló rendszerállapotát egy másik kiszolgálóról. A következő lépések végrehajtásával állítsa vissza a rendszerállapotot egy különálló kiszolgálón.  

A következő lépésekben használt terminológiák a következők:

- *Forrásoldali gép* – az eredeti gép, amelyről a biztonsági mentés készült, és amely jelenleg nem érhető el.
- *Célszámítógép* – az a gép, amelyre az Adathelyreállítás történik.
- *Minta* -tár – az a Recovery Services tároló, amelybe a *forrásszámítógép* és a *célszámítógép* regisztrálva van. <br/>

> [!NOTE]
> Az egyik gépről származó biztonsági másolatok nem állíthatók vissza olyan gépre, amely az operációs rendszer korábbi verzióját futtatja. Egy Windows Server 2016 rendszerű gépről készített biztonsági másolatok például nem állíthatók vissza a Windows Server 2012 R2-re. Az inverz azonban lehetséges. A Windows Server 2012 R2 biztonsági másolatait a Windows Server 2016 visszaállítására használhatja.
>

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult a *célszámítógépen*.
2. Győződjön meg arról, hogy a *célszámítógép* és a *forrásszámítógép* ugyanahhoz a Recovery Services-tárolóhoz van regisztrálva.
3. A munkafolyamat elindításához kattintson az **adatok helyreállítása** elemre.
4. **Másik kiszolgáló** kiválasztása

    ![Egy másik kiszolgáló](./media/backup-azure-restore-system-state/anotherserver.png)

5. Adja meg a tár hitelesítő adatait tartalmazó fájlt, amely megfelel a *mintatartó*tárolónak. Ha a tároló hitelesítőadat-fájlja érvénytelen (vagy lejárt), töltse le a tárolóban lévő új *hitelesítőadat-fájlt* a Azure Portal. A tároló hitelesítőadat-fájljának megadása után megjelenik a tár hitelesítőadat-fájljához társított Recovery Services tároló.

6. A biztonsági mentési kiszolgáló kiválasztása panelen válassza ki a *forrás gépet* a megjelenő gépek listájából.
7. A helyreállítási mód kiválasztása panelen válassza a **rendszerállapot** lehetőséget, majd kattintson a **tovább**gombra.

    ![Keresés](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. A **kötet és dátum kiválasztása** ablaktáblán válassza ki a kívánt helyreállítási pontot a naptárban. Bármely helyreállítási pontról visszaállítható. A **félkövérrel szedett** dátumok azt jelzik, hogy legalább egy helyreállítási pont rendelkezésre áll. Ha kiválasztott egy dátumot, ha több helyreállítási pont is elérhető, válassza ki az adott helyreállítási pontot az **idő** legördülő menüből.

    ![Elemek keresése](./media/backup-azure-restore-system-state/select-date.png)

9. Miután kiválasztotta a visszaállítani kívánt helyreállítási pontot, kattintson a **tovább**gombra.

10. A rendszerállapot- **helyreállítási mód kiválasztása** panelen adja meg azt a célhelyet, ahol a rendszerállapot-fájlokat helyre szeretné állítani, majd kattintson a **tovább**gombra.

    ![Encryption](./media/backup-azure-restore-system-state/recover-as-files.png)

    A példányok **létrehozása, hogy mindkét verzióval rendelkezzen**, másolatot készít az egyes fájlokról egy meglévő rendszerállapot-archívumban a teljes rendszerállapot-Archívum másolatának létrehozása helyett.

11. Ellenőrizze a helyreállítás részleteit a megerősítő ablaktáblán, és kattintson a **helyreállítás**gombra.

    ![kattintson a helyreállítás gombra a helyreállítási folyamat megerősítéséhez](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Másolja a *WindowsImageBackup* könyvtárat a kiszolgáló nem kritikus kötetére (például D:\)). Általában a Windows operációs rendszer kötete a kritikus kötet.

13. A helyreállítási folyamat befejezéséhez használja a következő szakaszt a visszaállított rendszerállapot- [fájlok Windows Serveren](#apply-restored-system-state-on-a-windows-server)való alkalmazásához.




## <a name="apply-restored-system-state-on-a-windows-server"></a>Visszaállított rendszerállapot alkalmazása Windows Serveren

Ha az Azure Recovery Services Agent használatával helyreállítja a rendszerállapotot, az Windows Server biztonsági másolat segédprogrammal alkalmazza a helyreállított rendszerállapotot a Windows Serverre. A Windows Server biztonsági másolat segédprogram már elérhető a kiszolgálón. A következő lépések azt ismertetik, hogyan alkalmazhatja a helyreállított rendszerállapotot.

1. A következő parancsok segítségével indítsa újra a kiszolgálót a *Címtárszolgáltatások helyreállító módjában*. Rendszergazdai jogú parancssorban:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Az újraindítás után nyissa meg a Windows Server biztonsági másolat beépülő modult. Ha nem tudja, hol telepítette a beépülő modult, keresse meg **Windows Server biztonsági másolat**a számítógépet vagy a kiszolgálót.

    Az asztali alkalmazás megjelenik a keresési eredmények között.

3. A beépülő modulban válassza a **helyi biztonsági mentés**lehetőséget.

    ![Válassza ki a helyi biztonsági mentést a visszaállításhoz](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. A helyi biztonsági mentési konzol **műveletek paneljén**kattintson a **helyreállítás** elemre a helyreállítási varázsló megnyitásához.

5. Válassza a lehetőséget, **egy másik helyen tárolt biztonsági másolatot**, és kattintson a **tovább**gombra.

   ![másik kiszolgálóra történő helyreállítás kiválasztása](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. A hely típusának megadásakor válassza a **távoli megosztott mappa** lehetőséget, ha a rendszerállapot biztonsági mentése egy másik kiszolgálóra lett helyreállítva. Ha a rendszerállapot helyi helyre lett állítva, válassza a **helyi meghajtók**lehetőséget.

    ![Válassza ki, hogy a helyreállítás a helyi kiszolgálóról vagy más](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Adja meg a *WindowsImageBackup* könyvtár elérési útját, vagy válassza ki a könyvtárat tartalmazó helyi meghajtót (például D:\WindowsImageBackup), amely a rendszerállapot-fájlok helyreállításának részeként lett helyreállítva az Azure Recovery Services Agent használatával, és kattintson a Tovább gombra. .

    ![a megosztott fájl elérési útja](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Válassza ki a visszaállítani kívánt rendszerállapot-verziót, és kattintson a **tovább**gombra.

9. A helyreállítási típus kiválasztása panelen válassza a **rendszerállapot** lehetőséget, majd kattintson a **tovább**gombra.

10. A rendszerállapot-helyreállítás helyeként válassza az **eredeti hely**lehetőséget, majd kattintson a **tovább**gombra.

11. Tekintse át a megerősítő adatokat, ellenőrizze az újraindítási beállításokat, majd kattintson a **helyreállítás** elemre a visszaállított rendszerállapot-fájlok alkalmazásához.

    ![Indítsa el a rendszerállapot-fájlok visszaállítását](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>A rendszerállapot-helyreállításra vonatkozó különleges szempontok Active Directory kiszolgálón

A rendszerállapot biztonsági másolata Active Directory-adattal rendelkezik. A következő lépésekkel állíthatja vissza Active Directory-tartomány szolgáltatást (AD DS) az aktuális állapotból egy korábbi állapotba.

1. Indítsa újra a tartományvezérlőt a Címtárszolgáltatások helyreállító módjában.
2. A AD DS helyreállításához kövesse az [alábbi lépéseket Windows Server biztonsági másolat](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) parancsmagok használatához.


## <a name="troubleshoot-failed-system-state-restore"></a>Sikertelen rendszerállapot-visszaállítás – hibakeresés

Ha a rendszerállapot alkalmazásának előző folyamata nem fejeződik be sikeresen, a Windows-kiszolgáló helyreállításához használja a Windows helyreállítási környezetét (Win RE). A következő lépések bemutatják, hogyan lehet helyreállítani a Win RE használatával. Ezt a lehetőséget csak akkor használja, ha a Windows Server nem indul el rendesen a rendszerállapot visszaállítása után. A következő folyamat törli a nem rendszerértékeket, körültekintően járjon el.

1. Indítsa el a Windows Servert a Windows helyreállítási környezetbe (Win RE).

2. A három elérhető lehetőség közül válassza a hibakeresés lehetőséget.

    ![menü megnyitása](./media/backup-azure-restore-system-state/winre-1.png)

3. A **Speciális beállítások** képernyőn válassza a **parancssor** lehetőséget, és adja meg a kiszolgáló-rendszergazda felhasználónevét és jelszavát.

   ![menü megnyitása](./media/backup-azure-restore-system-state/winre-2.png)

4. Adja meg a kiszolgáló-rendszergazda felhasználónevét és jelszavát.

    ![menü megnyitása](./media/backup-azure-restore-system-state/winre-3.png)

5. Amikor rendszergazdai módban nyitja meg a parancssort, futtassa a következő parancsot a rendszerállapot biztonsági másolati verzióinak beszerzéséhez.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![rendszerállapot biztonsági mentési verzióinak beolvasása](./media/backup-azure-restore-system-state/winre-4.png)

6. Futtassa a következő parancsot a biztonsági mentésben elérhető összes kötet lekéréséhez.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![rendszerállapot biztonsági mentési verzióinak beolvasása](./media/backup-azure-restore-system-state/winre-5.png)

7. A következő parancs helyreállítja a rendszerállapot biztonsági mentésének részét képező összes kötetet. Vegye figyelembe, hogy ez a lépés csak a rendszerállapot részét képező kritikus köteteket helyreállítja. A rendszer törli az összes nem rendszeradatot.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![rendszerállapot biztonsági mentési verzióinak beolvasása](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>További lépések
* Most, hogy helyreállítja a fájlokat és mappákat, kezelheti [a biztonsági mentéseket](backup-azure-manage-windows-server.md).
