---
title: Gyakori kérdések – SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Ebben a cikkben az SAP HANA-adatbázisok azure backup szolgáltatás használatával történő biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszok at.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155392"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Gyakori kérdések – SAP HANA-adatbázisok biztonsági mentése az Azure virtuális gépeken

Ez a cikk az SAP HANA-adatbázisok azure backup szolgáltatás használatával történő biztonsági mentésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Naponta hány teljes biztonsági mentés támogatott?

Naponta csak egy teljes biztonsági mentést támogatunk. Nem lehet különbözeti biztonsági mentés és teljes biztonsági mentés aktiválva ugyanazon a napon.

### <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok létrehoznak riasztásokat?

Nem. A sikeres biztonsági mentési feladatok nem hoznak létre riasztásokat. A riasztások csak a sikertelen biztonsági mentési feladatokhoz kerülnek elküldésre. A portálriasztások részletes viselkedését [itt](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)dokumentálja. Ha azonban még a sikeres feladatokra is szeretne riasztásokat, használhatja az [Azure Monitort.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Megnézhetem az ütemezett biztonsági mentési feladatokat a Biztonsági másolat készítési feladatok menüben?

A Biztonsági másolat menücsak ad-hoc biztonsági mentési feladatokat jelenít meg. Ütemezett feladatok esetén használja az [Azure Monitort.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

### <a name="are-future-databases-automatically-added-for-backup"></a>A jövőbeli adatbázisokról is automatikusan készül biztonsági mentés?

Nem, ez jelenleg nem támogatott.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Ha törölök egy adatbázist egy példányból, mi történik a biztonsági másolatokkal?

Ha egy adatbázis takaregy SAP HANA-példány, az adatbázis biztonsági mentések továbbra is megkísérelt. Ez azt jelenti, hogy a törölt adatbázis a **Biztonsági másolat elemek** alatt nem megfelelő állapotúként jelenik meg, és továbbra is védett.
Az adatbázis védelmének leállításának helyes módja a Biztonsági mentés leállítása az adatbázis **adatainak törlésével.**

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Ha megváltoztatom az adatbázis nevét a védelem után, mi lesz a viselkedés?

Az átnevezett adatbázist a kezelés új adatbázisként kezeli. Ezért a szolgáltatás úgy kezeli ezt a helyzetet, mintha az adatbázis nem található volna, és a biztonsági mentések sikertelenek lennének. Az átnevezett adatbázis új adatbázisként jelenik meg, és védelemre kell konfigurálni.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Melyek az SAP HANA-adatbázisok azure-beli virtuális gépeken való biztonsági mentése előfeltételei?

Tekintse meg az [előfeltételeket,](tutorial-backup-sap-hana-db.md#prerequisites) és [mi az előzetes regisztrációs parancsfájl nem](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) szakaszok.

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Milyen engedélyeket kell beállítani az Azure-nak az SAP HANA-adatbázisok biztonsági mentésére?

Az előzetes regisztrációs parancsfájl futtatása beállítja a szükséges engedélyeket, hogy az Azure biztonsági másolatot az SAP HANA-adatbázisok. További, amit az előzetes regisztrációs szkript nem [itt](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>A biztonsági mentések 1.0-ról 2.0-ra történő áttelepítése után működnek?

Tekintse meg a hibaelhárítási útmutató nak ezt a [részét.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20)

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Az Azure HANA Backup virtuális IP-cím (terheléselosztó) és nem virtuális gép ellen állítható be?

Jelenleg nem képesek létrehozni a megoldást egy virtuális IP ellen egyedül. Szükségünk van egy virtuális gép re a megoldás végrehajtásához.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Sap HANA rendszerreplikációval (HSR) rendelkezem, hogyan konfigurálhatom a biztonsági mentést ehhez a telepítéshez?

A HSR elsődleges és másodlagos csomópontjait két különálló, nem kapcsolódó virtuális gépként kezeli a rendszer. A biztonsági mentést az elsődleges csomóponton kell konfigurálnia, és amikor a feladatátvétel megtörténik, a másodlagos csomóponton kell konfigurálnia a biztonsági mentést (amely most lesz az elsődleges csomópont). A biztonsági mentés nem történik automatikus "feladatátvétel" a másik csomópontra.

## <a name="restore"></a>Visszaállítás

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Miért nem látom azt a HANA rendszert, amelybe vissza szeretném állítani az adatbázisomat?

Ellenőrizze, hogy az SAP HANA-példány célhoz való visszaállítás összes előfeltétele teljesül-e. További információ: [Előfeltételek – SAP HANA-adatbázisok visszaállítása az Azure virtuális gépben.](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Miért nem sikerül a Felülírási adatbázis-visszaállítás az adatbázisban?

A visszaállítás során győződjön meg arról, hogy a **Kényszerítés felülírása** beállítás be van jelölve.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Miért jelenik meg a "Forrás- és célrendszerek a visszaállításhoz nem kompatibilisek" hibaüzenet?

Tekintse meg az SAP HANA Megjegyzés [1642148](https://launchpad.support.sap.com/#/notes/1642148) megtudhatja, hogy milyen visszaállítási típusok jelenleg támogatottak.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan biztonsági másolatot az Azure-beli virtuális gépeken futó [SAP HANA-adatbázisokról.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
