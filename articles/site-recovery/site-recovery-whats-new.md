---
title: A Azure Site Recovery újdonságai
description: A Azure Site Recovery bevezetett új szolgáltatások összegzését tartalmazza.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: raynew
ms.openlocfilehash: 8e034153a2e98a101527f411c78ace6e46b01b29
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937527"
---
# <a name="whats-new-in-site-recovery"></a>A Site Recovery újdonságai

A [Azure site Recovery](site-recovery-overview.md) szolgáltatás folyamatosan frissül és fejleszthető. Ez a cikk a legfrissebb kiadásokról, új funkciókról és új tartalmakról nyújt tájékoztatást. Ez az oldal rendszeresen frissül.

Az [Azure](https://azure.microsoft.com/updates/?product=site-recovery) Updates csatornán követheti és előfizethet site Recovery frissítési értesítéseire.

## <a name="supported-updates"></a>Támogatott frissítések

Site Recovery-összetevők esetében az N-4 verziót támogatjuk, ahol N a legújabb kiadású verzió. Ezeket az alábbi táblázat foglalja össze.

**Update** |  **Egyesített beállítás** | **Konfigurációs kiszolgáló petesejtjei** | **Mobilitási szolgáltatás ügynöke** | **Site Recovery szolgáltató** | **Recovery Services ügynök**
--- | --- | --- | --- | --- | ---
[40-es összesítés](https://support.microsoft.com/help/4517283/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0
[39-es összesítés](https://support.microsoft.com/help/4517283/) | 9.27.5308.1 | 5.1.4600.0 | 9.27.5308.1 | 5.1.4600.0 | 2.0.9165.0
[38-es összesítés](https://support.microsoft.com/help/4513507/) | 9.26.5269.1 | 5.1.4500.0 | 9.26.5269.1 | 5.1.4500.0 | 2.0.9165.0
[37-es összesítés](https://support.microsoft.com/help/4508614/) | 9.25.5241.1 | 5.1.4300.0 | 9.25.5241.1 | 5.1.4300.0 | 2.0.9163.0
[36-es összesítés](https://support.microsoft.com/help/4503156/) | 9.24.5211.1 | 5.1.4150.0 | 9.24.5211.1 | 5.1.4150.0 | 2.0.9160.0 
        

[További](service-updates-how-to.md) információ a frissítések telepítéséről és támogatásáról.


## <a name="updates-september-2019"></a>Frissítések (2019. szeptember)

### <a name="update-rollup-40"></a>40-es kumulatív frissítés

A 40-es [kumulatív frissítés](h https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)




### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az Azure-beli virtuális gép vész-helyreállításának új funkciói a táblázatban vannak összegezve.

**Funkció** | **Részletek**
--- | ---
**Törlés a feladat-visszavétel után** | A másodlagos Azure-ba történő feladatátvétel után, majd az elsődleges régióba való visszalépés után Site Recovery automatikusan törli a gépeket a másodlagos régióban. Nem szükséges manuálisan törölni a virtuális gépeket és a hálózati adaptereket.
**A feladatátvételi teszt megtartja az IP-címet** | Mostantól megtarthatja a forrás virtuális gép IP-címét a vész-helyreállítási részletezés során, és egy statikus IP-címet is kiválaszthat a feladatátvételi teszthez.

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
Új Process Server-riasztások | Új Process Server-riasztások lettek hozzáadva. [További információk](vmware-physical-azure-monitor-process-server.md). 

### <a name="hyper-v-disaster-recovery"></a>Hyper-V vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
Tárfiók | A Site Recovery mostantól támogatja a Hyper-V-ről az Azure-ba irányuló, a tűzfalon engedélyezve lévő Storage-fiókok használatát.  A tűzfalat támogató Storage-fiókokat célként fiókként vagy gyorsítótárbeli tárolóként is kiválaszthatja. Ha tűzfalat használó fiókot használ, engedélyezze a megbízható Microsoft-szolgáltatások engedélyezését.<br/><br/> Ezt a Hyper-V virtuális gépek támogatják a System Center VMM vagy anélkül.


## <a name="updates-august-2019"></a>Frissítések (2019. augusztus)

### <a name="update-rollup-39"></a>39-es kumulatív frissítés

A 39-es [kumulatív frissítés](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az Azure-beli virtuális gép vész-helyreállításának új funkciói a táblázatban vannak összegezve.

**Funkció** | **Részletek**
--- | ---
**Titkosítás az Azure AD nélkül** | Az Azure AD-alkalmazás nélküli titkosítás mostantól támogatott az Azure-beli virtuális gépek replikálásához Windows rendszerű felügyelt lemezeken.
**Feladatátvételhez szükséges hálózati erőforrások** | Ha feladatátvételt végez egy másik régióba, most hálózati erőforrás-beállításokat (NSG, terheléselosztást, nyilvános IP-címet) csatolhat egy virtuális géphez. 

## <a name="updates-july-2019"></a>Frissítések (2019. július)

### <a name="update-rollup-38"></a>38. kumulatív frissítés

A 38-es [kumulatív frissítés](https://support.microsoft.com/help/4513507/) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="general"></a>Általános

A Site Recovery mostantól támogatja az általános célú v2 Storage-fiókok használatát a gyorsítótár-tároláshoz vagy a célhelyek tárolásához. Korábban csak a v1 támogatott.

### <a name="vmware-to-azure-disaster-recovery"></a>Vészhelyreállítás VMware-ről Azure-ba

A lemezeket mostantól akár 8 TB-ra is replikálhatja, ha felügyelt lemezekkel rendelkező Azure-beli virtuális gépre végez replikálást.


## <a name="updates-june-2019"></a>Frissítések (2019. június)

### <a name="update-rollup-37"></a>37-es kumulatív frissítés

A 37-es [kumulatív frissítés](https://support.microsoft.com/help/4508614/) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítései (a kumulatív részletezéssel)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**GPT-partíciók** | Az 37-as kumulatív frissítéstől kezdve (a mobilitási szolgáltatás verziójának 9.25.5241.1) legfeljebb öt GPT-partíció támogatott az UEFI-ben. A frissítés előtt négy támogatott.



## <a name="updates-may-2019"></a>Frissítések (május 2019)

### <a name="update-rollup-36"></a>36. kumulatív frissítés

A 36-es [kumulatív frissítés](https://support.microsoft.com/help/4503156) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Hozzáadott lemezek replikálása** | A vész-helyreállításra már engedélyezett Azure-beli virtuális gépekhez hozzáadott adatlemezek replikálásának engedélyezése. [További információk](azure-to-azure-enable-replication-added-disk.md).
**Automatikus frissítések** | Ha olyan Azure-beli virtuális gépeken futó mobilitási szolgáltatás bővítményének automatikus frissítését konfigurálja, amelyeken engedélyezve van a vész-helyreállítás, mostantól kiválaszthatja a használni kívánt Automation-fiókot a Site Recovery által létrehozott alapértelmezett fiók használata helyett. [További információk](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**A Process Server figyelése** | A helyszíni VMware virtuális gépek és fizikai kiszolgálók vész-helyreállításához, a kiszolgáló állapotának javításához és a riasztások kezeléséhez a folyamat-kiszolgálókkal kapcsolatos problémák figyelése és hibaelhárítása. [További információk](vmware-physical-azure-monitor-process-server.md). 





## <a name="updates-march-2019"></a>Frissítések (2019. március)

### <a name="update-rollup-35"></a>35-es kumulatív frissítés

A 35-es [kumulatív frissítés](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint)
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezett módon)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Felügyelt lemezek** | A helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálása mostantól közvetlenül az Azure-ban felügyelt lemezekre történik. A helyszíni információk egy gyorsítótárbeli Storage-fiókba lesznek továbbítva az Azure-ban, és a helyreállítási pontok a célhelyen felügyelt lemezeken jönnek létre. Ez biztosítja, hogy ne kelljen több cél Storage-fiókot kezelnie.
**Konfigurációs kiszolgáló** | Site Recovery mostantól támogatja a több hálózati adapterrel rendelkező konfigurációs kiszolgálókat. A konfigurációs kiszolgáló a tárolóban való regisztrálása előtt adjon hozzá további adaptereket a konfigurációs kiszolgáló virtuális géphez. Ha ezt követően hozzáadja, újra kell regisztrálnia a kiszolgálót a tárolóban.


## <a name="updates-february-2019"></a>Frissítések (február 2019)

### <a name="update-rollup-34"></a>34-es kumulatív frissítés 

A 34-es [kumulatív frissítés](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).


### <a name="update-rollup-33"></a>33-es kumulatív frissítés 

A 33-es [kumulatív frissítés](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).


### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási 
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Hálózati leképezés** | Az Azure-beli virtuális gépek vész-helyreállítási folyamata esetén a replikáció engedélyezésekor bármilyen elérhető célszámítógépet is használhat. 
**Standard SSD** | Most már beállíthatja az Azure-beli virtuális gépek vész-helyreállítását [standard SSD lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)használatával.
**Közvetlen tárolóhelyek** | Az Azure VM-alkalmazásokon futó alkalmazások vész-helyreállítását a magas rendelkezésre állás érdekében [közvetlen tárolóhelyek](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) használatával állíthatja be.  A Közvetlen tárolóhelyek (S2D) és a Site Recovery együttes használata átfogó védelmet nyújt az Azure-beli virtuális gépek számítási feladataihoz. A S2D lehetővé teszi a vendég fürtök üzemeltetését az Azure-ban. Ez különösen akkor hasznos, ha egy virtuális gép kritikus alkalmazást (például SAP ASCS Layer, SQL Server vagy kibővített fájlkiszolgáló) üzemeltet.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló – vész-helyreállítás
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Linux rendszerű BRTFS fájlrendszer** | A Site Recovery mostantól támogatja a VMware virtuális gépek replikálását a BRTFS fájlrendszerrel. A replikáció nem támogatott, ha:<br/><br/>– A replikálás engedélyezése után a rendszer a BTRFS fájlrendszer alkötetét módosítja.<br/><br/>– A fájlrendszer több lemezre oszlik.<br/><br/>– A BTRFS fájlrendszer támogatja a RAID-t.
**Windows Server 2019** | A Windows Server 2019 rendszerű gépekhez hozzáadott támogatás.


## <a name="updates-january-2019"></a>Frissítések (január 2019)


### <a name="accelerated-networking-azure-vms"></a>Gyorsított hálózatkezelés (Azure-beli virtuális gépek)

A gyorsított hálózatkezelés lehetővé teszi az egyszintű I/O-virtualizálás (SR-IOV) használatát egy virtuális gépre, és javítja a hálózat teljesítményét. Ha engedélyezi egy Azure-beli virtuális gép replikálását, Site Recovery észleli, hogy engedélyezve van-e a gyorsított hálózat. Ha igen, a feladatátvételt követően Site Recovery automatikusan a gyorsított hálózatkezelést konfigurálja a célként megadott replika Azure virtuális gépen, [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) és [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)rendszereken egyaránt.

[További információk](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>32-es kumulatív frissítés 

A 32-es [kumulatív frissítés](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatást kapott a RedHat munkaállomás 6/7-es verziójához, valamint az Ubuntu, a Debian és a SUSE új kernel-verzióihoz.
**Közvetlen tárolóhelyek** | A Site Recovery Közvetlen tárolóhelyek (S2D) használatával támogatja az Azure-beli virtuális gépeket.

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>A VMware virtuális gépek/fizikai kiszolgálók katasztrófa utáni helyreállítása

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.
 
**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás lett hozzáadva a RedHat Enterprise Linux 7,6, a RedHat munkaállomás 6/7, Oracle Linux 6.10/7.6, valamint az Ubuntu, a Debian és a SUSE új kernel-verzióihoz.


### <a name="update-rollup-31"></a>31. kumulatív frissítés 

A [31. kumulatív frissítés](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="vmware-vmsphysical-servers-replication"></a>VMware virtuális gépek/fizikai kiszolgálók replikációja 
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.
**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás lett hozzáadva Oracle Linux 6,8 és 6,9/7.0 rendszerhez, valamint a UEK5 kernelhez.
**LVM** | Az LVM és a LVM2 kötetek támogatása.<br/><br/> A lemezpartíció és az LVM kötetek/boot könyvtára mostantól támogatott.
**Címtárak** | Ezeket a címtárakat külön partícióként, illetve nem ugyanazon rendszerlemezen található fájlrendszereknél adták hozzá a rendszer:<br/><br/> /(root),/boot,/usr,/usr/local,/var,/etc.
**Windows Server 2008** | A dinamikus lemezekhez hozzáadott támogatás.
**Feladatátvétel** | Továbbfejlesztett feladatátvételi idő a VMware virtuális gépeknél, ahol a storvsc és a vsbus nem rendszerindítási illesztőprogramok.
**UEFI-támogatás** | Az Azure virtuális gépek nem támogatják az UEFI rendszerindítási típust. Most már áttelepítheti a helyszíni fizikai kiszolgálókat az UEFI-ből az Azure-ba Site Recovery használatával. A Site Recovery áttelepíti a kiszolgálót úgy, hogy az áttelepítés előtt átalakítja a rendszerindítási típust a BIOS-ba. Site Recovery korábban csak virtuális gépek esetében támogatta ezt a konverziót. A támogatás a Windows Server 2012-es vagy újabb verzióját futtató fizikai kiszolgálókon érhető el.

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | A Oracle Linux 6,8 és 6.9/7.0 esetében támogatott. és a UEK5 kernelhez.
**Linux rendszerű BRTFS fájlrendszer** | Azure-beli virtuális gépek esetén támogatott.
**Azure-beli virtuális gépek a rendelkezésre állási zónákban** | Engedélyezheti a replikálást egy másik régióba a rendelkezésre állási zónákban üzembe helyezett Azure-beli virtuális gépek esetében. Mostantól engedélyezheti az Azure-beli virtuális gépek replikálását, és beállíthatja a feladatátvétel célját egyetlen virtuálisgép-példányra, egy rendelkezésre állási csoportba tartozó virtuális gépre vagy egy rendelkezésre állási zónában lévő virtuális gépre. A beállítás nem érinti a replikálást. [Olvassa el](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) a bejelentést.
**Tűzfallal kompatibilis tároló (portál/PowerShell)** | Támogatott a [tűzfalat használó Storage-fiókok](https://docs.microsoft.com/azure/storage/common/storage-network-security)támogatása.<br/><br/> Az Azure-beli virtuális gépeket nem felügyelt lemezekkel replikálhatja a tűzfalat támogató Storage-fiókokba egy másik Azure-régióba a vész-helyreállítás érdekében.<br/><br/> A nem felügyelt lemezekhez a tűzfalat támogató Storage-fiókokat célként tároló fiókokként használhatja.<br/><br/> A Portálon és a PowerShell használatával támogatott.

## <a name="updates-december-2018"></a>Frissítések (2018. december)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>A mobilitási szolgáltatás (Azure-beli virtuális gépek) automatikus frissítései

Site Recovery hozzáadott egy lehetőséget a mobilitási szolgáltatás bővítményének automatikus frissítéseihez. A mobilitási szolgáltatás bővítmény a Site Recovery által replikált összes Azure-beli virtuális gépre telepítve van. A replikáció engedélyezésekor kiválaszthatja, hogy engedélyezi-e Site Recovery számára a bővítmény frissítéseinek kezelését.

A frissítések nem igénylik a virtuális gépek újraindítását, és nem érintik a replikálást. [További információk](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Díjszabás az Azure-beli virtuális gép vész-helyreállításához

Az Azure-beli virtuális gépek vész-helyreállítási költségei a virtuálisgép-licencelési költségek, valamint a hálózati és tárolási költségek. Az Azure egy [díjszabási számológépet](https://aka.ms/a2a-cost-estimator) biztosít, amely segít kideríteni ezeket a költségeket. A Site Recovery mostantól [](https://aka.ms/a2a-cost-estimator) egy példaként értékeli a becslést, amely egy három rétegből álló, 12 standard HDD lemezzel és 6 prémium SSD lemezzel rendelkező, háromrészes alkalmazáson alapuló minta-telepítés árát tartalmazza.

- A minta azt feltételezi, hogy a standard és 20 GB-os adatváltozások napi 10 GB-ban változnak.
- Az adott központi telepítés esetében módosíthatja a változókat a költségek becslése érdekében.
- Megadhatja a virtuális gépek számát, a felügyelt lemezek számát és típusát, valamint a virtuális gépeken várható teljes adatváltozási arányt.
- Emellett a sávszélességgel kapcsolatos költségek becsléséhez is alkalmazhat tömörítési tényezőt.

[Olvassa el](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) a bejelentést.


## <a name="updates-october-2018"></a>Frissítések (október 2018)

### <a name="update-rollup-30"></a>30. kumulatív frissítés 

A [30. kumulatív frissítés](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Régió támogatása** | Site Recovery támogatás hozzáadva az Australia Central 1 és az Australia Central 2 rendszerhez.
**A lemez titkosításának támogatása** | Az Azure AD-alkalmazással titkosított Azure-beli virtuális gépek vész-helyreállításának támogatása Azure Disk Encryption (ADE) titkosítással. [További információk](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Lemez kizárása** | A nem inicializált lemezek mostantól automatikusan ki vannak zárva az Azure-beli virtuális gépek replikálásakor.
**Tűzfallal kompatibilis tároló (PowerShell)** | Támogatott a [tűzfalat használó Storage-fiókok](https://docs.microsoft.com/azure/storage/common/storage-network-security)támogatása.<br/><br/> Az Azure-beli virtuális gépeket nem felügyelt lemezekkel replikálhatja a tűzfalat támogató Storage-fiókokba egy másik Azure-régióba a vész-helyreállítás érdekében.<br/><br/> A nem felügyelt lemezekhez a tűzfalat támogató Storage-fiókokat célként tároló fiókokként használhatja.<br/><br/> Csak a PowerShell használatával támogatott.


### <a name="update-rollup-29"></a>29. kumulatív frissítés 

A [29. kumulatív frissítés](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).


## <a name="updates-august-2018"></a>Frissítések (2018. augusztus)

### <a name="update-rollup-28"></a>28. kumulatív frissítés 

A [28. kumulatív frissítés](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási 
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatott a RedHat Enterprise Linux 6,10; CentOS 6,10.<br/><br/>
**Felhő-támogatás** | Támogatott vész-helyreállítás az Azure-beli virtuális gépek számára a németországi felhőben.
**Az előfizetések közötti vész-helyreállítás** | Az Azure-beli virtuális gépek egyik régióban való replikálásának támogatása egy másik előfizetésben, ugyanazon a Azure Active Directory bérlőn belül. [További információk](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware virtuális gép/fizikai kiszolgáló vész-helyreállítás 
Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás hozzáadva a RedHat Enterprise Linux 6,10, CentOS 6,10.<br/><br/> A Linux-alapú virtuális gépek, amelyek a GUID partíciós tábla (GPT) partíciós stílust használják a régi BIOS-kompatibilitási módban, mostantól támogatottak. További információért tekintse át az Azure-beli virtuális gépekkel kapcsolatos [gyakori kérdéseket](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) . 
**Vész-helyreállítási virtuális gépek a Migrálás után** | Az Azure-ba migrált helyszíni VMware virtuális gépek másodlagos régióba való vész-helyreállításának támogatása anélkül, hogy a replikáció engedélyezése előtt el kellene távolítani a mobilitási szolgáltatást a virtuális gépen.
**Windows Server 2008** | A Windows Server 2008 R2/2008 64-bit és 32-bit rendszert futtató gépek áttelepítésének támogatása.<br/><br/> Csak áttelepítés (replikálás és feladatátvétel). A feladat-visszavétel nem támogatott.

## <a name="updates-july-2018"></a>Frissítések (2018. július)

### <a name="update-rollup-27-july-2018"></a>27. kumulatív frissítés (2018. július)

A [27. kumulatív frissítés](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Site Recovery ügynökök és szolgáltatók frissítése (a kumulatív részletezés szerint).
**Hibajavítások/javítások** | Számos javítás és javítás (az összesítésben részletezve).

### <a name="azure-vm-disaster-recovery"></a>Azure-beli virtuális gép vész-helyreállítási 

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | A támogatás a Red Hat Enterprise Linux 7,5-hez lett hozzáadva.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware virtuális gép/fizikai kiszolgáló vész-helyreállítás 

Az ebben a hónapban hozzáadott funkciók összegzése a táblázatban látható.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | A támogatás a Red Hat Enterprise Linux 7,5, SUSE Linux Enterprise Server 12 rendszerhez lett hozzáadva.



## <a name="next-steps"></a>További lépések

Naprakészen tarthatja frissítéseit az [Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) oldalon.
