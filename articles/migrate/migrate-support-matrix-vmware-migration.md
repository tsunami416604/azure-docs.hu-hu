---
title: A VMware-áttelepítés támogatása az Azure Áttelepítésében
description: Ismerje meg a VMware virtuálisgép-áttelepítés támogatását az Azure Áttelepítésben.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: bf9cc471eef31edd513358a97d2ece17015ba781
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313998"
---
# <a name="support-matrix-for-vmware-migration"></a>A VMware-áttelepítés támogatási mátrixa

Ez a cikk összefoglalja a virtuális gépekkel az [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) szolgáltatással való áttelepítés támogatási beállításait és korlátait. Ha a VMware virtuális gépek Azure-ba való áttelepítésre vonatkozó értékelésének értékelésére vonatkozó információkat keres, tekintse át az [értékelési támogatási mátrixot.](migrate-support-matrix-vmware.md)


## <a name="migration-options"></a>Migrálási lehetőségek

A VMware virtuális gépeket többféleképpen is áttelepítheti:

- Ügynök nélküli áttelepítés: Telepítse a virtuális gépeket anélkül, hogy bármit is telepítenie kellene rájuk. Az [Azure Migrate készülék ügynök](migrate-appliance.md) nélküli áttelepítéshez.
- Ügynökalapú áttelepítés: Telepítsen egy ügynököt a virtuális gép replikációra. Ügynökalapú áttelepítés esetén telepítenie kell egy [replikációs berendezést.](migrate-replication-appliance.md)

Tekintse át [ezt a cikket,](server-migrate-overview.md) hogy megtudja, melyik módszert szeretné használni.

## <a name="migration-limitations"></a>A migrálásra vonatkozó korlátozások

- Egyszerre legfeljebb 10 virtuális gépet választhat ki a replikációhoz. Ha több gépet szeretne áttelepíteni, majd replikálja a 10-es csoportokban.
- A VMware ügynök nélküli áttelepítése esetén egyszerre legfeljebb 100 replikáció futtatható.

## <a name="agentless-vmware-servers"></a>Agentless-VMware kiszolgálók

**Vmware** | **Részletek**
--- | ---
**VMware vCenter Server** | 5.5-ös, 6.0-s, 6.5-ös vagy 6.7-es verzió.
**VMware vSphere ESXI gazdagép** | 5.5-ös, 6.0-s, 6.5-ös vagy 6.7-es verzió.
**vCenter Server engedélyek** | Az ügynök nélküli áttelepítés a [Készülék áttelepítése (Migrate Appliance) (Ügynök](migrate-appliance.md)nélküli áttelepítés) segítségével használja a készülék áttelepítése lehetőséget. A készüléknek szüksége van a következő engedélyekre:<br/><br/> - **Datastore.Browse**: Lehetővé teszi a virtuális gép naplófájljainak böngészését a pillanatkép létrehozásának és törlésének elhárításához.<br/><br/> - **Datastore.LowLevelFileOperations**: Olvasási/írási/törlési/átnevezési műveletek engedélyezése az adattár böngészőjében a pillanatkép létrehozásának és törlésének hibaelhárításához.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking**: Lehetővé teszi vagy letiltja a virtuális gép lemezek változáskövetését, hogy a pillanatképek között lekérődzhet a módosított adatblokkok.<br/><br/> - **VirtualMachine.Configuration.DiskLease**: A virtuális gép lemezbérleti műveleteinek engedélyezése a lemez olvasásához a VMware vSphere virtuálislemez-fejlesztő készlet (VDDK) használatával.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess**: (kifejezetten a vSphere 6.0 és újabb) Lehetővé teszi a lemez megnyitása a virtuális gépen véletlenszerű olvasási hozzáférést a lemezen a VDDK használatával.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess**: Lehetővé teszi a lemez megnyitását a virtuális gépen, a lemez olvasásához a VDDK használatával.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskRandomAccess**: Lehetővé teszi a lemez megnyitását a virtuális gépen, a lemez olvasásához a VDDK használatával.<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload**: Lehetővé teszi a virtuális géphez társított fájlok olvasási műveleteit, a naplók letöltését és hiba esetén a hiba elhárítását.<br/><br/> - **VirtualMachine.SnapshotManagement.***: Lehetővé teszi a virtuális gép pillanatképeinek létrehozását és kezelését a replikációhoz.<br/><br/> - **Virtuális gép.Interakció.Kikapcsolás:** Engedélyezze a virtuális gép kikapcsolt állapotát az Azure-ba való migrálás során.



## <a name="agentless-vmware-vms"></a>Ügynök nélküli VMware virtuális gépek

**Támogatás** | **Részletek**
--- | ---
**Támogatott operációs rendszerek** | [Az](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) Azure által támogatott Windows és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operációs rendszerek ügynök nélküli áttelepítéssel telepíthetők át.
**Az Azure szükséges módosításai** | Egyes virtuális gépek szükség lehet a módosításokat, hogy azok az Azure-ban futtatható. Az Azure Migrate automatikusan módosítja ezeket a módosításokat a következő operációs rendszereken:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> Más operációs rendszerek esetében az áttelepítés előtt manuálisan kell elvégeznia a módosításokat. A vonatkozó cikkek utasításokat tartalmaznak erre vonatkozóan.
**Linux rendszerindítás** | Ha a /boot egy dedikált partíción található, akkor az operációs rendszer lemezén kell lennie, és nem szabad több lemezre osztani.<br/> Ha a /boot a gyökérpartíció (/) része, akkor a '/' partíciónak az operációs rendszer lemezén kell lennie, és nem kell más lemezekre is kiterjednie.
**UEFI rendszerindítás** | UEFI-rendszerindítással rendelkező virtuális gépek áttelepítése nem támogatott.
**Lemezméret** | 2 TB operációs rendszer lemeze; 8 TB adatlemezek esetén.
**Lemezkorlátok** |  Virtuális gépenként legfeljebb 60 lemez.
**Titkosított lemezek/kötetek** | A titkosított lemezekkel/kötetekkel rendelkező virtuális gépek áttelepítése nem támogatott.
**Megosztott lemezfürt** | Nem támogatott.
**Független lemezek** | Nem támogatott.
**RDM/áthaladási lemezek** | Ha a virtuális gépek RDM-vagy átviteli lemezekkel rendelkeznek, ezek a lemezek nem lesznek replikálva az Azure-ba.
**NFS** | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.
**iSCSI-tárolók** | Az iSCSI-tárolókkal rendelkező virtuális gépek nem támogatottak az ügynök nélküli áttelepítéshez.
**Többutas IO** | Nem támogatott.
**Tároló vMotion** | Nem támogatott. A replikáció nem működik, ha a virtuális gép a vMotion tárolót használja.
**Összeállt hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.
**Céllemez** | Virtuális gépek csak felügyelt lemezekre (standard HDD, prémium szintű SSD) telepíthető kátrájába az Azure-ban.
**Egyidejű replikáció** | 100 virtuális gép/vCenter Server. Ha több, telepítse át őket 100-as kötegekben.


## <a name="agentless-azure-migrate-appliance"></a>Ügynök nélküli Azure Áttelepítés imát 
Ügynök nélküli áttelepítés i a VMware virtuális gépüzembe helyezett Azure Migrate készüléket használja.

- További információ a VMware [készülékkövetelményeiről.](migrate-appliance.md#appliance---vmware)
- Ismerje meg azokat az [URL-címeket, amelyekhez](migrate-appliance.md#url-access) a készüléknek szüksége van.

## <a name="agentless-ports"></a>Ügynök nélküli portok

**Eszköz** | **Kapcsolat**
--- | ---
Készülék | Kimenő kapcsolatok a 443-as porton replikált adatok azure-ba való feltöltéséhez, valamint a replikációt és áttelepítést koordináló Azure Migrate szolgáltatásokkal való kommunikációhoz.
vCenter-kiszolgáló | Bejövő kapcsolatok a 443-as porton, hogy a készülék koordinálhassa a replikációt - pillanatképek létrehozása, adatok másolása, pillanatképek kiadása
vSphere/EXSI gazdagép | Bejövő a 902-es TCP-porton, hogy a készülék adatokat replikáljon a pillanatképekből.


## <a name="agent-based-vmware-servers"></a>Ügynökalapú VMware-kiszolgálók
Ez a táblázat a VMware virtualizációs kiszolgálóinak értékelési támogatását és korlátait foglalja össze.

**VMware követelmények** | **Részletek**
--- | ---
**VMware vCenter Server** | 5.5-ös, 6.0-s, 6.5-ös vagy 6.7-es verzió.
**VMware vSphere ESXI gazdagép** | 5.5-ös, 6.0-s, 6.5-ös vagy 6.7-es verzió.
**vCenter Server engedélyek** | A vCenter Server írásvédett fiókja.

## <a name="agent-based-vmware-vms"></a>Ügynök-alapú VMware virtuális gépek

A tábla összefoglalja a VMware virtuális gép által az ügynökalapú áttelepítéssel áttelepíteni kívánt vmware virtuális gépek támogatását.

**Támogatás** | **Részletek**
--- | ---
**Gépmunkaterhelés** | Az Azure Migrate támogatja a támogatott gépen futó munkaterhelés (például Active Directory, SQL-kiszolgáló stb.) áttelepítését.
**Operációs rendszerek** | A legfrissebb információkért tekintse át az operációs rendszer site recovery [támogatási szolgáltatását.](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) Az Azure Migrate azonos virtuálisgép-operációs rendszer-támogatást biztosít.
**Linux fájlrendszer/vendégtároló** | A legfrissebb információkért tekintse át a [Linux fájlrendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) a Site Recovery szolgáltatáshoz. Az Azure Migrate azonos Linux fájlrendszer-támogatással rendelkezik.
**Hálózat/tárhely** | A legfrissebb információkért tekintse át a [hely-helyreállítás hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#network) és [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#storage) előfeltételeit. Az Azure Migrate azonos hálózati/tárolási követelményeket biztosít.
**Azure-követelmények** | A legfrissebb információkért tekintse át az [Azure-hálózat,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)és [számítási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) követelmények Site Recovery. Az Azure Migrate azonos követelményekkel rendelkezik a VMware-áttelepítéshez.
**Mobilitási szolgáltatás** | A Mobilitási szolgáltatás ügynökkell telepíteni minden virtuális gép áttelepíteni kívánt.
**UEFI rendszerindítás** | Az azure-ban áttelepített virtuális gép automatikusan bios-rendszerindítási virtuális gépté alakul át.<br/><br/> Az operációs rendszer lemezének legfeljebb négy partícióval kell rendelkeznie, és a köteteket NTFS-sel kell formázni.
**Céllemez** | Virtuális gépek csak felügyelt lemezekre (standard HDD, prémium szintű SSD) telepíthető kátrájába az Azure-ban.
**Lemezméret** | 2 TB operációs rendszer lemeze; 8 TB adatlemezek esetén.
**Lemezkorlátok** |  Virtuális gépenként legfeljebb 63 lemez.
**Titkosított lemezek/kötetek** | A titkosított lemezekkel/kötetekkel rendelkező virtuális gépek áttelepítése nem támogatott.
**Megosztott lemezfürt** | Nem támogatott.
**Független lemezek** | Támogatott.
**Áthaladási lemezek** | Támogatott.
**NFS** | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.
**iSCSI-tárolók** | Az iSCSI-tárolókkal rendelkező virtuális gépek nem támogatottak az ügynök nélküli áttelepítéshez.
**Többutas IO** | Nem támogatott.
**Tároló vMotion** | Támogatott
**Összeállt hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.




## <a name="agent-based-replication-appliance"></a>Ügynökalapú replikációs berendezés 

Ha a replikációs berendezést az Azure Migrate hubban található OVA sablon használatával állítja be, a készülék a Windows Server 2016 rendszert futtatja, és megfelel a támogatási követelményeknek. Ha a replikációs berendezést manuálisan állítja be egy fizikai kiszolgálón, győződjön meg arról, hogy az megfelel a követelményeknek.

- További információ a [VMware replikációs berendezésre vonatkozó követelményeiről.](migrate-replication-appliance.md#appliance-requirements)
- A MySQL-t fel kell szerelni a készülékre. További információ a [telepítési lehetőségekről.](migrate-replication-appliance.md#mysql-installation)
- Ismerje meg a replikációs készülék által elérni szükséges [URL-címeket](migrate-replication-appliance.md#url-access) és [portokat.](migrate-replication-appliance.md#port-access)

## <a name="agent-based-ports"></a>Ügynökalapú portok

**Eszköz** | **Kapcsolat**
--- | ---
Virtuális gépek | A virtuális gépeken futó Mobilitás szolgáltatás kommunikál a https 443-as porton lévő helyszíni replikációs eszközzel (konfigurációs kiszolgálóval) a replikáció kezeléséhez.<br/><br/> A virtuális gépek replikációs adatokat küldenek a folyamatkiszolgálónak (amely a konfigurációs kiszolgálógépen fut) a HTTPS 9443 bejövő porton. Ez a port módosítható.
Replikációs berendezés | A replikációs berendezés vezényli a replikációt az Azure-ral HTTPS 443 kimenő porton keresztül.
Folyamatkiszolgáló | A folyamatkiszolgáló fogadja a replikációs adatokat, optimalizálja és titkosítja azokat, és elküldi az Okat az Azure storage-ba a 443-as kimenő porton keresztül.<br/> Alapértelmezés szerint a folyamatkiszolgáló fut a replikációs berendezésen.

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie az ebben a táblázatban összegzett Azure virtuális gép követelményeinek. Ha a Site Recovery a replikáció előfeltételeinek ellenőrzését futtatja, az ellenőrzés sikertelen lesz, ha a követelmények némelyike nem teljesül.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Ellenőrzi a támogatott VMware VM operációs rendszerek áttelepítéséhez.<br/> A támogatott operációs rendszeren futó munkaterhelések áttelepíthetők. | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemez mérete | Akár 2048 GB. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Akár 4095 GB | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A gép replikációjának engedélyezése előtt le kell tiltani a BitLocker szolgáltatást.
a virtuális gép neve | 1 és 63 karakter között.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse a számítógép tulajdonságainak értékét a Site Recovery szolgáltatásban.
Csatlakozás áttelepítés után-Windows | Csatlakozás Windows rendszert futtató Azure-beli virtuális gépekhez az áttelepítés után:<br/> - Az áttelepítés előtt lehetővé teszi az RDP a helyszíni virtuális gép. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/> A helyek közötti VPN-hozzáférés hez engedélyezze az RDP-t, és engedélyezze az RDP szolgáltatást a **Windows tűzfal** -> **engedélyezett alkalmazásaiban és szolgáltatásaiban** **a tartományi és magánhálózatokhoz.** Ezenkívül ellenőrizze, hogy az operációs rendszer SAN-házirendje **OnlineAll lesz-e.** [További információ](prepare-for-migration.md). |
Csatlakozás áttelepítés után-Linux | Csatlakozás Az Azure virtuális gépekhez az SSH használatával történő áttelepítés után:<br/> Az áttelepítés előtt a helyszíni gépen ellenőrizze, hogy a Secure Shell szolgáltatás indításra van-e állítva, és hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.<br/> Feladatátvétel után az Azure virtuális gép, engedélyezze a bejövő kapcsolatokat az SSH-port a hálózati biztonsági csoport szabályok a feladatátvételi virtuális gép, és az Azure-alhálózat, amelyhez csatlakozik. Emellett adjon hozzá egy nyilvános IP-címet a virtuális géphez. |  


## <a name="next-steps"></a>További lépések

[Válassza ki](server-migrate-overview.md) a VMware áttelepítési beállítás.
