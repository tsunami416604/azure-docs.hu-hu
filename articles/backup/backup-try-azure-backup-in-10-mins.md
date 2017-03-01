---
title: "Fájlok és mappák biztonsági mentése Windowsról Azure-ba a Resource Manager-alapú üzemi modell segítségével | Microsoft Docs"
description: "Windows-fájlok és -mappák biztonsági mentése az Azure-ba egy Resource Manager-alapú üzemelő példányon."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "biztonsági mentés menete; biztonsági mentési útmutató; fájlok és mappák biztonsági mentése"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/10/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: 1a87af9efeb6c00f3c67f2c2d8d8f2e0491d248d
ms.openlocfilehash: cb464ad823b9123ad0bfa66e0df64a616e3fb5d0


---
# <a name="first-look-back-up-files-and-folders-in-resource-manager-deployment"></a>Áttekintés: Fájlok és mappák biztonsági mentése a Resource Manager-alapú üzemelő példányban
Ez a cikk leírja, hogyan készíthet biztonsági másolatot a Windows Server (vagy Windows-számítógép) fájljairól és mappáiról az Azure-ba Resource Manager-alapú üzemelő példány használatával. Ez az oktatóanyag végigvezeti az alapokon. Ha el szeretné kezdeni az Azure Backup használatát, jó helyen van.

Ha többet szeretne megtudni az Azure Backupról, olvassa el ezt az [áttekintést](backup-introduction-to-azure-backup.md).

A fájlok és mappák az Azure-ba való biztonsági mentéséhez ezeket a tevékenységeket kell elvégezni:

![1. lépés](./media/backup-try-azure-backup-in-10-mins/step-1.png) Azure-előfizetés beszerzése (ha még nem rendelkezik ilyennel).<br>
![2. lépés](./media/backup-try-azure-backup-in-10-mins/step-2.png) Recovery Services-tároló létrehozása.<br>
![3. lépés](./media/backup-try-azure-backup-in-10-mins/step-3.png) A szükséges fájlok letöltése.<br>
![4. lépés](./media/backup-try-azure-backup-in-10-mins/step-4.png) A Recovery Services-ügynök telepítése és regisztrálása.<br>
![5. lépés](./media/backup-try-azure-backup-in-10-mins/step-5.png) Biztonsági másolat készítése a fájlokról és mappákról.

![Windows rendszerű gép biztonsági mentése az Azure Backuppal](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## <a name="get-an-azure-subscription"></a>Azure-előfizetés beszerzése
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

    * válassza az **Új létrehozása** lehetőséget, ha új erőforráscsoportot szeretne létrehozni.
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

    Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, folytassa a **georedundáns** beállítás használatát. Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit. A [georedundáns](../storage/storage-redundancy.md#geo-redundant-storage) és a [helyileg redundáns](../storage/storage-redundancy.md#locally-redundant-storage) tárolási lehetőségekről többet olvashat ebben a [Tárhely-redundancia áttekintésben](../storage/storage-redundancy.md).

A tároló létrehozása után konfigurálja azt a fájlok és mappák biztonsági mentésére.

## <a name="configure-the-vault"></a>A tároló konfigurálása
1. A Recovery Services-tároló (az imént létrehozott tároló) paneljén a Bevezetés szakaszban kattintson a **Biztonsági mentés** elemre, majd a **Bevezetés a biztonsági mentés használatába** panelen válassza a **Biztonsági mentés célja** elemet.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Megnyílik a **Biztonsági mentés célja** panel.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. A **Hol futnak az alkalmazások és szolgáltatások?** legördülő menüből válassza a **Helyszíni** lehetőséget.

    Azért kell a **Helyszíni** lehetőséget választania, mert a Windows Server- vagy Windows-számítógépe egy fizikai gép, amely nem az Azure része.

3. A **Miről szeretne biztonsági másolatot készíteni?** menüben válassza a **Fájlok és mappák** lehetőséget, és kattintson az **OK** gombra.

    ![Fájlok és mappák konfigurálása](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

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
> A biztonsági mentés engedélyezése az Azure Portalon keresztül még nem érhető el. Használja a Microsoft Azure Recovery Services-ügynököt biztonsági másolat készítésére a fájlokról és mappákról.
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

## <a name="back-up-your-files-and-folders"></a>Biztonsági másolat készítése a fájlokról és mappákról
A kezdeti biztonsági mentésbe két fő feladat tartozik:

* A biztonsági mentés ütemezése
* A fájlok és mappák biztonsági mentése első alkalommal

A kezdeti biztonsági mentés végrehajtásához használja a Microsoft Azure Recovery Services-ügynököt.

### <a name="to-schedule-the-backup-job"></a>A biztonsági mentési feladat ütemezése
1. Nyissa meg a Microsoft Azure Recovery Services-ügynököt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Az Azure Recovery Services-ügynök indítása](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)
2. A Recovery Services-ügynökben kattintson a **Biztonsági mentés ütemezése** gombra.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)
3. A Biztonsági mentés ütemezése varázsló Első lépések oldalán kattintson a **Tovább** gombra.
4. Az Elemek kijelölése biztonsági mentéshez oldalon kattintson az **Elemek hozzáadása** lehetőségre.
5. Jelölje ki azokat a fájlokat és mappákat, amelyekről biztonsági másolatot szeretne készíteni, majd kattintson az **OK** gombra.
6. Kattintson a **Tovább** gombra.
7. A **Biztonsági mentés ütemezésének megadása** lapon határozza meg a **biztonsági mentés ütemezését**, és kattintson a **Tovább** gombra.

    Napi (legfeljebb napi háromszori) vagy heti biztonsági mentéseket ütemezhet.

    ![Windows Server biztonsági mentés elemei](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)

   > [!NOTE]
   > A biztonsági mentés ütemezésének meghatározásával kapcsolatos további információért tekintse meg a [Use Azure Backup to replace your tape infrastructure](backup-azure-backup-cloud-as-tape.md) (Az Azure Backup használata a szalagos infrastruktúra lecseréléséhez) című cikket.
   >

8. A **Megőrzési házirend kiválasztása** oldalon válassza ki a biztonsági másolat **megőrzési házirendjét**.

    A megőrzési házirend megadja, hogy mennyi ideig tárolódnak a biztonsági mentési adatok. Ahelyett, hogy mindegyik biztonsági mentési ponthoz „lapos házirendet” határozna meg, különböző megőrzési házirendeket határozhat meg a biztonsági másolat készítésének ideje alapján. Igényei szerint módosíthatja a napi, heti, havi és évi megőrzési házirendeket.
9. A Kezdeti biztonsági mentés típusának kiválasztása oldalon válassza ki a kezdeti biztonsági mentés típusát. Hagyja bejelölve az **Automatikusan a hálózaton keresztül** beállítást, majd kattintson a **Tovább** gombra.

    Automatikusan készíthet biztonsági másolatot a hálózaton keresztül, vagy offline készíthet biztonsági másolatot. Ezen cikk többi része az automatikus biztonsági mentés folyamatát írja le. Ha offline biztonsági mentést szeretne végezni, további információért tekintse meg az [Offline backup workflow in Azure Backup](backup-azure-backup-import-export.md) (Offline biztonsági mentési munkafolyamat az Azure Backupban) című cikket.
10. A Jóváhagyás lapon ellenőrizze az információkat, majd kattintson a **Befejezés** gombra.
11. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>A fájlok és mappák biztonsági mentése első alkalommal
1. A Recovery Services-ügynökben kattintson a **Biztonsági mentés** gombra a hálózaton keresztüli kezdeti összehangolás befejezéséhez.

    ![Windows Server biztonsági másolat készítése](./media/backup-try-azure-backup-in-10-mins/backup-now.png)
2. A Jóváhagyás lapon tekintse át azokat a beállításokat, amelyeket a Biztonsági másolat készítése varázsló a gép biztonsági mentéséhez fog használni. Ezután kattintson a **Biztonsági mentés** gombra.
3. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha bezárja a varázslót a biztonsági mentési folyamat befejezése előtt, a varázsló továbbra is fut a háttérben.

A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

![IR befejezve](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Következő lépések
* További részletek a [Windows rendszerű gépek biztonsági mentéséről](backup-configure-vault.md).
* Most, hogy biztonsági másolatot készített a fájlokról és mappákról, [kezelheti a tárlókat és a kiszolgálókat](backup-azure-manage-windows-server.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).



<!--HONumber=Feb17_HO3-->


