---
title: "Azure Backup-ügynököt fájlok és mappák biztonsági mentése |} Microsoft Docs"
description: "A Microsoft Azure Backup szolgáltatás ügynökének használatával Windows-fájlok és mappák biztonsági mentése az Azure-bA. Recovery Services-tároló létrehozása, a biztonsági mentési ügynök telepítése, a biztonsági mentési házirend meghatározása és a fájlok és mappák a kezdeti biztonsági mentés futtatására."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "mentési tároló; Készítsen biztonsági másolatot a Windows server; biztonsági mentési Időablakok;"
ms.assetid: 7f5b1943-b3c1-4ddb-8fb7-3560533c68d5
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 947b985fe242c026e94d78664fa41f666334392a
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-resource-manager-deployment-model"></a>Windows-kiszolgálóról vagy -ügyfél biztonsági mentése az Azure-ba a Resource Manager-alapú üzemi modell használatával
Ez a cikk azt ismerteti, hogyan biztonsági másolatot készíthet a Windows Server (és a Windows-ügyfél) fájlok és mappák Azure Resource Manager telepítési modellel Azure Backup szolgáltatásnál.

[!INCLUDE [learn-about-deployment-models](../../includes/backup-deployment-models.md)]

![Biztonsági mentési folyamat lépései](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Előkészületek
Biztonsági mentése a kiszolgáló vagy az ügyfél az Azure-ba, az Azure-fiók szükséges. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) néhány percig.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
Recovery Services-tároló olyan entitás, amely tárolja a biztonsági mentések és a helyreállítási pontokat hoz létre adott idő alatt. A Recovery Services-tároló alkalmazott a védett fájlok és mappák biztonsági mentési házirendet is tartalmaz. Recovery Services-tároló létrehozásakor is válassza ki a megfelelő tárolási redundancia lehetőséget.

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


### <a name="set-storage-redundancy"></a>Set adattároló redundanciája, amely
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

  Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, folytassa a **georedundáns** beállítás használatát. Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit. A [georedundáns](../storage/common/storage-redundancy.md#geo-redundant-storage) és a [helyileg redundáns](../storage/common/storage-redundancy.md#locally-redundant-storage) tárolási lehetőségekről többet olvashat ebben a [Tárhely-redundancia áttekintésben](../storage/common/storage-redundancy.md).

Most, hogy létrehozta a tárolót, készítse elő az infrastruktúrát, letöltése és telepítése a Microsoft Azure Recovery Services Agent ügynököt, töltse le a tároló és ezen hitelesítő adatok használatával regisztrálhatja az ügynököt a fájlok és mappák biztonsági a tároló.

## <a name="configure-the-vault"></a>A tároló konfigurálása

1. A Recovery Services-tároló (az imént létrehozott tároló) paneljén a Bevezetés szakaszban kattintson a **Biztonsági mentés** elemre, majd a **Bevezetés a biztonsági mentés használatába** panelen válassza a **Biztonsági mentés célja** elemet.

  ![A biztonsági mentés célja panel megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

  Megnyílik a **Biztonsági mentés célja** panel. Ha a Recovery Services-tároló korábban lett konfigurálva, majd a **biztonsági mentési cél** paneleken kattintva megjelenik **biztonsági mentés** a Recovery Services tároló panel.

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

## <a name="network-and-connectivity-requirements"></a>Hálózati és kapcsolati követelmények

A machine /-proxy korlátozott internet-hozzáférést, ha győződjön meg arról, hogy tűzfal beállításait a machine /-proxy a engedélyezése a következő URL-címekkel vannak konfigurálva: <br>
    1. www.msftncsi.com
    2. *.Microsoft.com
    3. *.WindowsAzure.com
    4. *.microsoftonline.com
    5. *.windows.net


## <a name="create-the-backup-policy"></a>A biztonsági mentési házirend létrehozása
A biztonsági mentési házirend az ütemezés, ha a helyreállítási pontokat készít, és mennyi ideig megőrzi a helyreállítási pontok. A fájlok és mappák biztonsági mentési házirend létrehozásához használja a Microsoft Azure Backup szolgáltatás ügynöke.

### <a name="to-create-a-backup-schedule"></a>Biztonsági mentési ütemezés létrehozása
1. Nyissa meg a Microsoft Azure Backup szolgáltatás ügynöke. A megkereséséhez keressen rá a gépen a **Microsoft Azure Backup** kifejezésre.

    ![Indítsa el az Azure Backup szolgáltatás ügynöke](./media/backup-configure-vault/snap-in-search.png)
2. A biztonsági mentési ügynök **műveletek** ablaktáblán kattintson a **biztonsági mentés ütemezése** biztonsági mentés ütemezése varázsló indításához.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)

3. A a **bevezetés** lap az ütemezett biztonsági mentés varázsló kattintson **következő**.
4. Az a **elemek kijelölése biztonsági mentéshez** kattintson **elemek hozzáadása**.

  Az elemek kijelölése párbeszédpanel nyílik meg.

5. Válassza ki a fájlokat és mappákat védeni, és kattintson a kívánt **OK**.
6. Az a **elemek kijelölése biztonsági mentéshez** kattintson **következő**.
7. Az a **adja meg a biztonsági mentés ütemezése** lapon adja meg a biztonsági mentési ütemezést, és kattintson **következő**.

    Napi (legfeljebb napi háromszori) vagy heti biztonsági mentéseket ütemezhet.

    ![Windows Server biztonsági mentés elemei](./media/backup-configure-vault/specify-backup-schedule-close.png)

   > [!NOTE]
   > A biztonsági mentés ütemezésének meghatározásával kapcsolatos további információért tekintse meg a [Use Azure Backup to replace your tape infrastructure](backup-azure-backup-cloud-as-tape.md) (Az Azure Backup használata a szalagos infrastruktúra lecseréléséhez) című cikket.
   >
   >

8. A a **válassza ki az adatmegőrzési** lapon, válassza ki az adott adatmegőrzési szabályokról a a biztonsági másolatot, majd kattintson a **következő**.

    Az adatmegőrzési Megadja azt az időtartamot, amely a biztonsági másolatot. Ahelyett, hogy mindegyik biztonsági mentési ponthoz „lapos házirendet” határozna meg, különböző megőrzési házirendeket határozhat meg a biztonsági másolat készítésének ideje alapján. Igényei szerint módosíthatja a napi, heti, havi és évi megőrzési házirendeket.
9. A Kezdeti biztonsági mentés típusának kiválasztása oldalon válassza ki a kezdeti biztonsági mentés típusát. Hagyja bejelölve az **Automatikusan a hálózaton keresztül** beállítást, majd kattintson a **Tovább** gombra.

    Automatikusan készíthet biztonsági másolatot a hálózaton keresztül, vagy offline készíthet biztonsági másolatot. Ezen cikk többi része az automatikus biztonsági mentés folyamatát írja le. Ha offline biztonsági mentést szeretne végezni, további információért tekintse meg az [Offline backup workflow in Azure Backup](backup-azure-backup-import-export.md) (Offline biztonsági mentési munkafolyamat az Azure Backupban) című cikket.
10. A Jóváhagyás lapon ellenőrizze az információkat, majd kattintson a **Befejezés** gombra.
11. Miután a varázsló befejezte a biztonsági mentési ütemezés létrehozását, kattintson a **Bezárás** gombra.

### <a name="enable-network-throttling"></a>Hálózati sávszélesség-szabályozás engedélyezése
A Microsoft Azure Backup szolgáltatás ügynökének biztosít a hálózati sávszélesség-szabályozás. Szabályozza, hogyan adatátvitel során használt hálózati sávszélesség-szabályozás. Ez a vezérlő akkor lehet hasznos, ha biztonsági kell során az adatokat munkaidő, de nem szeretné a biztonsági mentési folyamat zavarja a más internetes forgalmat. Sávszélesség-szabályozás biztonsági mentése és visszaállítása tevékenységek vonatkozik.

> [!NOTE]
> Hálózati sávszélesség-szabályozás nem érhető el a Windows Server 2008 R2 SP1, Windows Server 2008 SP2 vagy Windows 7 (szervizcsomagokkal). Az Azure biztonsági mentési hálózati szabályozásával szolgáltatásminőség (QoS) a helyi operációs rendszer kapcsolatba lép. Bár az Azure Backup védheti az ilyen operációs rendszerek, QoS érhető el, ezek a rendszerek verziójának Azure biztonsági mentési hálózati sávszélesség-szabályozás nem működik. Az összes egyéb hálózati sávszélesség-szabályozás használható [támogatott operációs rendszerek](backup-azure-backup-faq.md).
>
>

**Hálózati sávszélesség-szabályozás engedélyezése**

1. Kattintson a Microsoft Azure Backup szolgáltatás ügynökének **tulajdonságainak módosítása**.

    ![Tulajdonságainak módosítása](./media/backup-configure-vault/change-properties.png)
2. Az a **sávszélesség-szabályozási** lapon jelölje be a **engedélyezi az internetes sávszélesség szabályozásának a biztonsági mentési műveleteknél** jelölőnégyzetet.

    ![Hálózati sávszélesség-szabályozás](./media/backup-configure-vault/throttling-dialog.png)
3. Miután engedélyezte a sávszélesség-szabályozás, adja meg az engedélyezett sávszélesség vonatkozó biztonsági mentési adatátvitel során **időpontokat a munkaidőhöz** és **munkaidőn kívüli**.

    A sávszélesség értékek 512 kilobit / másodperc (Kbps) kezdődik, és folytathatja a legfeljebb 1,023 megabájt / másodperc (MBps). Is kijelölni a kezdő és a Befejezés **időpontokat a munkaidőhöz**, és a hét melyik napjain figyelembe vett munkanapok. Óra között útmutatóul szolgálnak a kijelölt munkahelyi kívül óra munkaórákon kívüli időre.
4. Kattintson az **OK** gombra.

### <a name="to-back-up-files-and-folders-for-the-first-time"></a>A fájlok és mappák biztonsági mentése első alkalommal
1. Kattintson a biztonságimásolat-készítő ügynök **biztonsági másolat készítése most** befejeződik, a kezdeti összehangolása a hálózaton keresztül.

    ![Windows Server biztonsági másolat készítése](./media/backup-configure-vault/backup-now.png)
2. A Jóváhagyás lapon tekintse át azokat a beállításokat, amelyeket a Biztonsági másolat készítése varázsló a gép biztonsági mentéséhez fog használni. Ezután kattintson a **Biztonsági mentés** gombra.
3. A varázsló bezárásához kattintson a **Bezárás** gombra. Ha ezt a biztonsági mentési folyamat befejezése előtt teszi, a varázsló továbbra is fut a háttérben.

A kezdeti biztonsági mentés befejezése után a **Feladat befejezve** állapot jelenik meg a biztonsági mentési konzolon.

![IR befejezve](./media/backup-configure-vault/ircomplete.png)

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Következő lépések
Virtuális gépek vagy más munkaterhelések biztonsági mentésével kapcsolatos további információkért lásd:

* Most, hogy biztonsági másolatot készített a fájlokról és mappákról, [kezelheti a tárlókat és a kiszolgálókat](backup-azure-manage-windows-server.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
