---
title: "Az Azure recovery services-tárolók és a kiszolgálók felügyeletére |} Microsoft Docs"
description: "Ez az oktatóanyag segítségével megtudhatja, hogyan az Azure recovery services-tárolók és a kiszolgálók kezelésére."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: 4eea984b-7ed6-4600-ac60-99d2e9cb6d8a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: markgal
ms.openlocfilehash: 5922e308f5c205a07bd329c28322ae82cea0e1fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Windows-gépek Azure Recovery Services-tárolóinak és -kiszolgálóinak figyelése és kezelése
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-manage-windows-server.md)
> * [Klasszikus](backup-azure-manage-windows-server-classic.md)
>
>

Ebben a cikkben az Azure-portál és a Microsoft Azure Backup szolgáltatás ügynöke elérhető biztonsági mentési figyelő és a felügyeleti feladatok áttekintést találhat. Ez a cikk azt feltételezi, hogy már rendelkezik Azure-előfizetéssel, és legalább egy Recovery Services-tároló létrehozása.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Nyissa meg a Recovery Services-tároló

A Recovery Services-tároló irányítópult látható a részleteit, illetve a Recovery Services-tároló attribútumait.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) használata az Azure-előfizetéshez.
2. A központ menüben kattintson a **több szolgáltatások**.

    ![Recovery Services-tárolók 1.lépés listájának megnyitása](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

3. Nyissa meg a Recovery Services-tároló szeretné. A párbeszédpanelen, kezdje el begépelni **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Recovery Services-tárolók** megjelenítse a Recovery Services-tárolók az előfizetésben.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-manage-windows-server/browse-to-rs-vaults-2.png) <br/>

    A Recovery Services-tárolók listáját nyitja meg.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Tárolók a listából válassza ki a megnyitni kívánt Recovery Services-tároló nevét. Ekkor megnyílik a Recovery Services-tároló irányítópult panel.

    ![helyreállítási szolgáltatások tároló irányítópult](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Most, hogy a Recovery Services-tároló nyitotta meg, próbálkozzon a figyelést, vagy a felügyeleti feladatokat a.

## <a name="monitor-backup-jobs-and-alerts"></a>A figyelő biztonsági mentési feladatok és riasztások

Figyelheti a feladatok és riasztások az irányítópultról Recovery Services tárolóban, ahol láthatja:

* Biztonsági mentési riasztás részletei
* Fájlok és mappák, valamint az Azure virtuális gép a felhőben védelme
* Teljes Azure-ban felhasznált tárterület
* Biztonsági mentési feladat állapota

![Biztonsági mentési irányítópult feladatok](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Minden egyes ezen csempék az információk kattintva megnyílik a társított panel, ahol a kapcsolódó feladatok kezelése.

Az irányítópult tetején:

* Beállítások hozzáférést biztosít az elérhető biztonsági mentési feladatokat.
* A biztonsági mentés - biztonsági másolatot készíteni az új fájlok és mappák (vagy Azure virtuális gépek) számára a Recovery Services-tároló segítségével.
* Törlés – a recovery services-tároló már nem használatos, ha törlése tárhely felszabadítása érdekében. Törlés csak akkor engedélyezett, miután az összes védett kiszolgálón, hogy törölték a tárolóból.

![Biztonsági mentési irányítópult feladatok](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>A biztonsági mentéseket az Azure Backup szolgáltatás ügynöke riasztások:
| Riasztási szint | Értesítések küldése |
| --- | --- |
| Kritikus |Sikertelen biztonsági mentéshez, helyreállítási hiba |
| Figyelmeztetés |A biztonsági mentés befejeződött, de figyelmeztetéseket generált (Ha kevesebb mint száz fájlok nem készül biztonsági mentés okozta problémák miatt, és több mint egymillió fájlok sikeresen biztonsági mentése) |
| Tájékoztató |None |

## <a name="manage-backup-alerts"></a>Biztonsági riasztások kezelése
Kattintson a **biztonsági mentési riasztások** csempére kattintva nyissa meg a **biztonsági mentési riasztások** panel és kezelheti a riasztásokat.

![Biztonsági mentési riasztás](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

A biztonsági riasztások csempén a számát mutatja:

* kritikus riasztás feloldva a legutóbbi 24 órában
* az elmúlt 24 órában feloldatlan figyelmeztető riasztások

Kattintson a valamennyi kapcsolatot tart, hogy a **biztonsági mentési riasztások** szűrt láthassák ezeket a riasztásokat (kritikus vagy figyelmeztetési) panelen.

A biztonsági mentési riasztások paneljéről meg:

* Válassza ki a megfelelő információkkal együtt a riasztásokat.

    ![Oszlopok kiválasztása](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* Riasztások szűrése a súlyosság, állapota és a kezdő és záró időpont.

    ![Riasztások szűrése](./media/backup-azure-manage-windows-server/filter-alerts.png)
* Súlyosság, a gyakoriság és a címzettek értesítések konfigurálása, valamint figyelmeztetések engedélyezése vagy letiltása.

    ![Riasztások szűrése](./media/backup-azure-manage-windows-server/configure-notifications.png)

Ha **/ riasztási** választotta a **értesítendő** gyakoriság nincs csoportosítás vagy e-mailek csökkenése történik. Minden egyes riasztás 1 értesítési eredményez. Ez az alapértelmezett beállítás, és a feloldási e-mailt is küld azonnal.

Ha **óránkénti kivonatoló** választotta a **értesítendő** egy e-mailt küld a felhasználó közölve, hogy van a gyakoriság feloldatlan új riasztások jönnek létre az elmúlt órában. A feloldási e-mailt küld az órát végén.

Is elküldi a riasztásokat a következő súlyossági szintek:

* Kritikus
* Figyelmeztetés
* Információ

A riasztás inaktiválja a **inaktiválása** gombot a projekt részleteit megjelenítő panelen. Kattintva inaktiválása, megadhatja a feloldási megjegyzések.

Úgy dönt, hogy az oszlopok, akkor jelenik meg a riasztás részeként a **oszlopok kiválasztása** gombra.

> [!NOTE]
> Az a **beállítások** panelen kezelheti a biztonsági mentési riasztás kiválasztásával **figyelés és jelentéskészítés > riasztások és események > biztonsági mentésekkel kapcsolatos riasztások** , majd kattintson **szűrő** vagy  **Értesítések konfigurálása**.
>
>

## <a name="manage-backup-items"></a>Biztonsági mentés elemek kezelése
A helyi biztonsági kezelése már elérhető a felügyeleti portálon. A biztonsági mentés szakaszában az irányítópulton a **biztonsági másolati elemei** csempe a tárolóba védett biztonsági másolati elemei számát jeleníti meg.

Kattintson a **-mappákban** a biztonsági mentés elemeknél csempére.

![Biztonsági másolati elemei csempe](./media/backup-azure-manage-windows-server/backup-items-tile.png)

A biztonsági másolati elemei panel nyílik meg a szűrő beállítása fájlok és mappák, ahol minden egyes konkrét biztonsági másolat felsorolt elem látható.

![Biztonsági másolati elemei](./media/backup-azure-manage-windows-server/backup-item-list.png)

Ha egy adott biztonsági mentési elemet a listáról válassza ki, alapvető részletes adatainak megadása, hogy az elem látható.

> [!NOTE]
> Az a **beállítások** panelen, kezelheti a fájlok és mappák kiválasztásával **védett elemek > biztonsági mentés elemek** és jelölje be **-mappákban** a legördülő menüből.
>
>

![A beállítások biztonsági másolati elemei](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Biztonsági mentési feladatok kezelése
Biztonsági mentés mind a helyszíni feladatokat, (Ha a helyszíni kiszolgáló biztonsági mentése az Azure-bA), és az Azure biztonsági mentések láthatók az irányítópulton.

Az irányítópult a biztonsági mentés területen a biztonsági mentési feladat csempe feladatok számát jeleníti meg:

* folyamatban van
* nem sikerült az elmúlt 24 órában.

A biztonsági mentési feladatok kezeléséhez kattintson a **biztonsági mentési feladatok** csempe, amely a biztonsági mentési feladatok paneljének megnyitása.

![A beállítások biztonsági másolati elemei](./media/backup-azure-manage-windows-server/backup-jobs.png)

A biztonsági mentési feladatok panelről a rendelkezésre álló információk módosítása a **oszlopok kiválasztása** gombra az oldal tetején.

Használja a **szűrő** gombra, majd a fájlok és mappák és az Azure virtuális gép biztonsági mentése között.

Ha nem látja a biztonsági mentés fájlokat és mappákat, kattintson a **szűrő** gombbal és válassza ki **fájlok és mappák** a elemtípus menüből.

> [!NOTE]
> Az a **beállítások** panelen kiválasztásával kezelheti a biztonsági mentési feladatok **figyelés és jelentéskészítés > feladatok > biztonsági mentési feladatok** és jelölje be **-mappákban** a legördülő menüben menü.
>
>

## <a name="monitor-backup-usage"></a>Biztonsági másolat használatának figyelése
Az irányítópult a biztonsági mentés területen a biztonsági mentés használata csempe az Azure-ban felhasznált tárhely jeleníti meg. Tárhelyhasználatot biztosított:

* A tárolóhoz rendelt felhőalapú LRS-tároló használata
* A felhőalapú Georedundáns tárolás használata a tárolóhoz rendelt

## <a name="manage-your-production-servers"></a>Az üzemi kiszolgálók kezelése
Az üzemi kiszolgálók kezeléséhez kattintson **beállítások**.

Kattintson a kezelés **biztonsági infrastruktúra > az üzemi kiszolgálók**.

A rendelkezésre álló üzemi kiszolgálók az üzemi kiszolgálók panel listáját. Kattintson egy kiszolgálón nyissa meg a részletek a listában.

![Védett elemek](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Nyissa meg az Azure Backup szolgáltatás ügynöke
Nyissa meg a **Microsoft Azure Backup szolgáltatás ügynökének** (rákeresve a gépen található *a Microsoft Azure Backup szolgáltatás*).

![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/snap-in-search.png)

Az a **műveletek** érhető el a biztonságimásolat-készítő ügynök konzol jobb akkor hajtsa végre az alábbi felügyeleti feladatokat:

* Kiszolgáló regisztrálása
* Biztonsági mentés ütemezése
* Biztonsági másolat készítése
* Tulajdonságainak módosítása

![A Microsoft Azure Backup szolgáltatás ügynöke konzol műveletek](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> A **adatok helyreállítása**, lásd: [fájlokat állíthatja vissza a Windows server vagy a Windows-ügyfélszámítógép](backup-azure-restore-windows-server.md).
>
>

## <a name="modify-the-backup-schedule"></a>A biztonsági mentési ütemezés módosítása
1. Kattintson a Microsoft Azure Backup szolgáltatás ügynökének **biztonsági mentés ütemezése**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. Az a **ütemezett biztonsági mentés varázsló** hagyja a **biztonsági másolati elemei vagy időpontok szerepelnek módosítása** jelölőnégyzetet és kattintson **következő**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. Ha szeretné-e hozzáadása vagy módosítása elemek, a a **elemek kijelölése biztonsági mentéshez** képernyőn kattintson **elemek hozzáadása**.

    Azt is beállíthatja **kizárások beállításai** ezen a lapon, a varázslóban. Ha ki szeretné zárni a fájlok vagy fájltípusok olvassa el, hogyan adhat hozzá [kizárások beállításai](#manage-exclusion-settings).
4. Válassza ki a fájlok és mappák biztonsági mentése, és kattintson a kívánt **gépházban**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. Adja meg a **biztonsági mentés ütemezése** kattintson **következő**.

    (A 3-szor naponkénti maximum) napi vagy heti biztonsági mentései is ütemezheti.

    ![Windows Server biztonsági mentés elemei](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Adja meg a biztonsági mentés ütemezése esetén, tekintse meg a részletes [cikk](backup-azure-backup-cloud-as-tape.md).
   >

6. Válassza ki a **adatmegőrzési** a biztonsági másolatot, majd kattintson a **következő**.

    ![Windows Server biztonsági mentés elemei](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. Az a **megerősítő** képernyőn tekintse át az adatokat, és kattintson a **Befejezés**.
8. Miután a varázsló befejezi a **biztonsági mentés ütemezése**, kattintson a **Bezárás**.

    Védelmi módosítása, után ellenőrizheti, hogy biztonsági mentést a megfelelő váltanak a **feladatok** lapra, és erősítse meg, hogy a módosítások megjelennek a biztonsági mentési feladatok.

## <a name="enable-network-throttling"></a>Hálózati sávszélesség-szabályozás engedélyezése

Az Azure Backup szolgáltatás ügynökének biztosítja a sávszélesség-szabályozási fülre, amely lehetővé teszi, hogy vezérlését a hálózati sávszélesség használatának adatátvitel során. Ez a vezérlő akkor lehet hasznos, ha biztonsági kell során az adatokat munkaidő, de nem szeretné a biztonsági mentési folyamat zavarja a más internetes forgalmat. Átviteli biztonsági mentése és visszaállítása a tevékenységek adatok szabályozás vonatkozik.  

Elemre a szabályozás engedélyezéséhez:

1. Az a **Backup szolgáltatás ügynökének**, kattintson a **tulajdonságainak módosítása**.
2. Az a ** szabályozás lapra, válassza ki **engedélyezi az internetes sávszélesség szabályozásának a biztonsági mentési műveleteknél**.

    ![Hálózati sávszélesség-szabályozás](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    Miután engedélyezte a sávszélesség-szabályozás, adja meg a megengedett sávszélesség vonatkozó biztonsági mentési adatátvitel során **időpontokat a munkaidőhöz** és **munkaidőn kívüli**.

    A sávszélesség értékek kezdődjenek 512 kilobájt / másodperc (Kbps), és folytathatja a legfeljebb 1023 megabájt / másodperc (Mbps). Is kijelölni a kezdő és a Befejezés **időpontokat a munkaidőhöz**, és a hét melyik napjain minősülnek munkahelyi nap. A megadott munkaidőn kívül idő tekinthető munkaidőn kívüli időszakra.
3. Kattintson az **OK** gombra.

## <a name="manage-exclusion-settings"></a>Kizárási beállításainak kezelése
1. Nyissa meg a **Microsoft Azure Backup szolgáltatás ügynökének** (a gép kereséssel megtalálja *a Microsoft Azure Backup szolgáltatás*).

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. Kattintson a Microsoft Azure Backup szolgáltatás ügynökének **biztonsági mentés ütemezése**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. A biztonsági mentés ütemezése varázsló hagyja a a **biztonsági másolati elemei vagy időpontok szerepelnek módosítása** jelölőnégyzetet és kattintson **következő**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. Kattintson a **kizárások beállításai**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. Kattintson a **adja hozzá a kizárási**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. Válassza ki azt a helyet, és kattintson a **OK**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. Adja hozzá a fájl kiterjesztése a **Fájltípus** mező.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    .Mp3 bővítmény felvétele

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Egy másik bővítmény hozzáadásához kattintson **Kizárás hozzáadása** , és adja meg egy másik típus fájlnévkiterjesztés (.jpeg-bővítmény hozzáadása).

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. Ha a bővítményeket adott hozzá, kattintson **OK**.
9. A biztonsági mentés ütemezése varázsló segítségével kattintva folytatni **következő** mindaddig, amíg a **visszaigazolási lapja**, kattintson a **Befejezés**.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**1. KÉRDÉS. A biztonsági mentési feladat állapota: módon fejezte be az Azure Backup szolgáltatás ügynöke, miért nem az beszerzése kerülnek azonnal portálon?**

1. válasz Nincs maximális késleltetés 15 perc között a biztonsági mentési feladat állapotát a következő megjelenik az Azure Backup szolgáltatás ügynöke és az Azure-portálon.

**Q.2, amikor egy biztonsági mentési feladat sikertelen lesz, mennyi ideig tart a riasztást?**

A.2 riasztás jelenik meg, az Azure biztonsági mentési hiba 20 perc belül.

**3. NEGYEDÉVÉBEN. Ha egy e-mailt nem küldi el, ha értesítések beállítása eset van?**

3. válasz Az alábbiakban az esetekben, amikor az értesítés nem lesz elküldve riasztás zaj csökkentése érdekében:

* Ha az értesítések beállítása óránkénti, és az következik be van-e és órán belül megoldott riasztás
* Feladat megszakadt.
* Második biztonsági mentési feladat sikertelen volt, mert az eredeti biztonsági mentési feladat van folyamatban.

## <a name="troubleshooting-monitoring-issues"></a>Figyelési problémák elhárítása
**Probléma:** feladatok és/vagy az Azure Backup szolgáltatás ügynökének származó riasztások nem jelennek meg a portálon.

**Hibaelhárítási lépések:** a folyamat ```OBRecoveryServicesManagementAgent```, a feladat és riasztás adatokat küld az Azure Backup szolgáltatás. Ez a folyamat állapottal alkalmanként vagy -leállítás.

1. Ellenőrizze, hogy a folyamat nem fut, nyissa meg a **Feladatkezelő** és ellenőrizze, hogy a ```OBRecoveryServicesManagementAgent``` folyamat fut.
2. Feltételezve, hogy a folyamat nem fut, nyissa meg a **Vezérlőpult** , és keresse meg a szolgáltatások listájában. Indítsa el, vagy indítsa újra a **Microsoft Azure Recovery Services Management Agent**.

    További információért keresse meg a naplózásra kerül:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`Példa:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Következő lépések
* [Windows Server vagy a Windows ügyfél visszaállítása az Azure-ból](backup-azure-restore-windows-server.md)
* Azure biztonsági mentés kapcsolatos további információkért lásd: [Azure biztonsági mentés áttekintése](backup-introduction-to-azure-backup.md)
* Látogasson el a [Azure biztonsági mentési fórum](http://go.microsoft.com/fwlink/p/?LinkId=290933)
