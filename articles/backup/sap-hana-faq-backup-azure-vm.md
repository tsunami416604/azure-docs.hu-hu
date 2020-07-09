---
title: Gyakori kérdések – SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Ebből a cikkből megismerheti a SAP HANA adatbázisok a Azure Backup szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszokat.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: ddc4af9a164de3a822e8aebd6c0a4db769ec62a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262582"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Gyakori kérdések – SAP HANA adatbázisok biztonsági mentése Azure-beli virtuális gépeken

Ez a cikk az SAP HANA adatbázisok Azure Backup szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Hány teljes biztonsági mentést támogat naponta?

Naponta csak egy teljes biztonsági mentést támogatunk. Ugyanazon a napon nem rendelkezhet különbözeti biztonsági mentéssel és teljes biztonsági mentéssel.

### <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok létrehoznak riasztásokat?

Nem. A sikeres biztonsági mentési feladatok nem hoznak fel riasztásokat. A rendszer csak a sikertelen biztonsági mentési feladatokhoz küld riasztásokat. A portálon megjelenő riasztások részletes viselkedését [itt](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)dokumentáljuk. Ha azonban még a sikeres feladatok esetében is érdekli a riasztás, [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)is használhatja.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Láthatom az ütemezett biztonsági mentési feladatokat a biztonsági mentési feladatok menüben?

A biztonsági mentési feladat menü csak az alkalmi biztonsági mentési feladatokat jeleníti meg. Ütemezett feladatokhoz használja a [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>A jövőbeli adatbázisokról is automatikusan készül biztonsági mentés?

Nem, ez jelenleg nem támogatott.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Ha egy példányból törölek egy adatbázist, mi fog történni a biztonsági másolatok?

Ha egy adatbázis eldobása egy SAP HANA-példányból történik, az adatbázis biztonsági mentései továbbra is próbálkoznak. Ez azt jelenti, hogy a törölt adatbázis nem Kifogástalan állapotba kerül, a **biztonsági mentési elemek** alatt, és továbbra is védve van.
Az adatbázis védelmének leállítására szolgáló helyes módszer a **biztonsági mentés leállítása az adatbázisban lévő adatok törlésével** .

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Ha a védelem után módosítom az adatbázis nevét, mi lesz a viselkedés?

Az átnevezett adatbázist új adatbázisként kezeli a rendszer. Ezért a szolgáltatás úgy fogja kezelni ezt a helyzetet, mintha az adatbázis nem található, és a biztonsági mentések sikertelenek lesznek. Az átnevezett adatbázis új adatbázisként fog megjelenni, és a védelemre kell beállítani.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Mik a SAP HANA-adatbázisok Azure-beli virtuális gépen való biztonsági mentésének előfeltételei?

Tekintse át az [előfeltételeket](tutorial-backup-sap-hana-db.md#prerequisites) , valamint azt, hogy [az előzetes regisztrációs parancsfájl milyen szakaszt tartalmaz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Milyen engedélyeket kell beállítani, hogy az Azure biztonsági másolatot készítsen SAP HANA adatbázisokról?

Az előzetes regisztrációs parancsfájl futtatásakor a szükséges engedélyek megadásával engedélyezheti az Azure számára a SAP HANA adatbázisok biztonsági mentését. [Itt](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)megtalálhatja az előzetes regisztrációs szkriptet.

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>A biztonsági mentések a SDC-ről a MDC-be való SAP HANA Migrálás után fognak működni?

Tekintse át a hibaelhárítási útmutató [ezen szakaszát](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid) .

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Be lehet állítani az Azure HANA Backup szolgáltatást egy virtuális IP-cím (terheléselosztó) és nem virtuális gép között?

Jelenleg nincs lehetőség arra, hogy a megoldást a virtuális IP-címekre állítsa be. A megoldás végrehajtásához virtuális gépre van szükség.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Hogyan helyezhetek át egy igény szerinti biztonsági mentést a helyi fájlrendszerbe az Azure-tároló helyett?

1. Várja meg, amíg a jelenleg futó biztonsági mentés készen áll a kívánt adatbázisra (a studióból való bejelentkezés befejezéséhez)
1. Tiltsa le a naplók biztonsági mentését, és állítsa be a katalógus biztonsági mentését a **fájlrendszerre** a kívánt adatbázisra a következő lépések segítségével:
1. Kattintson duplán a **SYSTEMDB**  ->  **konfigurálása**  ->  **adatbázis**  ->  **-szűrő kiválasztása (napló)** elemre.
    1. Enable_auto_log_backup beállítása **nem** értékre
    1. Log_backup_using_backint beállítása **hamis** értékre
1. Igény szerinti biztonsági mentést készíthet a kívánt adatbázisról, és megvárhatja, hogy a biztonsági mentés és a katalógus biztonsági mentése befejeződjön.
1. Térjen vissza az előző beállításokra, hogy a biztonsági mentések az Azure-tárolóba haladjanak:
    1. Enable_auto_log_backup beállítása **Igen** értékre
    1. Log_backup_using_backint beállítása **true (igaz** ) értékre

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Hogyan használhatom a SAP HANA biztonsági mentést a HANA-replikálási beállítással?

A Azure Backup jelenleg nem képes megérteni egy HSR beállítását. Ez azt jelenti, hogy a HSR elsődleges és másodlagos csomópontjai két önálló, nem kapcsolódó virtuális gépre lesznek kezelve. Először konfigurálnia kell a biztonsági mentést az elsődleges csomóponton. Ha feladatátvétel történik, a biztonsági mentést a másodlagos csomóponton kell konfigurálni (amely most az elsődleges csomópont lesz). Nincs automatikus feladatátvétel a másik csomópontra történő biztonsági mentésből.

Ha az aktív (elsődleges) csomópontról szeretne biztonsági másolatot készíteni egy adott időpontra vonatkozóan, **átválthatja a védelmet** a másodlagos csomópontra, amely most már az elsődleges feladatátvétel után válik elérhetővé.

Az alábbi lépéseket követve hajthatja végre a **kapcsoló védelmét**:

- A [védelem leállítása](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (az adatmegőrzéssel) az elsődlegesen
- Az [előzetes regisztrációs parancsfájl](https://aka.ms/scriptforpermsonhana) futtatása a másodlagos csomóponton
- A másodlagos csomóponton [található adatbázisok felderítése](tutorial-backup-sap-hana-db.md#discover-the-databases) és a [biztonsági mentések konfigurálása](tutorial-backup-sap-hana-db.md#configure-backup)

Ezeket a lépéseket manuálisan kell végrehajtani minden feladatátvétel után. Ezeket a lépéseket a Azure Portalon kívül parancssori/HTTP-REST-en keresztül is végrehajthatja. A lépések automatizálásához használhatja az Azure-runbook.

Itt látható egy részletes példa arra, hogyan kell elvégezni a **váltást** :

Ebben a példában két csomóponttal rendelkezik – az 1. csomópont (elsődleges) és a 2. csomópont (másodlagos) a HSR-készletben.  A biztonsági mentések konfigurálása az 1. csomóponton történik. Ahogy fent említettük, ne próbálkozzon még a biztonsági másolatok konfigurálásával a 2. csomóponton.

Ha az első feladatátvétel történik, a 2. csomópont lesz az elsődleges. Majd

1. Állítsa le az 1. csomópont (korábbi elsődleges) védelmét az adat megőrzése beállítással.
1. Futtassa az előzetes regisztrációs parancsfájlt a 2. csomóponton (amely most az elsődleges).
1. Fedezze fel az adatbázisokat a 2. csomóponton, rendeljen biztonsági mentési házirendet, és konfigurálja a biztonsági mentéseket

Ezt követően az első teljes biztonsági mentés a 2. csomóponton, a befejezést követően pedig a naplók biztonsági mentései kezdődnek.

Ha a következő feladatátvétel történik, az 1. csomópont ismét elsődleges lesz, a 2. csomópont pedig másodlagos lesz. Most ismételje meg a folyamatot:

1. Állítsa le a 2. csomópont védelmét az adat megőrzése beállítással.
1. Futtassa az előzetes regisztrációs parancsfájlt az 1. csomóponton (amely még az elsődleges lesz)
1. Ezután [folytassa a biztonsági mentést](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) az 1. csomóponton a szükséges házirenddel (mivel a biztonsági mentéseket korábban leállították az 1. csomóponton).

Ezután a teljes biztonsági mentés újra aktiválódik az 1. csomóponton, majd a befejezés után a naplók biztonsági mentései megkezdődnek.

## <a name="restore"></a>Visszaállítás

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Miért nem látom, hogy a HANA-rendszeren Szeretném visszaállítani az adatbázist?

Ellenőrizze, hogy teljesülnek-e a visszaállítás a cél SAP HANA példányra vonatkozó előfeltételei. További információ: [Előfeltételek – SAP HANA adatbázisok visszaállítása az Azure-beli virtuális gépen](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Miért nem sikerül felülírni az adatbázis-visszaállítást?

Győződjön meg arról, hogy a helyreállítás **kényszerítése** beállítás be van jelölve.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Miért látom a "forrás-és célszámítógépek visszaállítása nem kompatibilis" hibaüzenetet?

A jelenleg támogatott visszaállítási típusok megtekintéséhez tekintse meg a [1642148](https://launchpad.support.sap.com/#/notes/1642148) -es SAP HANA megjegyzését.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>Használhatok a SLES-on futó adatbázis biztonsági másolatát egy RHEL HANA rendszerre való visszaállításra, vagy fordítva?

Igen, a SLES-on futó HANA-adatbázison aktiválható streaming Backups használatával visszaállíthatja egy RHEL HANA rendszerre, és fordítva. Ez azt jelenti, hogy a rendszer a folyamatos átviteli biztonsági mentést is lehetővé teszi. Azonban győződjön meg arról, hogy a HANA rendszer, amelyet vissza szeretne állítani, és a visszaállításhoz használt HANA-rendszer egyaránt kompatibilis az SAP alapján történő visszaállítással. Tekintse meg SAP HANA Megjegyzés [1642148](https://launchpad.support.sap.com/#/notes/1642148) , hogy mely típusú visszaállítási típusok kompatibilisek.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [készíthet biztonsági mentést](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) az Azure-beli virtuális gépeken futó SAP HANA adatbázisokról.
