---
title: Gyakori kérdések – SQL Server-adatbázisok biztonsági mentése Az Azure virtuális gépein
description: Válaszok az SQL Server-adatbázisok Azure-beli virtuális gépeken való biztonsági mentésével kapcsolatos gyakori kérdésekre az Azure Backup segítségével.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247708"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Gyakori kérdések az Azure virtuális gépek biztonsági mentésén futó SQL Server-adatbázisokról

Ez a cikk az Azure virtuális gépeken (VM-eken) futó és az [Azure Backup](backup-overview.md) szolgáltatás használatával kapcsolatos gyakori kérdésekre ad választ.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Használhatom az Azure biztonsági mentést az IaaS virtuális géphez és az SQL Serverhez ugyanazon a gépen?

Igen, a virtuális gép biztonsági és SQL biztonsági mentése is rendelkezhet ugyanazon a virtuális gépen. Ebben az esetben belsőleg aktiváljuk a csak másolási teljes biztonsági mentést a virtuális gépen, hogy ne csonkolja a naplókat.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Újrapróbálkozik a megoldás, vagy automatikusan meggyógyítja a biztonsági mentéseket?

Bizonyos körülmények között az Azure Backup szolgáltatás elindítja a javító biztonsági mentéseket. Auto-heal megtörténhet bármely hat feltétel alább említett:

- Ha a napló vagy a különbözeti biztonsági mentés LSN-érvényesítési hiba miatt sikertelen, a következő napló- vagy különbözeti biztonsági mentés helyett teljes biztonsági másolatlett.
- Ha a napló vagy a különbözeti biztonsági mentés előtt nem történt teljes biztonsági mentés, akkor a napló vagy a különbözeti biztonsági mentés helyett teljes biztonsági másolatlett.
- Ha a legutóbbi teljes biztonsági mentés időpontban lévő időpontja 15 napnál régebbi, a következő napló- vagy különbözeti biztonsági mentés helyett teljes biztonsági másolatlá alakul át.
- A bővítmény frissítése miatt megszakítandó összes biztonsági mentési feladat újra aktiválódik a frissítés befejezése után, és a bővítmény elindul.
- Ha úgy dönt, hogy felülírja az adatbázist a visszaállítás során, a következő napló/különbözeti biztonsági mentés sikertelen lesz, és helyette egy teljes biztonsági mentés aktiválódik.
- Azokban az esetekben, ahol a teljes biztonsági mentés re van szükség a naplóláncok az adatbázis-helyreállítási modell változása miatt, a teljes automatikusan aktiválódik a következő ütemezés.

Automatikus gyógyulás, mivel a képesség alapértelmezés szerint minden felhasználó számára engedélyezve van; Azonban abban az esetben, ha úgy dönt, hogy leiratkozik róla, majd hajtsa végre az alábbi:

- Az SQL Server-példány *C:\Program Files\Azure Workload Backup\bin* mappájában hozza létre vagy szerkesztse az **ExtensionSettingsOverrides.json** fájlt.
- Az **ExtensionSettingsOverrides.json**fájlban állítsa be *a(z) {"EnableAutoHealer": false}* beállítást.
- Mentse a módosításokat, és zárja be a fájlt.
- Az SQL Server-példányon nyissa meg a **Feladatkezelés segédprogramot,** majd indítsa újra az **AzureWLBackupCoordinatorSvc** szolgáltatást.

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Szabályozhatom, hogy hány egyidejű biztonsági mentés fut az SQL-kiszolgálón?

Igen. Szabályozhatja a biztonsági mentési házirend futásának sebességét, hogy minimalizálja az SQL Server-példányra gyakorolt hatást. A beállítás módosítása:

1. Az SQL Server-példány *C:\Program Files\Azure Workload Backup\bin* mappájában hozza létre az *ExtensionSettingsOverrides.json* fájlt.
2. Az *ExtensionSettingsOverrides.json* fájlban módosítsa a **DefaultBackupTasksThreshold** beállítást alacsonyabb értékre (például 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
A DefaultBackupTasksThreshold alapértelmezett értéke **20**.

3. Mentse a módosításokat, és zárja be a fájlt.
4. Az SQL Server példányon nyissa meg a **Feladatkezelőt.** Indítsa újra az **AzureWLBackupCoordinatorSvc** szolgáltatást.<br/> <br/>
 Bár ez a módszer segít, ha a biztonsági másolat készítő alkalmazás nagy mennyiségű erőforrást használ, az SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) általánosabb módja a processzor, a fizikai IO és a memória mennyiségének meghatározására, amelyet a bejövő alkalmazáskérelmek használhatnak.

> [!NOTE]
> A UX-ben még mindig előre haladhat, és ütemezhet annyi biztonsági mentést egy adott időpontban, de a fenti példa szerint 5 csúszó ablakban dolgoznak fel.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Futtathatok teljes biztonsági másolatot egy másodlagos replikáról?

Az SQL-korlátozások nak megfelelően futtathatja a Csak másolás teljes biztonsági mentést a másodlagos replikán; azonban a teljes biztonsági mentés nem engedélyezett.

## <a name="can-i-protect-availability-groups-on-premises"></a>Védhetem a rendelkezésre állási csoportokat a helyszínen?

Nem. Az Azure Backup védi az Azure-ban futó SQL Server-adatbázisokat. Ha egy rendelkezésre állási csoport (AG) az Azure és a helyszíni gépek között oszlik meg, az AG csak akkor védhető, ha az elsődleges replika fut az Azure-ban. Emellett az Azure Backup csak azokat a csomópontokat védi, amelyek ugyanabban az Azure-régióban futnak, mint a Recovery Services-tároló.

## <a name="can-i-protect-availability-groups-across-regions"></a>Biztosíthatok védelmet különböző régiókban található rendelkezésre állási csoportok számára?

Az Azure Backup Recovery Services tároló javunkra észleli és védi az összes csomópontot, amelyek ugyanabban a régióban, mint a tároló. Ha az SQL Server Mindig rendelkezésre állási csoport több Azure-régióra is kiterjed, állítsa be a biztonsági mentést az elsődleges csomót tartalmazó régióból. Az Azure Backup képes észlelni és védeni a rendelkezésre állási csoportban lévő összes adatbázist a biztonsági mentési preferenciának megfelelően. Ha a biztonsági mentési beállítás nem teljesül, a biztonsági mentések sikertelenek, és megkapja a hibariasztást.

## <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok létrehoznak riasztásokat?

Nem. A sikeres biztonsági mentési feladatok nem hoznak létre riasztásokat. A riasztások csak a sikertelen biztonsági mentési feladatokhoz kerülnek elküldésre. A portálriasztások részletes viselkedését [itt](backup-azure-monitoring-built-in-monitor.md)dokumentálja. Azonban abban az esetben, ha érdekel, még a sikeres feladatok esetén is rendelkezik riasztásokkal, [használhatja a Figyelés az Azure Monitor használatával.](backup-azure-monitoring-use-azuremonitor.md)

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Megnézhetem az ütemezett biztonsági mentési feladatokat a Biztonsági másolat készítési feladatok menüben?

A **Biztonsági másolat menü** ben csak igény szerinti biztonsági mentési feladatok jelennek meg. Ütemezett feladathasználata [figyelés az Azure Monitor használatával.](backup-azure-monitoring-use-azuremonitor.md)

## <a name="are-future-databases-automatically-added-for-backup"></a>A jövőbeli adatbázisokról is automatikusan készül biztonsági mentés?

Igen, ezt a képességet [automatikus védelemmel](backup-sql-server-database-azure-vms.md#enable-auto-protection)érheti el.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Ha egy automatikusan védett példányból törlök egy adatbázist, mi történik a biztonsági másolatokkal?

Ha egy adatbázist egy automatikusan védett példányról dobnak ki, a rendszer továbbra is megkísérli az adatbázis biztonsági másolatait. Ez azt jelenti, hogy a törölt adatbázis a **Biztonsági másolat elemek** alatt nem megfelelő állapotúként jelenik meg, és továbbra is védett.

Az adatbázis védelmének leállításának helyes módja a **Biztonsági mentés leállítása** az adatbázis **adatainak törlésével.**  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Ha leállítom egy automatikusan védett adatbázis biztonsági mentési műveletét, mi lesz a viselkedése?

Ha **leállítja a biztonsági mentést az adatok megőrzésével,** nem kerül sor jövőbeli biztonsági mentésekre, és a meglévő helyreállítási pontok érintetlenek maradnak. Az adatbázis továbbra is védettnek minősül, és a Biztonsági másolat elemek alatt jelenik **meg.**

Ha **leállítja a biztonsági mentést a törlési adatokkal,** nem kerül sor jövőbeli biztonsági mentésekre, és a meglévő helyreállítási pontok is törlődnek. Az adatbázis nem védettnek minősül, és a példány alatt jelenik meg a Biztonsági másolat konfigurálása című részben. Azonban, ellentétben más up-protected adatbázisok, amelyek manuálisan választható, vagy hogy lehet kapni automatikusan védett, ez az adatbázis jelenik meg szürkén, és nem lehet kiválasztani. Az adatbázis újbóli védelmének egyetlen módja az automatikus védelem letiltása a példányon. Most már kiválaszthatja ezt az adatbázist, és konfigurálhatja a védelmet, vagy újra engedélyezheti az automatikus védelmet a példányon.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Ha megváltoztatom az adatbázis nevét a védelem után, mi lesz a viselkedés?

Az átnevezett adatbázist a kezelés új adatbázisként kezeli. Ezért a szolgáltatás fogja kezelni ezt a helyzetet, mintha az adatbázis nem található, és nem a biztonsági mentések.

Kiválaszthatja az adatbázist, amely most átlett nevezve, és konfigurálhatja a védelmet. Abban az esetben, ha az automatikus védelem engedélyezve van a példányon, az átnevezett adatbázis automatikusan észlelhető és védett lesz.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Miért nem látom az automatikusan védett példányok hozzáadott adatbázisát?

Előfordulhat, hogy [egy automatikusan védett példányhoz hozzáadott](backup-sql-server-database-azure-vms.md#enable-auto-protection) adatbázis nem jelenik meg azonnal a védett elemek alatt. Ennek az az oka, hogy a felderítés általában 8 óránként fut. Az új adatbázisokat azonban azonnal felderítheti és megvédheti, ha manuálisan futtat egy felderítést az **Újrafelfedezz adatok kiválasztása**lehetőséget választva, ahogy az az alábbi képen látható:

  ![Újonnan hozzáadott adatbázis manuális felderítése](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [biztonsági másolatot lehet egy Azure-beli](backup-azure-sql-database.md) virtuális rendszeren futó SQL Server-adatbázisról.
