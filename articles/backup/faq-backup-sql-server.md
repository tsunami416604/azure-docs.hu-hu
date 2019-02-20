---
title: Az SQL Server-adatbázisok biztonsági másolatának az Azure virtuális gépekhez az Azure Backup szolgáltatással kapcsolatos gyakori kérdések
description: Az Azure virtuális gépekhez az Azure Backup az SQL Server-adatbázisok biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszokat biztosít.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430779"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>Az SQL Server rendszert futtató Azure virtuális gép biztonsági mentésével kapcsolatos gyakori kérdések

Ez a cikk az Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentésével kapcsolatos gyakori kérdésekre is válaszol a [Azure Backup](backup-overview.md) szolgáltatás.

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.



## <a name="can-i-throttle-the-backup-speed"></a>Képes szabályozni a biztonsági mentési sebesség?

Igen. A sebesség, amellyel a biztonsági mentési szabályzat fut, és egy SQL Server-példány gyakorolt hatás minimalizálása érdekében szabályozhatja. A beállítás módosítása:
1. Az SQL Server-példányon az a *C:\Program Files\Azure munkaterhelés Backup\bin mappa*, hozzon létre a **ExtensionSettingsOverrides.json** fájlt.
2. Az a **ExtensionSettingsOverrides.json** fájl, módosítsa a **DefaultBackupTasksThreshold** alacsonyabb értékűre (például 5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Mentse a módosításokat. Zárja be a fájlt.
4. Nyissa meg az SQL Server-példány **Feladatkezelő**. Indítsa újra a **AzureWLBackupCoordinatorSvc** szolgáltatás.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Futtathatok egy teljes biztonsági mentés egy másodlagos replikáról?
Nem. Ez a funkció nem támogatott.

## <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok ne hozzon létre riasztásokat?

Nem. A sikeres biztonsági mentési feladatok ne készítsen riasztásokat. Csak a biztonsági mentési feladatok sikertelenek, riasztást kap.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>A feladatok menü ütemezett biztonsági mentési feladatok számára látható adatok

Nem. A **biztonsági mentési feladatok** menü mutatja az igény szerinti feladat részleteit, de nem ütemezett biztonsági mentési feladatot. Ha bármely ütemezett biztonsági mentési feladat sikertelen, a részletek a sikertelen feladat riasztások érhető el. Figyelheti az összes ütemezett és ad hoc ad hoc biztonsági mentési feladatot, használja [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Jövőbeli adatbázisok automatikusan hozzáadódnak a biztonsági mentés?

Nem. Amikor konfigurál egy SQL Server-példány védelmét, ha a kiszolgáló szintű lehetőséget választja, az összes adatbázis kerülnek. Adatbázisok hozzáadása SQL Server-példányra védelem konfigurálása után, ha manuálisan kell hozzáadni az új adatbázisok biztosítja a védelmet. Az adatbázisok automatikusan nem szerepelnek a konfigurált védelmet.

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>Hogyan indítsa újra a védelmet a helyreállítás típusának módosítása után?

Indítson egy teljes biztonsági mentést. Naplóalapú biztonsági mentések a várt módon kezdődik.

## <a name="can-i-protect-availability-groups-on-premises"></a>Védheti a helyi rendelkezésre állási csoportokkal?

Nem. Az Azure Backup védi az Azure-ban futó SQL-kiszolgálók. Ha egy rendelkezésre állási csoport (rendelkezésre állási csoport) az Azure-ban és a helyszíni gépek között megoszlik, a rendelkezésre állási csoport védelme csak akkor, ha az elsődleges replika az Azure-ban fut-e. Emellett az Azure Backup csak az ugyanabban a régióban az Azure és a Recovery Services-tárolónak futtató csomópontok védi.

## <a name="can-i-protect-availability-groups-across-regions"></a>Védhetők rendelkezésre állási csoportjai régiók között?

Az Azure Backup helyreállítási tár a is észleli, és és a Recovery Services-tárolónak ugyanabban a régióban lévő összes csomópont védelmét. Ha több Azure-régióra kiterjedő SQL mindig a rendelkezésre állási csoport, konfigurálja a régióból, amely rendelkezik az elsődleges csomópont biztonsági mentést szeretne. Az Azure Backup tudják észleli, és a rendelkezésre állási csoport biztonsági mentési preferenciáját állapotokban található összes adatbázis védelmét. Ha a biztonsági mentési preferenciáját nem teljesül, biztonsági mentések sikertelenek lesznek, és kap a hibája című riasztás.

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>Kizárhatok adatbázisok az automatikus védelem engedélyezve van?

Nem, [automatikus védelem](backup-azure-sql-database.md#enable-auto-protection) a teljes példányra vonatkozik. Szelektív védelme nem adatbázis-példány automatikus védelmének használatával.

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>Használhatok különböző házirendeket automatikusan védett példány?

Ha már rendelkezik néhány védett adatbázis egyik példányában, továbbra is a megfelelő szabályzatokkal védett bekapcsolása után, akár **ON** a [automatikus védelem](backup-azure-sql-database.md#enable-auto-protection) lehetőséget. Azonban a nem védett adatbázisok együtt, amelyekre a jövőben jelentene csak egyetlen szabályzatot, amely alatt kell **biztonsági mentés konfigurálása** után az adatbázisok ki van jelölve. Valójában a ellentétben más védett adatbázisok nem is módosítható a házirend-adatbázis automatikusan védett példány alatt.
Ha azt szeretné, ehhez a kizárólag az tiltsa le az automatikus védelme a példányon zajlik, és módosíthatja a szabályzatot, hogy az adatbázis. Most már engedélyezheti újra ez a példány automatikus védelmét.

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>Ha az automatikus védelem töröl egy adatbázist biztonsági mentések leáll?

Nem, az adatbázis automatikusan védett példányából megszakadása, a biztonsági mentéseket, hogy az adatbázis vannak továbbra is kísérlet. Ez azt jelenti, hogy a törölt adatbázisok alatt állapotúként jelenik meg kezdődik-e **biztonsági másolati elemek** és továbbra is védett módon kezelik.

Ez az adatbázis védelmének megszüntetése csak úgy, hogy tiltsa le a [automatikus védelem](backup-azure-sql-database.md#enable-auto-protection) zajlik a példányon majd válassza a **biztonsági mentés leállítása** lehetőség **biztonsági másolati elemek**az adatbázishoz. Most már engedélyezheti újra ez a példány automatikus védelmét.

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>Miért nem látom, hogy hozzáadott-adatbázis automatikusan védett példány?

Nem láthatók az újonnan hozzáadott adatbázis- [automatikusan védett](backup-azure-sql-database.md#enable-auto-protection) keretében azonnal védett példány védett elemek. Ennek oka az, a felderítés általában 8 óránként futtatja. Azonban a felhasználó segítségével futtathat egy kézi felderítés **adatbázisok helyreállításához** azonnal látható módon adatbázisok felderítéséhez és védelme az új lehetőség a kép alatt:

  ![Újonnan hozzáadott adatbázisait megtekintése](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](backup-azure-sql-database.md) -beli virtuális gépen futó SQL Server-adatbázis biztonsági mentése.
