---
title: "Készítsen biztonsági másolatot a Windows rendszer állapotát az Azure-bA |} Microsoft Docs"
description: "Ismerje meg, a rendszerállapot biztonsági mentését a Windows Server és/vagy a Windows számítógépek az Azure-bA."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: carmonm
editor: 
keywords: "biztonsági mentés menete; biztonsági mentési útmutató; fájlok és mappák biztonsági mentése"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: saurse;markgal
ms.openlocfilehash: 6fbd96935f444d8b0c6d068ebd0d28e612f19816
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>A Resource Manager telepítés Windows rendszerállapot biztonsági mentését
Ez a cikk ismerteti a rendszerállapot biztonsági mentését a Windows Server az Azure-bA. Ez az oktatóanyag végigvezeti az alapokon.

Ha többet szeretne megtudni az Azure Backupról, olvassa el ezt az [áttekintést](backup-introduction-to-azure-backup.md).

Ha még nincs Azure-előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/), amellyel bármely Azure-szolgáltatást elérhet.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
A fájlok és mappák biztonsági mentéséhez létre kell hoznia egy Recovery Services-tárolót abban a régióban, ahol az adatokat tárolni szeretné. Emellett a tároló replikálásának módját is meg kell határoznia.

### <a name="to-create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-előfizetésével.
2. A központi menüben kattintson a **További szolgáltatások** elemre, majd az erőforrások listájában írja be a **Recovery Services** szöveget, és kattintson a **Recovery Services-tárolók** elemre.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Ha az előfizetés Recovery Services-tárolókat tartalmaz, a tárolók fel vannak sorolva.
3. A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** elemre.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Megnyílik a Recovery Services-tároló panelje, a rendszer pedig egy **Név**, **Előfizetés**, **Erőforráscsoport** és **Hely** megadását kéri.

    ![Recovery Services-tároló létrehozása – 3. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez. Írjon be egy 2–50 karakter hosszúságú nevet. Ennek egy betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.

5. Az **Előfizetés** szakaszban, az Azure-előfizetés kiválasztásához használja a legördülő menüt. Ha csak egy előfizetést használ, az az előfizetés jelenik meg, és továbbléphet a következő lépésre. Ha nem biztos benne, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (vagy javasolt) előfizetést. Csak akkor lesz több választási lehetőség, ha a szervezetéhez tartozó fiók több Azure-előfizetéssel van összekötve.

6. Az **Erőforráscsoport** szakaszban:

    * válassza az **Új létrehozása** lehetőséget, ha erőforráscsoportot szeretne létrehozni.
    Vagy
    * válassza a **Meglévő használata** lehetőséget, és kattintson a legördülő menüben az elérhető erőforráscsoportok listájának megtekintéséhez.

  Átfogó információk az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).

7. Kattintson a **Hely** elemre a tárolóhoz tartozó földrajzi régió kiválasztásához. Ez a választás határozza meg a földrajzi régiót, ahová az adatok biztonsági másolata el lesz küldve.

8. Kattintson a Recovery Services-tároló panel alján a **Létrehozás** gombra.

    A Recovery Services-tároló létrehozása több percet is igénybe vehet. Figyelje az állapotértesítéseket a portál jobb felső területén. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában. Ha több perc után sem látja a tárolót, kattintson a **Frissítés** gombra.

    ![Kattintson a Frissítés gombra](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Ha látja a tárolót a Recovery Services-tárolók listájában, készen áll tárhely-redundancia beállítására.

### <a name="set-storage-redundancy-for-the-vault"></a>Tárhely-redundancia beállítása a tárolóhoz
A Recovery Services-tároló létrehozásakor győződjön meg róla, hogy a tárhely-redundancia a saját igényei szerint van beállítva.

1. A **Recovery Services-tárolók** panelen kattintson az új tárolóra.

    ![A Recovery Services-tárolók listájából válassza ki az új tárolót](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Ha kiválasztja a tárolót, a **Recovery Services-tároló** panel leszűkül, és a Beállítások panel (*amelynek tetején a tároló neve látható*), valamint a tároló részleteit tartalmazó panel nyílik meg.

    ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. Használja a függőleges csúszkát az új tároló Beállítások paneljén a legörgetéshez a Kezelés szakaszhoz, és kattintson a **Biztonsági mentési infrastruktúra** lehetőségre.
    Megnyílik a Biztonsági mentési infrastruktúra panel.
3. A Biztonsági mentési infrastruktúra panelen kattintson a **Biztonsági mentés konfigurációja** elemre a **Biztonsági mentés konfigurációja** panel megnyitásához.

    ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Válassza ki a megfelelő tárolóreplikációs beállítást a tárolóhoz.

    ![a tároló konfigurálásának lehetőségei](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, folytassa a **georedundáns** beállítás használatát. Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit. A [georedundáns](../storage/common/storage-redundancy.md#geo-redundant-storage) és a [helyileg redundáns](../storage/common/storage-redundancy.md#locally-redundant-storage) tárolási lehetőségekről többet olvashat ebben a [Tárhely-redundancia áttekintésben](../storage/common/storage-redundancy.md).

Most, hogy létrehozta a tárolót, konfigurálja a Windows rendszerállapotról készít biztonsági mentést.

## <a name="configure-the-vault"></a>A tároló konfigurálása
1. A Recovery Services-tároló (az imént létrehozott tároló) paneljén a Bevezetés szakaszban kattintson a **Biztonsági mentés** elemre, majd a **Bevezetés a biztonsági mentés használatába** panelen válassza a **Biztonsági mentés célja** elemet.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Megnyílik a **Biztonsági mentés célja** panel.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. A **Hol futnak az alkalmazások és szolgáltatások?** legördülő menüből válassza a **Helyszíni** lehetőséget.

    Azért kell a **Helyszíni** lehetőséget választania, mert a Windows Server- vagy Windows-számítógépe egy fizikai gép, amely nem az Azure része.

3. Az a **miről szeretne biztonsági másolatot készíteni?** menüjében válassza **rendszerállapot**, és kattintson a **OK**.

    ![Fájlok és mappák konfigurálása](./media/backup-azure-system-state/backup-goal-system-state.png)

    Miután az OK gombra kattint, a **Biztonsági mentés célja** mellett megjelenik egy pipa, és megnyílik **Az infrastruktúra előkészítése** panel.

    ![Ha konfigurálta a biztonsági mentés célját, a következő lépés az infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. **Az infrastruktúra előkészítése** panelen kattintson **A Windows Server- vagy a Windows-ügyfél ügynökének letöltése** elemre.

    ![infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Ha Windows Server Essential verziót használ, akkor a Windows Server Essential ügynökét töltse le. Egy előugró menü rákérdez, hogy futtatni vagy menteni kívánja-e a MARSAgentInstaller.exe fájlt.

    ![MARSAgentInstaller párbeszédpanel](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. A letöltési előugró menüben kattintson a **Mentés** gombra.

    Alapértelmezés szerint az **MARSagentinstaller.exe** fájlt a rendszer a Downloads mappába menti. Amikor a telepítő végzett, megjelenik egy előugró ablak, amely rákérdez, hogy szeretné-e futtatni a telepítőt, vagy megnyitni a mappát.

    ![infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Az ügynököt még nem kell telepíteni. A tároló hitelesítő adatainak letöltése után telepítheti az ügynököt.

6. **Az infrastruktúra előkészítése** panelen kattintson a **Letöltés** elemre.

    ![a tároló hitelesítő adatainak letöltése](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    A tároló hitelesítő adatait a rendszer a Letöltések mappába menti. Miután a tároló hitelesítő adatainak letöltése befejeződött, megjelenik egy előugró ablak, amely rákérdez, hogy szeretné-e megnyitni vagy menteni a hitelesítő adatokat. Kattintson a **Save** (Mentés) gombra. Ha véletlenül a **Megnyitás** gombra kattint, hagyja, hogy sikertelen legyen a párbeszédpanel, amely megpróbálja megnyitni a tároló hitelesítő adatait. A tároló hitelesítő adatai nem nyithatók meg. Folytassa a következő lépéssel. A tároló hitelesítő adatai a Letöltések mappában találhatók.   

    ![a tároló hitelesítő adatainak letöltése befejeződött](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

> [!NOTE]
> A biztonsági mentés engedélyezése az Azure Portalon keresztül még nem érhető el. A Microsoft Azure Recovery Services Agent használatával Windows Server rendszerállapot biztonsági mentését.
>

1. Keresse meg és kattintson duplán az **MARSagentinstaller.exe** fájlra a Letöltések mappában (vagy más mentési helyen).

    A telepítő egy sor üzenetet jelenít meg, miközben kibontja, telepíti és regisztrálja a Recovery Services-ügynököt.

    ![a Recovery Services-ügynök telepítőjének futtatása, hitelesítő adatok](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Végezze el a Microsoft Azure Recovery Services ügynök telepítővarázslójának lépéseit. A varázsló lépéseinek elvégzéséhez a következőket kell tennie:

   * Válasszon egy helyet a telepítés és a gyorsítótár mappája számára.
   * Adja meg a proxykiszolgáló információit, ha proxykiszolgálóval csatlakozik az internethez.
   * Adja meg a felhasználónevének és jelszavának részleteit, ha hitelesített proxyt használ.
   * Adja meg a tároló letöltött hitelesítő adatait.
   * Mentse a titkosítási jelszót egy biztonságos helyen.

     > [!NOTE]
     > Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem tud segíteni az adatok biztonsági másolatának visszaállításában. Mentse a fájlt egy biztonságos helyen. Erre szükség van a biztonsági másolat visszaállításához.
     >
     >

Az ügynök most telepítve van, és a gépe regisztrálva van a tárolóban. Készen áll a biztonsági mentés konfigurálására és ütemezésére.

## <a name="back-up-windows-server-system-state-preview"></a>Készítsen biztonsági másolatot a Windows Server rendszer állapota (előzetes verzió)
A kezdeti biztonsági másolatot a három feladatokból áll:

* Rendszerállapot biztonsági mentésének segítségével az Azure Backup szolgáltatás ügynökének engedélyezése
* A biztonsági mentés ütemezése
* A fájlok és mappák biztonsági mentése első alkalommal

A kezdeti biztonsági mentés végrehajtásához használja a Microsoft Azure Recovery Services-ügynököt.

### <a name="to-enable-system-state-backup-using-the-azure-backup-agent"></a>Rendszerállapot biztonsági mentésének segítségével az Azure Backup szolgáltatás ügynöke engedélyezése

1. Egy PowerShell-munkamenetben futtassa a következő parancs futtatásával állítsa le az Azure Backup motor.

  ```
  PS C:\> Net stop obengine
  ```

2. Nyissa meg a Windows beállításjegyzéket.

  ```
  PS C:\> regedit.exe
  ```

3. Adja hozzá a következő beállításkulcs DWord értéket.

  | Beállításjegyzékbeli elérési út | Beállításkulcs | Duplaszó |
  |---------------|--------------|-------------|
  | HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | TurnOffSSBFeature | 2 |

4. Indítsa újra a biztonsági mentés motor hajtja végre a következő parancsot egy rendszergazda jogú parancssort a.

  ```
  PS C:\> Net start obengine
  ```

### <a name="to-schedule-the-backup-job"></a>A biztonsági mentési feladat ütemezése

1. Nyissa meg a Microsoft Azure Recovery Services-ügynököt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Az Azure Recovery Services-ügynök indítása](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. A Recovery Services-ügynökben kattintson a **Biztonsági mentés ütemezése** gombra.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. A Biztonsági mentés ütemezése varázsló Első lépések oldalán kattintson a **Tovább** gombra.

4. Az Elemek kijelölése biztonsági mentéshez oldalon kattintson az **Elemek hozzáadása** lehetőségre.

5. Válassza ki **rendszerállapot** majd **OK**.

6. Kattintson a **Tovább** gombra.

7. A rendszerállapot biztonsági mentése és a megőrzési ütemezés automatikusan értéke készítsen biztonsági másolatot minden vasárnap 9:00 PM helyi időben, és a megőrzési idő 60 napra van állítva.

   > [!NOTE]
   > Rendszerállapot biztonsági mentési és adatmegőrzési házirend automatikusan konfigurálja. Ha a biztonsági mentést a fájlok és mappák mellett a Windows Server rendszer állapotáról, adja meg, csak a biztonsági mentési és adatmegőrzési házirend a fájl biztonsági mentésekhez, a varázslóból. 
   >

8. A Jóváhagyás lapon ellenőrizze az információkat, majd kattintson a **Befejezés** gombra.

9. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Biztonsági mentése a Windows Server rendszer állapota első alkalommal

1. Győződjön meg arról, hogy nincsenek függőben lévő frissítések a Windows Server, a számítógép újraindítása szükséges.

2. A Recovery Services-ügynökben kattintson a **Biztonsági mentés** gombra a hálózaton keresztüli kezdeti összehangolás befejezéséhez.

    ![Windows Server biztonsági másolat készítése](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. A Jóváhagyás lapon tekintse át azokat a beállításokat, amelyeket a Biztonsági másolat készítése varázsló a gép biztonsági mentéséhez fog használni. Ezután kattintson a **Biztonsági mentés** gombra.

4. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha bezárja a varázslót a biztonsági mentési folyamat befejezése előtt, a varázsló továbbra is fut a háttérben.

5. Ha a kiszolgálón kívül a Windows Server rendszerállapot biztonsági mentése a fájlokat és mappákat a biztonsági mentés most varázsló csak mentésére fájlokat. Hajtsa végre az alkalmi rendszerállapot biztonsági mentése, használja a következő PowerShell-parancsot:

    ```
    PS C:\> Start-OBSystemStateBackup
    ```

  A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

  ![IR befejezve](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Az alábbi kérdések és válaszok kiegészítő információkat.

### <a name="what-is-the-staging-volume"></a>Mi az az átmeneti kötet?

Az átmeneti kötet a köztes helyre, ahol a natív módon, a Windows Server biztonsági másolat készít elő a rendszerállapot biztonsági mentését jelöli. Az Azure Backup szolgáltatás ügynökének majd tömöríti és titkosítja a köztes biztonsági mentési és biztonságos HTTPS protokollon keresztül elküldi a konfigurált Recovery Services-tároló. **Erősen ajánlott kapcsolatot hoz létre az átmeneti köteten nem-Windows-OS köteten. Azt láthatja, hogy a rendszerállapot biztonsági mentéseit problémákat, ha a hibaelhárítás első lépése a átmeneti kötet hely annak ellenőrzése, hogy áll.** 

### <a name="how-can-i-change-the-staging-volume-path-specified-in-the-azure-backup-agent"></a>Hogyan lehet módosítani a átmeneti kötet megadott elérési út az Azure Backup szolgáltatás ügynökének?

Az átmeneti kötet alapértelmezés szerint a gyorsítótár mappában található. 

1. Ez a hely módosításához használja a következő parancsot (a rendszergazda jogú parancssorból):
  ```
  PS C:\> Net stop obengine
  ```

2. Ezután frissítse a következő beállításjegyzék-bejegyzések az új kötet átmeneti mappa elérési útját.

  |Beállításjegyzékbeli elérési út|Beállításkulcs|Érték|
  |-------------|------------|-----|
  |HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | SSBStagingPath | Új átmeneti kötet hely |

Az átmeneti elérési út kis-és nagybetűket, és a pontos azonos kis-és nagybetűk, mi létezik a kiszolgálón kell lennie. 

3. Az átmeneti kötet elérési út módosítása után indítsa újra a biztonsági mentés motor:
  ```
  PS C:\> Net start obengine
  ```
4. A módosított elérési utat átvételéhez, nyissa meg a Microsoft Azure Recovery Services Agent ügynököt, és rendszerállapot ad hoc biztonsági mentés.

### <a name="why-is-the-system-state-default-retention-set-to-60-days"></a>A rendszerállapot alapértelmezett megőrzési miért van beállítva 60 napra?

A rendszerállapot biztonsági mentését élettartama megegyezik a Windows Server Active Directory szerepkör "szemétgyűjtési" beállítást. A törlésre kijelölt élettartama bejegyzés alapértelmezett értéke 60 nap. Ez az érték akkor állíthatók be a címtár szolgáltatást (NTDS) konfigurációs objektum.

### <a name="how-do-i-change-the-default-backup-and-retention-policy-for-system-state"></a>Hogyan módosítható az alapértelmezett biztonsági mentési és adatmegőrzési rendszerállapot?

Az alapértelmezett biztonsági mentési és adatmegőrzési rendszerállapot módosíthatja:
1. Állítsa le a biztonsági mentés motor. A következő parancsot egy emelt szintű parancssorból.

  ```
  PS C:\> Net stop obengine
  ```

2. Adja hozzá, vagy frissítse a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider következő beállításjegyzékbeli kulcs bejegyzéseit.

  |Rendszerleíró adatbázis neve|Leírás|Érték|
  |-------------|-----------|-----|
  |SSBScheduleTime|A biztonsági másolat készítésének idején konfigurálásához használt. Alapértelmezett érték a helyi idő du. 9.|DWord: Formátum HHMM (decimális) például 2130 9:30 PM helyi ideje|
  |SSBScheduleDays|Az nap, amikor a rendszerállapot biztonsági mentését kell végrehajtani, a megadott időpontban konfigurálásához használt. Egyes számjegyeket adja meg a napokat a hét. 0 a vasárnapot jelenti, 1 hétfő, és így tovább. Alapértelmezett napi biztonsági mentéshez a vasárnapot jelenti.|DWord: napokat a hét például 1230 ütemez biztonsági mentések hétfő, kedd, szerda és vasárnap (decimális) biztonsági mentés futtatására.|
  |SSBRetentionDays|A biztonsági mentés napokban konfigurálásához használt. Alapértelmezett érték 60. Megengedett maximális értéket 180.|DWord: Napig szeretné megőrizni a biztonsági mentés (decimális).|

3. A következő paranccsal indítsa újra a biztonsági mentési motor.
    ```
    PS C:\> Net start obengine
    ```

4. Nyissa meg a Microsoft Recovery Services Agent ügynököt.

5. Kattintson a **biztonsági mentés ütemezése** majd **következő** mindaddig, amíg a módosítások megjelenése.

6. Kattintson a **Befejezés** a módosítások életbe léptetéséhez.


## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Következő lépések
* További részletek a [Windows rendszerű gépek biztonsági mentéséről](backup-configure-vault.md).
* Most, hogy biztonsági másolatot készített a fájlokról és mappákról, [kezelheti a tárlókat és a kiszolgálókat](backup-azure-manage-windows-server.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
