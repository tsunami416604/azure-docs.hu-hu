---
title: Az SQL Server-adatbázisok biztonsági másolatának az Azure virtuális gépekhez az Azure Backup szolgáltatással kapcsolatos gyakori kérdések
description: Az Azure virtuális gépekhez az Azure Backup az SQL Server-adatbázisok biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszok.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sachdevaswati
ms.openlocfilehash: 8d6323c73e5313a29b7b0df09ebdd24a190879f5
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876428"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Egy Azure virtuális gépek biztonsági mentésének futó SQL Server-adatbázisok – gyakori kérdések

Ez a cikk az SQL Server-adatbázisok biztonsági mentésével kapcsolatos általános kérdéseket válaszol meg futtató Azure virtuális gépeken (VM), és használják a [Azure Backup](backup-overview.md) szolgáltatás.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Nem a megoldás próbálkozzon újra, vagy automatikus javítása a biztonsági mentéseket?

Bizonyos körülmények között az Azure Backup szolgáltatás elindítja a helyreigazító biztonsági mentést. Automatikus javítása fordulhat elő, az alábbi hat feltételek bármelyike:

  - Napló vagy különbségi biztonsági másolat LSN-érvényesítési hiba miatt nem sikerül, ha tovább napló vagy különbségi biztonsági másolat inkább alakítja át egy teljes biztonsági mentést.
  - Ha nincs teljes biztonsági mentés egy napló vagy a különbözeti biztonsági mentése előtt történt, adott napló vagy a különbözeti biztonsági másolat inkább alakítja át egy teljes biztonsági mentés.
  - Ha a legutóbbi teljes biztonsági-időponthoz régebbi, mint 15 napon keresztül, a következő napló vagy a különbözeti biztonsági másolat inkább alakítja át egy teljes biztonsági mentést.
  - A biztonsági mentési feladatok, amelyek a get-bővítmény frissítése miatt megszakadt újra akkor indul el, miután a frissítés befejezése és a bővítmény elindult.
  - Kíván visszaállítás során az adatbázis felülírása, ha a következő naplófájl, illetve különbségi biztonsági mentés sikertelen lesz, és a egy teljes biztonsági mentés akkor aktiválódik, helyette.
  - Olyan esetekben, ahol egy teljes biztonsági mentés szükséges az adatbázis helyreállítási modelljének módosítása miatt a napló láncok alaphelyzetbe egy teljes lekérdezi automatikusan elindul, a következő ütemezés szerint.

Automatikus – javítása, egy olyan funkció az összes felhasználó számára alapértelmezés szerint engedélyezve van; Azonban abban az esetben az elutasítás azt választja, majd hajtsa végre az alábbi:

  * Az SQL Server-példányon az a *C:\Program Files\Azure munkaterhelés Backup\bin* mappa létrehozása vagy szerkesztése a **ExtensionSettingsOverrides.json** fájlt.
  * Az a **ExtensionSettingsOverrides.json**állítsa be *{"EnableAutoHealer": false}*.
  * Mentse a módosításokat, és zárja be a fájlt.
  * Nyissa meg az SQL Server-példány **kezelheti a feladat** , majd indítsa újra a **AzureWLBackupCoordinatorSvc** szolgáltatás.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>Szabályozhatom, hogy hány egyidejű biztonsági mentések futtatása az SQL Server?

Igen. A sebesség, amellyel a biztonsági mentési szabályzat fut, és egy SQL Server-példány gyakorolt hatás minimalizálása érdekében szabályozhatja. A beállítás módosítása:
1. Az SQL Server-példányon a a *C:\Program Files\Azure munkaterhelés Backup\bin* mappában hozzon létre a *ExtensionSettingsOverrides.json* fájlt.
2. Az a *ExtensionSettingsOverrides.json* fájl, módosítsa a **DefaultBackupTasksThreshold** (például 5) alacsonyabb értékűre állítja. <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Mentse a módosításokat, és zárja be a fájlt.
4. Nyissa meg az SQL Server-példány **Feladatkezelő**. Indítsa újra a **AzureWLBackupCoordinatorSvc** szolgáltatás.

> [!NOTE]
> Felhasználói továbbra is lépjen tovább, és tetszőleges számú biztonsági mentések ütemezése az egy adott időpontban azonban fog feldolgozása egy csúszóablakban tegyük fel, 5, a fenti példa szerint.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Futtathatok egy teljes biztonsági mentés egy másodlagos replikáról?
SQL-korlátozások megfelelően futtathatja másolása csak teljes biztonsági mentés másodlagos másodpéldányon; azonban a teljes biztonsági mentés nem engedélyezett.

## <a name="can-i-protect-availability-groups-on-premises"></a>Rendelkezésre állási csoportok a helyszíni tudja megvédeni?
Nem. Az Azure Backup védi az Azure-ban futó SQL Server-adatbázisok. Ha egy rendelkezésre állási csoport (rendelkezésre állási csoport) az Azure-ban és a helyszíni gépek között megoszlik, a rendelkezésre állási csoport védelme csak akkor, ha az elsődleges replika az Azure-ban fut-e. Emellett az Azure Backup csak azoknak a csomópontoknak ugyanabban a régióban az Azure és a Recovery Services-tárolónak futtató védi.

## <a name="can-i-protect-availability-groups-across-regions"></a>Védhetők rendelkezésre állási csoportjai régiók között?
Az Azure Backup helyreállítási tárban a is észleli, és a a tárral azonos régióban lévő összes csomópont védelmét. Ha az SQL Server Always On rendelkezésre állási csoport több Azure-régióra is kiterjed, állítsa be a biztonsági mentés a régióból, amely rendelkezik az elsődleges csomópont. Az Azure Backup a is észleli, és a biztonsági mentési preferenciáját szerint a rendelkezésre állási csoportban található összes adatbázis védelmét. Ha a biztonsági mentési preferenciáját nem teljesül, biztonsági mentések sikertelenek lesznek, és hiba értesítést.

## <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok ne hozzon létre riasztásokat?
Nem. A sikeres biztonsági mentési feladatok ne készítsen riasztásokat. Csak a biztonsági mentési feladatok sikertelenek, riasztást kap. Részletes portál riasztások viselkedésének szerződését [Itt](backup-azure-monitoring-built-in-monitor.md). Azonban abban az esetben is érdeklik a rendelkezik a riasztások használhatja még a sikeres feladatokra [figyelése az Azure Monitor használatával](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Láthatja a biztonsági mentési feladatok menü ütemezett biztonsági mentési feladatot?
A **biztonsági mentési feladat** menü csak akkor jelenik meg az ad hoc biztonsági mentési feladatok. Ütemezett feladatot használni [figyelése az Azure Monitor használatával](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Jövőbeli adatbázisok automatikusan hozzáadódnak a biztonsági mentés?
Igen, akkor érhető el ez a funkció a [automatikus védelem](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Ha egy adatbázist egy autoprotected-példányból, mi történik a biztonsági mentéseket?
Ha egy adatbázist egy autoprotected példányból megszakad, a az adatbázis biztonsági mentéséhez továbbra is történt kísérlet. Ez azt jelenti, hogy a törölt adatbázisok alatt állapotúként jelenik meg kezdődik-e **biztonsági másolati elemek** , és továbbra is védve van.

Ez az adatbázis védelmének megszüntetése a megfelelő módszer az, hogy tegye **biztonsági mentés leállítása** a **adatok törlése** ezen az adatbázison.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Ha le egy autoprotected adatbázis biztonsági mentési művelet mi lesz a viselkedés?
Ha mégis **biztonsági mentés leállítása az adatok megőrzése**, nem készít jövőbeli biztonsági kerül sor, és a meglévő helyreállítási pontok csoportengedély változatlan marad. Az adatbázis még figyelembe venni, mivel a védett és alatt látható lesz a **biztonsági mentési elemek**.

Ha mégis **biztonsági mentés leállítása az adatok törlésével**, nem készít jövőbeli biztonsági kerül sor, és a meglévő helyreállítási pontok szintén törölve lesznek. Az adatbázis minősülnek nem védett és a példány konfigurálása biztonsági mentés alatt kell feltüntetni. Azonban más felfelé védett adatbázisok, které lze vybrat manuálisan, vagy beszerezheti a autoprotected, ellentétben az adatbázis kiszürkítve jelenik, és nem jelölhető ki. Ez az adatbázis védelmének újbóli beállításához egyetlen módja, hogy a példány automatikus védelmének letiltásához. Most válassza ki az adatbázist, és konfigurálja a védelmet a, vagy engedélyezze újra a példány automatikus védelmét.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Ha módosítom az adatbázis nevét, védett azt követően, mi lesz a viselkedés?
Egy új adatbázist újra elnevezett adatbázist kell kezelni. Ezért a szolgáltatás kezeli ezt a helyzetet, ha az adatbázis nem található, és a biztonsági mentés sikertelen.

Kiválaszthatja az adatbázis, amely lett nevezve, és konfigurálja a védelmet, a. Abban az esetben az automatikus védelme a példányon engedélyezve van, az átnevezett adatbázis lehet automatikusan észleli és védett.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Miért nem látom, hogy egy hozzáadott adatbázisait autoprotected példányt?
Egy adatbázis-, amikor [hozzáadása egy autoprotected példányhoz](backup-sql-server-database-azure-vms.md#enable-auto-protection) előfordulhat, hogy nem jelenik meg azonnal a védett elemek. Ennek oka az, a felderítés általában 8 óránként futtatja. Azonban felderítése, és azonnal védelme új adatbázisokat, abban az esetben, ha a felderítést kiválasztásával manuális futtatása **adatbázisok helyreállításához**, ahogy az alábbi képen látható.

  ![Egy újonnan hozzáadott adatbázisait manuálisan felderítése](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [egy SQL Server-adatbázis biztonsági mentése](backup-azure-sql-database.md) futtató-beli virtuális gépen.
