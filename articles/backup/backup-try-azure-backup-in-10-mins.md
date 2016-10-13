<properties
   pageTitle="Fájlok és mappák biztonsági mentése Windowsról Azure-ba az Azure Backuppal a Resource Manager-alapú üzemi modell segítségével | Microsoft Azure"
   description="Megtudhatja, hogyan készíthet biztonsági másolatot a Windows Server-adatokról egy tároló létrehozásával, a Recovery Services-ügynök telepítésével és a fájlok és mappák biztonsági másolatának az Azure-ba való elkészítésével."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="cfreeman"
   editor=""
   keywords="biztonsági mentés menete; biztonsági mentési útmutató"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="09/27/2016"
   ms.author="markgal;"/>


# Áttekintés: Fájlok és mappák biztonsági mentése az Azure Backuppal a Resource Manager-alapú üzemi modell segítségével

Ez a cikk leírja, hogyan készíthet biztonsági másolatot a Windows Server (vagy Windows-ügyfél) fájljairól és mappáiról az Azure-ba az Azure Backuppal a Resource Manager használatával. Ez az oktatóanyag végigvezeti az alapokon. Ha el szeretné kezdeni az Azure Backup használatát, jó helyen van.

Ha többet szeretne megtudni az Azure Backupról, olvassa el ezt az [áttekintést](backup-introduction-to-azure-backup.md).

A fájlok és mappák az Azure-ba való biztonsági mentéséhez ezeket a tevékenységeket kell elvégezni:

![1. lépés ](./media/backup-try-azure-backup-in-10-mins/step-1.png) Azure-előfizetés beszerzése (ha még nem rendelkezik ilyennel).<br>
![2. lépés](./media/backup-try-azure-backup-in-10-mins/step-2.png) Recovery Services-tároló létrehozása.<br>
![3. lépés](./media/backup-try-azure-backup-in-10-mins/step-3.png) A szükséges fájlok letöltése.<br>
![4. lépés](./media/backup-try-azure-backup-in-10-mins/step-4.png) A Recovery Services-ügynök telepítése és regisztrálása.<br>
![5. lépés](./media/backup-try-azure-backup-in-10-mins/step-5.png) Biztonsági másolat készítése a fájlokról és mappákról.

![Windows rendszerű gép biztonsági mentése az Azure Backuppal](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## 1. lépés: Azure-előfizetés beszerzése

Ha még nincs Azure-előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/), amellyel bármely Azure-szolgáltatást elérhet.

## 2. lépés: Recovery Services-tároló létrehozása

A fájlok és mappák biztonsági mentéséhez létre kell hoznia egy Recovery Services-tárolót abban a régióban, ahol az adatokat tárolni szeretné. Emellett a tároló replikálásának módját is meg kell határoznia.

### Recovery Services-tároló létrehozása

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-előfizetésével.

2. A központi menüben kattintson a **Tallózás** elemre, majd az erőforrások listájában írja be a **Recovery Services** szöveget, és kattintson a **Recovery Services-tárolók** elemre.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-try-azure-backup-in-10-mins/browse-to-rs-vaults.png) <br/>

3. A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** elemre.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Megnyílik a Recovery Services-tároló panelje, a rendszer pedig egy **Név**, **Előfizetés**, **Erőforráscsoport** és **Hely** megadását kéri.

    ![Recovery Services-tároló létrehozása – 5. lépés](./media/backup-try-azure-backup-in-10-mins/rs-vault-attributes.png)

4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához.

5. Kattintson az **Előfizetés** elemre az elérhető előfizetések listájának megtekintéséhez.

6. Kattintson az **Erőforráscsoport** elemre az elérhető erőforráscsoportok listájának megtekintéséhez, vagy kattintson az **Új** elemre egy új erőforráscsoport létrehozásához.

7. Kattintson a **Hely** elemre a tárolóhoz tartozó földrajzi régió kiválasztásához. Ez a választás határozza meg a földrajzi régiót, ahová az adatok biztonsági másolata el lesz küldve.

8. Kattintson a **Létrehozás** gombra.

    Ha nem látja a tárolót a listában a művelet befejezése után, kattintson a **Frissítés** gombra. Amikor a lista frissül, kattintson a tároló nevére.

### A tárhely-redundancia meghatározása
Amikor először hoz létre Recovery Services-tárolót, meghatározza a tároló replikálásának módját.

1. Kattintson az új tárolóra az irányítópult megnyitásához.

2. A **Beállítások** panelen, amely a tároló irányítópultjával automatikusan megnyílik, kattintson a **Biztonsági mentési infrastruktúra** elemre.

3. A Biztonsági mentési infrastruktúra panelen kattintson a **Biztonsági mentési konfiguráció** elemre a **Tárolóreplikáció típusa** megtekintéséhez.

    ![Recovery Services-tároló létrehozása – 5. lépés](./media/backup-try-azure-backup-in-10-mins/backup-infrastructure.png)

4. Válassza ki a megfelelő tárolóreplikációs beállítást a tárolóhoz.

    ![A Recovery Services-tárolók listája](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, folytassa a georedundáns tárolás használatát. Ha az Azure-t nem elsődleges biztonsági mentési tároló végpontjaként használja, akkor válassza a helyileg redundáns tárolást, amely csökkenti az adatok az Azure-ban való tárolásának költségeit. A [georedundáns](../storage/storage-redundancy.md#geo-redundant-storage) és a [helyileg redundáns](../storage/storage-redundancy.md#locally-redundant-storage) tárolási lehetőségekről többet olvashat ebben az [áttekintésben](../storage/storage-redundancy.md).

Most, hogy létrehozott egy tárolót, előkészíti az infrastruktúrát a fájlok és mappák biztonsági mentéséhez. Ehhez letölti a Microsoft Azure Recovery Services-ügynököt és a tároló hitelesítő adatait.

## 3. lépés – Fájlok letöltése

1. A Recovery Services-tároló irányítópultján kattintson a **Beállítások** elemre.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/settings-button.png)

2. Kattintson az **Első lépések > Biztonsági mentés** elemre a Beállítások panelen.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/getting-started-backup.png)

3. Kattintson **A biztonsági mentés célja** elemre a Biztonsági mentés panelen.

    ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/backup-goal.png)

4. A Hol futnak az alkalmazások és szolgáltatások? menüből válassza a **Helyszíni** lehetőséget.

5. A Miről szeretne biztonsági másolatot készíteni? menüben válassza a **Fájlok és mappák** lehetőséget, és kattintson az **OK** gombra.

### A Recovery Services-ügynök letöltése

1. **Az infrastruktúra előkészítése** panelen kattintson **A Windows Server vagy a Windows ügyfél ügynökének letöltése** elemre.

    ![infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-short.png)

2. Kattintson a **Mentés** gombra a letöltési előugró menüben. Alapértelmezés szerint az **MARSagentinstaller.exe** fájlt a rendszer a Downloads mappába menti.

### A tároló hitelesítő adatainak letöltése

1. Kattintson a **Letöltés > Mentés** gombra Az infrastruktúra előkészítése panelen.

    ![infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-download.png)

## 4. lépés – Az ügynök telepítése és regisztrálása

>[AZURE.NOTE] A biztonsági mentés Azure Portalon keresztüli engedélyezése hamarosan elérhető lesz. Jelenleg a Microsoft Azure Recovery Services-ügynökkel a helyszínen készít biztonsági másolatokról a fájlokról és mappákról.

1. Keresse meg és kattintson duplán az **MARSagentinstaller.exe** fájlra a Downloads mappában (vagy más mentési helyen).

2. Végezze el a Microsoft Azure Recovery Services ügynök telepítővarázslójának lépéseit. A varázsló lépéseinek elvégzéséhez a következőket kell tennie:

    - Válasszon egy helyet a telepítés és a gyorsítótár mappája számára.
    - Adja meg a proxykiszolgáló információit, ha proxykiszolgálóval csatlakozik az internethez.
    - Adja meg a felhasználónevének és jelszavának részleteit, ha hitelesített proxyt használ.
    - Adja meg a tároló letöltött hitelesítő adatait.
    - Mentse a titkosítási jelszót egy biztonságos helyen.

    >[AZURE.NOTE] Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem tud segíteni az adatok biztonsági másolatának visszaállításában. Mentse a fájlt egy biztonságos helyen. Erre szükség van a biztonsági másolat visszaállításához.

Az ügynök most telepítve van, és a gépe regisztrálva van a tárolóban. Készen áll a biztonsági mentés konfigurálására és ütemezésére.

## 5. lépés: Biztonsági másolat készítése a fájlokról és mappákról

A kezdeti biztonsági mentésbe két fő feladat tartozik:

- A biztonsági mentés ütemezése
- A fájlok és mappák biztonsági mentése első alkalommal

A kezdeti biztonsági mentés végrehajtásához a Microsoft Azure Recovery Services-ügynököt használja.

### A biztonsági mentés ütemezése

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

    >[AZURE.NOTE] A biztonsági mentés ütemezésének meghatározásával kapcsolatos további információért tekintse meg a [Use Azure Backup to replace your tape infrastructure](backup-azure-backup-cloud-as-tape.md) (Az Azure Backup használata a szalagos infrastruktúra lecseréléséhez) című cikket.

8. A **Megőrzési házirend kiválasztása** oldalon válassza ki a biztonsági másolat **megőrzési házirendjét**.

    A megőrzési házirend határozza meg a biztonsági másolat tárolásának időtartamát. Ahelyett, hogy mindegyik biztonsági mentési ponthoz „lapos házirendet” határozna meg, különböző megőrzési házirendeket határozhat meg a biztonsági másolat készítésének ideje alapján. Igényei szerint módosíthatja a napi, heti, havi és évi megőrzési házirendeket.

9. A Kezdeti biztonsági mentés típusának kiválasztása oldalon válassza ki a kezdeti biztonsági mentés típusát. Hagyja bejelölve az **Automatikusan a hálózaton keresztül** beállítást, majd kattintson a **Tovább** gombra.

    Automatikusan készíthet biztonsági másolatot a hálózaton keresztül, vagy offline készíthet biztonsági másolatot. Ezen cikk többi része az automatikus biztonsági mentés folyamatát írja le. Ha offline biztonsági mentést szeretne végezni, további információért tekintse meg az [Offline backup workflow in Azure Backup](backup-azure-backup-import-export.md) (Offline biztonsági mentési munkafolyamat az Azure Backupban) című cikket.

10. A Jóváhagyás lapon ellenőrizze az információkat, majd kattintson a **Befejezés** gombra.

11. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

### A fájlok és mappák biztonsági mentése első alkalommal

1. A Recovery Services-ügynökben kattintson a **Biztonsági mentés** gombra a hálózaton keresztüli kezdeti összehangolás befejezéséhez.

    ![Windows Server biztonsági másolat készítése](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

2. A Jóváhagyás lapon tekintse át azokat a beállításokat, amelyeket a Biztonsági másolat készítése varázsló a gép biztonsági mentéséhez fog használni. Ezután kattintson a **Biztonsági mentés** gombra.

3. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha ezt a biztonsági mentési folyamat befejezése előtt teszi, a varázsló továbbra is fut a háttérben.

A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

![IR befejezve](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).

## Következő lépések
- További részletek a [Windows rendszerű gépek biztonsági mentéséről](backup-configure-vault.md).
- Most, hogy biztonsági másolatot készített a fájlokról és mappákról, [kezelheti a tárlókat és a kiszolgálókat](backup-azure-manage-windows-server.md).
- Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).



<!--HONumber=Sep16_HO4-->


