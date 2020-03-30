---
title: Fizikai kiszolgálóáttelepítés támogatása az Azure Áttelepítésében
description: Ismerje meg, hogy miként támogatja a fizikai kiszolgálók migrálását az Azure Áttelepítésben.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269548"
---
# <a name="support-matrix-for-physical-server-migration"></a>A kiszolgáló fizikai áttelepítésének támogatási mátrixa

Ez a cikk összefoglalja a fizikai kiszolgálók [Áttelepítése: Kiszolgálóáttelepítés](migrate-services-overview.md#azure-migrate-server-migration-tool) szolgáltatással történő áttelepítés támogatási beállításait és korlátait. Ha az Azure-ba való áttelepítés fizikai kiszolgálóinak értékeléséről szeretne információt keresni, tekintse át az [értékelési támogatási mátrixot.](migrate-support-matrix-physical.md)


## <a name="overview"></a>Áttekintés

A helyszíni gépeket ügynökalapú replikációval fizikai kiszolgálóként telepítheti át. Ezzel az eszközzel a gépek széles körét telepítheti át az Azure-ba:

- Helyszíni fizikai kiszolgálók.
- Olyan platformok által virtualizált virtuális gépek, mint az Xen, a KVM.
- Hyper-V vms vagy VMware virtuális gépek, ha valamilyen okból nem szeretné használni a szabványos [Hyper-V](tutorial-migrate-hyper-v.md) vagy [VMware](server-migrate-overview.md) folyamatok.
- Privát felhőkben futó virtuális gépek.
- Nyilvános felhőkben, például amazon webszolgáltatásokban (AWS) vagy a Google Cloud Platformon (GCP) futó virtuális gépek.


## <a name="migration-limitations"></a>A migrálásra vonatkozó korlátozások

Egyszerre legfeljebb 10 gépet választhat ki a replikációhoz. Ha több gépet szeretne áttelepíteni, majd replikálja a 10-es csoportokban.


## <a name="physical-server-requirements"></a>Fizikai kiszolgáló követelmények

A tábla összefoglalja az ügynökalapú áttelepítéssel áttelepíteni kívánt fizikai kiszolgálók támogatását.

**Támogatás** | **Részletek**
--- | ---
**Gépmunkaterhelés** | Az Azure Migrate támogatja a támogatott gépen futó munkaterhelés (például Active Directory, SQL-kiszolgáló stb.) áttelepítését.
**Operációs rendszerek** | A legfrissebb információkért tekintse át az operációs rendszer site recovery [támogatási szolgáltatását.](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) Az Azure Migrate azonos operációs rendszer-támogatást biztosít.
**Linux fájlrendszer/vendégtároló** | A legfrissebb információkért tekintse át a [Linux fájlrendszer támogatását](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) a Site Recovery szolgáltatáshoz. Az Azure Migrate azonos Linux fájlrendszer-támogatást biztosít.
**Hálózat/tárhely** | A legfrissebb információkért tekintse át a [hely-helyreállítás hálózati](../site-recovery/vmware-physical-azure-support-matrix.md#network) és [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#storage) előfeltételeit. Az Azure Migrate azonos hálózati/tárolási követelményeket biztosít.
**Azure-követelmények** | A legfrissebb információkért tekintse át az [Azure-hálózat,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [tárolási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)és [számítási](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) követelmények Site Recovery. Az Azure Áttelepítés azonos követelményekkel rendelkezik a fizikai kiszolgáló áttelepítése.
**Mobilitási szolgáltatás** | A Mobilitási szolgáltatás ügynökét minden áttelepíteni kívánt számítógépre telepíteni kell.
**UEFI rendszerindítás** | Az Azure-ban áttelepített gép automatikusan bios-rendszerindítással indítja az Azure virtuális gépét.<br/><br/> Az operációs rendszer lemezének legfeljebb négy partícióval kell rendelkeznie, és a köteteket NTFS-sel kell formázni.
**Céllemez** | A gépek csak felügyelt lemezekre (standard HDD, prémium szintű SSD) telepíthetők át az Azure-ban.
**Lemezméret** | 2 TB operációs rendszer lemeze; 8 TB adatlemezek esetén.
**Lemezkorlátok** |  Akár 63 lemez gépenként.
**Titkosított lemezek/kötetek** |  A titkosított lemezekkel/kötetekkel rendelkező gépek áttelepítése nem támogatott.
**Megosztott lemezfürt** | Nem támogatott.
**Független lemezek** | Támogatott.
**Áthaladási lemezek** | Támogatott.
**NFS** | A gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.
**iSCSI-tárolók** | Az iSCSI-tárolókkal rendelkező gépek nem támogatottak az ügynök nélküli áttelepítéshez.
**Többutas IO** | Nem támogatott.
**Tároló vMotion** | Támogatott
**Összeállt hálózati adapterek** | Nem támogatott.
**IPv6** | Nem támogatott.



## <a name="replication-appliance-requirements"></a>Replikációs berendezés követelményei

Ha a replikációs berendezést manuálisan állítja be egy fizikai kiszolgálón, győződjön meg arról, hogy az megfelel a táblázatban összefoglalt követelményeknek. Ha az Azure Migrate replikációs berendezést vmware virtuális gépként állítja be az Azure Migrate hubban biztosított OVA sablon használatával, a készülék a Windows Server 2016-hoz van beállítva, és megfelel a támogatási követelményeknek. 

- További információ a [replikációs berendezésekre vonatkozó követelményekről.](migrate-replication-appliance.md#appliance-requirements)
- A MySQL-t fel kell szerelni a készülékre. További információ a [telepítési lehetőségekről.](migrate-replication-appliance.md#mysql-installation)
- Ismerje meg azokat az [URL-címeket, amelyekhez](migrate-replication-appliance.md#url-access) a replikációs eszköznek szüksége van.

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie az ebben a táblázatban összegzett Azure virtuális gép követelményeinek. Ha a Site Recovery a replikáció előfeltételeinek ellenőrzését futtatja, az ellenőrzés sikertelen lesz, ha a követelmények némelyike nem teljesül.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Ellenőrzi a támogatott operációs rendszereket.<br/> A támogatott operációs rendszeren futó munkaterhelések áttelepíthetők. | Az ellenőrzés sikertelen, ha nem támogatott.
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

Fizikai kiszolgálók [áttelepítése.](tutorial-migrate-physical-virtual-machines.md)
