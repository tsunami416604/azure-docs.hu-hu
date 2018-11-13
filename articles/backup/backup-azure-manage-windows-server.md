---
title: Az Azure Recovery Services-tárolókra és -kiszolgálók kezelése
description: Feladatok és a egy Azure helyreállítási tárban riasztások kezelése.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/21/2018
ms.author: markgal
ms.openlocfilehash: c9b3b027a52a6847c566c385c304edc195f7fdda
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250696"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Recovery Services-tárolók figyelése és kezelése

Ez a cikk bemutatja, hogyan használható a Recovery Services-tároló **áttekintése** irányítópultot, a Recovery Services-tárolók figyelése és kezelése. Recovery Services-tárolót a listában megnyitásakor a **áttekintése** megnyílik a kiválasztott tároló irányítópultja. Az irányítópult különböző információt nyújt a tárolóhoz. Nincsenek *csempék* adott megjelenítése: állapota kritikus és figyelmeztetési szintű riasztások, a folyamatban lévő és a sikertelen biztonsági mentési feladatok és mennyisége helyileg redundáns tárolás (LRS) és a georedundáns Társzolgáltatási (GRS) használt. Ha Ön Azure virtuális gépek biztonsági mentése a tárolóba a [ **biztonsági mentés előzetes ellenőrzési állapota** csempe megjeleníti minden olyan kritikus vagy figyelmeztetési elemek](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/). Az alábbi ábra a **áttekintése** irányítópultján **Contoso-tároló**. A **biztonsági másolati elemek** csempén van kilenc elemek regisztrálva van a tárolóban.

![Recovery services-tároló irányítópult](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Ebben a cikkben az Előfeltételek: egy Azure-előfizetést, egy Recovery Services-tárolót és, hogy legalább egy biztonsági másolati elemek konfigurálva a tárolóhoz.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Nyissa meg a Recovery Services-tároló

Riasztások figyelése, vagy megtekintheti a felügyeleti adatait egy Recovery Services-tárolót, nyissa meg a tárolóban.

1. Jelentkezzen be a [az Azure Portal](https://portal.azure.com/) Azure-előfizetése.

2. Kattintson a portál **minden szolgáltatás**.

   ![1. lépés-lista megnyitása a Recovery Services-tárolók](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Az a **minden szolgáltatás** párbeszédpanelen írja be **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Ha a **Recovery Services-tárolók** lehetőség jelenik meg, kattintson rá a Recovery Services-tárolók listájának megnyitásához az előfizetésében.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. A tárolók listájában kattintson az egy tároló megnyitása a **áttekintése** irányítópultot. 

    ![Recovery services-tároló irányítópult](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Az irányítópult csempéi riasztásokat és a biztonsági mentési feladat adatainak használ.

## <a name="monitor-backup-jobs-and-alerts"></a>Biztonsági mentési feladatok figyelése és riasztások

A Recovery Services-tároló **áttekintése** irányítópult csempéi biztosít a figyelés és használati adatok. A csempék jelennek meg a figyelés szakaszban megjelenített kritikus és figyelmeztetési szintű riasztások, és folyamatban lévő és sikertelen feladatokat. Kattintson egy adott riasztás vagy a feladatra kattintva megnyithatja a biztonsági mentési riasztások és a biztonsági mentési feladatok menü, az adott feladat vagy riasztás szűrve.

![Irányítópult biztonsági mentési feladatok](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

A figyelés szakaszban eredményeit jeleníti meg előre meghatározott **biztonsági mentési riasztások** és **biztonsági mentési feladatok** lekérdezéseket. A figyelés csempék kapcsolatban naprakész információkat nyújtanak:

* Biztonsági mentési feladatok (az elmúlt 24 órában) számára kritikus és figyelmeztetési riasztások
* Előzetes ellenőrzési állapota az Azure virtuális gépek – az előzetes ellenőrzési állapotát, részletes információkat talál a [biztonsági mentés előzetes ellenőrzési állapotát a biztonsági mentés blog](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/).
* A folyamat és a feladatok, amelyek (az elmúlt 24 órában) sikertelen volt a biztonsági mentési feladatok.

Adja meg a használati csempék:

* A tároló konfigurálása biztonsági másolati elemek száma.
* Az Azure storage (az LRS és a GRS elválasztva) a tároló által igénybe vett.

A társított menü megnyitásához kattintson a csempék (kivéve a biztonsági mentési tár). A fenti ábrán a biztonsági riasztások csempén látható három kritikus riasztások. A biztonsági riasztások csempén a kritikus riasztások sorára kattint, megnyílik a biztonsági mentési riasztások kritikus riasztások szűrve.

![A kritikus riasztásokról szűrt biztonsági mentésekkel kapcsolatos riasztások menü](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

A biztonsági mentési riasztások menüből, a fenti képen szerint van szűrve: állapota aktív, súlyosság kritikus, és idő az azt megelőző 24 órához.

## <a name="manage-backup-alerts"></a>Biztonsági riasztások kezelése

A biztonsági mentési riasztások menü, a Recovery Services-tároló menüjében kattintson **biztonsági mentési riasztások**.

![Biztonsági mentési riasztások](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

A biztonsági mentési riasztások jelentés felsorolja a riasztásokat a tárolóhoz. 

![Biztonsági mentési riasztások](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Riasztások

A biztonsági mentési riasztások listájában a szűrt riasztások a kiválasztott információit jeleníti meg. A biztonsági mentési riasztások menüben szűrhet kritikus vagy figyelmeztető riasztások.

| Riasztási szint | Eseményeket, amelyek a riasztások generálására lehetőséget |
| ----------- | ----------- |
| Kritikus | Kap kritikus riasztást küld, amikor: biztonsági mentési feladat sikertelen, a helyreállítási feladat sikertelen, és amikor védelem leállítása a kiszolgálón, de megőrizni az adatokat.|
| Figyelmeztetés | Figyelmeztető riasztást küld, amikor: biztonsági mentési feladatok elvégzéséhez, de figyelmeztetésekkel, például ha 100-nál kevesebb fájlok nem készül biztonsági másolat okozta problémák miatt, vagy ha több mint 1 000 000 fájlok sikeresen biztonsági mentése). |
| Tájékoztató | jelenleg nincsenek tájékoztató riasztások használatban vannak. |

### <a name="viewing-alert-details"></a>Riasztás részleteinek megjelenítése

A biztonsági mentési riasztások jelentés minden egyes riasztás részleteinek nyolc követi nyomon. Használja a **oszlopok kiválasztása** gombra az adatok a jelentés szerkesztéséhez.

![Biztonsági mentési riasztások](./media/backup-azure-manage-windows-server/backup-alerts.png)

Alapértelmezésben a részletekről kivételével **legutóbbi előfordulás időpontja**, a jelentésben jelennek meg.

* Riasztás
* Biztonsági másolati elem
* Védett kiszolgáló
* Severity
* Időtartam
* Létrehozás ideje
* status
* Legutóbbi előfordulás időpontja

### <a name="change-the-details-in-alerts-report"></a>A részleteket a riasztási jelentés módosítása

1. A jelentés adatainak változik a **biztonsági mentési riasztások** menüben kattintson a **oszlopok kiválasztása**.

   ![Biztonsági mentési riasztások](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   A **oszlopok kiválasztása** menü megnyitása.

2. Az a **oszlopok kiválasztása** menüben válassza ki a jelentésben megjeleníteni kívánt részleteit.

    ![Válassza ki az oszlopok menü](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Kattintson a **kész** a módosítások mentéséhez és a kiválasztása oszlopok menü bezárásához.

   Ha a módosításokat, de nem szeretne megtartani a módosításokat, kattintson a **alaphelyzetbe** visszaadása a kijelölt a legutóbb mentett a konfigurációt.

### <a name="change-the-filter-in-alerts-report"></a>Módosítsa a szűrőt a riasztási jelentés

Használja a **szűrő** módosíthatja a súlyosságot, állapotát, a kezdő és befejező időpontja a riasztások menüből. 

> [!NOTE]
> A biztonsági mentési riasztások szerkesztési szűrő a kritikus vagy figyelmeztető riasztások, a tároló irányítópultjának nem változik.
>  

1. Módosítsa a biztonsági mentési riasztások szűrőt, a biztonsági mentési riasztások menüből, kattintson a **szűrő**.

   ![Válassza ki a szűrőhöz menü](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   A szűrés menün jelenik meg.

   ![Válassza ki a szűrőhöz menü](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Szerkessze a súlyosságot, állapotát, a Kezdés időpontja vagy a záró időpontot, és kattintson a **kész** a módosítások mentéséhez.

## <a name="configuring-notifications-for-alerts"></a>A riasztások értesítéseinek konfigurálása

Az értesítéseket e-mailek létrehozásához, egy figyelmeztető vagy kritikus riasztás esetén konfigurálhatja. Küldhet e-mailes riasztásokhoz óránként, vagy ha egy adott riasztás történik.

   ![Riasztások szűrése](./media/backup-azure-manage-windows-server/configure-notification.png)

Alapértelmezés szerint az E-mail-értesítések vannak **a**. Kattintson a **ki** e-mail értesítéseket leállítani.

Az a **értesítendő** szabályozhatja, válassza a **/ riasztási** Ha nem szeretné a csoportosítás, vagy még nincs sok elem, sikerült riasztást. Minden egyes riasztás eredményezi egy értesítési (az alapértelmezett beállítás), és a megoldási e-mail küldése azonnal.

Ha **óránkénti összefoglaló**, egy e-mailt küld a címzettek elmagyarázza a feloldatlan riasztások az elmúlt órában jön létre. Az óra végén egy megoldási e-mailt küld.

Válassza ki a riasztás súlyosságát (kritikus vagy figyelmeztetés) e-mail létrehozására szolgál. Jelenleg nincsenek riasztások információkat.

## <a name="manage-backup-items"></a>Biztonsági másolati elemek kezelése

Recovery Services-tároló számos különböző típusú biztonsági mentési adatokat tárolja. Biztonsági mentési típusok teljes listáját lásd: [, amely alkalmazások és számítási feladatok biztonsági mentésre alkalmas](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use). A különféle kiszolgálók, számítógépek, adatbázisok és számítási feladatok kezeléséhez kattintson a **biztonsági másolati elemek** csempére kattintva tekintheti meg a tároló tartalmát.

![Biztonsági másolati elemek csempe](./media/backup-azure-manage-windows-server/backup-items.png)

Megnyílik a biztonságimásolat-kezelés típusa szerint vannak rendezve, biztonsági mentési elemek listája.

![biztonsági mentési elemek listája](./media/backup-azure-manage-windows-server/list-backup-items.png)

Ismerje meg a védett példányok egy adott típusú, kattintson a cikk a biztonságimásolat-kezelés típusa oszlopban. Például a fenti ábrán két Azure virtuális gépek vannak a tár védett. Kattintson a **Azure virtuális gép**, megnyílik a védett virtuális gépek listáját ebben a tárban.

![biztonsági mentés típusa listája](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

A virtuális gépek listáját tartalmazó adatok rendelkezik: a társított erőforráscsoportot a korábbi [biztonsági mentés előzetes ellenőrzési](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/), utolsó biztonsági mentés állapota és a legújabb visszaállítási pont dátuma. A három pontra, az utolsó oszlopban a gyakori feladatok aktiválása menü megnyitása. Az egyes biztonsági mentési eltér a megadott oszlop, a hasznos adatok.

![biztonsági mentés típusa listája](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Biztonsági mentési feladatok kezelése

A **biztonsági mentési feladatok** tár irányítópult-csempét jeleníti meg, amelyek az elmúlt 24 órában folyamatban vagy a sikertelen feladatok száma. A csempe betekintést a biztonsági mentési feladatok menüben.

![A beállítások biztonsági másolati elemek](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

A feladatokkal kapcsolatos további részletekért kattintson **folyamatban lévő** vagy **sikertelen** az adott állapotot szűrve biztonsági mentési feladatok menü megnyitásához.

### <a name="backup-jobs-menu"></a>Biztonsági mentési feladatok menü

A **biztonsági mentési feladatok** menü az elem típusa, a művelet, állapot, Kezdés időpontja és időtartama információit jeleníti meg.  

A tároló elemre a biztonsági mentési feladatok menü megnyitásához kattintson **biztonsági mentési feladatok**. 

![A beállítások biztonsági másolati elemek](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Megnyílik a biztonsági mentési feladatok.

![A beállítások biztonsági másolati elemek](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

A biztonsági mentési feladatok menü látható minden műveletre, az összes biztonsági mentési típusok, állapota az elmúlt 24 óra. Használat **szűrő** módosítani a szűrőket. A szűrők mutatjuk be a következő szakaszok.

A szűrők módosítása:

1. Kattintson a tároló biztonsági mentési feladatok menü **szűrő**.

   ![A beállítások biztonsági másolati elemek](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    A szűrőhöz menü megnyitása.

   ![A beállítások biztonsági másolati elemek](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. A szűrő beállításait, és kattintson a **kész**. A szűrt lista frissül, az új beállítások alapján.

#### <a name="item-type"></a>Elemtípus

A cikk a biztonságimásolat-felügyelet típusa, a védett példány esetében. Négyféle; a következő listája. Megtekintheti az összes konfigurációelem-típusok, vagy egy elem típusa. Nem választhat ki két vagy három konfigurációelem-típusok. Az elérhető Konfigurációelem-típusok a következők:

* Minden elemtípus
* Azure-os virtuális gép
* Fájlok és mappák
* Azure Storage
* Azure-beli számítási

#### <a name="operation"></a>Művelet

Egy műveletet, vagy minden művelet tekintheti meg. Nem választhat ki két vagy három művelet. Az elérhető műveletek a következők:

* Minden művelet
* Regisztráljon
* Biztonsági mentés konfigurálása
* Backup
* Visszaállítás
* Biztonsági mentés letiltása
* biztonsági mentési adatok törlése

#### <a name="status"></a>status

Minden állapota vagy egy tekintheti meg. Nem választhat ki két vagy három állapota. A lehetséges állapotok a következők:

* Minden állapot
* Befejezve
* Folyamatban
* Meghiúsult
* Megszakítva
* Figyelmeztetésekkel fejeződött be

#### <a name="start-time"></a>Kezdés időpontja

Dátumát és időpontját, amely a lekérdezés kezdődik. Az alapértelmezett érték egy 24 órás időszakban.

#### <a name="end-time"></a>Befejezés időpontja

Dátumát és időpontját, amikor a lekérdezés vége.

### <a name="export-jobs"></a>Feladatok exportálása

Használat **feladatok exportálása** , hozzon létre egy táblázatot tartalmazó feladatok menü kapcsolatos összes információ. A táblázat egy olyan lap, amely tartalmazza az összes feladat összegzését, és minden egyes lapjaihoz rendelkezik.

Kattintson a feladatok adatok exportálására egy számolótáblába, **feladatok exportálása**. A szolgáltatás egy speadsheet a tároló és a dátum neve használatával hoz létre, de a nevet módosíthatja.

## <a name="monitor-backup-usage"></a>Biztonsági mentés használat monitorozása

A biztonsági mentési tár irányítópult-csempét jeleníti meg az Azure-ban felhasznált storage szolgáltatás. Tárhelyhasználat lett megadva:

* A felhő a tárolóhoz rendelt LRS storage használata
* Georedundáns tárolás felhőhasználat a tárolóhoz rendelt


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Q1. Mennyi időt vesz igénybe az Azure backup-ügynök feladatot állapot tükrözik a portálon?**

1. válasz Az Azure Portalon az Azure Backup szolgáltatás ügynöke feladat állapotát tükröző legfeljebb 15 perc is eltarthat.

**2. NEGYEDÉVI. Amikor egy biztonsági mentési feladat sikertelen volt, mennyi ideig tart kell riasztást küldenie?**

2. válasz Riasztást hoz létre az Azure biztonsági mentési hiba 20 perc belül.

**3. NEGYEDÉVI. Van egy esetet, ahol egy e-mailt nem küldhetők, ha értesítések be vannak állítva?**

3. válasz Igen. Az alábbi esetekben nem kap értesítést.

* Ha az értesítések óránkénti vannak konfigurálva, és a egy riasztás kiadása és feloldása egy órán belül
* Amikor egy feladat meg lett szakítva
* Ha egy második biztonsági mentési feladat sikertelen lesz, mert folyamatban van az eredeti biztonsági mentési feladat

## <a name="troubleshooting-monitoring-issues"></a>Figyelési kapcsolatos hibák elhárítása

**Probléma:** feladatok és/vagy az Azure Backup ügynök származó riasztások nem jelennek meg a portálon.

**Hibaelhárítási lépések:** a folyamat ```OBRecoveryServicesManagementAgent```, a feladat és riasztás adatokat küld az Azure Backup szolgáltatással. Egyes esetekben ez a folyamat letöltés állapottal vagy -leállítás.

1. Annak ellenőrzéséhez, hogy a folyamat nem fut, nyissa meg a **Feladatkezelő**, és ellenőrizze ```OBRecoveryServicesManagementAgent``` fut-e.

2. Ha a folyamat nem fut, nyissa meg a **Vezérlőpult**, és keresse meg a szolgáltatások listájában. Elindítani vagy újraindítani **a Microsoft Azure Recovery Services Management Agent**.

    További információkért keresse meg a naplók:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Példa:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>További lépések
* [A Windows Server vagy a Windows ügyfél visszaállítása az Azure-ból](backup-azure-restore-windows-server.md)
* Az Azure Backuppal kapcsolatos további tudnivalókért lásd: [Azure Backup áttekintése](backup-introduction-to-azure-backup.md)
* Látogasson el a [Azure Backup fórum](https://go.microsoft.com/fwlink/p/?LinkId=290933)
