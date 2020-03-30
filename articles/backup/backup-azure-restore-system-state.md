---
title: A rendszerállapot visszaállítása Windows Server kiszolgálóra
description: Lépésről lépésre magyarázat a Windows Server rendszerállapotának az Azure biztonsági mentéséből történő visszaállításához.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 6d46a091a4e620e26d05735f12a201009663e65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602467"
---
# <a name="restore-system-state-to-windows-server"></a>A rendszerállapot visszaállítása Windows Server kiszolgálóra

Ez a cikk bemutatja, hogyan állíthatja vissza a Windows Server rendszerállapot-biztonsági mentéseket egy Azure Recovery Services-tárolóból. A rendszerállapot visszaállításához rendelkeznie kell egy rendszerállapot-biztonsági másolattal (amelyet a [Rendszerállapot biztonsági mentése](backup-azure-system-state.md#back-up-windows-server-system-state)című útmutató segítségével hoztak létre, és győződjön meg arról, hogy [telepítette a Microsoft Azure Recovery Services (MARS) ügynök legújabb verzióját.](https://aka.ms/azurebackup_agent) A Windows Server rendszerállapot-adatok helyreállítása egy Azure Recovery Services-tárolóból két lépésből áll:

1. Rendszerállapot visszaállítása fájlként az Azure Backup ból. A rendszerállapot nak az Azure Backup ból fájlként történő visszaállításakor a következőket teheti:
   * A rendszerállapot visszaállítása ugyanarra a kiszolgálóra, ahol a biztonsági mentéseket készítették, vagy
   * A Rendszerállapot-fájl visszaállítása egy másik kiszolgálóra.

2. Alkalmazza a visszaállított rendszerállapot-fájlokat Windows Server rendszerre.

## <a name="recover-system-state-files-to-the-same-server"></a>Rendszerállapotfájlok visszaállítása ugyanarra a kiszolgálóra

Az alábbi lépések bemutatják, hogyan állíthatja vissza a Windows Server-konfigurációt egy korábbi állapotba. A kiszolgáló konfigurációjának visszagörgetése egy ismert, stabil állapotba rendkívül értékes lehet. A következő lépések visszaállítják a kiszolgáló rendszerállapotát a Helyreállítási szolgáltatások tárolójából.

1. Nyissa meg a **Microsoft Azure Backup** beépülő modult. Ha nem tudja, hol volt telepítve a beépülő modul, keressen a számítógépen vagy a **kiszolgálón**a Microsoft Azure Backup .

    Az asztali alkalmazásnak meg kell jelennie a keresési eredmények között.

2. A varázsló elindításához kattintson az **Adatok helyreállítása** gombra.

    ![Adatok helyreállítása](./media/backup-azure-restore-windows-server/recover.png)

3. Az **Első lépések** ablaktáblán az adatok ugyanarra a kiszolgálóra vagy számítógépre való visszaállításához válassza a Ez a **kiszolgáló`<server name>`( )** lehetőséget, majd kattintson a **Tovább**gombra.

    ![Válassza ezt a kiszolgálót, ha vissza szeretné állítani az adatokat ugyanarra a gépre](./media/backup-azure-restore-system-state/samemachine.png)

4. A **Helyreállítási mód kiválasztása** ablaktáblán válassza a **Rendszerállapot** lehetőséget, majd kattintson a **Tovább**gombra.

    ![Fájlok tallózása](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. A Kötet **és dátum kiválasztása** ablaktáblában jelölje ki a helyreállítási pontot a naptárban.

    Bármely helyreállítási pontból visszaállíthatja az időt. A **félkövérrel szedett** dátumok legalább egy helyreállítási pont elérhetőségét jelzik. Ha kiválasztott egy dátumot, ha több helyreállítási pont is rendelkezésre áll, válassza ki az adott helyreállítási pontot az **Idő** legördülő menüből.

    ![Mennyiség és dátum](./media/backup-azure-restore-system-state/select-date.png)

6. Miután kiválasztotta a visszaállítani kívánt helyreállítási pontot, kattintson a **Tovább**gombra.

    Az Azure Backup csatlakoztatja a helyi helyreállítási pontot, és helyreállítási kötetként használja.

7. A következő ablaktáblán adja meg a helyreállított rendszerállapot-fájlok célját, és a **Tallózás gombra** kattintva nyissa meg a Windows Intézőt, és keresse meg a kívánt fájlokat és mappákat. A **Példánylétrehozása, hogy mindkét verzióval rendelkezik,** a teljes rendszerállapot-archívum másolata helyett az egyes fájlok másolatát hozza létre egy meglévő rendszerállapot-fájlarchívumban.

    ![Helyreállítási lehetőségek](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Ellenőrizze a helyreállítás részleteit a **Megerősítés** ablaktáblán, és kattintson a **Helyreállítás gombra.**

   ![kattintson a Helyreállítás gombra a helyreállítási művelet nyugtázásához](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Másolja a *WindowsImageBackup* könyvtárat a helyreállítási célba a kiszolgáló nem kritikus kötetére. Általában a Windows operációs rendszer kötete a kritikus kötet.

10. A helyreállítás sikeres befejezése után kövesse a [Visszaállított rendszerállapot-fájlok alkalmazása a Windows Server kiszolgálóra](backup-azure-restore-system-state.md)című szakaszlépéseit a rendszerállapot-helyreállítási folyamat befejezéséhez.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Rendszerállapot-fájlok helyreállítása másik kiszolgálóra

Ha a Windows Server sérült vagy nem érhető el, és a Windows Server rendszerállapotának helyreállításával stabil állapotba szeretné állítani, a sérült kiszolgáló rendszerállapotát egy másik kiszolgálóról állíthatja vissza. A következő lépésekkel állítsa vissza a rendszerállapotot egy külön kiszolgálón.  

Az alábbi lépésekben használt terminológia a következőket foglalja magában:

* *Forrásgép* – Az eredeti gép, amelyről a biztonsági mentés készült, és amely jelenleg nem érhető el.
* *Célgép* – Az a gép, amelyre az adatok helyreállnak.
* *Mintatároló* – A Recovery Services-tároló, amelyhez a *Forrásgép* és a *célgép* regisztrálva van. <br/>

> [!NOTE]
> Az egyik gépről vett biztonsági mentések nem állíthatók vissza az operációs rendszer korábbi verzióját futtató gépre. Egy Windows Server 2016-gépről készített biztonsági mentések például nem állíthatók vissza a Windows Server 2012 R2 rendszerre. Azonban az inverz lehetséges. A Windows Server 2016 visszaállításához a Windows Server 2012 R2 rendszerbiztonsági mentései is használhatók.
>

1. Nyissa meg a **Microsoft Azure Biztonsági mentés** beépülő modult a *célgépen.*
2. Győződjön meg arról, hogy a *célgép* és a *forrásgép* ugyanahhoz a Recovery Services-tárolóhoz van regisztrálva.
3. A munkafolyamat elindításához kattintson az **Adatok helyreállítása** gombra.
4. **Másik kiszolgáló** kiválasztása

    ![Egy másik szerver](./media/backup-azure-restore-system-state/anotherserver.png)

5. Adja meg a tároló hitelesítő fájlját, amely megfelel a *mintatárolónak.* Ha a tároló hitelesítő fájl érvénytelen (vagy lejárt), töltse le az új tároló hitelesítő fájl az Azure *Portalon a minta-tárolóból.* A tároló hitelesítő fájljának megadása után megjelenik a tároló hitelesítő fájlhoz társított Helyreállítási szolgáltatások tárolója.

6. A Tartalékkiszolgáló kiválasztása ablaktáblán válassza ki a *Forrásgépet* a megjelenített gépek listájából.
7. A Helyreállítási mód kiválasztása ablaktáblán válassza a **Rendszerállapot** lehetőséget, és kattintson a **Tovább**gombra.

    ![Keresés](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. A **Kötet és dátum kiválasztása** ablaktábla Naptár ablaktábláján jelöljön ki egy helyreállítási pontot. Bármely helyreállítási pontból visszaállíthatja az időt. A **félkövérrel szedett** dátumok legalább egy helyreállítási pont elérhetőségét jelzik. Ha kiválasztott egy dátumot, ha több helyreállítási pont is rendelkezésre áll, válassza ki az adott helyreállítási pontot az **Idő** legördülő menüből.

    ![Elemek keresése](./media/backup-azure-restore-system-state/select-date.png)

9. Miután kiválasztotta a visszaállítani kívánt helyreállítási pontot, kattintson a **Tovább**gombra.

10. A **Rendszerállapot-helyreállítási mód kiválasztása** ablaktáblán adja meg azt a célt, ahová a rendszerállapot-fájlokat helyre kívánja állítani, majd kattintson a **Tovább**gombra.

    ![Titkosítás](./media/backup-azure-restore-system-state/recover-as-files.png)

    A **Példánylétrehozása, hogy mindkét verzióval rendelkezik,** a teljes rendszerállapot-archívum másolata helyett az egyes fájlok másolatát hozza létre egy meglévő rendszerállapot-fájlarchívumban.

11. Ellenőrizze a helyreállítás részleteit a Megerősítés ablaktáblán, majd kattintson a **Helyreállítás gombra.**

    ![a helyreállítási folyamat megerősítéséhez kattintson a Helyreállítás gombra](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Másolja a *WindowsImageBackup* könyvtárat a kiszolgáló nem kritikus kötetére (például D:\). Általában a Windows operációs rendszer kötete a kritikus kötet.

13. A helyreállítási folyamat befejezéséhez a következő szakaszban [alkalmazza a visszaállított rendszerállapot-fájlokat Windows Server rendszeren](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Visszaállított rendszerállapot alkalmazása Windows Serveren

Miután helyreállított a rendszerállapotot fájlként az Azure Recovery Services Agent használatával, a Windows Server biztonsági másolat segédprogrammal alkalmazza a helyreállított rendszerállapotot a Windows Server kiszolgálóra. A Windows Server biztonsági másolat segédprogram már elérhető a kiszolgálón. A következő lépések bemutatják, hogyan kell alkalmazni a helyreállított rendszerállapotot.

1. A következő parancsokkal indítsa újra a kiszolgálót *címtárszolgáltatások javítása módban.* Rendszergazda jogú parancssorban:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Az újraindítás után nyissa meg a Windows Server biztonsági másolat beépülő modult. Ha nem tudja, hol volt telepítve a beépülő modul, keressen a számítógépen vagy a kiszolgálón a **Windows Server biztonsági másolat segédprogramot.**

    Az asztali alkalmazás megjelenik a keresési eredmények között.

3. A beépülő modulban válassza a **Helyi biztonsági mentés lehetőséget.**

    ![válassza a Helyi biztonsági mentés lehetőséget a visszaállításhoz](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. A Helyi biztonságimásolat-készítő konzol **Műveletek munkaablakában**kattintson a **Helyreállítás** gombra a Helyreállítási varázsló megnyitásához.

5. Válassza a másik **helyen tárolt biztonsági másolat**lehetőséget, majd kattintson a **Tovább**gombra.

   ![úgy dönt, hogy visszaállítani egy másik szerverre](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. A helytípus megadásakor válassza a **Távoli megosztott mappa** lehetőséget, ha a rendszerállapot biztonsági mentését egy másik kiszolgálóra állították vissza. Ha a rendszerállapot helyreállt helyben, válassza a **Helyi meghajtók**lehetőséget.

    ![válassza ki, hogy a helyreállítás a helyi szervervagy egy másik](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Adja meg a *WindowsImageBackup* könyvtár elérési útját, vagy válassza ki a könyvtárat tartalmazó helyi meghajtót (például D:\WindowsImageBackup), amely az Azure Recovery Services Agent segítségével helyreállt a Rendszerállapot-fájlok helyreállításának részeként, majd kattintson a **Tovább**gombra.

    ![a megosztott fájl elérési útja](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Jelölje ki a visszaállítani kívánt rendszerállapot-verziót, majd kattintson a **Tovább**gombra.

9. A Helyreállítási típus kiválasztása ablaktáblában válassza a **Rendszerállapot** lehetőséget, és kattintson a **Tovább**gombra.

10. A rendszerállapot-helyreállítás helyéhez válassza az **Eredeti hely**lehetőséget, majd kattintson a **Tovább**gombra.

11. Tekintse át a megerősítés részleteit, ellenőrizze az újraindítási beállításokat, és kattintson a **Helyreállítás** gombra a visszaállított rendszerállapot-fájlok alkalmazásához.

    ![a rendszerállapot-fájlok visszaállítása](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Az Active Directory-kiszolgáló rendszerállapot-helyreállításának speciális szempontjai

A rendszerállapot biztonsági mentése tartalmazza az Active Directory adatait. Az alábbi lépésekkel állíthatja vissza az Active Directory tartományi szolgáltatást (AD DS) az aktuális állapotáról egy korábbi állapotra.

1. Indítsa újra a tartományvezérlőt címtárszolgáltatások visszaállítási üzemmódjában (DSRM).
2. Az [alábbi](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) lépéseket követve a Windows Server biztonsági másolat parancsmagjainak használatához az AD DS helyreállítása.

## <a name="troubleshoot-failed-system-state-restore"></a>Sikertelen rendszerállapot-visszaállítás hibaelhárítása

Ha a rendszerállapot alkalmazásának előző folyamata nem fejeződik be sikeresen, a Windows helyreállítási környezet (Win RE) segítségével helyreállítsa a Windows Server rendszert. A következő lépések bemutatják, hogyan lehet a Win RE használatával helyreállítani. Csak akkor használja ezt a beállítást, ha a Windows Server rendszer-visszaállítás után nem indul el megfelelően. A következő folyamat törli a nem rendszeradatokat, legyen óvatos.

1. Indítsa el a Windows Server rendszert a Windows helyreállítási környezetbe (Win RE).

2. A három rendelkezésre álló beállítás közül válassza a Hibaelhárítás lehetőséget.

    ![megnyitás menü](./media/backup-azure-restore-system-state/winre-1.png)

3. A **Speciális beállítások** képernyőn válassza a **Parancssor** lehetőséget, és adja meg a kiszolgáló rendszergazdájának felhasználónevét és jelszavát.

   ![megnyitás menü](./media/backup-azure-restore-system-state/winre-2.png)

4. Adja meg a kiszolgáló rendszergazdájának felhasználónevét és jelszavát.

    ![megnyitás menü](./media/backup-azure-restore-system-state/winre-3.png)

5. Amikor rendszergazdai módban megnyitja a parancssort, futtassa a következő parancsot a Rendszerállapot biztonsági másolat verzióinak lekérése érdekében.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![Rendszerállapot biztonsági másolat verzióinak bekésezése](./media/backup-azure-restore-system-state/winre-4.png)

6. Futtassa a következő parancsot a biztonsági mentésben elérhető összes kötet levételéhez.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Rendszerállapot biztonsági másolat verzióinak bekésezése](./media/backup-azure-restore-system-state/winre-5.png)

7. A következő parancs helyreállítja a rendszerállapot biztonsági másolatának részét tartalmazó összes kötetet. Vegye figyelembe, hogy ez a lépés csak a rendszerállapot részét tartalmazó kritikus köteteket állítja helyre. Minden nem rendszeradat törlődik.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![Rendszerállapot biztonsági másolat verzióinak bekésezése](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>További lépések

* Most, hogy helyreállt a fájlok és mappák, kezelheti [a biztonsági másolatokat.](backup-azure-manage-windows-server.md)
