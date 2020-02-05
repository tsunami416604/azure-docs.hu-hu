---
title: VMware-Migrálás támogatása Azure Migrateban
description: További információ a VMware virtuális gépek áttelepítésének támogatásáról Azure Migrateban.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e5a2f40611f6b358a8b5ff1dfb99cadebae4fab6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77013994"
---
# <a name="support-matrix-for-vmware-migration"></a>A VMware-Migrálás támogatási mátrixa

Ez a cikk a VMware virtuális gépek [Azure Migrate: kiszolgáló áttelepítéssel](migrate-services-overview.md#azure-migrate-server-migration-tool) történő áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze. Ha a VMware virtuális gépeknek az Azure-ba való áttelepítésre való vizsgálatával kapcsolatos információkat keres, tekintse át az [értékelés támogatási mátrixát](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Áttelepítési beállítások

A VMware virtuális gépeket több módon is áttelepítheti:

- Ügynök nélküli Migrálás esetén: a virtuális gépek áttelepítése anélkül, hogy bármit telepíteni kellene. Az ügynök nélküli Migrálás [Azure Migrate berendezését](migrate-appliance.md) telepíti.
- Ügynök-alapú áttelepítés esetén: telepítsen egy ügynököt a virtuális gépre a replikáláshoz. Az ügynök-alapú áttelepítés esetében telepítenie kell egy [replikációs berendezést](migrate-replication-appliance.md).

Tekintse át [ezt a cikket](server-migrate-overview.md) , hogy kiderítse, melyik módszert szeretné használni.

## <a name="migration-limitations"></a>Áttelepítési korlátozások

- Egyszerre legfeljebb 10 virtuális gépet választhat a replikáláshoz. Ha több gépet szeretne áttelepíteni, a replikálást a 10 csoportba.
- A VMware ügynök nélküli Migrálás esetében akár 100 replikálást is futtathat egyszerre.

## <a name="agentless-vmware-servers"></a>Ügynök nélkül – VMware-kiszolgálók

**VMware** | **Részletek**
--- | ---
**VMware vCenter Server** | 5,5, 6,0, 6,5 vagy 6,7.
**VMware vSphere ESXI-gazdagép** | 5,5, 6,0, 6,5 vagy 6,7.
**engedélyek vCenter Server** | Az ügynök nélküli áttelepítés az áttelepítési [készüléket](migrate-appliance.md)használja. A készüléknek ezekre az engedélyekre van szüksége:<br/><br/> - **adattár. Tallózás**: engedélyezi a virtuális gépek naplófájljainak böngészését a pillanatképek létrehozásával és törlésével kapcsolatos hibák megoldásához.<br/><br/> **Adattár. LowLevelFileOperations**: olvasási/írási/törlési/átnevezési műveletek engedélyezése az adattár böngészőben a pillanatképek létrehozásához és törléséhez.<br/><br/> - **VirtualMachine. Configuration. DiskChangeTracking**: engedélyezheti vagy letilthatja a virtuálisgép-lemezek módosításának nyomon követését, így a pillanatképek között megváltoztathatja az adatblokkokat.<br/><br/> - **VirtualMachine. Configuration. DiskLease**: a virtuális gép lemez-címbérleti műveleteinek engedélyezése a lemez olvasásához a VMware vSphere Virtual Disk Development Kit (VDDK) használatával.<br/><br/> - **VirtualMachine. kiépítés. AllowReadOnlyDiskAccess**: lehetővé teszi a lemez megnyitását egy virtuális gépen, hogy beolvassa a lemezt a VDDK használatával.<br/><br/> - **VirtualMachine. kiépítés. AllowVirtualMachineDownload**: engedélyezi az olvasási műveleteket a virtuális géphez társított fájlokon, letölti a naplókat, és hiba esetén hibaelhárítást végez.<br/><br/> -* * VirtualMachine. SnapshotManagement. * * *: lehetővé teszi a virtuális gépek pillanatképek létrehozását és kezelését a replikáláshoz.<br/><br/> - **virtuális gép. interakció.** kikapcsolás: lehetővé teszi, hogy a virtuális gép ki legyen kapcsolva az Azure-ba való áttelepítés során.



## <a name="agentless-vmware-vms"></a>Ügynök nélkül – VMware virtuális gépek

**Támogatás** | **Részletek**
--- | ---
**Támogatott operációs rendszerek** | Az Azure által támogatott Windows-és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) - [alapú](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) operációs rendszerek az ügynök nélküli Migrálás használatával telepíthetők át.
**Az Azure szükséges módosításai** | Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek. A Azure Migrate a következő operációs rendszerek esetében automatikusan végrehajtja ezeket a módosításokat:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Más operációs rendszerek esetében manuálisan kell elvégezni a módosításokat az áttelepítés előtt. A kapcsolódó cikkek erre vonatkozó utasításokat tartalmaznak.
**Linux rendszerű rendszerindítás** | Ha a/boot dedikált partíción van, akkor az operációsrendszer-lemezen kell lennie, és nem szabad több lemezre osztania.<br/> Ha a/boot a gyökér (/) partíció része, akkor a "/" partíciónak az operációsrendszer-lemezen kell lennie, és nem szabad más lemezekre kiterjednie.
**UEFI-rendszerindítás** | Az UEFI-rendszerindítással rendelkező virtuális gépek migrálása nem támogatott.
**Lemez mérete** | 2 TB operációsrendszer-lemez; 4 TB adatlemezek esetében.
**Lemezterület-korlátok** |  Akár 60 lemez/virtuális gép.
**Titkosított lemezek/kötetek** | A titkosított lemezekkel/kötetekkel rendelkező virtuális gépek migrálása nem támogatott.
**Megosztott lemezes fürt** | Nem támogatott.
**Független lemezek** | Nem támogatott.
**RDM/továbbító lemezek** | Ha a virtuális gépek RDM vagy továbbító lemezzel rendelkeznek, ezek a lemezek nem replikálódnak az Azure-ba.
**NFS** | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.
**iSCSI-tárolók** | Az iSCSI-tárolókkal rendelkező virtuális gépek nem támogatottak az ügynök nélküli Migrálás esetén.
**Többutas IO** | Nem támogatott.
**Tárolási vMotion** | Nem támogatott. A replikáció nem fog működni, ha a virtuális gép tárolási vMotion használ.
**Összevont hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.
**Céllemez** | A virtuális gépeket csak felügyelt lemezekre lehet áttelepíteni (standard HDD, prémium SSD) az Azure-ban.
**Egyidejű replikáció** | 100 virtuális gépek száma vCenter Server. Ha többre van szüksége, a 100-es kötegekben telepítse át őket.


## <a name="agentless-azure-migrate-appliance"></a>Ügynök nélküli – Azure Migrate berendezés 
Az ügynök nélküli áttelepítés a Azure Migrate készüléket használja, amelyet VMware virtuális gépen helyeztek üzembe.

- A VMware-es [készülékekre vonatkozó követelmények](migrate-appliance.md#appliance---vmware) ismertetése.
- További információ a készülék eléréséhez szükséges [URL-címekről](migrate-appliance.md#url-access) .

## <a name="agentless-ports"></a>Ügynök nélkül – portok

**Eszköz** | **Kapcsolat**
--- | ---
Berendezés | Az 443-as porton kimenő kapcsolatok a replikált adatok az Azure-ba való feltöltéséhez, valamint a replikáció és az áttelepítés összehangolása Azure Migrate szolgáltatásokkal való kommunikációhoz.
vCenter-kiszolgáló | Bejövő kapcsolatok a 443-as porton, hogy a készülék koordinálja a replikációt – pillanatképek létrehozása, Adatmásolás, kiadási Pillanatképek
vSphere/ESXI-gazdagép | Bejövő a 902-es TCP-porton, hogy a készülék Pillanatképek adatait replikálja.


## <a name="agent-based-vmware-servers"></a>Ügynök-alapú – VMware-kiszolgálók
Ez a táblázat összefoglalja a VMware virtualizációs kiszolgálók értékelésének támogatását és korlátozásait.

**VMware-követelmények** | **Részletek**
--- | ---
**VMware vCenter Server** | 5,5, 6,0, 6,5 vagy 6,7.
**VMware vSphere ESXI-gazdagép** | 5,5, 6,0, 6,5 vagy 6,7.
**engedélyek vCenter Server** | Írásvédett fiók a vCenter Serverhoz.

## <a name="agent-based-vmware-vms"></a>Ügynök-alapú – VMware virtuális gépek

A táblázat összefoglalja a VMware VM-támogatást az áttelepíteni kívánt VMware virtuális gépekhez az ügynök-alapú áttelepítés használatával.

**Támogatás** | **Részletek**
--- | ---
**Gépi munkaterhelés** | A Azure Migrate támogatja az olyan munkaterhelések áttelepítését (például Active Directory, SQL Server stb.), amelyek egy támogatott gépen futnak.
**Operációs rendszerek** | A legfrissebb információkért tekintse át Site Recovery [operációs rendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) . Azure Migrate azonos virtuálisgép-operációsrendszer-támogatást biztosít.
**Linux fájlrendszer/vendég tárterület** | A legfrissebb információkért tekintse át a Site Recovery [Linux-fájlrendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) . Azure Migrate azonos a Linux fájlrendszer támogatásával.
**Hálózat/tárterület** | A legfrissebb információkért tekintse át a Site Recovery [hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#network) és [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#storage) előfeltételeit. Azure Migrate azonos hálózati/tárolási követelményeket biztosít.
**Azure-követelmények** | A legfrissebb információkért tekintse át az [Azure hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)és [számítási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) követelményeit site Recovery. A Azure Migrate a VMware-Migrálás követelményeivel azonos.
**Mobilitási szolgáltatás** | Az áttelepíteni kívánt virtuális gépeken telepíteni kell a mobilitási szolgáltatás ügynökét.
**UEFI-rendszerindítás** | Az Azure-ban áttelepített virtuális gép automatikusan BIOS rendszerindító virtuális gépre lesz konvertálva.<br/><br/> Az operációsrendszer-lemez legfeljebb négy partíciót tartalmazhat, és a köteteket NTFS fájlrendszerrel kell formázni.
**Céllemez** | A virtuális gépeket csak felügyelt lemezekre lehet áttelepíteni (standard HDD, prémium SSD) az Azure-ban.
**Lemez mérete** | 2 TB operációsrendszer-lemez; 8 TB adatlemezek esetében.
**Lemezterület-korlátok** |  Akár 63 lemez/virtuális gép.
**Titkosított lemezek/kötetek** | A titkosított lemezekkel/kötetekkel rendelkező virtuális gépek migrálása nem támogatott.
**Megosztott lemezes fürt** | Nem támogatott.
**Független lemezek** | Támogatott.
**Továbbító lemezek** | Támogatott.
**NFS** | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.
**iSCSI-tárolók** | Az iSCSI-tárolókkal rendelkező virtuális gépek nem támogatottak az ügynök nélküli Migrálás esetén.
**Többutas IO** | Nem támogatott.
**Tárolási vMotion** | Támogatott
**Összevont hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.




## <a name="agent-based-replication-appliance"></a>Ügynök-alapú replikációs berendezés 

Amikor beállítja a replikációs berendezést az Azure Migrate központban megadott petesejtek sablonnal, a készülék futtatja a Windows Server 2016-et, és megfelel a támogatási követelményeknek. Ha a replikációs készüléket manuálisan állítja be egy fizikai kiszolgálón, akkor ellenőrizze, hogy az megfelel-e a követelményeknek.

- Ismerje meg a VMware-hez készült [replikációs berendezések követelményeit](migrate-replication-appliance.md#appliance-requirements) .
- A készülékre telepíteni kell a MySQL-t. Tudnivalók a [telepítési lehetőségekről](migrate-replication-appliance.md#mysql-installation).
- A replikációs berendezés eléréséhez szükséges [URL-címek](migrate-replication-appliance.md#url-access) és [portok](migrate-replication-appliance.md#port-access) ismertetése.

## <a name="agent-based-ports"></a>Ügynök-alapú portok

**Eszköz** | **Kapcsolat**
--- | ---
VG | A virtuális gépeken futó mobilitási szolgáltatás a replikációs felügyelet érdekében a HTTPS 443 bejövő porton keresztül kommunikál a helyszíni replikációs berendezéssel (konfigurációs kiszolgálóval).<br/><br/> A virtuális gépek replikációs adatküldést küldenek a folyamat-kiszolgálónak (amely a konfigurációs kiszolgáló gépen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
Replikációs berendezés | A replikációs berendezés az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
Kiszolgáló feldolgozása | A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.<br/> Alapértelmezés szerint a Process Server fut a replikációs berendezésen.

## <a name="azure-vm-requirements"></a>Azure-beli virtuális gépekre vonatkozó követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek. Ha Site Recovery futtatja az előfeltétel-ellenőrzés replikálását, akkor az ellenőrzés sikertelen lesz, ha egyes követelmények nem teljesülnek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Ellenőrzi a VMware virtuális gépek támogatott operációs rendszereinek áttelepítését.<br/> A támogatott operációs rendszereken futó munkaterhelések áttelepíthetők. | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációs rendszer lemezének mérete | Akár 2 048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Legfeljebb 4 095 GB | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott virtuális merevlemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A számítógép replikálásának engedélyezése előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 – 63 karakter.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse az értéket a Site Recovery számítógép tulajdonságai között.
Kapcsolat az áttelepítés után – Windows | Kapcsolódás a Windows rendszerű Azure-beli virtuális gépekhez a Migrálás után:<br/> – Az áttelepítés előtt engedélyezi az RDP-t a helyszíni virtuális gépen. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/> A helyek közötti VPN-hozzáféréshez engedélyezze az RDP-t, és engedélyezze az RDP használatát a **Windows tűzfalon** -> **engedélyezett alkalmazásokat és szolgáltatásokat** a **tartomány-és magánhálózat** számára. Továbbá győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll**értékre van állítva. [További információk](prepare-for-migration.md). |
Kapcsolat Migrálás után – Linux | Kapcsolódás az Azure-beli virtuális gépekhez az SSH használatával történő áttelepítés után:<br/> Az áttelepítés előtt a helyszíni gépen győződjön meg arról, hogy a Secure Shell szolgáltatás indításra van beállítva, és hogy a tűzfalszabályok engedélyezik az SSH-kapcsolatokat.<br/> A feladatátvételt követően az Azure-beli virtuális gépen engedélyezze az SSH-porthoz való bejövő kapcsolatokat a hálózati biztonsági csoportra vonatkozó szabályokra vonatkozóan a feladatátvételen átesett virtuális gépen, valamint azt az Azure-alhálózatot, amelyhez csatlakoztatva van. Továbbá adjon hozzá egy nyilvános IP-címet a virtuális géphez. |  


## <a name="next-steps"></a>Következő lépések

[Válassza ki](server-migrate-overview.md) a VMware áttelepítési lehetőséget.
