---
title: Az Azure VM Backup – gyakori kérdések
description: 'Válaszok a következő gyakori kérdésekre: hogyan működik az Azure-beli virtuális gépek biztonsági mentése, mik a korlátozások, és mi történik, ha módosítások történnek a szabályzatban'
services: backup
author: trinadhk
manager: shreeshd
keywords: azure-beli virtuális gép biztonsági mentése, azure-beli virtuális gép visszaállítása, biztonsági mentési szabályzat
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: 58b0622da2ef617e652c8bb9dacbf7daa2d79966
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058409"
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Kérdések az Azure VM Backup szolgáltatással kapcsolatban
A cikk gyakori kérdésekre adott válaszokat tartalmazó szakaszaiban gyorsan áttekinthető az Azure VM Backup összetevőinek működése. Egyes válaszokban részletes információkat tartalmazó cikkekre mutató hivatkozások találhatók. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>A Recovery Services-tárolók támogatják a klasszikus vagy a Resource Manager alapú virtuális gépeket? <br/>
A Recovery Services-tárolók mindkét modellt támogatják.  A Recovery Services-tárolót is készítsen biztonsági másolatot egy klasszikus virtuális gép vagy egy Resource Manager virtuális Gépet.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Mely konfigurációkat nem támogatottak az Azure VM backup?
Lépkedjen végig [támogatott operációs rendszerek](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup) és [korlátozások a virtuális gép biztonsági mentése](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Miért nem látom a virtuális gépemet a Biztonsági mentés konfigurálása varázslóban?
Biztonsági mentés konfigurálása varázslóban az Azure Backup csak sorolja fel, amelyek virtuális gépek:
  * Még nem védettek, ellenőrizheti a virtuális gép biztonsági mentési állapotának lépjen a virtuális gép paneljén, és a beállítások menüjében a biztonsági mentési állapotának ellenőrzése. További információ a [Virtuális gép biztonsági mentési állapotának ellenőrzéséről](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu)
  * A virtuális géppel azonos régióba tartozik

## <a name="backup"></a>Biztonsági mentés
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Az igény szerinti biztonsági mentési feladatok ugyanazt a megőrzési ütemezést követik, mint az ütemezett biztonsági mentések?
Nem. A megőrzési tartomány egy igény szerinti biztonsági mentéshez adjon meg. Alapértelmezés szerint 30 napig őrzi meg portálról elindításakor. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Nemrég engedélyeztem az Azure Disk Encryption szolgáltatást néhány virtuális gépen. A biztonsági mentések továbbra is működni fognak?
Engedélyeznie kell az Azure Backup szolgáltatásnak a Key Vault elérését. Ezeket az engedélyeket a PowerShellben adhatja meg, a [PowerShell-dokumentáció](backup-azure-vms-automation.md) *Biztonsági mentés engedélyezése* szakaszában található lépések végrehajtásával.

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Egy virtuális gép lemezeit felügyelt lemezekre migráltam. A biztonsági mentések továbbra is működni fognak?
Igen, biztonsági mentések zökkenőmentesen működik, és konfigurálja újra a biztonsági mentés nincs szükség. 

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Virtuális gép leállt. Egy igény szerinti vagy ütemezett biztonsági mentési munkahelyi lesz?
Igen. Akkor is, ha egy gép le van állítva a biztonsági mentések használhatók, és a helyreállítási pont van megjelölve rendszerösszeomlás egységes. További részletekért tekintse meg az adatok konzisztenciájának szakaszában [Ez a cikk](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Egy folyamatban lévő biztonsági mentési feladat lemondható?
Igen. Megszakíthatja a biztonsági mentési feladat, ha "Pillanatkép elkészítéséhez" fázisban. **Egy feladat nem szakítható meg, ha folyamatban van az adatátviteli pillanatképből**. 

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>Erőforráscsoport zárolási bekapcsolva a biztonsági másolat felügyelt lemezes virtuális gépek A biztonsági mentések továbbra is működni fognak?
Ha a felhasználó zárolja magát az erőforráscsoportot, a Backup szolgáltatás, nem tudja törölni a régebbi helyreállítási pontokat. Emiatt új biztonsági mentései kezdenek, a háttérbeli előírt maximális 18 visszaállítási pontok korlátozva van. Ha a biztonsági mentések egy belső hiba miatt nem működik a RG zárolás után, kövesse az alábbi [a visszaállítás eltávolítására vonatkozó lépéseket pont gyűjtemény](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock).

### <a name="does-backup-policy-take-daylight-saving-timedst-into-account"></a>Biztonsági mentési szabályzat nem figyelembe vennie a nyári mentése Time(DST)?
Nem. Vegye figyelembe, hogy dátum és idő a helyi számítógépen megjelenik a helyi idő és a nyári időszámítás aktuális. Így a beállított ütemezett biztonsági mentések idejét nyári Időszámítás miatt a helyi idő eltérő lehet.

## <a name="restore"></a>Visszaállítás
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>A lemezek visszaállítását vagy a teljes virtuális gép visszaállítását válasszam?
Úgy az Azure teljes virtuális gép visszaállítását, mint a gyors létrehozási lehetőséget. Állítsa vissza a virtuális gép lehetőséget módosításokat a lemezek nevét, ezeket a lemezeket, nyilvános IP-címek és hálózati adapter által használt tárolók nevei. A módosítás az egyedi-e a virtuális gép létrehozása során létrehozott erőforrások karbantartása szükséges. Azonban nem felveszi a virtuális gép rendelkezésre állási csoporthoz. 

A lemezek visszaállítását a következőkhöz használhatja:
  * A virtuális gép, amely az időponthoz kötött például a megadott méret módosításával létrejön testreszabása
  * Adja hozzá a konfigurációk, amelyek nem találhatók meg a biztonsági mentés idején 
  * A létrejövő erőforrások elnevezési konvencióinak vezérléséhez
  * Virtuális gép hozzáadása rendelkezésre állási csoporthoz
  * A bármely más olyan konfiguráció, amely csak a PowerShell vagy deklaratív Sablondefiníció használatával érhető el
  
### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>Nem felügyelt lemezes virtuális gép biztonsági másolatait használhatja a managed Disks szolgáltatásba magasabb saját lemezek visszaállítása?
Igen, az áttelepítése lemezeit nem felügyelt kezelt előtt készített biztonsági másolatok is használhatja. Alapértelmezés szerint visszaállítási virtuális gép feladat nem felügyelt lemezekkel rendelkező hoz létre egy virtuális Gépet. Visszaállítás lemezek funkció használatával állítsa vissza a lemezeket, és ezek segítségével egy virtuális gép létrehozása felügyelt lemezeken. 

### <a name="what-is-the-procedure-to-restore-a-vm-to-a-restore-point-taken-before-the-conversion-from-unmanaged-to-managed-disks-was-done-for-a-vm"></a>Mi az az eljárást követve virtuális gép visszaállítása egy visszaállítási pontra felügyeltről felügyelt lemezekre való konvertálást egy virtuális gép elkészítése előtt?
Ebben az esetben alapértelmezés szerint visszaállítási virtuális gép feladat létrehoz egy virtuális gép nem felügyelt lemezek. A felügyelt lemezekkel rendelkező virtuális gép létrehozásához:
1. [Nem felügyelt lemezek visszaállítása](tutorial-restore-disk.md#restore-a-vm-disk)
2. [A visszaállított lemez átalakítása felügyelt lemezek](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)
3. [A felügyelt lemezekkel rendelkező virtuális gép létrehozása](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk) <br>
Powershell-parancsmagok, tekintse meg [Itt](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="can-i-restore-the-vm-if-my-vm-is-deleted"></a>Vissza tudok állítani a virtuális gép törlésekor a virtuális gépem?
Igen. Életciklus-VM és a megfelelő biztonsági mentési elem eltérőek. Így még akkor is, ha törli a virtuális gép, megnyithatja a megfelelő elemet a helyreállítási tárban lévő biztonsági mentési és helyreállítási pontok egyikével a visszaállítás elindítása. 

## <a name="manage-vm-backups"></a>Virtuális gép biztonsági mentéseinek kezelése
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Mi történik, ha módosítom a biztonsági mentési szabályzatot a virtuális gépen vagy gépeken?
Ha egy új szabályzatot alkalmaznak egy virtuális gépen alkalmazza, ütemezése és megőrzése az új házirendet kell követni. Ha növeli a megőrzési időtartamot, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak. Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítási feladat során törlendőként lesznek megjelölve. 

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Hogyan helyezhetem át erőforráscsoportok között az Azure backup-ban regisztrált virtuális gép?
Kövesse az alábbi lépéseket az erőforrás-csoport sikeresen készíteni virtuális gép áthelyezése 
1. Ideiglenesen állítsa le a biztonsági mentés és a biztonsági másolatok adatainak megőrzése
2. A virtuális gép áthelyezése a céloldali erőforráscsoport
3. Ismételt védelem azonos vagy új tárolóval

Felhasználók állíthatja vissza az áthelyezési művelet előtt létrehozott rendelkezésre álló helyreállítási pontokból.


