---
title: Gyakori kérdések – SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Ebből a cikkből megismerheti a SAP HANA adatbázisok a Azure Backup szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszokat.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: d9d10e38885ba814045d8476b83671153feb7b8c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919685"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Gyakori kérdések – SAP HANA adatbázisok biztonsági mentése Azure-beli virtuális gépeken

Ez a cikk az SAP HANA adatbázisok Azure Backup szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="backup"></a>Biztonsági mentés

### <a name="how-many-full-backups-are-supported-per-day"></a>Hány teljes biztonsági mentést támogat naponta?

Naponta csak egy teljes biztonsági mentést támogatunk. A különbözeti biztonsági másolat és a teljes biztonsági mentés ugyanazon a napon aktiválódik.

### <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok riasztásokat hozzanak létre?

Nem. A sikeres biztonsági mentési feladatok nem hoznak fel riasztásokat. A rendszer csak a sikertelen biztonsági mentési feladatokhoz küld riasztásokat. A portálon megjelenő riasztások részletes viselkedését [itt](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)dokumentáljuk. Ha azonban érdekli, hogy a sikeres feladatok esetében is vannak riasztások, használhatja a [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Láthatom az ütemezett biztonsági mentési feladatokat a biztonsági mentési feladatok menüben?

A biztonsági mentési feladat menü csak az alkalmi biztonsági mentési feladatokat jeleníti meg. Ütemezett feladatokhoz használja a [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>A jövőbeli adatbázisok automatikusan hozzáadódnak a biztonsági mentéshez?

Nem, ez jelenleg nem támogatott.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Ha egy példányból törölek egy adatbázist, mi fog történni a biztonsági másolatok?

Ha egy adatbázis eldobása egy SAP HANA-példányból történik, az adatbázis biztonsági mentései továbbra is próbálkoznak. Ez azt jelenti, hogy a törölt adatbázis nem Kifogástalan állapotba kerül, a **biztonsági mentési elemek** alatt, és továbbra is védve van.
Az adatbázis védelmének leállítására szolgáló helyes módszer a **biztonsági mentés leállítása az adatbázisban lévő adatok törlésével** .

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Ha a védelem után módosítom az adatbázis nevét, mi lesz a viselkedés?

Az átnevezett adatbázist új adatbázisként kezeli a rendszer. Ezért a szolgáltatás úgy fogja kezelni ezt a helyzetet, mintha az adatbázis nem található, és a biztonsági mentések sikertelenek lesznek. Az átnevezett adatbázis új adatbázisként fog megjelenni, és a védelemre kell beállítani.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Mik a SAP HANA-adatbázisok Azure-beli virtuális gépen való biztonsági mentésének előfeltételei?

Tekintse át az [előfeltételeket](tutorial-backup-sap-hana-db.md#prerequisites) , valamint azt, hogy [az előzetes regisztrációs parancsfájl milyen szakaszt tartalmaz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Milyen engedélyeket kell beállítani az Azure-hoz a SAP HANA adatbázisok biztonsági mentéséhez?

Az előzetes regisztrációs parancsfájl futtatásakor a szükséges engedélyek megadásával engedélyezheti az Azure számára a SAP HANA adatbázisok biztonsági mentését. [Itt](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)megtalálhatja az előzetes regisztrációs szkriptet.

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>A biztonsági mentések a 1,0 és 2,0 közötti SAP HANA áttelepítése után fognak működni?

Tekintse át a hibaelhárítási útmutató [ezen szakaszát](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) .

## <a name="restore"></a>Visszaállítás

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Miért nem látom, hogy a HANA-rendszeren Szeretném visszaállítani az adatbázist?

Ellenőrizze, hogy teljesülnek-e a visszaállítás a cél SAP HANA példányra vonatkozó előfeltételei. További információ: [Előfeltételek – SAP HANA adatbázisok visszaállítása az Azure-beli virtuális gépen](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Miért nem sikerül felülírni az adatbázis-visszaállítást?

Győződjön meg arról, hogy a helyreállítás **kényszerítése** beállítás be van jelölve.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Miért látom a "forrás-és célszámítógépek visszaállítása nem kompatibilis" hibaüzenetet?

A jelenleg támogatott visszaállítási típusok megtekintéséhez tekintse meg a [1642148](https://launchpad.support.sap.com/#/notes/1642148) -es SAP HANA megjegyzését.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [készíthet biztonsági mentést](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) az Azure-beli virtuális gépeken futó SAP HANA adatbázisokról.
