---
title: Azure virtuális gép biztonsági mentése az Azure biztonsági mentés támogatási mátrixa
description: Támogatási beállításait és korlátozások összegzést tartalmaz a biztonsági mentése Azure virtuális gépek az Azure Backup szolgáltatással.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: b99d6285942bafe5467827c30b5ba2e42094fdf3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430884"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM backup-támogatási mátrixa
Használhatja a [Azure Backup szolgáltatás](backup-overview.md) biztonsági mentése a helyszíni gépek és a számítási feladatok és az Azure virtuális gépeken. Ez a cikk összefoglalja a támogatási beállításait és korlátozások biztonsági mentése Azure-beli virtuális gépek (VM) az Azure Backup szolgáltatással.

Egyéb támogatási mátrixok:

- [Általános támogatási mátrix](backup-support-matrix.md) for Azure Backkup
- [Támogatási mátrix](backup-support-matrix-mabs-dpm.md) a Microsoft Azure Backup server/DOM biztonsági mentés
- [Támogatási mátrix](backup-support-matrix-mars-agent.md) biztonsági mentéshez az a MARS-ügynök



## <a name="supported-scenarios"></a>Támogatott esetek

Itt látható, hogyan lehet biztonsági másolatot készíteni, és Azure virtuális gépek visszaállítása az Azure Backup szolgáltatással.


**Forgatókönyv** | **Biztonsági mentés** | **Ügynök** |**Visszaállítás** 
--- | --- | --- | ---
**Közvetlen biztonsági mentést Azure-beli virtuális** | Készítsen biztonsági másolatot a teljes virtuális gép  | Nincs ügynök van szükség az Azure virtuális gépen. Az Azure Backup telepíti, és használja a bővítményt a [Azure Virtuálisgép-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) a virtuális gépen. | Állítsa vissza a következőképpen:<br/><br/> - **Alapszintű virtuális gép létrehozása**. Ez akkor hasznos, ha a virtuális gép nem rendelkezik külön konfigurációval például több IP-címek.<br/><br/> - **A Virtuálisgép-lemez visszaállítása**. Állítsa vissza a lemezt. Ezután csatlakoztassa azt egy meglévő virtuális Gépet, vagy hozzon létre egy új virtuális Gépet a lemezről a PowerShell használatával.<br/><br/> - **Cserélje le a virtuális gép lemezének**. Ha a virtuális gép létezik, és a felügyelt lemezek (titkosítatlanul) használ, lemez visszaállítása, és cserélje le a virtuális gépen meglévő lemez használatával.<br/><br/> - **Meghatározott fájlok és mappák visszaállítása**. Fájlok és mappák visszaállíthatja egy virtuális Gépet, a teljes virtuális gép helyett.  
**Közvetlen biztonsági mentést Azure-beli virtuális (csak Windows)** | Készítsen biztonsági másolatot az adott fájlok/mappák/kötet | Telepítse a [a Microsoft Azure Recovery Services-(MARS) ügynök](backup-azure-file-folder-backup-faq.md).<br/><br/> A MARS-ügynök mellett a biztonsági mentési bővítményt a fájl vagy mappa szintjén a virtuális gép biztonsági másolatának az Azure Virtuálisgép-ügynök futtatásával. | Állítsa vissza az adott mappákat és fájlokat.
**Biztonsági mentése Azure virtuális gép biztonsági mentési kiszolgálóra** |  Készítsen biztonsági másolatot a fájlok/mappák/kötetek; rendszerállapot/operációs rendszer nélküli rendszerfájlok; fel az adatokat, a System Center DPM vagy a Microsoft Azure Backup Server (MAB kiszolgálót).<br/><br/> A DPM/MABS majd biztonsági másolatot készít a backup-tárolóba | A MABS és a DPM védelmi ügynök telepítése a virtuális gépen. A MARS-ügynök telepítve van a DPM/MABS.| Állítsa vissza a fájlok/mappák/kötetek; rendszerállapot/operációs rendszer nélküli rendszerfájlok; Alkalmazásadatok. 

További információ a biztonsági mentés a biztonsági mentési server(backup-architecture.md#architecture-back-up-to-dpmmabs), és [memóriakonfigurációt](backup-support-matrix-mabs-dpm.md).


## <a name="supported-backup-actions"></a>Támogatott biztonsági mentési műveletek

**Művelet** | **Támogatás**
--- | ---
Amikor létrehoz egy Windows Azure virtuális gép biztonsági mentésének engedélyezése | Támogatott:  A Windows Server 2016 (Core adatközpont vagy Datacenter); A Windows Server 2012 R2 DataCenter; A Windows Server 2008 R2 SP1
Amikor létrehoz egy Linux rendszerű virtuális gép biztonsági mentésének engedélyezése | Támogatott:<br/><br/> - Ubuntu Server: 1710, 1704, 1604 (LTS), 1404 (LTS)<br/><br/> – A Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> – SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3<br/><br/> -Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
A virtuális gép biztonsági másolatának hogy leállítás/kapcsolat/kérő VM | Támogatott.<br/><br/> Pillanatkép az összeomlás-konzisztens csak, nem alkalmazáskonzisztens.
Lemezek biztonsági mentése a managed disks-ba való migrálás után. | Támogatott.<br/><br/> Biztonsági mentés továbbra is működni fog. Nincs szükség műveletre.
Lemezek biztonsági mentése a felügyelt erőforrászárat csoport engedélyezése után | Nem támogatott.<br/><br/> Biztonsági mentés nem lehet törölni a régebbi erőforrás pontokat, és a biztonsági mentések elkezdi sikertelen lehet, ha a visszaállítási pontok maximális korlátot.
Virtuális gép biztonsági mentési szabályzat módosítása | Támogatott.<br/><br/> A virtuális gép készül biztonsági másolat új szabályzat ütemezése és megőrzése beállításainak használatával. Adatmegőrzési beállítások vannak bővítve, ha a meglévő helyreállítási pontok megjelölve és tartani. Ha Ön csökkenti a meglévő helyreállítási pontok törli a következő tisztítási feladat, és végül törli.
Biztonsági mentési feladatok megszakítása | Támogatott pillanatkép folyamat során.<br/><br/> Nem támogatott, ha a pillanatkép-tárolóba átvitt. 
A virtuális gép biztonsági másolatának egy másik régióba vagy előfizetésbe |  Nem támogatott.
Biztonsági mentések napi (keresztül az Azure Virtuálisgép-bővítmény) | Ütemezett napi egy biztonsági mentés.<br/><br/> Naponta legfeljebb négy igény szerinti biztonsági mentéseket is igénybe vehet.
Biztonsági mentések napi (keresztül a MARS-ügynök) | Ütemezett napi három biztonsági mentés. 
(A DPM/MABS) keresztül napi biztonsági mentés | Ütemezett napi két biztonsági mentést.
Havi/évi biztonsági mentés   | Nem támogatott, ha a biztonsági másolat készítése az Azure Virtuálisgép-bővítménnyel. Csak napi és heti használata támogatott.<br/><br/> A szabályzat állíthat havi/évi megőrzési időszak a napi/heti biztonsági mentések megőrzési idejét.
Óra automatikus beállítása | Nem támogatott.<br/><br/> Az Azure Backup nem használ a nyári időszámítás – mentés változásokat figyelembe óra automatikus illesztését, amikor a virtuális gép biztonsági mentésével.<br/><br/>  Igény szerint manuálisan módosítsa a házirendet.
[Biztonsági szolgáltatások hibrid biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  Az funkciók letiltása nem támogatott.



## <a name="operating-system-support-windows"></a>Operációs rendszer támogatásának (Windows)

A táblázat összefoglalja a támogatott operációs rendszerek, amikor a Windows Azure virtuális gépek biztonsági mentésének.

**Forgatókönyv** | **Operációsrendszer-támogatást** 
--- | ---
Biztonsági mentés az Azure-beli Virtuálisgép-ügynök bővítmény | Windows-ügyfél: Nem támogatott<br/><br/> Windows Server: A Windows Server 2008 R2 vagy újabb 
A MARS-ügynök biztonsági mentése | [Támogatott](backup-support-matrix-mars-agent.md#support-for-direct-backups) operációs rendszereket.
Készítsen biztonsági másolatot a DPM vagy MABS-n keresztül | Biztonsági mentés a támogatott operációs rendszerek [MABS](backup-mabs-protection-matrix.md) és [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Linux biztonsági mentés támogatása

Íme, mi támogatott, ha azt szeretné, Linux rendszerű gépek biztonsági mentéséhez.

**Művelet** | **Támogatás**
--- | --- 
Linux rendszerű Azure virtuális gépek biztonsági mentése Azure-beli Linux rendszerű Virtuálisgép-ügynökkel rendelkező | Alkalmazáskonzisztens biztonsági mentés.<br/><br/> Alkalmazáskonzisztens biztonsági mentés használatával [egyéni parancsfájlok](backup-azure-linux-app-consistent.md).<br/><br/> Visszaállítás során létrehozhat egy új virtuális Gépet, visszaállíthatja a lemez és, amelyek segítségével hozzon létre egy virtuális Gépet, vagy a lemez visszaállítása és kicserél egy lemezt egy meglévő virtuális gép használatával. Egyes fájlok és mappák is helyreállíthatja.
Linux rendszerű Azure virtuális gépek biztonsági mentése a MARS-ügynök | Nem támogatott.<br/><br/> A MARS-ügynök csak Windows-gépeken telepíthető.
A DPM/MABS Linux rendszerű Azure virtuális gépek biztonsági mentése | Nem támogatott.

## <a name="operating-system-support-linux"></a>Operációs rendszer támogatásának (Linux)

Linux-alapú Azure virtuális gép biztonsági mentése, az Azure Backup támogatja a Linux [Azure által támogatott disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Vegye figyelembe:

- Az Azure Backup nem támogatja a Core OS Linux.
- Az Azure Backup nem támogatja a 32 bites operációs rendszereken.
- Más bring-your-own Linux-disztribúciók előfordulhat, hogy működik, amíg a [Linuxhoz készült Azure-beli Virtuálisgép-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) érhető el a virtuális gépen, és amely támogatja a Pythont.



## <a name="backup-frequency-and-retention"></a>Biztonsági mentési gyakoriság és megőrzési

**Beállítás** | **Korlátok** 
--- | --- 
Helyreállítási pontok maximális száma védett példányonként (a gép/számítási feladat | 9999
Helyreállítási pontok maximális lejárati idő | Korlátlan
Tároló (az Azure Virtuálisgép-bővítmény) maximális biztonsági mentés gyakorisága | Naponta egyszer
Maximális biztonsági mentési gyakoriságát tárolóba (MARS-ügynök) | Napi három biztonsági mentés.
A DPM/MABS maximális biztonsági mentés gyakorisága | 15 percenként az SQL Serverhez<br/><br/> Miután egy órában más számítási feladatokhoz.
Helyreállítási pont megőrzése | Napi, heti, havi, éves.
Maximális megőrzési időtartam | Biztonsági mentés gyakoriságától függően.
A DPM/MABS lemezen helyreállítási pontok | 64 fájlkiszolgálókhoz, 448 alkalmazások kiszolgálói.<br/><br/> Szalag-helyreállítási pontok a helyszíni dpm korlátlanok.


## <a name="supported-restore-methods"></a>Támogatott visszaállítási módszer

**Restore metódus** | **Részletek**
--- | ---
**Új virtuális gép létrehozása** | A visszaállítási folyamat során egy virtuális Gépet hozhat létre. <br/><br/> Ezt a beállítást lekérdezi az alapszintű virtuális gép üzembe helyezéséig. Megadhatja a virtuális gép neve, a erőforráscsoportot, virtuális hálózat, alhálózat és storage.  
**Lemez visszaállítása** | Lemez visszaállítása, és ezzel hozzon létre egy virtuális gép<br/><br/> Ha ezt a lehetőséget választja, az Azure Backup a tárolóból adatokat másol egy tárfiókot, amelyet választja. A visszaállítási feladat létrehoz egy sablont. Ez a sablon letöltése és használatával adja meg az egyéni virtuális gép beállításait, és hozzon létre egy virtuális Gépet.<br/><br/> Ez a beállítás lehetővé teszi, hogy további beállítások megadását, amely az előző beállítás a virtuális gép létrehozásához.<br/><br/>
**Meglévő lemez cseréje** | Lemez visszaállítása, és a visszaállított lemez használatával, amely jelenleg a virtuális gép olyan lemezt cserél ki.
**Fájlok visszaállítása** | A kiválasztott helyreállítási pont fájlokat próbál helyreállítani. Csatlakoztatásához a Virtuálisgép-lemez a helyreállítási pontból parancsfájl letöltése. Ezután a helyre, és amikor elkészült, válassza le a lemez kívánt fájlok és mappák található kötetek között tallózva.

## <a name="support-for-file-level-restore"></a>Fájlszintű visszaállítás támogatása

**Visszaállítás** | **Támogatott**
--- | --- 
Fájlok visszaállítása a különböző operációs rendszerek | Minden olyan gépen, amelyen a biztonsági másolat virtuális géppel azonos (vagy kompatibilis) operációs rendszer fájljait állíthatja vissza. Tekintse meg a [kompatibilis operációs rendszer tábla](backup-azure-restore-files-from-vm.md#system-requirements).
A klasszikus virtuális gépeket a fájlok visszaállítása | Nem támogatott. 
Fájlok visszaállítása titkosított virtuális gépek | Nem támogatott.
Fájlok visszaállítása a hálózatú tárfiókokat | Nem támogatott.
A fájlok a Windows tárolóhelyek használó virtuális gépek visszaállítása | A visszaállítás nem támogatott ugyanazon virtuális gépen.<br/><br/> Ehelyett a fájlok visszaállítása egy kompatibilis virtuális gépen.
Linux virtuális gép LVM/raid-tömbök fájlok helyreállítása | A visszaállítás nem támogatott ugyanazon virtuális gépen.<br/><br/> Állítsa vissza a kompatibilis virtuális gép.
Speciális hálózati beállításokkal rendelkező fájlok visszaállítása | A visszaállítás nem támogatott ugyanazon virtuális gépen. <br/><br/> Állítsa vissza a kompatibilis virtuális gép.

## <a name="support-for-vm-management"></a>Virtuálisgép-kezelés támogatása

A táblázat összefoglalja a biztonsági mentés támogatása közben a virtuális gép felügyeleti feladatokat, például hozzáadásával vagy Virtuálisgép-lemezek.

**Visszaállítás** | **Támogatott** 
--- | --- 
A visszaállítási előfizetés/régió és zóna között. | Nem támogatott 
Egy meglévő virtuális gép visszaállítása | Cserélje le a lemez beállítást használja.
Lemez visszaállítása engedélyezve van az Azure Storage Service Encryption (SSE) a storage-fiók | Nem támogatott.<br/><br/> Állítsa vissza, amely nem rendelkezik az SSE engedélyezve van.
Vegyes tárfiókok visszaállítása | Nem támogatott.<br/><br/> A tárfiók típusa alapján, az összes visszaállított lemez lesz prémium vagy standard szintű és nem vegyes. 
A zrs-t használó tárfiókot visszaállítása | Nem támogatott
Közvetlenül egy rendelkezésre állási csoportot a virtuális gép visszaállítása | Felügyelt lemezek esetén állítsa vissza a lemezt, és a rendelkezésre állási készlet lehetőséget használja a sablonban.<br/><br/> Nem felügyelt lemezek esetén nem támogatott. Nem felügyelt lemezek esetén állítsa vissza a lemezt, és ezután létrehoz egy virtuális Gépet a rendelkezésre állási csoportban.
Nem felügyelt virtuális gépek biztonsági másolatának visszaállítása után frissíteni kell a kezelni | Támogatott.<br/><br/> Állítsa vissza a lemezeket, és hozzon létre egy felügyelt virtuális Gépen.
Visszaállítási pont, a managed Disks szolgáltatásba a virtuális gép áttelepítése előtt a virtuális gép visszaállítása | Támogatott.<br/><br/> Állítsa vissza a nem felügyelt lemezek (alapértelmezett), a visszaállított lemez átalakítása felügyelt lemez és a felügyelt lemezekkel rendelkező virtuális gép létrehozása.
Állítsa vissza egy virtuális Gépet, amely törölve lett. | Támogatott.<br/><br/> Visszaállíthatja a virtuális gép helyreállítási pontból.
Tartományvezérlő (DC) a portálon keresztül multi-DC konfiguráció részét képező virtuális gépek visszaállítása | Támogatott, ha a lemez visszaállítása, és hozzon létre egy virtuális Gépet PowerShell-lel.
Másik virtuális hálózatban a virtuális gép visszaállítása |  Támogatott.<br/><br/> A virtuális hálózat azonos előfizetésben és régióban kell lennie.




## <a name="vm-compute-support"></a>Virtuális gépek számítási támogatása

**Számítás** | **Támogatás** 
--- | --- 
Virtuális gép mérete |   Minden olyan Azure virtuális gép méretét legalább 2 processzormag és 1 GB RAM.<br/><br/> [További információ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 
A virtuális gépek biztonsági mentése [rendelkezésre állási csoportok](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) | Támogatott.<br/><br/> A kapcsoló használatával gyorsan hozzon létre egy virtuális Gépet egy rendelkezésre álló csoportot a virtuális gép nem állítható vissza. Ehelyett a virtuális gép visszaállításakor kell a lemez visszaállítása és a használatával helyezhet üzembe egy virtuális Gépet, vagy a lemez visszaállítása, és cserélje le a meglévő lemez használatával. 
A virtuális gépek biztonsági mentése [rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Nem támogatott.  
Az üzembe helyezett virtuális gépek biztonsági mentése [hibrid használati Benefit (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Támogatott.
Az üzembe helyezett virtuális gépek biztonsági mentése egy [méretezési csoportot](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |   Nem támogatott   
Az üzembe helyezett virtuális gépek biztonsági mentése a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (A Microsoft által közzétett külső) |   Támogatott.<br/><br/> A virtuális gép támogatott operációs rendszernek kell futnia.<br/><br/> A virtuális gép fájljait helyreállításakor visszaállíthatja csak egy kompatibilis operációs rendszer (nem egy korábbi vagy későbbi operációs rendszer).
A (külső) egyéni lemezkép alapján üzembe helyezett virtuális gépek biztonsági mentése |    Támogatott.<br/><br/> A virtuális gép támogatott operációs rendszernek kell futnia.<br/><br/> A virtuális gép fájljait helyreállításakor visszaállíthatja csak egy kompatibilis operációs rendszer (nem egy korábbi vagy későbbi operációs rendszer).
Virtuális gépek biztonsági mentése az Azure-bA migrálása   | Támogatott.<br/><br/> Biztonsági mentése a virtuális Gépet, a Virtuálisgép-ügynök a migrált gépen telepítve van. 



## <a name="vm-storage-support"></a>Virtuálisgép-tároló támogatása

**Összetevő** | **Támogatás**
--- | ---
Az Azure virtuális gép adatlemezek | Készítsen biztonsági másolatot egy 16 vagy annál kisebb adatlemezekkel rendelkező virtuális Gépet.
Adatlemez mérete | Az egyes lemezek legfeljebb 4095 GB is lehet.<br/><br/> Azure VM backup (más néven azonnali visszaállítása) legújabb verzióját futtatja, ha a lemez mérete legfeljebb 4 TB-os támogatottak. [További információk](backup-instant-restore-capability.md).
Tárolási típus | Standard HDD, standard SSD, prémium szintű SSD <br/><br/> Standard SSD támogatott, ha az Azure VM backup (más néven azonnali visszaállítása) legújabb verzióját futtatja, a standard SSD használata támogatott. [További információk](backup-instant-restore-capability.md).
Felügyelt lemezek | Támogatott
Titkosított lemezek | Támogatott.<br/><br/> Az Azure Disk Encryption (ADE) engedélyezve van az Azure virtuális gépek (a vagy anélkül, hogy az Azure AD-alkalmazás) is készíthető.<br/><br/> Titkosított virtuális gépek nem állítható helyre, a fájl vagy mappa szintjén. A teljes virtuális gép helyreállítása kell.<br/><br/> Engedélyezheti a titkosítást az Azure Backup által már védett virtuális gépeken.
Az engedélyezett Írásgyorsító lemezek | Nem támogatott.<br/><br/> Ha futtatja a legújabb Azure VM backup (más néven [azonnali visszaállítása](backup-instant-restore-capability.md)), lemezeket zárhat ki a biztonsági másolatból engedélyezett Írásgyorsító.
A deduplikált lemezek biztonsági mentése | Nem támogatott.
Lemez hozzáadása a védett virtuális gép | Támogatott.
A védett virtuális gépek a lemez átméretezése | Támogatott.

## <a name="vm-network-support"></a>Virtuálisgép-hálózatok támogatása


**Összetevő** | **Támogatás**
--- | ---
Hálózati adapterek száma | Legfeljebb egy adott Azure-beli Virtuálisgép-méretet a támogatott hálózati adapterek maximális száma.<br/><br/> Hálózati adapter a virtuális gép létrehozásakor a visszaállítási folyamat során jönnek létre.<br/><br/> A visszaállított virtuális gép hálózati adaptereinek száma tükrözi a virtuális gép hálózati adaptereinek száma, ha engedélyezte a védelmet. Miután engedélyezte a hálózati adapter eltávolítása nem érinti a száma.
Külső és belső terheléselosztó |   Támogatott. <br/></br> [További](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) speciális hálózati beállításokkal rendelkező virtuális gépek visszaállítása.
Több fenntartott IP-cím |  Támogatott. <br/></br> [További](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) speciális hálózati beállításokkal rendelkező virtuális gépek visszaállítása.
Több hálózati adapterrel rendelkező virtuális gépek  | Támogatott. <br/></br> [További](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) speciális hálózati beállításokkal rendelkező virtuális gépek visszaállítása.
Nyilvános IP-címekkel rendelkező virtuális gépek    | Támogatott.<br/><br/> Kell egy meglévő nyilvános IP-címet társítani a hálózati Adapterre, vagy hozzon létre egy címet és a visszaállítás befejezését követően társítsa azt a hálózati adapterhez.
Hálózati biztonsági csoport (NSG) a hálózati adapter (al). |   Támogatott.
Fenntartott IP-cím (statikus) | Nem támogatott.<br/><br/> Biztonsági mentés egy virtuális Gépet egy fenntartott IP-cím és a nem meghatározott végpontot.
Dinamikus IP-cím |    Támogatott.<br/><br/> Ha a hálózati adapter a forrás virtuális gép használ, a dinamikus IP-címkezelést, alapértelmezés szerint a hálózati adapter a visszaállított virtuális Gépet a rendszer túl.
Traffic Manager | Támogatott<br/><br/>. Ha a biztonsági másolat virtuális gép Traffic Managerben, manuálisan adja hozzá a visszaállított virtuális Gépet az azonos a Traffic Manager szüksége. 
Azure DNS | Támogatott.
Egyéni DNS |    Támogatott.
Kimenő kapcsolat HTTP-proxyn keresztül | Támogatott.<br/><br/> Egy hitelesített proxyk nem támogatottak. 
Virtuális hálózati Szolgáltatásvégpontok  | Támogatott.<br/><br/> Tűzfal- és VNet tárfiók-beállítások minden hálózati hozzáférés lehetővé teszi. 



## <a name="vm-securityencryption-support"></a>Virtuális gép biztonsági/titkosítás támogatása

Az Azure Backup támogatja a titkosítás az átvitel és inaktív adatok:

Hálózati forgalom az Azure-bA:
- Biztonsági mentés a Recovery Services-tárolóba kiszolgálókról érkező forgalom az Advanced Encryption Standard 256 eljárással van titkosítva.
- Biztonsági mentési adatok egy biztonságos HTTPS-kapcsolaton keresztül zajlik.
- A biztonsági mentési adatokat a Recovery Services-tárolót, titkosított formában van tárolva.
- Csak Ön rendelkezik az adatok zárolásának feloldására szolgáló jelszóval. A Microsoft nem tudja visszafejteni a biztonsági mentési adatokat.
    > [!WARNING]
    > Miután beállította a tárolót, csak Önnek lesz hozzáférése a titkosítási kulcshoz. A Microsoft soha nem őriz másolatot, és nem rendelkezik hozzáféréssel a kulcshoz. Ha az ügyfél elveszíti a kulcsot, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.
Adatbiztonság:
- Amikor az Azure virtuális gépek biztonsági mentésének kell titkosítás beállítása *belül* a virtuális gépet.
- Az Azure Backup támogatja az Azure Disk Encryption szolgáltatást, amely a BitLockert használja Windows rendszerű virtuális gépeken és a **dm-crypt**-et Linux rendszerű virtuális gépeken.
- Az Azure Backup a háttérben az [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) használatával biztosít védelmet az inaktív adatok számára.


**Gép** | **Az átvitel során** | **Inaktív állapotban**
--- | --- | ---
A helyi Windows számítógépek DPM/MABS nélkül | ![Igen][green] | ![Igen][green]
Azure-beli virtuális gépek | ![Igen][green] | ![Igen][green] 
Helyszíni/Azure-beli virtuális gépek DPM-mel | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek a MABS | ![Igen][green] | ![Igen][green]



## <a name="vm-compression-support"></a>Virtuális gépek tömörítés támogatása

Backup támogatja a tömörítést, a biztonsági mentési forgalom, az alábbi táblázat foglalja össze. Vegye figyelembe:

- Az Azure virtuális gépek a Virtuálisgép-bővítmény olvassa be az adatokat közvetlenül az Azure storage-fiókot a tárolóhálózaton keresztül, ezért nem kell tömöríteni a forgalmat.
- DPM- vagy MABS használja, ha az adatokat, biztonsági másolatot a DPM/MABS, sávszélesség mentése előtt is tömörítse. 

**Gép** | **Tömörítse a MABS és DPM (TCP)** | **(HTTPS) tömörítése a tárba**
--- | --- | ---
A helyi Windows számítógépek DPM/MABS nélkül | NA | Igen
Azure-beli virtuális gépek | NA | NA
Helyszíni/Azure-beli virtuális gépek DPM-mel | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek a MABS | ![Igen][green] | ![Igen][green]




## <a name="next-steps"></a>További lépések

- [Azure-beli virtuális gépek biztonsági mentése](backup-azure-arm-vms-prepare.md)
- [Közvetlen Windows-gépek biztonsági mentése](tutorial-backup-windows-server-to-azure.md), anélkül, hogy egy biztonsági mentési kiszolgálóra.
- [Állítsa be a MABS](backup-azure-microsoft-azure-backup.md) Azure adatforrásáig és vissza másolatot a MABS történő biztonsági mentés.
- [Állítsa be a DPM](backup-azure-dpm-introduction.md) Azure adatforrásáig és vissza másolatot a dpm-hez történő biztonsági mentés.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

