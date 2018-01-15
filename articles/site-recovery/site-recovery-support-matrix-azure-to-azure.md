---
title: "Az Azure Site Recovery mátrix a replikálása Azure-bA az Azure-ból |} Microsoft Docs"
description: "Foglalja össze a támogatott operációs rendszerek és konfigurációk az Azure Site Recovery replikálása az Azure virtuális gépek (VM) több régióban másikra vész-helyreállítási igényekre."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/08/2017
ms.author: sujayt
ms.openlocfilehash: c15583b9420355bb7c35bd107b899c59e80e3741
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2018
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>Az Azure Site Recovery mátrix a replikálása Azure-bA az Azure-ból


>[!NOTE]
>
> Az Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.

Ez a cikk az Azure Site Recovery replikálódik, és az Azure virtuális gépek helyreállítani egy régió tartozik egy másik régióban támogatott konfigurációk és összetevőket foglalja össze.

## <a name="user-interface-options"></a>A felhasználói felület lehetőségei

**Felhasználói felület** |  **Támogatott / nem támogatott**
--- | ---
**Azure Portal** | Támogatott
**Klasszikus portál** | Nem támogatott
**PowerShell** | Jelenleg nem támogatott.
**REST API** | Jelenleg nem támogatott.
**Parancssori felület** | Jelenleg nem támogatott.


## <a name="resource-move-support"></a>Erőforrás-áthelyezés támogatása

**Erőforrás-Áthelyezés típusa** | **Támogatott / nem támogatott** | **Megjegyzések**  
--- | --- | ---
**Erőforráscsoportok közötti áthelyezése közben tároló** | Nem támogatott |A Recovery services-tároló csoportok között nem helyezhető át.
**Számítási, tárolási és hálózati erőforráscsoportok közötti áthelyezése közben** | Nem támogatott |Ha egy virtuális gép (vagy a kapcsolódó összetevők, például a tárolási és hálózati) után a replikáció, tiltsa le a replikációt, és engedélyezze a replikálást a virtuális gép újra szüksége.



## <a name="support-for-deployment-models"></a>Üzembe helyezési modellel támogatása

**Telepítési modell** | **Támogatott / nem támogatott** | **Megjegyzések**  
--- | --- | ---
**Klasszikus** | Támogatott | Csak a klasszikus virtuális gépek replikálása, és végezze el a helyreállítást a klasszikus virtuális gépként. Az erőforrás-kezelő virtuális gépként nem lehet helyreállítani. Ha a klasszikus virtuális gépek virtuális hálózat nélkül, és közvetlenül az Azure-régió, nem támogatott.
**Resource Manager** | Támogatott |

>[!NOTE]
>
> 1. Az Azure virtuális gépek replikálása egy előfizetés egy másik a vész-helyreállítási eljárással nem támogatott.
> 2. Áttelepítése Azure virtuális gépek különböző előfizetéseknél nem támogatott.
> 3. Áttelepítése Azure virtuális gépek ugyanabban a régióban belül nem támogatott.
> 4. Azure virtuális gépek áttelepítéséhez klasszikus telepítési modellből a Resource manager üzembe helyezési modellben nem támogatott.

## <a name="support-for-replicated-machine-os-versions"></a>A replikált gép operációsrendszer-verziók támogatása

Az alábbi támogatási esetén alkalmazható bármilyen munkaterhelést futtató az említett operációs rendszer.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core, az asztali élmény kiszolgáló) *
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2: legalább SP1

>[!NOTE]
>
> \*Windows Server 2016 Nano Server nem támogatott.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4
- Ubuntu 14.04 LTS Server [ (támogatott kernel verziók)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (támogatott kernel verziók)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7
- Debian 8
- Oracle Enterprise Linux 6.4, vagy a Red Hat kompatibilis kernel, vagy a szoros vállalati Kernel kiadás 3 (UEK3) 6.5
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

(A gépek replikálásához SLES 11 SP3 az SLES 11 SP4 frissítés nem támogatott. Ha a replikált gép SLES 11SP3 az SLES 11 SP4 frissítették, meg kell tiltsa le a replikációt, és a post újra a frissítést a gép védelméhez.)

>[!NOTE]
>
> Ubuntu kiszolgálók jelszóalapú hitelesítés és bejelentkezés használatával, és a felhő virtuális gépeket, konfigurálhatja a felhő inicializálás csomag segítségével lehet esetén feladatátvevő (attól függően, hogy a cloudinit konfigurációs.) letiltja a jelszó alapú bejelentkezés A beállítások menüből a jelszó alaphelyzetbe állításával jelszóalapú bejelentkezési újból engedélyezni a virtuális gép lehet (a támogatási és HIBAELHÁRÍTÁSI alatt. szakasz) a virtuális gép az Azure portálon keresztül.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Az Azure virtuális gépek támogatott Ubuntu kernel verziók

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel-verzió** |
--- | --- | --- |
14.04 LTS | 9.10 | a 3.13.0-121-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-81-generic 4.4.0-21-Generic |
14.04 LTS | 9.11 | a 3.13.0-125-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-83-generic 4.4.0-21-Generic |
14.04 LTS | 9.12 | a 3.13.0-132-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-96-generic 4.4.0-21-Generic |
14.04 LTS | 9.13 | a 3.13.0-137-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-104-generic 4.4.0-21-Generic |
16.04 LTS | 9.10 | a 4.4.0-81-generic, 4.4.0-21-Generic<br/>a 4.8.0-56-generic, 4.8.0-34-Generic<br/>a 4.10.0-24-generic 4.10.0-14-Generic |
16.04 LTS | 9.11 | a 4.4.0-83-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-27-generic 4.10.0-14-Generic |
16.04 LTS | 9.12 | a 4.4.0-96-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-35-generic 4.10.0-14-Generic |
16.04 LTS | 9.13 | a 4.4.0-104-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic 4.10.0-14-Generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Támogatott fájlrendszerek és a Vendég tárolási konfigurációk alakíthatók ki Linux operációs rendszert futtató Azure virtuális gépeken

* Fájlrendszer: ext3, ext4, ReiserFS (Suse Linux Enterprise Server csak), XFS
* Kötetkezelő: LVM2
* A többutas szoftver: eszköz leképezője

## <a name="region-support"></a>Terület támogatása

Replikálja, és a virtuális gépek helyreállítása földrajzi fürt bármely két régiók között.

**Földrajzi fürt** | **Azure-régiók**
-- | --
Amerikai | Kanada keleti, Kanada központi, Dél-USA középső RÉGIÓJA, központi USA nyugati régiója, USA keleti régiója, USA keleti régiója 2. régiója, USA nyugati régiója, 2 USA nyugati régiója, USA középső RÉGIÓJA, Észak USA középső RÉGIÓJA
Európa | Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Észak-Európa, Nyugat-Európa
Ázsia | Dél-Indiában, közép-Indiában, Délkelet-Ázsiában, kelet-ázsiai keleti, japán, Nyugat-japán, koreai központi koreai Dél
Ausztrália   | Kelet-Ausztrália Ausztrália óceáni térség délkeleti régiója
Azure Government    | USA – (kormányzati) Virginia, USA – (kormányzati) Iowa, USA – (kormányzati) Arizona, USA – (kormányzati) Texas, USA DOD kelet, USA DOD középső régiója
Németország | Németország központi, Németország szerepel
Kína | Kínai keleti, Kína északi régiója

>[!NOTE]
>
> Dél-Brazília régió csak replikálásához és déli középső Régiójában, Nyugat középső Régiójában, USA keleti régiója, USA keleti régiója 2. régiója, USA nyugati régiója, USA 2. nyugati és északi középső Régiójában régiók egyikéhez feladatátvételt és a feladat-visszavételt.


## <a name="support-for-compute-configuration"></a>Számítási konfigurációhoz támogatása

**Konfigurálás** | **Támogatott/nem támogatott** | **Megjegyzések**
--- | --- | ---
Méret | Bármely Azure Virtuálisgép-méretet a CPU-magokat legalább 2 és 1 GB RAM | Tekintse meg [Azure virtuálisgép-méretek](../virtual-machines/windows/sizes.md)
Rendelkezésre állási csoportok | Támogatott | Portál "Replikáció engedélyezése" lépése során az alapértelmezett beállítást használja, ha a a rendelkezésre állási csoportok pedig automatikusan létrehozott adatforrását régió konfigurációja alapján. Módosíthatja a célként megadott rendelkezésre állási csoport "replikált elemek > Beállítások > Számítás és hálózat > rendelkezésre állási csoport" bármikor.
Hibrid használata juttatás (HUB) virtuális gépek | Támogatott | Ha a forrás virtuális gép HUB licenc engedélyezve van, a feladatátvételi teszt vagy feladatátvételi VM is használja a központ licenc.
Virtuálisgép-méretezési csoportok | Nem támogatott |
A gyűjtemény lemezképei Azure - Microsoft közzétett | Támogatott | Támogatott, amíg a Site Recovery által támogatott operációs rendszeren futtatja a virtuális gép
Azure-katalógus lemezképek - harmadik féltől származó közzétett | Támogatott | Támogatott, amíg a Site Recovery által támogatott operációs rendszeren futtatja a virtuális Gépet.
Egyéni lemezképek - harmadik féltől származó közzétett | Támogatott | Támogatott, amíg a Site Recovery által támogatott operációs rendszeren futtatja a virtuális Gépet.
Virtuális gépek áttelepítése a Site Recovery | Támogatott | Ha a rendszer egy VMware vagy fizikai gépre az Azure Site Recovery segítségével telepíti át, távolítsa el a mobilitási szolgáltatás régebbi verzióját, és indítsa újra a gépet, mielőtt replikálása az Azure-régió, egy másik szüksége.

## <a name="support-for-storage-configuration"></a>Tárolási konfiguráció támogatása

**Konfigurálás** | **Támogatott/nem támogatott** | **Megjegyzések**
--- | --- | ---
Maximális operációsrendszer-lemez mérete | 2048 GB | Tekintse meg [virtuális gépek által használt lemezek.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Maximális adattároló lemezeinek mérete | 4095 GB | Tekintse meg [virtuális gépek által használt lemezek.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Adatlemezek száma | Egy adott Azure virtuális gép mérete által támogatott legfeljebb 64 | Tekintse meg [Azure virtuálisgép-méretek](../virtual-machines/windows/sizes.md)
Ideiglenes lemez | Mindig ki vannak zárva a replikációból | Ideiglenes lemez ki van zárva a replikációból mindig. Az Azure útmutatás szerint ideiglenes lemezen ne helyezzen állandó adatokat. Tekintse meg [Azure virtuális gépeken mennyiségű ideiglenes lemezes](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) további részleteket.
A lemezen adatváltozási sebesség | Legfeljebb egy lemezen 6 MB/s | Ha átlagos adatváltozási sebessége a lemez túl 6 MB/s folyamatosan, replikációs fog nem dolgozza. Azonban ha egy alkalmanként adatokat kapacitásnövelés, és az adatmódosítási arány nagyobb, mint 6 MB/s egy kis ideig, és előre replikációs fog szinkronizálásához. Ebben az esetben jelenhet meg némileg késleltetett helyreállítási pontokat.
Standard szintű storage-fiókok lemezek | Támogatott |
Prémium szintű storage-fiókok lemezek | Támogatott | Ha egy virtuális gép prémium és standard szintű storage-fiókok elosztva lemezzel rendelkezik, válassza az egyes lemezek ugyanazt a tárolási konfigurációt, hogy a cél régióban másik cél tárfiók
Standard szintű felügyelt lemez | Nem támogatott |  
Prémium szintű felügyelt lemez | Nem támogatott |
Tárolóhelyek | Támogatott |         
Titkosítását (SSE) | Támogatott | Gyorsítótár és a cél storage-fiókok válassza ki az engedélyezett SSE tárfiókot.     
Az Azure Disk Encryption (ADE) | Nem támogatott |
Gyakran használt adatok hozzáadása lemez | Nem támogatott | Ha ad hozzá, vagy távolítsa el a virtuális Gépre adatlemez, szeretné tiltsa le a replikációt, és engedélyezze újra a virtuális gép replikálását.
Lemez kizárása | Nem támogatott|   Ideiglenes lemez alapértelmezés szerint ki van zárva.
LRS | Támogatott |
GRS | Támogatott |
RA-GRS | Támogatott |
ZRS | Nem támogatott |  
Ritkán használt adatok és a gyakran használt adatok tárolási | Nem támogatott | Virtuálisgép-lemezek használata nem támogatott a ritkán használt adatok és a gyakran használt adatok tárolási
Virtuális hálózati Szolgáltatásvégpontok (Azure Storage tűzfalak és virtuális hálózatok)  | Nem | Adott Azure virtuális hálózatokhoz való hozzáférés engedélyezése a replikált adatok tárolására szolgáló gyorsítótár tárfiókok használata nem támogatott. 
Általános célú V2 storage-fiókok (a gyakran és ritkán. szint) | Nem | Tranzakciós költségek növekedését jelentősen képest általános célú tárfiókok 1-es verzió

>[!IMPORTANT]
> Győződjön meg arról, hogy a virtuális gép lemez méretezhetőségi és Teljesítménycélok az erőforrásigények [Linux](../virtual-machines/linux/disk-scalability-targets.md) vagy [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuális gépek a teljesítménnyel kapcsolatos problémák elkerülése érdekében. Ha követi az alapértelmezett beállításokat, a Site Recovery hoz létre a szükséges lemezek és a storage-fiókok a forrás-konfigurációtól függően. Ha testre szabhatja, és válassza ki a saját beállításait, győződjön meg arról, hogy kövesse a lemez méretezhetőségi és Teljesítménycélok a forrás virtuális gépeknek.

## <a name="support-for-network-configuration"></a>Hálózati konfiguráció támogatása
**Konfigurálás** | **Támogatott/nem támogatott** | **Megjegyzések**
--- | --- | ---
Hálózati adapter (NIC) | Legfeljebb egy adott Azure virtuális gép mérete által támogatott hálózati adapterek maximális száma | Hálózati adaptert a virtuális gép létrehozásakor a feladatátvételi teszt vagy a feladatátvételi művelet részeként jönnek létre. A hálózati adapterek számát a virtuális gép feladatátvételi attól függ, a hálózati adapterek száma a forrás virtuális gép van a replikáció során. Ha Ön hozzáadása a hálózati adapter replikáció engedélyezése után, nem befolyásolja a virtuális gép feladatátvevő NIC száma.
Internetes Load Balancer | Támogatott | Társítsa a előre konfigurált load balancer egy azure Automation szolgáltatásbeli parancsfájl használata a helyreállítási tervben kell.
Belső terheléselosztó | Támogatott | Társítsa a előre konfigurált load balancer egy azure Automation szolgáltatásbeli parancsfájl használata a helyreállítási tervben kell.
Nyilvános IP-cím| Támogatott | Egy már meglévő nyilvános IP-cím a hálózati adapterhez társítani vagy hozzon létre egyet, és társítsa a hálózati adapterhez, egy azure Automation szolgáltatásbeli parancsfájl használata a helyreállítási tervben kell.
NSG a hálózati Adapteren (erőforrás-kezelő)| Támogatott | A hálózati adapterhez, egy azure Automation szolgáltatásbeli parancsfájl használata a helyreállítási tervben szereplő NSG társítása kell.  
NSG alhálózat (Resource Manager és klasszikus)| Támogatott | A hálózati adapterhez, egy azure Automation szolgáltatásbeli parancsfájl használata a helyreállítási tervben szereplő NSG társítása kell.
NSG a virtuális gép (klasszikus)| Támogatott | A hálózati adapterhez, egy azure Automation szolgáltatásbeli parancsfájl használata a helyreállítási tervben szereplő NSG társítása kell.
Fenntartott IP (statikus IP-cím) / megőrizni a forrás IP-címe | Támogatott | Ha a forrás virtuális gép hálózati adapter van statikus IP-konfigurációt, és a cél alhálózathoz az azonos IP-cím elérhető, a virtuális gép feladatátvételi hozzá van rendelve. A célként megadott alhálózat nem rendelkezik az azonos IP-cím elérhető, ha az alhálózat elérhető IP-címek egyikét a virtuális gép számára van fenntartva. Megadhat egy fix IP-cím az Ön által választott, a "replikált elemek > Beállítások > Számítás és hálózat > hálózati illesztőt. Válassza ki a hálózati Adaptert, és adja meg az alhálózatot és IP-címe a kiválasztott.
Dinamikus IP| Támogatott | Ha a hálózati adapter a forrás virtuális gép dinamikus IP-konfiguráció, a hálózati adapter a feladatátvétel virtuális gép is alapértelmezés szerint dinamikus. Megadhat egy fix IP-cím az Ön által választott, a "replikált elemek > Beállítások > Számítás és hálózat > hálózati illesztőt. Válassza ki a hálózati Adaptert, és adja meg az alhálózatot és IP-címe a kiválasztott.
A Traffic Manager integrálása | Támogatott | A traffic manager úgy, hogy a továbbítódik a végpont rendszeresen forrás régióban, és a cél régióban feladatátvétel esetén az endpoint előre konfigurálhatja.
Azure DNS által felügyelt | Támogatott |
Egyéni DNS  | Támogatott |    
Nem hitelesített Proxy | Támogatott | Tekintse meg [hálózati útmutató.](site-recovery-azure-to-azure-networking-guidance.md)    
Hitelesített Proxy | Nem támogatott | Ha a virtuális gép egy hitelesített proxykiszolgálót használ a kimenő hálózati kapcsolatot, akkor nem replikálhatók Azure Site Recovery segítségével.    
Webhelyek közötti VPN a helyszíni (vagy anélkül ExpressRoute)| Támogatott | Ellenőrizze, hogy a udr-EK és NSG-ket úgy, hogy a Site recovery nem továbbítódik a helyszíni. Tekintse meg [hálózati útmutató.](site-recovery-azure-to-azure-networking-guidance.md)  
Virtuális hálózat virtuális Hálózatot kapcsolathoz | Támogatott | Tekintse meg [hálózati útmutató.](site-recovery-azure-to-azure-networking-guidance.md)  


## <a name="next-steps"></a>További lépések
- További információ [hálózati útmutatót az Azure virtuális gépek replikálása](site-recovery-azure-to-azure-networking-guidance.md)
- A munkaterhelések számára a védelmének megkezdéséhez [Azure virtuális gépek replikálása](site-recovery-azure-to-azure.md)
