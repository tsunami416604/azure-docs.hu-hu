---
title: Fájlok és mappák biztonsági mentése az Azure Backup szolgáltatás ügynökének használatával
description: A Microsoft Azure Backup ügynök használatával Windows-fájlok és mappák biztonsági mentése az Azure-bA. Hozzon létre egy Recovery Services-tárolót, a Backup-ügynök telepítése, a biztonsági mentési házirend meghatározása és a fájlok és mappák a kezdeti biztonsági mentés futtatása.
services: backup
author: rayne-wiselman
manager: carmonm
keywords: Backup-tárolóba; Windows-kiszolgáló biztonsági mentése biztonsági mentési Időablakok;
ms.service: backup
ms.topic: conceptual
ms.date: 8/5/2018
ms.author: raynew
ms.openlocfilehash: 4e138311b58d56f3a188347d43ed12287d43e1bd
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874005"
---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-resource-manager-deployment-model"></a>Windows-kiszolgálóról vagy -ügyfél biztonsági mentése az Azure-ba a Resource Manager-alapú üzemi modell használatával
Ez a cikk azt ismerteti, hogyan készíthet biztonsági másolatot a Windows Server (vagy a Windows-ügyfél) fájljairól és mappáiról az Azure az Azure Backuppal a Resource Manager üzemi modell használatával.

![Biztonsági mentési folyamat lépései](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Előkészületek
Biztonsági mentése a kiszolgáló vagy ügyfél az Azure-ba, szüksége van egy Azure-fiókra. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) mindössze néhány perc alatt.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
Recovery Services-tároló olyan entitás, amely tárolja a biztonsági mentéseket és helyreállítási pontokat hoz létre, idővel. A Recovery Services-tárolót is tartalmaz a alkalmazni a védett fájlok és mappák biztonsági mentési szabályzat. Amikor létrehoz egy Recovery Services-tárolót, is válassza ki a megfelelő beállítását.

### <a name="to-create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-előfizetésével.
2. A központi menüben kattintson a **Minden szolgáltatás** elemre, majd az erőforrások listájában írja be a **Recovery Services** szöveget, és kattintson a **Recovery Services-tárolók** elemre.

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


### <a name="set-storage-redundancy"></a>Tárhely-redundancia beállítása
Amikor először hoz létre Recovery Services-tárolót, meghatározza a tároló replikálásának módját.

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

  Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, folytassa a **georedundáns** beállítás használatát. Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat ebben a [Tárhely-redundancia áttekintésben](../storage/common/storage-redundancy.md).

Most, hogy létrehozott egy tárolót, előkészíti az infrastruktúrát a fájlok és mappák biztonsági mentése letöltésével és a Microsoft Azure Recovery Services-ügynök telepítése, a tároló hitelesítő adatainak letöltése, és ezeket a hitelesítő adatokat segítségével regisztrálhatja az ügynököt a a tároló.

## <a name="configure-the-vault"></a>A tároló konfigurálása

1. A Recovery Services-tároló (az imént létrehozott tároló) paneljén a Bevezetés szakaszban kattintson a **Biztonsági mentés** elemre, majd a **Bevezetés a biztonsági mentés használatába** panelen válassza a **Biztonsági mentés célja** elemet.

  ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

  Megnyílik a **Biztonsági mentés célja** panel. Ha a Recovery Services-tároló korábban lett konfigurálva, akkor a **biztonsági mentés célja** panelek kattintva megnyílik **biztonsági mentés** a Recovery Services-tároló panelen.

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


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

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

## <a name="network-and-connectivity-requirements"></a>Hálózati és kapcsolati követelmények

Ha a gép/proxy korlátozott internet-hozzáféréssel rendelkezik, győződjön meg arról, hogy a gép/proxy a tűzfal beállításait a következő URL-címek vannak konfigurálva: <br>
    1. www.msftncsi.com
    2. *.Microsoft.com
    3. *.WindowsAzure.com
    4. *.microsoftonline.com
    5. *.windows.net


## <a name="create-the-backup-policy"></a>A biztonsági mentési házirend létrehozása
A biztonsági mentési szabályzat, a helyreállítási pontok ütemezése, és mennyi ideig maradnak a helyreállítási pontokat. A fájlok és mappák biztonsági mentési szabályzat létrehozásához használja a Microsoft Azure Backup-ügynök.

### <a name="to-create-a-backup-schedule"></a>Biztonsági mentési ütemezés létrehozása
1. Nyissa meg a Microsoft Azure Backup ügynököt. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Indítsa el az Azure Backup-ügynök](./media/backup-configure-vault/snap-in-search.png)
2. A Backup-ügynök **műveletek** ablaktáblán kattintson a **biztonsági mentés ütemezése** a biztonsági mentés ütemezése varázsló elindításához.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)

3. A a **első lépések** lapon kattintson a biztonsági mentés ütemezése varázsló **tovább**.
4. Az a **elemek kijelölése biztonsági mentéshez** kattintson **elemek hozzáadása**.

  Elemek kiválasztása párbeszédpanel nyílik meg.

5. Válassza ki a fájlokat és mappákat védeni, és kattintson a kívánt **OK**.
6. Az a **elemek kijelölése biztonsági mentéshez** kattintson **tovább**.
7. Az a **biztonsági mentési ütemezés megadása** lapon adja meg a biztonsági mentési ütemezését, és kattintson **tovább**.

    Napi (legfeljebb napi háromszori) vagy heti biztonsági mentéseket ütemezhet.

    ![Windows Server biztonsági mentés elemei](./media/backup-configure-vault/specify-backup-schedule-close.png)

   > [!NOTE]
   > A biztonsági mentés ütemezésének meghatározásával kapcsolatos további információért tekintse meg a [Use Azure Backup to replace your tape infrastructure](backup-azure-backup-cloud-as-tape.md) (Az Azure Backup használata a szalagos infrastruktúra lecseréléséhez) című cikket.
   >
   >

8. Az a **adatmegőrzési szabályzat kijelölése** lapon, válassza ki az adott adatmegőrzési szabályokról a a biztonsági másolatot, majd kattintson a **tovább**.

    A megőrzési házirend megadja az időtartam, amely a biztonsági másolat tárolódik. Ahelyett, hogy mindegyik biztonsági mentési ponthoz „lapos házirendet” határozna meg, különböző megőrzési házirendeket határozhat meg a biztonsági másolat készítésének ideje alapján. Igényei szerint módosíthatja a napi, heti, havi és évi megőrzési házirendeket.
9. A Kezdeti biztonsági mentés típusának kiválasztása oldalon válassza ki a kezdeti biztonsági mentés típusát. Hagyja bejelölve az **Automatikusan a hálózaton keresztül** beállítást, majd kattintson a **Tovább** gombra.

    Automatikusan készíthet biztonsági másolatot a hálózaton keresztül, vagy offline készíthet biztonsági másolatot. Ezen cikk többi része az automatikus biztonsági mentés folyamatát írja le. Ha offline biztonsági mentést szeretne végezni, további információért tekintse meg az [Offline backup workflow in Azure Backup](backup-azure-backup-import-export.md) (Offline biztonsági mentési munkafolyamat az Azure Backupban) című cikket.
10. A Jóváhagyás lapon ellenőrizze az információkat, majd kattintson a **Befejezés** gombra.
11. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

### <a name="enable-network-throttling"></a>Hálózati sávszélesség szabályozásának engedélyezése
A Microsoft Azure Backup szolgáltatás ügynökének biztosít a hálózati sávszélesség-szabályozás. Szabályozás szabályozza a hálózati sávszélesség felhasználásának adatátvitel során. Ez a vezérlő akkor lehet hasznos, ha készítsen biztonsági másolatot kell munkaidő során az adatokat, de nem szeretné, a biztonsági mentési folyamat zavarja a többi internetes forgalmat. Szabályozás biztonsági mentése és a visszaállítást tevékenységekre vonatkozik.

> [!NOTE]
> Hálózati sávszélesség-szabályozás nem áll rendelkezésre a Windows Server 2008 R2 SP1, Windows Server 2008 SP2 vagy Windows 7 (a szervizcsomagok). A szolgáltatás szabályozása az Azure Backup hálózati szolgáltatásminőség (QoS) a helyi operációs rendszer bekövetkezése várható. Bár az Azure Backup védheti az ilyen operációs rendszerek, a QoS ezeken a platformokon elérhető verzióját az Azure Backup hálózati sávszélesség-szabályozás nem működik. Hálózati sávszélesség-szabályozás használható bármely más [támogatott operációs rendszerek](backup-azure-backup-faq.md).
>
>

**A hálózati sávszélesség szabályozásának engedélyezése**

1. A Microsoft Azure Backup-ügynökben kattintson **tulajdonságainak módosítása**.

    ![Tulajdonságok módosítása](./media/backup-configure-vault/change-properties.png)
2. Az a **sávszélesség-szabályozási** lapon jelölje be a **szabályozása a biztonsági mentési műveletek internetes sávszélességének** jelölőnégyzetet.

    ![Hálózati sávszélesség-szabályozás](./media/backup-configure-vault/throttling-dialog.png)
3. Miután engedélyezte a szabályozás, az engedélyezett sávszélesség megadása a biztonsági mentési adatok átvitele során **munkaidő** és **munkaidőn kívüli**.

    A sávszélesség-értékek legalább 512 kilobit / másodperc (Kbps)-kor kezdődik, és folytathatja a akár 1,023 megabájt / másodperc (MBps). Is kijelöli a kezdő és a Befejezés **munkaidő**, és a hét mely napján számít napokon. Óra számítanak a kijelölt munkán óra munkaórákon kívüli.
4. Kattintson az **OK** gombra.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>A fájlok és mappák biztonsági mentése első alkalommal
1. Kattintson a biztonsági mentési ügynök **azonnali biztonsági mentés** a hálózaton keresztüli kezdeti összehangolás befejezéséhez.

    ![Windows Server biztonsági másolat készítése](./media/backup-configure-vault/backup-now.png)
2. A Jóváhagyás lapon tekintse át azokat a beállításokat, amelyeket a Biztonsági másolat készítése varázsló a gép biztonsági mentéséhez fog használni. Ezután kattintson a **Biztonsági mentés** gombra.
3. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha ezt a biztonsági mentési folyamat befejezése előtt teszi, a varázsló továbbra is fut a háttérben.

A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

![IR befejezve](./media/backup-configure-vault/ircomplete.png)

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>További lépések
Virtuális gépek vagy más számítási feladatok biztonsági mentésével kapcsolatos további információkért lásd:

* Most, hogy biztonsági másolatot készített a fájlokról és mappákról, [kezelheti a tárlókat és a kiszolgálókat](backup-azure-manage-windows-server.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
