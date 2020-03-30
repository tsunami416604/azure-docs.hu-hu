---
title: Az Azure Recovery Services tárolóinak és kiszolgálóinak kezelése
description: Ebből a cikkből megtudhatja, hogy miként használhatja a Helyreállítási szolgáltatások tárolójának áttekintése irányítópultot a Recovery Services-tárolók figyeléséhez és kezeléséhez.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 1a4d23c157700f42422cfe7ca8fa1c49e2cf128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131975"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Recovery Services-tárolók figyelése és kezelése

Ez a cikk bemutatja, hogyan használhatja a Helyreállítási szolgáltatások **tárolójának áttekintése** irányítópultot a Recovery Services-tárolók figyeléséhez és kezeléséhez. Amikor megnyit egy Recovery Services-tárolót a listából, megnyílik a kijelölt tároló **áttekintő** irányítópultja. Az irányítópult különböző részleteket tartalmaz a tárolóról. Vannak *csempék,* amelyek azt mutatják: a kritikus és figyelmeztető riasztások állapotát, a folyamatban lévő és a sikertelen biztonsági mentési feladatok, valamint a helyileg redundáns tárolás (LRS) és a geo redundáns tárolás (GRS) használt mennyisége. Ha biztonsági másolatot készít az Azure-beli virtuális gépekről a tárolóba, a [ **Biztonsági mentés előtti ellenőrzés állapota** csempe megjeleníti a kritikus vagy figyelmeztető elemeket.](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status) Az alábbi kép a **Contoso-vault** **áttekintő** irányítópultja látható. A **Biztonsági másolat elemek** csempe azt mutatja, hogy kilenc elem van regisztrálva a tárolóban.

![helyreállítási szolgáltatások tárolójának irányítópultja](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

A cikk előfeltételei a következők: egy Azure-előfizetés, egy Recovery Services-tároló, és hogy legalább egy biztonsági mentési elem van konfigurálva a tárolóhoz.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Helyreállítási szolgáltatások tárolójának megnyitása

Riasztások figyeléséhez, vagy a Recovery Services-tároló felügyeleti adatainak megtekintéséhez nyissa meg a tárolót.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-előfizetésével.

2. A portálon kattintson a **Minden szolgáltatás**elemre.

   ![A Helyreállítási szolgáltatások tárolóinak listájának megnyitása 1.](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. A **Minden szolgáltatás** párbeszédpanelen írja be a **Helyreállítási szolgáltatások című szöveget.** Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor megjelenik a **Helyreállítási szolgáltatások tárolói** beállítás, kattintson rá a Helyreállítási szolgáltatások tárolóinak listájának megnyitásához az előfizetésben.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. A tárolók listájában kattintson egy tárolóra az **áttekintő** irányítópult megnyitásához.

    ![helyreállítási szolgáltatások tárolójának irányítópultja](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Az áttekintő irányítópult csempéket használ a riasztások és a biztonsági mentési feladat adatainak megjelenítéséhez.

## <a name="monitor-backup-jobs-and-alerts"></a>Biztonsági mentési feladatok és riasztások figyelése

A Helyreállítási szolgáltatások tároló **áttekintése** irányítópult csempéket biztosít a figyelési és használati információkhoz. A Figyelés szakasz csempéi kritikus és figyelmeztetési riasztásokat, valamint folyamatban lévő és sikertelen feladatokat jelenítenek meg. Kattintson egy adott riasztásra vagy feladatra a Biztonsági másolat riasztások vagy a Feladatmentési feladatok menü megnyitásához, amely az adott feladatra vagy riasztásra szűrve van.

![Irányítópult-feladatok biztonsági mentése](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

A figyelésszakasz az előre definiált **biztonsági mentési riasztások** és a biztonsági **mentési feladatok** lekérdezések eredményeit jeleníti meg. A figyelési csempék naprakész információkat nyújtanak a következőkről:

* Kritikus és figyelmeztető riasztások a biztonsági mentési feladatokhoz (az elmúlt 24 órában)
* Az Azure-beli virtuális gépek állapotának előzetes ellenőrzése. Az előzetes ellenőrzés állapotáról a [Biztonsági másolat előzetes ellenőrzés állapota című](#backup-pre-check-status)témakörben talál teljes körű információt.
* A biztonsági mentési feladatok folyamatban vannak, és a sikertelen feladatok (az elmúlt 24 órában).

A Használati csempék a következőket biztosítják:

* A tárolóhoz konfigurált biztonsági mentési elemek száma.
* Az Azure storage (lrs és GRS elválasztva) a tároló által felhasznált.

A társított menü megnyitásához kattintson a csempékre (kivéve a Biztonsági tárolót). A fenti képen a Biztonsági mentési riasztások csempe három kritikus riasztást jelenít meg. A Biztonsági másolat riasztások csempe Kritikus riasztások sorára kattintva megnyílik a kritikus riasztásokra szűrt biztonsági mentési riasztások.

![A biztonsági mentési riasztások menüje szűrve a kritikus riasztásokra](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

A Biztonsági mentési riasztások menü a fenti képen a következők szerint szűri: Az állapot aktív, a súlyosság kritikus, az idő pedig az előző 24 óra.

### <a name="backup-pre-check-status"></a>Biztonsági mentés előzetes ellenőrzése állapota

Biztonsági mentés előzetes ellenőrzések ellenőrizze a virtuális gépek konfigurációját, hogy a problémák, amelyek hátrányosan befolyásolhatják a biztonsági mentések. Ezek az adatok összesítése, így megtekintheti közvetlenül a Recovery Services Vault irányítópultján, és javaslatokat nyújt a korrekciós intézkedéseket a sikeres fájl-konzisztens vagy alkalmazás-konzisztens biztonsági mentések biztosítása érdekében. Nincs szükségük infrastruktúrára, és nincs többletköltségük.  

Backup Pre-Checks run as part of the scheduled backup operations for your Azure VMs. A következő államok egyikével zárnak:

* **Átadott:** Ez az állapot azt jelzi, hogy a virtuális gép konfigurációjának sikeres biztonsági mentéshez kell vezetnie, és nem kell korrekciós intézkedést végrehajtani.
* **Figyelmeztetés:** Ez az állapot a virtuális gép konfigurációjában egy vagy több olyan problémát *jelez,* amely biztonsági mentési hibákhoz vezethet. A sikeres biztonsági mentések biztosításához *ajánlott* lépéseket tartalmaz. Például a legújabb virtuálisgép-ügynök telepítése nem okozhat időnként biztonsági mentéseket. Ez a helyzet figyelmeztető állapotot biztosítana.
* **Kritikus :** Ez az állapot a virtuális gép konfigurációjában egy vagy több kritikus problémát jelez, amelyek biztonsági mentési hibákhoz *vezetnek,* és a sikeres biztonsági mentések biztosításához *szükséges* lépéseket biztosítják. Például egy hálózati probléma által okozott frissítés az NSG-szabályok egy virtuális gép, biztonsági mentéseket okoz, mivel megakadályozza, hogy a virtuális gép kommunikál az Azure Backup szolgáltatással. Ez a helyzet kritikus állapotot biztosítana.

Az alábbi lépéseket követve indítsa el a helyreállítási szolgáltatások tárolójában a virtuális gépek biztonsági mentéseinek biztonsági mentései által jelentett problémák megoldását.

* Válassza ki a **Biztonsági mentés i.ve)-ellenőrzés állapota (Azure-beli virtuális gépek)** csempét a Recovery Services Vault irányítópultján.
* Jelöljön ki minden olyan virtuális gép, amelynek biztonsági mentési előzetes ellenőrzése állapota **kritikus** vagy **figyelmeztetés.** Ez a művelet megnyitja a **virtuális gép részletei** ablaktáblán.
* Válassza ki az ablaktábla értesítését az ablaktábla tetején a konfigurációs probléma leírásának és a javítási lépéseknek a felfedéséhez.

## <a name="manage-backup-alerts"></a>Biztonsági mentési riasztások kezelése

A Biztonsági másolat riasztások menü eléréséhez kattintson a Helyreállítási szolgáltatások tároló jának **menüjében a Biztonsági másolat készítési riasztások parancsra.**

![Biztonsági mentési riasztások](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

A Biztonsági mentési riasztások jelentés felsorolja a riasztást a tároló.

![Biztonsági mentési riasztások](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Riasztások

A Biztonsági másolat riasztások lista megjeleníti a szűrt riasztások kijelölt adatait. A Biztonsági másolat riasztások menüben szűrheti a kritikus vagy figyelmeztető riasztásokat.

| Riasztási szint | Riasztásokat generáló események |
| ----------- | ----------- |
| Kritikus | Kritikus riasztásokat kap, ha: Biztonsági mentési feladatok sikertelenek, helyreállítási feladatok sikertelenek, és amikor leállítja a védelmet a kiszolgálón, de megőrzi az adatokat.|
| Figyelmeztetés | Figyelmeztetésjelenik meg, ha: A biztonsági mentési feladatok figyelmeztetésekkel vannak kiegészítve, például ha sérülés miatt nem készült 100-nál kevesebb fájl ról biztonsági másolat, vagy ha több mint 1 000 000 fájlról sikerült biztonsági másolat készül). |
| Tájékoztató | jelenleg nincsenek információs riasztások használatban. |

### <a name="viewing-alert-details"></a>A riasztások részleteinek megtekintése

A Biztonsági másolat riasztások jelentés nyolc részletet követ nyomon az egyes riasztásokról. Az **Oszlopok kiválasztása** gombbal szerkesztheti a jelentés részleteit.

![Biztonsági mentési riasztások](./media/backup-azure-manage-windows-server/backup-alerts.png)

Alapértelmezés szerint a **legutóbbi előfordulási idő**kivételével minden részlet megjelenik a jelentésben.

* Riasztás
* Biztonsági másolat eleme
* Védett kiszolgáló
* Severity
* Időtartam
* Létrehozás iódülete
* status
* Legutóbbi előfordulás ióma

### <a name="change-the-details-in-alerts-report"></a>A riasztások jelentés részleteinek módosítása

1. A jelentés adatainak módosításához kattintson a Biztonsági másolat menü Oszlopok **kiválasztása** **parancsára.**

   ![Biztonsági mentési riasztások](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Megnyílik **az Oszlopok kiválasztása** menü.

2. Az **Oszlopok kiválasztása** menüben válassza ki a jelentésben megjeleníteni kívánt részleteket.

    ![Oszlopok kiválasztása menü](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. A Módosítások mentéséhez kattintson a **Kész** gombra, és zárja be az Oszlopok kiválasztása menüt.

   Ha módosításokat hajt végre, de nem szeretné megtartani a módosításokat, kattintson az **Alaphelyzet gombra,** ha vissza szeretné állítani a kijelölt et az utoljára mentett konfigurációba.

### <a name="change-the-filter-in-alerts-report"></a>A szűrő módosítása a riasztások jelentésben

A **Szűrő** menüben módosíthatja a riasztások súlyossági, állapot-, kezdési és befejezési idejét.

> [!NOTE]
> A Biztonsági mentési riasztások szűrő szerkesztése nem módosítja a kritikus vagy figyelmeztetési riasztásokat a tároló áttekintése irányítópulton.
>  

1. A Biztonsági másolat szolgáltatás szűrő módosításához kattintson a Biztonsági másolat menü **Szűrő parancsára.**

   ![Szűrő menü kiválasztása](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Megjelenik a Szűrő menü.

   ![Szűrő menü kiválasztása](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Módosítsa a súlyossági, az állapot- és kezdési időpontot, majd kattintson a **Kész** gombra a módosítások mentéséhez.

## <a name="configuring-notifications-for-alerts"></a>Értesítések konfigurálása riasztásokhoz

Értesítésekkel kelthet létre e-maileket figyelmeztetésvagy kritikus riasztás esetén. Óránként, illetve adott riasztás esetén küldhet e-mailes értesítéseket.

   ![Szűrőriasztások](./media/backup-azure-manage-windows-server/configure-notification.png)

Alapértelmezés szerint az e-mail értesítések be vannak **kapcsolva.** Az e-mail értesítések leállításához kattintson a **Ki** gombra.

Az **Értesítés** vezérlőben válassza a **Figyelmeztetésenként lehetőséget,** ha nem szeretne csoportosítást, vagy nincs sok olyan elem, amely riasztásokat generálhat. Minden riasztás egy értesítést eredményez (ez az alapértelmezett beállítás), és a rendszer azonnal egy megoldási e-mailt küld.

Ha az **Óránkénti kivonatolás lehetőséget választja,** a címzettek e-mailt küldenek az elmúlt órában létrehozott feloldatlan riasztásokról. A megoldás e-mailt küld ki az óra végén.

Válassza ki az e-mailek létrehozásához használt riasztás súlyosságát (Kritikus vagy figyelmeztetés). Jelenleg nincsenek információs riasztások.

## <a name="manage-backup-items"></a>Biztonsági másolat elemeinek kezelése

A Recovery Services-tároló sokféle biztonsági mentési adatot tartalmaz. [További információ](backup-overview.md#what-can-i-back-up) arról, hogy miről tudsz biztonsági másolatot kapni. A különböző kiszolgálók, számítógépek, adatbázisok és munkaterhelések kezeléséhez kattintson a **Biztonsági másolat elemek** csempére a tároló tartalmának megtekintéséhez.

![Biztonsági másolat elemeinek csempéje](./media/backup-azure-manage-windows-server/backup-items.png)

Megnyílik a Biztonsági másolat elemeinek listája a Biztonsági másolat kezelése típusa szerint rendezve.

![Biztonsági másolat elemeinek listája](./media/backup-azure-manage-windows-server/list-backup-items.png)

A védett példányok egy adott típusának feltárásához kattintson az elemre a Biztonságimásolat-kezelés típusa oszlopban. A fenti képen például két Azure virtuális gép védett ebben a tárolóban. Az **Azure Virtuálisgép**elemre kattintva megnyitja a védett virtuális gépek listáját ebben a tárolóban.

![Biztonsági másolat típusának listája](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

A virtuális gépek listája hasznos adatokat tartalmazza: a társított erőforráscsoportot, a korábbi [biztonsági mentés előtti ellenőrzést](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status), az utolsó biztonsági mentés állapotát és a legutóbbi visszaállítási pont dátumát. A három pont az utolsó oszlopban megnyitja a menüt a gyakori feladatok elindításához. Az oszlopokban megadott hasznos adatok az egyes biztonsági mentési típusok esetében eltérőek.

![Biztonsági másolat típusának listája](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Biztonsági mentési feladatok kezelése

A **biztonsági mentési feladatok** csempe a tároló irányítópultján mutatja a folyamatban lévő vagy sikertelen feladatok száma az elmúlt 24 órában. A csempe betekintést nyújt a Biztonsági mentési feladatok menübe.

![Elemek biztonsági és biztonsági másolatot a beállításokból](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

A feladatok további részleteinek megtekintéséhez kattintson a **Folyamatban** vagy a **Sikertelen** gombra a Biztonsági másolat feladatok menünek az adott állapotra szűrt megnyitásához.

### <a name="backup-jobs-menu"></a>Biztonsági mentési feladatok menü

A **Biztonsági másolat menü** az Elemtípusra, a Műveletre, az Állapotra, a Kezdési időpontra és az Időtartamparancsra vonatkozó információkat jeleníti meg.  

A Biztonsági másolat készítési feladatok menümegnyitásához kattintson a tároló főmenüjében a **Biztonsági másolat készítési feladatok parancsra.**

![Elemek biztonsági és biztonsági másolatot a beállításokból](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Megnyílik a biztonsági mentési feladatok listája.

![Elemek biztonsági és biztonsági másolatot a beállításokból](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

A Biztonsági mentési feladatok menü az összes művelet állapotát jeleníti meg az összes biztonsági mentési típuson az elmúlt 24 órában. A szűrők módosítása a **Szűrő** segítségével. A szűrőket a következő szakaszok ismertetik.

A szűrők módosítása:

1. A tároló biztonsági mentési feladatok menüjében kattintson a **Szűrő gombra.**

   ![Elemek biztonsági és biztonsági másolatot a beállításokból](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Megnyílik a Szűrő menü.

   ![Elemek biztonsági és biztonsági másolatot a beállításokból](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Adja meg a szűrőbeállításokat, és kattintson **a Kész gombra.** A szűrt lista az új beállítások alapján frissül.

#### <a name="item-type"></a>Cikk típusa

Az elemtípus a védett példány biztonsági mentési felügyeleti típusa. Négy típusa van; lásd az alábbi listát. Megtekintheti az összes cikktípust vagy egy cikktípust. Két vagy három cikktípus t nem választhat ki. A rendelkezésre álló elemtípusok a következők:

* Minden cikktípus
* Azure virtuális gép
* Fájlok és mappák
* Azure Storage
* Azure-munkaterhelés

#### <a name="operation"></a>Művelet

Megtekintheti egy műveletet, vagy az összes műveletet. Két vagy három művelet nem jelölhető ki. A rendelkezésre álló műveletek a következők:

* Minden művelet
* Regisztráljon
* Biztonsági mentés konfigurálása
* Backup
* Visszaállítás
* Biztonsági másolat letiltása
* Biztonsági mentési adatok törlése

#### <a name="status"></a>status

Megtekintheti az Összes állapotot vagy egyet. Két vagy három állapot nem jelölhető ki. A rendelkezésre álló állapotok a következők:

* Minden állapot
* Befejezve
* Folyamatban
* Sikertelen
* Megszakítva
* Figyelmeztetésekkel kiegészítve

#### <a name="start-time"></a>Kezdési idő

A lekérdezés elkezdődiknap és időpontja. Az alapértelmezett érték egy 24 órás időszak.

#### <a name="end-time"></a>Befejezési idő

A lekérdezés befejeződésének napja és időpontja.

### <a name="export-jobs"></a>Feladatok exportálása

**A Feladatok exportálása** segítségével hozzon létre egy számolótáblát, amely tartalmazza az összes Feladat menüinformációt. A számolótáblának egy lapja van, amely az összes feladat összegzését tartalmazza, és minden feladathoz külön lapot tartalmaz.

Ha a feladatadatokat számolótáblába szeretné exportálni, kattintson **a Feladatok exportálása**gombra. A szolgáltatás létrehoz egy számolótáblát a tároló nevével és dátumával, de módosíthatja a nevet.

## <a name="monitor-backup-usage"></a>Biztonsági másolat használatának figyelése

Az irányítópult on-ra a Biztonsági mentési tároló csempe az Azure-ban felhasznált tárterületet jeleníti meg. A tárhelyfelhasználás a következőkre van lehetővé téve:

* A tárolóhoz társított felhőlrs-tárhelyhasználat
* A tárolóhoz társított felhőbeli GRS-tárhelyhasználat

## <a name="troubleshooting-monitoring-issues"></a>Figyelési problémák elhárítása

**Probléma:** Az Azure Backup-ügynök feladatai és/vagy riasztásai nem jelennek meg a portálon.

**Hibaelhárítási lépések:** A folyamat ```OBRecoveryServicesManagementAgent```, a , elküldi a feladat és a riasztási adatokat az Azure Backup szolgáltatás. Alkalmanként ez a folyamat elakadhat vagy leállhat.

1. A folyamat futtatásának ellenőrzéséhez nyissa meg a ```OBRecoveryServicesManagementAgent``` **Feladatkezelőt,** és ellenőrizze, hogy fut-e.

2. Ha a folyamat nem fut, nyissa meg a **Vezérlőpultot,** és tallózzon a szolgáltatások listájában. Indítsa el vagy indítsa újra a **Microsoft Azure helyreállítási szolgáltatások kezelőkezelőt**.

    További információkért böngésszen a naplók között:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`Például:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>További lépések

* [Windows Server vagy Windows-ügyfél visszaállítása az Azure-ból](backup-azure-restore-windows-server.md)
* Ha többet szeretne megtudni az Azure Backup ról, olvassa el az [Azure Backup áttekintése című témakört.](backup-introduction-to-azure-backup.md)
