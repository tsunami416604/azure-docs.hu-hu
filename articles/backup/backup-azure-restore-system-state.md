---
title: 'Az Azure Backup: Windows Server rendszerállapot visszaállítása'
description: Lépés magyarázat a Windows Server rendszerállapotának visszaállítása az Azure-ban biztonsági lépésekre.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: 31784c44d7561c690278aa4764e98992fbca73ae
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230784"
---
# <a name="restore-system-state-to-windows-server"></a>A Windows Server rendszerállapotának visszaállítása

Ez a cikk ismerteti a Windows Server rendszerállapotának biztonsági mentések visszaállítása egy Azure helyreállítási tárat. Rendszerállapot visszaállítása, rendelkeznie kell egy rendszerállapot biztonsági mentése (utasításait használatával létrehozott [rendszerállapot biztonsági mentése](backup-azure-system-state.md#back-up-windows-server-system-state), és győződjön meg arról, hogy telepítette a [legújabb verzióját a Microsoft Azure Recovery Services (MARS) az ügynök](https://aka.ms/azurebackup_agent). A Windows Server rendszerállapotának adatok helyreállítása az Azure Recovery Services-tárolót a két lépésből áll:

1. Rendszerállapot visszaállítása az Azure Backup-fájlok formájában. Ha a rendszerállapot helyreállítása az Azure Backup-fájlok formájában, közül választhat:
  * Rendszerállapotának visszaállítása ugyanarra a kiszolgálóra, ahol a biztonsági mentések származnak, vagy
  * Rendszerállapotának visszaállítása a fájl egy másik kiszolgálóra.

2. A Windows Server a visszaállított rendszerállapot-fájlok érvényesek.


## <a name="recover-system-state-files-to-the-same-server"></a>Ugyanazon a kiszolgálón rendszerállapotfájlok
A következő lépések bemutatják, hogyan szeretné visszaállítani a Windows Server-konfigurációs korábbi állapotába. A kiszolgáló konfigurációjának visszaállítása egy ismert, stabil állapotba, rendkívül fontos lehet. Az alábbi lépéseket a kiszolgáló rendszerállapotának visszaállítása egy Recovery Services-tárolót. 

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, ahol a beépülő modul telepítve van, keresse a számítógép vagy kiszolgáló **a Microsoft Azure Backup**.

    A keresési eredmények között meg kell jelennie az asztali alkalmazás.

2. Kattintson a **adatok helyreállítása** varázsló elindításához.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

3. Az a **bevezetés** ugyanazon a kiszolgálón vagy számítógépen, hogy az adatok helyreállítását ablaktáblában válassza **ezen a kiszolgálón (`<server name>`)** kattintson **tovább**.

    ![Állítsa vissza az adatokat ugyanarra a gépre, a kiszolgáló lehetőség kiválasztása](./media/backup-azure-restore-system-state/samemachine.png)

4. A a **helyreállítási mód kiválasztása** panelen válassza a **rendszerállapot** majd **tovább**.

    ![Fájlok tallózása](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. A naptárban lévő **kötet és dátum kiválasztása** panelen válasszon ki egy helyreállítási pontot. 

    Időben bármelyik helyreállítási pontra visszaállíthatja. A dátumok **félkövér** azt jelzik, legalább egy helyreállítási pont rendelkezésre állását. Ha dátum, akkor válassza, ha több helyreállítási pont érhető el, válassza az adott helyreállítási pontot a **idő** legördülő menüből.

    ![Kötet és dátum](./media/backup-azure-restore-system-state/select-date.png)

6. Miután kiválasztotta a helyreállítási pontot a visszaállításhoz, kattintson a **tovább**.

    Az Azure Backup a helyi helyreállítási pontot csatlakoztatja, és használja azt, mint a helyreállítási kötet.

7. A következő panelen adja meg a helyreállított fájlok rendszerállapot célját, és kattintson a **Tallózás** nyissa meg a Windows Intézőt, és keresse meg a fájlokat és mappákat, amelyekről. A beállítás **példányt hoz létre, így mindkét verziót kell**, egyes fájlokon másolatát hozza létre a rendszerállapot fájl meglévő archívumot a a teljes rendszerállapot-archívum másolatot létrehozása helyett.

    ![Helyreállítási beállítások](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Ellenőrizze a helyreállítási részleteit a **megerősítő** panelt, és kattintson **helyreállítása**.

   ![Kattintson a vissza kell a helyreállítás művelet nyugtázása](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Másolás a *WindowsImageBackup* könyvtárat a a helyreállítás célját, a kiszolgáló nem kritikus fontosságú kötetre. A Windows operációs rendszer kötet általában a kritikus kötet jelenti.

10. Sikeres a helyreállítás után kövesse a lépéseket, a szakaszban [alkalmaz rendszerállapot fájlok visszaállítása a Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-files-to-the-windows-server), a rendszerállapot-helyreállítási folyamat befejezéséhez.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Egy másik kiszolgálóra rendszerállapotfájlok

Ha a Windows Server sérült vagy nem érhető el, és szeretné visszaállítani, stabil állapotba, ha a Windows Server rendszerállapotának visszaállítása, visszaállíthatja a sérült kiszolgáló rendszerállapotának egy másik kiszolgálóról. Használja a rendszer állapotának visszaállítása a következő lépéseket egy különálló kiszolgálón.  

Ezeket a lépéseket a használt terminológiával tartalmazza:

- *Forrásgép* – az eredeti gépet, amely a biztonsági mentéshez, és amelyet jelenleg nem érhető el.
- *Célszámítógép* – a gép, amelyre az adatok helyreállítása folyik.
- *Minta tároló* – a Recovery Services-tároló, amelyhez a *forrásgép* és *célgépen* van regisztrálva. <br/>

> [!NOTE]
> Az operációs rendszer korábbi verzióját futtató gép egyik gépről készített biztonsági másolatok nem lehet visszaállítani. Például a Windows Server 2016 gépről készített biztonsági másolatok nem állítható vissza, a Windows Server 2012 R2 rendszerre. Inverz azonban nem lehetséges. Biztonsági mentés a Windows Server 2012 R2 használatával állítsa vissza a Windows Server 2016-ban.
>

1. Nyissa meg a **a Microsoft Azure Backup** beépülő modult a *célgépen*.
2. Ügyeljen arra, hogy a *célgépen* és a *forrásgép* ugyanabban a helyreállítási tárban van regisztrálva.
3. Kattintson a **adatok helyreállítása** kezdeményezése a munkafolyamatot.
4. Válassza ki **egy másik kiszolgálóra**

    ![Egy másik kiszolgálóra](./media/backup-azure-restore-system-state/anotherserver.png)

5. Adja meg a tároló hitelesítő adatait tartalmazó fájlt, amely megfelel a *minta tároló*. Ha a tároló hitelesítőadat-fájlja érvénytelen (vagy lejárt), töltse le az új tárolói hitelesítő adatok fájlját a *minta tároló* az Azure Portalon. Miután a tároló hitelesítőadat-fájlja áll rendelkezésre, a Recovery Services-tárolót, a tároló hitelesítőadat-fájlja társított jelenik meg.

6. A Backup-kiszolgáló kiválasztása panelen válassza ki a *forrásgép* megjelenített gépet a listából.
7. A helyreállítási mód kiválasztása panelen válassza ki a **rendszerállapot** kattintson **tovább**. 

    ![Keresés](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. A naptárban lévő a **kötet és dátum kiválasztása** panelen válasszon ki egy helyreállítási pontot. Időben bármelyik helyreállítási pontra visszaállíthatja. A dátumok **félkövér** azt jelzik, legalább egy helyreállítási pont rendelkezésre állását. Ha dátum, akkor válassza, ha több helyreállítási pont érhető el, válassza az adott helyreállítási pontot a **idő** legördülő menüből. 

    ![Elemek keresése](./media/backup-azure-restore-system-state/select-date.png)

9. Miután kiválasztotta a helyreállítási pontot a visszaállításhoz, kattintson a **tovább**.

10. Az a **rendszer állapota helyreállítási mód kiválasztása** panelen adja meg a helyet, ahol állítható helyre, majd kattintson a rendszerállapot-fájlok **tovább**.

    ![Titkosítás](./media/backup-azure-restore-system-state/recover-as-files.png)

    A beállítás **példányt hoz létre, így mindkét verziót kell**, egyes fájlokon másolatát hozza létre a rendszerállapot fájl meglévő archívumot a a teljes rendszerállapot-archívum másolatot létrehozása helyett.

11. Ellenőrizze a részleteket a megerősítési panelen helyreállítás, és kattintson a **helyreállítása**. 

    ![a helyreállítás gombra kattintva erősítse meg a helyreállítási folyamat](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Másolás a *WindowsImageBackup* könyvtárat a kiszolgáló nem kritikus fontosságú kötetre (például D:\). A Windows operációs rendszer kötet általában a kritikus kötet a.

13. A helyreállítási folyamat végrehajtásához, használja a következő szakaszt [a alkalmazni a visszaállított rendszerállapot-fájlokat a Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Visszaállított rendszerállapot alkalmazása Windows Serveren

Egyszer helyreállított rendszerállapot-fájlként az Azure Recovery Services Agent, a visszaállított rendszerállapot alkalmazása Windows Server a Windows Server biztonsági másolat segédprogram használatával. A Windows Server biztonsági másolat segédprogram már elérhető a kiszolgálón. A következő lépések elmagyarázzák, hogyan alkalmazhatja a helyreállított rendszerállapot.

1. A következő parancsok segítségével indítsa újra a kiszolgálót a *javító módban*. Egy rendszergazdai jogú parancssorban:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Az újraindítás után a Windows Server biztonsági másolat beépülő modul megnyitásához. Ha nem tudja, ahol a beépülő modul telepítve van, keresse a számítógép vagy kiszolgáló **Windows Server biztonsági másolat**.

    Az asztali alkalmazás megjelenik a keresési eredmények között.

3. A beépülő modulban, válassza ki a **helyi biztonsági másolat**.

    ![Válassza ki a helyi biztonsági másolat visszaállítása onnan](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. A helyi biztonsági mentési konzolon a a **műveletek panel**, kattintson a **helyreállítása** a helyreállítási varázsló megnyitásához.

5. A beállítást, **egy másik helyen tárolt biztonsági másolat**, és kattintson a **tovább**.

   ![helyreállítása egy másik kiszolgálóra](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. A tárhely típusának megadása esetén válassza ki a **távoli megosztott mappa** , ha a rendszerállapot biztonsági mentése volt vissza egy másik kiszolgálóra. Ha a rendszerállapot helyileg volt helyreállítva, akkor válasszon **helyi meghajtók**. 

    ![Válassza ki a helyi kiszolgálón vagy egy másik helyreállítás](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Az elérési útját adja meg a *WindowsImageBackup* könyvtár, vagy válassza ki a helyi meghajtót tartalmazó ezt a könyvtárat (például D:\WindowsImageBackup), a rendszerállapot-fájlokat az Azure Recovery Services használatával helyreállítás részeként vissza Ügynök, és kattintson **tovább**.

    ![a megosztott fájl elérési útja](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Válassza ki a rendszerállapot verziót, amelyet szeretne visszaállítani, és kattintson a **tovább**.

9. A helyreállítási típus kiválasztása párbeszédpanelen válassza ki a **rendszerállapot** kattintson **tovább**.

10. A rendszerállapot-helyreállítás helyének kiválasztása **eredeti**, és kattintson a **tovább**.

11. Tekintse meg a jóváhagyás részleteit, ellenőrizze a újraindítási beállításokat, majd kattintson **helyreállítása** applly a visszaállított rendszerállapot a fájlokat.

    ![Indítsa el a visszaállítást rendszerállapot-fájlok](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Rendszerállapot-helyreállítást az Active Directory-kiszolgálóra vonatkozó különleges szempontok

A rendszerállapot biztonsági mentése az Active Directory-adatokat tartalmaz. Az alábbi lépéseket visszaállításához használja az Active Directory tartományi szolgáltatások (AD DS) a jelenlegi állapotában a korábbi állapotába.

1. Indítsa újra a tartományvezérlőt a Címtárszolgáltatások helyreállító módban (DSRM).
2. Kövesse a lépéseket [Itt](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) való helyreállítás az Active Directory tartományi szolgáltatások a Windows Server biztonsági másolat-parancsmagok használatával.


## <a name="troubleshoot-failed-system-state-restore"></a>Sikertelen rendszerállapot-visszaállítás hibaelhárítása

Az előző folyamat során a rendszer állapotának alkalmazása nem sikeres, ha a Windows helyreállítási környezet (Win-RE) használatával a Windows Server helyreállítása. A következő lépések azt ismertetik, hogyan nyerhet RE használatával állítsa helyre. Használja ezt a beállítást csak akkor, ha a Windows Server normál esetben nem töltődik a rendszerállapot-visszaállítás után. A következő folyamat törli a nem rendszer-adatokat, körültekintően járjon el. 

1. A Windows Server indítsa el a Windows helyreállítási környezet (Windows RE).

2. A három elérhető lehetőségek közül válassza ki a hibaelhárítás.

    ![menü megnyitása](./media/backup-azure-restore-system-state/winre-1.png)

3. Az a **speciális beállítások** képernyőn válassza ki **parancssor** , és adja meg a kiszolgálói rendszergazda felhasználónevét és jelszavát.

   ![menü megnyitása](./media/backup-azure-restore-system-state/winre-2.png)

4. Adja meg a kiszolgálói rendszergazda felhasználónevét és jelszavát.

    ![menü megnyitása](./media/backup-azure-restore-system-state/winre-3.png)

5. Amikor, nyissa meg a parancssort rendszergazdai módban, futtassa a következő parancs használatával beszerezheti a rendszerállapot biztonságimásolat-verziók.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Rendszerállapot biztonságimásolat-verziók beolvasása](./media/backup-azure-restore-system-state/winre-4.png)

6. Futtassa a következő parancsot beolvasni az összes kötet elérhető-e a biztonsági mentéshez.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Rendszerállapot biztonságimásolat-verziók beolvasása](./media/backup-azure-restore-system-state/winre-5.png)

7. A következő parancsot a rendszerállapot biztonsági mentését részét képező összes kötetet állítja helyre. Vegye figyelembe, hogy ez a lépés helyreállítja csak a kritikus köteteket, amelyek részei a rendszer állapotát. Minden nem rendszer-adat törlődik.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Rendszerállapot biztonságimásolat-verziók beolvasása](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>További lépések
* Most, hogy a fájlok és mappák helyreállítható már [a biztonsági másolatok kezelése a](backup-azure-manage-windows-server.md).
