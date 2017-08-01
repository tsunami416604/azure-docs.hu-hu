
---
title: "Azure VM Backup – gyakori kérdések | Microsoft Docs"
description: "Válaszok a következő gyakori kérdésekre: hogyan működik az Azure-beli virtuális gépek biztonsági mentése, mik a korlátozások, és mi történik, ha módosítások történnek a szabályzatban"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "azure-beli virtuális gép biztonsági mentése, azure-beli virtuális gép visszaállítása, biztonsági mentési szabályzat"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 8c06a90f58cf56ebb4e75e7567e237de7414a300
ms.contentlocale: hu-hu
ms.lasthandoff: 07/20/2017

---
# <a name="questions-about-the-azure-vm-backup-service"></a>Kérdések az Azure VM Backup szolgáltatással kapcsolatban
A cikk gyakori kérdésekre adott válaszokat tartalmazó szakaszaiban gyorsan áttekinthető az Azure VM Backup összetevőinek működése. Egyes válaszokban részletes információkat tartalmazó cikkekre mutató hivatkozások találhatók. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>A Recovery Services-tárolók támogatják a klasszikus vagy a Resource Manager alapú virtuális gépeket? <br/>
A Recovery Services-tárolók mindkét modellt támogatják.  Mind a klasszikus (azaz a klasszikus portálon létrehozott), mind a Resource Manager-alapú (azaz az Azure Portalon létrehozott) virtuális gépekről készíthet biztonsági mentést egy Recovery Services-tárolóba.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup-"></a>Mely konfigurációkat nem támogatja az Azure VM Backup?
Tekintse át a [támogatott operációs rendszereket](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup) és a [virtuális gépek biztonsági mentésének korlátozásait](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Miért nem látom a virtuális gépemet a Biztonsági mentés konfigurálása varázslóban?
A Biztonsági mentés konfigurálása varázslóban az Azure Backup csak a következő virtuális gépeket jeleníti meg:
* Még nem védettek – A virtuális gépek állapotát úgy ellenőrizheti, hogy a virtuális gép paneljének Beállítások menüjében ellenőrzi a Biztonsági mentés állapotát. További információ a [Virtuális gép biztonsági mentési állapotának ellenőrzéséről](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)
* A virtuális géppel azonos régióba tartozik

## <a name="backup"></a>Biztonsági mentés
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Az igény szerinti biztonsági mentési feladatok ugyanazt a megőrzési ütemezést követik, mint az ütemezett biztonsági mentések?
Nem. Az igény szerinti biztonsági mentési feladatokhoz meg kell adnia a megőrzési tartományt. A megőrzési idő alapértelmezés szerint 30 nap, ha a portálról indítja el a feladatot. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Nemrég engedélyeztem az Azure Disk Encryption szolgáltatást néhány virtuális gépen. A biztonsági mentések továbbra is működni fognak?
Engedélyeznie kell az Azure Backup szolgáltatásnak a Key Vault elérését. Ezeket az engedélyeket a PowerShellben adhatja meg, a [PowerShell-dokumentáció](backup-azure-vms-automation.md) *Biztonsági mentés engedélyezése* szakaszában található lépések végrehajtásával.

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Egy virtuális gép lemezeit felügyelt lemezekre migráltam. A biztonsági mentések továbbra is működni fognak?
Igen, a biztonsági mentések problémamentesen fognak működni, és nem kell újrakonfigurálnia a biztonsági mentést. 

## <a name="restore"></a>Visszaállítás
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>A lemezek visszaállítását vagy a teljes virtuális gép visszaállítását válasszam?
Az Azure-beli virtuális gép teljes visszaállítása a visszaállított virtuális gép gyors létrehozásának lehetőségeként is értelmezhető. A virtuális gép visszaállítása módosítja a lemezek nevét, a lemezek által használt tárolókat, a nyilvános IP-címeket és a hálózati adapterek nevét, hogy a virtuális gép létrehozásakor létrejövő erőforrások egyediek legyenek. Ezenkívül nem adja hozzá a virtuális gépet a rendelkezésre állási csoporthoz. 

A lemezek visszaállítását a következőkhöz használhatja:
* Olyan virtuális gép testreszabásához, amely az időponthoz kötött konfigurációból jön létre, például a biztonsági mentés konfigurációjában megadott méret módosításával
* Olyan konfigurációk hozzáadásához, amelyek nincsenek jelen a biztonsági mentés idején 
* A létrejövő erőforrások elnevezési konvencióinak vezérléséhez
* Virtuális gép hozzáadása rendelkezésre állási csoporthoz
* Olyan konfigurációval rendelkezik, amely csak a PowerShell vagy deklaratív sablondefiníció használatával érhető el

## <a name="manage-vm-backups"></a>Virtuális gép biztonsági mentéseinek kezelése
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Mi történik, ha módosítom a biztonsági mentési szabályzatot a virtuális gépen vagy gépeken?
Amikor új szabályzatot alkalmaznak egy virtuális gépen, az új szabályzat ütemezése és megőrzése lesz érvényes. Ha növeli a megőrzési időtartamot, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak. Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítás során törlendőként lesznek megjelölve. 

