---
title: Az SQL Server-adatbázisok biztonsági másolatának az Azure virtuális gépekhez az Azure Backup szolgáltatással kapcsolatos gyakori kérdések
description: Az Azure virtuális gépekhez az Azure Backup az SQL Server-adatbázisok biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszok.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: 3b7649a029c6c44cd8a25ea553ff2091f816dd3c
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214826"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Egy Azure virtuális gépek biztonsági mentésének futó SQL Server-adatbázisok – gyakori kérdések

Ez a cikk az SQL Server-adatbázisok biztonsági mentésével kapcsolatos általános kérdéseket válaszol meg futtató Azure virtuális gépeken (VM), és használják a [Azure Backup](backup-overview.md) szolgáltatás.

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.

## <a name="can-i-throttle-the-backup-speed"></a>Képes szabályozni a biztonsági mentési sebesség?

Igen. A sebesség, amellyel a biztonsági mentési szabályzat fut, és egy SQL Server-példány gyakorolt hatás minimalizálása érdekében szabályozhatja. A beállítás módosítása:
1. Az SQL Server-példányon a a *C:\Program Files\Azure munkaterhelés Backup\bin* mappában hozzon létre a *ExtensionSettingsOverrides.json* fájlt.
2. Az a *ExtensionSettingsOverrides.json* fájl, módosítsa a **DefaultBackupTasksThreshold** (például 5) alacsonyabb értékűre állítja. <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Mentse a módosításokat, és zárja be a fájlt.
4. Nyissa meg az SQL Server-példány **Feladatkezelő**. Indítsa újra a **AzureWLBackupCoordinatorSvc** szolgáltatás.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Futtathatok egy teljes biztonsági mentés egy másodlagos replikáról?
Nem. Ez a funkció nem támogatott.

## <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok ne hozzon létre riasztásokat?

Nem. A sikeres biztonsági mentési feladatok ne készítsen riasztásokat. Csak a biztonsági mentési feladatok sikertelenek, riasztást kap.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>A feladatok menü ütemezett biztonsági mentési feladatok számára látható adatok

Nem. A **biztonsági mentési feladatok** menü mutatja az igény szerinti feladat részleteit, de nem ütemezett biztonsági mentési feladatot. Minden ütemezett biztonsági mentési feladat sikertelen, ha a sikertelen feladat riasztások részleteinek találja. Minden ütemezett és nem ütemezett biztonsági mentési feladatok monitorozásához használja [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Jövőbeli adatbázisok automatikusan hozzáadódnak a biztonsági mentés?

Nem. A kiszolgálói szintű beállítás kiválasztásakor, egy SQL Server-példány védelmének beállításakor a rendszer hozzáadja az összes adatbázis. Miután beállította a védelmet, új adatbázisok védelméhez azokat manuálisan kell hozzáadni. Új adatbázis automatikusan védett nem.

##  <a name="how-do-i-restart-protection-after-i-change-recovery-type"></a>Hogyan újraindítása meg védelem, helyreállítás típusának módosítása után?

Indítson egy teljes biztonsági mentést. Naplóalapú biztonsági mentések a várt módon kezdődik.

## <a name="can-i-protect-availability-groups-on-premises"></a>Rendelkezésre állási csoportok a helyszíni tudja megvédeni?

Nem. Az Azure Backup védi az Azure-ban futó SQL Server-adatbázisok. Ha egy rendelkezésre állási csoport (rendelkezésre állási csoport) az Azure-ban és a helyszíni gépek között megoszlik, a rendelkezésre állási csoport védelme csak akkor, ha az elsődleges replika az Azure-ban fut-e. Emellett az Azure Backup csak azoknak a csomópontoknak ugyanabban a régióban az Azure és a Recovery Services-tárolónak futtató védi.

## <a name="can-i-protect-availability-groups-across-regions"></a>Védhetők rendelkezésre állási csoportjai régiók között?

Az Azure Backup helyreállítási tárban a is észleli, és a a tárral azonos régióban lévő összes csomópont védelmét. Ha az SQL Server Always On rendelkezésre állási csoport több Azure-régióra is kiterjed, állítsa be a biztonsági mentés a régióból, amely rendelkezik az elsődleges csomópont. Az Azure Backup a is észleli, és a biztonsági mentési preferenciáját szerint a rendelkezésre állási csoportban található összes adatbázis védelmét. Ha a biztonsági mentési preferenciáját nem teljesül, biztonsági mentések sikertelenek lesznek, és hiba értesítést.

## <a name="can-i-exclude-databases-with-autoprotection-enabled"></a>Kizárhatok adatbázisok autoprotection engedélyezve van?

Nem. Autoprotection [vonatkozik, az egész példány](backup-azure-sql-database.md#enable-auto-protection). Nem használhat autoprotection szelektív védelme érdekében az adatbázis egyik példányában.

## <a name="can-i-have-different-policies-in-an-autoprotected-instance"></a>Használhatok különböző házirendeket az autoprotected példányában?

Ha a példány már tartalmazza az egyes védett adatbázisok, azok továbbra is után is a megfelelő házirendeket a védendő [autoprotection bekapcsolása](backup-azure-sql-database.md#enable-auto-protection). A nem védett adatbázisok és a később hozzáadott adatbázisok azonban lesz az egyetlen házirendet. Ez a szabályzat alapján meghatározhatja **biztonsági mentés konfigurálása** az adatbázisok kiválasztása után. Valójában a ellentétben más védett adatbázisok nem is módosítható a házirend-adatbázis, amely az autoprotected példányában.
Annak az adatbázisnak a házirend módosítása csak úgy, hogy ideiglenesen letilthatja a-példány autoprotection. Majd újból engedélyezi a példány autoprotection.

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-will-backups-stop"></a>Ha egy adatbázist egy autoprotected-példányból, biztonsági mentés leáll?

Nem. Ha egy adatbázist egy autoprotected példányból megszakad, a az adatbázis biztonsági mentéséhez továbbra is történt kísérlet. Ez azt jelenti, hogy a törölt adatbázisok alatt állapotúként jelenik meg kezdődik-e **biztonsági másolati elemek** , és továbbra is védve van.

Ez az adatbázis védelmének megszüntetése csak úgy, hogy ideiglenesen [autoprotection letiltása](backup-azure-sql-database.md#enable-auto-protection) -példányon. Ezután a **biztonsági másolati elemek** válassza ki az adatbázis **biztonsági mentés leállítása**. Ezután újraengedélyezni autoprotection ehhez a példányhoz.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Miért nem látom, hogy egy hozzáadott adatbázisait autoprotected példányt?

Egy adatbázis-, amikor [hozzáadása egy autoprotected példányhoz](backup-azure-sql-database.md#enable-auto-protection) előfordulhat, hogy nem jelenik meg azonnal a védett elemek. Ennek oka az, a felderítés általában 8 óránként futtatja. Azonban felderítése, és azonnal védelme új adatbázisokat, abban az esetben, ha a felderítést kiválasztásával manuális futtatása **adatbázisok helyreállításához**, ahogy az alábbi képen látható.

  ![Egy újonnan hozzáadott adatbázisait manuálisan felderítése](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [egy SQL Server-adatbázis biztonsági mentése](backup-azure-sql-database.md) futtató-beli virtuális gépen.
