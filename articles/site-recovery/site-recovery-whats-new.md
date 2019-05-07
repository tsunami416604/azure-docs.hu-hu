---
title: What's new in Azure Site Recovery |} A Microsoft Docs
description: Az Azure Site Recovery-ben bevezetett új funkciók összegzést tartalmaz
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: raynew
ms.openlocfilehash: e2145fbbb5fa09aa3321742ca8a786822f6f0641
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148650"
---
# <a name="whats-new-in-site-recovery"></a>A Site Recovery újdonságai

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás frissített és továbbfejlesztett folyamatosan. Könnyebben naprakész, ez a cikk a legújabb kiadásaihoz, az új funkciók és az új tartalmakat talál információt. Ez az oldal rendszeresen frissül.

Ha javaslata van a Site Recovery szolgáltatásokat, szívesen [kíváncsiak a véleményére](https://feedback.azure.com/forums/256299-site-recovery).


## <a name="updates-march-2019"></a>Frissítések (2019. március)

### <a name="update-rollup-35"></a>35. kumulatív frissítés

[35. kumulatív frissítés](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Egy frissítést a Site Recovery-ügynökök és a szolgáltatók (leírt módon a kumulatív frissítést)
**A probléma javításokat/fejlesztései** | Számos javításai és továbbfejlesztései (leírt módon a kumulatív frissítést)

#### <a name="vmwarephysical-server-disaster-recovery"></a>VMware-ről/fizikai kiszolgáló vészhelyreállítása
Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**A felügyelt lemezek** | A helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálását mostantól közvetlenül a managed Disks szolgáltatásba, az Azure-ban. A helyszíni adatokat küld a gyorsítótárfiókot az Azure-ban, és helyreállítási pontokat hoz létre, a felügyelt lemezek a célhelyen. Ez biztosítja, hogy nem kell több cél tárfiókok kezelését.
**Konfigurációs kiszolgáló** | A Site Recovery mostantól támogatja a több hálózati adapterrel rendelkező a konfigurációs kiszolgálót. Hozzá kell adnia további adapterek és a konfigurációs kiszolgáló virtuális gép előtt a konfigurációs kiszolgálót regisztrálja a tárolóban. Ha ezt követően adja hozzá azt, újra kell regisztrálnia a kiszolgáló a tárolóban.


## <a name="updates-february-2019"></a>Frissítések (2019. február)

### <a name="update-rollup-34"></a>34. kumulatív frissítés 

[34. kumulatív frissítés](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery-ügynökök és a szolgáltatók (leírt módon az összegző) frissítése.
**A probléma javításokat/fejlesztései** | Javításai és továbbfejlesztései (leírt módon az összegző) száma.


### <a name="update-rollup-33"></a>33. kumulatív frissítés 

[33. kumulatív frissítés](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery-ügynökök és a szolgáltatók (leírt módon az összegző) frissítése.
**A probléma javításokat/fejlesztései** | Javításai és továbbfejlesztései (leírt módon az összegző) száma.


#### <a name="azure-vm-disaster-recovery"></a>Az Azure virtuális gépek vészhelyreállítása 
Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Hálózatleképezés** | Az Azure-beli virtuális gépek vészhelyreállításához mostantól használhatja bármely elérhető célhálózat Ha engedélyezi a replikációt. 
**Standard SSD** | Most már beállíthat vész-helyreállítása Azure virtuális gépek használatával [Standard SSD-lemez](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Közvetlen tárolóhelyek** | Beállíthatja az Azure-beli Virtuálisgép-alkalmazások futó alkalmazások vészhelyreállítása [a közvetlen tárolóhelyek](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) magas rendelkezésre állás érdekében.  Az Azure virtuális gépek számítási feladataihoz átfogó védelmet és a Site Recovery használatával a közvetlen tárolóhelyek (S2D) biztosít. Az S2D teszi lehetővé a vendégfürt az Azure-ban. Ez akkor különösen hasznos, ha egy virtuális gép, amelyen egy kritikus fontosságú alkalmazások, például az SAP ASCS réteget, az SQL Server vagy kibővített fájlkiszolgáló.


#### <a name="vmwarephysical-server-disaster-recovery"></a>VMware-ről/fizikai kiszolgáló vészhelyreállítása
Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Linux BRTFS fájlrendszer** | A Site Recovery mostantól támogatja a VMware virtuális gépek replikálását az BRTFS fájlrendszerrel. Replikáció nem támogatott, ha:<br/><br/>– A BTRFS alárendelt fájlrendszerkötet replikáció engedélyezése után megváltozott.<br/><br/>– A rendszer több lemez oszlik.<br/><br/>-A BTRFS fájlrendszer RAID támogatja.
**Windows Server 2019** | A Windows Server 2019 futtató gépek támogatása.


## <a name="updates-january-2019"></a>Frissítések (január 2019)

### <a name="accelerated-networking-azure-vms"></a>Gyorsított hálózatkezelés (Azure virtuális gépek)

Gyorsított hálózatkezelés engedélyezéséhez egygyökerű i/o-virtualizálás (SR-IOV) virtuális gépekhez, hálózati teljesítmény javításához. Amikor engedélyezi egy Azure virtuális gép replikációját, a Site Recovery észleli, hogy engedélyezve van-e a gyorsított hálózatkezelés. Ha van, miután a feladatátvétel a Site Recovery automatikusan konfigurálja a gyorsított hálózatkezelés a cél replika Azure virtuális Gépen, mindkét [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) és [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[További információk](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>32-es kumulatív frissítés 

[32. kumulatív frissítés](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery-ügynökök és a szolgáltatók (leírt módon az összegző) frissítése.
**A probléma javításokat/fejlesztései** | Javításai és továbbfejlesztései (leírt módon az összegző) száma.

#### <a name="azure-vm-disaster-recovery"></a>Az Azure virtuális gépek vészhelyreállítása

Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | Ubuntu, a Debian és a SUSE vonatkozó támogatási RedHat munkaállomás 6/7 és a kernel-verzióknál lett hozzáadva.
**Közvetlen tárolóhelyek** | A Site Recovery támogatja az Azure virtuális gépeken a közvetlen tárolóhelyek (S2D) segítségével.

#### <a name="vmware-vmsphysical-servers-replication"></a>VMware virtuális gépek/fizikai kiszolgálók replikálása 
**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | Ubuntu, a Debian és a SUSE vonatkozó támogatási Redhat Enterprise Linux 7.6, a RedHat munkaállomás 6/7, Oracle Linux 6.10/7.6 és új kernel-verzióknál lett hozzáadva.


### <a name="update-rollup-31"></a>A kumulatív frissítések 31. 

[31. kumulatív frissítés](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery-ügynökök és a szolgáltatók (leírt módon az összegző) frissítése.
**A probléma javításokat/fejlesztései** | Javításai és továbbfejlesztései (leírt módon az összegző) száma.

#### <a name="vmware-vmsphysical-servers-replication"></a>VMware virtuális gépek/fizikai kiszolgálók replikálása 
Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás hozzáadva az Oracle Linux 6.8 és 6.9/7.0-ban, illetve a UEK5 kernel.
**LVM** | Az LVM és LVM2 kötetek támogatása.<br/><br/> Mostantól támogatott a gyökérpartíció könyvtár lemezpartíción és LVM köteteken.
**Címtárak** | Támogatási ezekhez a könyvtárakhoz, állítsa be a különálló partíció vagy fájlrendszereket, amelyek ugyanazon a lemezen rendszer nem lett hozzáadva:<br/><br/> / (gyökér), gyökérpartíció, usr, /usr/local, /var, a/etc.
**Windows Server 2008** | A dinamikus lemezek támogatása.
**Feladatátvétel** | Továbbfejlesztett feladatátvételi ideje a VMware virtuális gépekhez, storvsc és vsbus nem rendszerindító illesztőprogramokkal.
**UEFI-támogatás** | Az Azure virtuális gépek nem támogatják az UEFI típusú rendszerindítást. Az Azure Site recoveryvel most már áttelepítheti a helyszíni fizikai kiszolgálóknak az UEFI Felülettel rendelkező. A Site Recovery a kiszolgáló áttelepíti a rendszerindítási típus áttérve BIOS áttelepítés előtt. A Site Recovery korábban ez a konverzió a virtuális gépek csak támogatott. A rendszer a Windows Server 2012 rendszert futtató fizikai kiszolgálók számára érhető el vagy újabb verzióját támogatja.

#### <a name="azure-vm-disaster-recovery"></a>Az Azure virtuális gépek vészhelyreállítása
Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatott Oracle Linux 6.8 és 6.9/7.0; lett hozzáadva és a UEK5 kernel számára.
**Linux BRTFS fájlrendszer** | Az Azure virtuális gépek esetében támogatott.
**Azure virtuális gépek rendelkezésre állási zónában** | Az Azure virtuális gépek rendelkezésre állási zónában üzembe helyezett egy másik régióba replikációs engedélyezheti. Most engedélyezze a replikációt egy Azure virtuális gép, és állítsa be a feladatátvételhez célként egyetlen Virtuálisgép-példány, egy virtuális Gépet egy rendelkezésre állási csoportban vagy egy virtuális Gépet egy rendelkezésre állási zónában. A beállítás nincs hatással a replikáció. [Olvasási](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) hírt adtunk.
**Tűzfal tároló (portal/PowerShell)** | Támogatása az [tűzfal tárfiókok](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> A tárfiókokhoz tűzfal engedélyezve van egy másik Azure-régióba vész-helyreállítási nem felügyelt lemezekkel rendelkező Azure virtuális gépeket replikálhatja.<br/><br/> Tűzfal storage-fiókok nem felügyelt lemezek esetén használhatja cél tárfiókot.<br/><br/> A támogatott portál és PowerShell használatával.

## <a name="updates-december-2018"></a>Frissítések (2018. December)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>A mobilitási szolgáltatást (Azure virtuális gépek) számára az automatikus frissítések

A Site Recovery a mobilitási szolgáltatás bővítmény automatikus frissítési beállítást hozzá. A mobilitási szolgáltatás bővítmény telepítve van az egyes Azure virtuális Gépeken, a Site Recovery által replikált. Ha engedélyezi a replikációt, akkor megadhatja, hogy lehetővé teszik a bővítmény frissítéseit kezelheti a Site Recovery.

Frissítések a virtuális gép újraindítása nem szükséges, és nem befolyásolják a replikáció. [További információk](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Az Azure-beli virtuális gépek vészhelyreállításához díjkalkulátor

Katasztrófa utáni helyreállítás az Azure virtuális gépek tekintetében a virtuális gép licencelési költségeit, és a hálózati és tárolási költségeit. Az Azure biztosít egy [díjkalkulátor](https://aka.ms/a2a-cost-estimator) segítségével számolhatja ki ezeket a díjakat. A Site Recovery most már biztosít egy [becslés díjszabási példa](https://aka.ms/a2a-cost-estimator) , amely egy üzembe helyezési minta alapján egy háromszintű alkalmazás hat virtuális gépek használata az 12 Standard HDD és prémium szintű SSD 6 lemezek díjai.

- A minta azt feltételezi, hogy a nap a standard szintű, és 20 GB prémium szint 10 GB-os adatmódosítást.
- Az adott üzembe helyezés költségeinek kiszámításához a változókat is módosíthatja.
- Megadhatja, hogy a virtuális gépek száma és típusa, a felügyelt lemezek számát, és a várható teljes adatváltozási sebessége a virtuális gép között várt.
- Ezenkívül a sávszélességgel kapcsolatos költségek becslése érdekében tömörítési tényezőt is alkalmazhat.

[Olvasási](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) hírt adtunk.


## <a name="updates-october-2018"></a>Frissítések (2018. október)

### <a name="update-rollup-30"></a>30. kumulatív frissítés 

[30. kumulatív frissítés](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery-ügynökök és a szolgáltatók (leírt módon az összegző) frissítése.
**A probléma javításokat/fejlesztései** | Javításai és továbbfejlesztései (leírt módon az összegző) száma.

#### <a name="azure-vm-disaster-recovery"></a>Az Azure virtuális gépek vészhelyreállítása
Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Régió támogatása** | Site Recovery támogatása az Ausztrália 1. középső régiója, Ausztrália 2. középső régiója és.
**Lemeztitkosítás támogatása** | Az Azure AD-alkalmazás az Azure Disk Encryption (ADE) titkosított Azure virtuális gépek vészhelyreállítása támogatása. [További információk](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Lemez kizárása** | Nem inicializált lemezek most már automatikusan kizárt Azure-beli Virtuálisgép-replikáció során.
**Tűzfal tároló (PowerShell)** | Támogatása az [tűzfal tárfiókok](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> A tárfiókokhoz tűzfal engedélyezve van egy másik Azure-régióba vész-helyreállítási nem felügyelt lemezekkel rendelkező Azure virtuális gépeket replikálhatja.<br/><br/> Tűzfal storage-fiókok nem felügyelt lemezek esetén használhatja cél tárfiókot.<br/><br/> Támogatott, csak a PowerShell használatával.


### <a name="update-rollup-29"></a>29-es kumulatív frissítés 

[29. kumulatív frissítés](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery-ügynökök és a szolgáltatók (leírt módon az összegző) frissítése.
**A probléma javításokat/fejlesztései** | Javításai és továbbfejlesztései (leírt módon az összegző) száma.


## <a name="updates-august-2018"></a>Frissítések (2018 augusztus)

### <a name="update-rollup-28"></a>28. kumulatív frissítés 

[28. kumulatív frissítés](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery-ügynökök és a szolgáltatók (leírt módon az összegző) frissítése.
**A probléma javításokat/fejlesztései** | Javításai és továbbfejlesztései (leírt módon az összegző) száma.

#### <a name="azure-vms-disaster-recovery"></a>Az Azure virtuális gépek vészhelyreállítása 
Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | RedHat Enterprise Linux 6.10; támogatott hozzáadva CentOS 6.10.<br/><br/>
**Cloud support támogatás** | Vész-helyreállítási támogatott Azure-beli virtuális gépek a Németországi felhőben.
**Az előfizetések közötti vész-helyreállítási** | Támogatás az Azure-beli virtuális gépek egy adott régióban egy másik előfizetésben, azonos Azure Active Directory-bérlőben található másik régióban való replikálásához. [További információk](https://aka.ms/cross-sub-blog).

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>A VMware virtuális gépek/fizikai kiszolgáló vészhelyreállítása 
Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | A RedHat Enterprise Linux 6.10, CentOS 6.10 támogatása.<br/><br/> Linux-alapú virtuális gépek használatát a GUID partíciós tábla (GPT) partícióval, ha az örökölt BIOS-kompatibilitási mód mostantól támogatottak. Tekintse át a [Azure virtuális gép – gyakori kérdések](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) további információt. 
**Az áttelepítés után virtuális gépek vészhelyreállítása** | Támogatja a vészhelyreállítást egy másodlagos régióba engedélyezése egy helyszíni VMware virtuális gép migrálása az Azure-bA anélkül, hogy távolítsa el a mobilitási szolgáltatás a virtuális gépen a replikáció engedélyezése előtt.
**Windows Server 2008** | Migrálás támogatása futó Windows Server 2008 R2 vagy 2008 64 bites és 32-bit-es gépek.<br/><br/> Áttelepítés csak (replikáció és feladatátvétel). Feladat-visszavétel nem támogatott.

## <a name="updates-july-2018"></a>Frissítések (2018 július)

### <a name="update-rollup-27-july-2018"></a>Kumulatív frissítés (2018 július) 27

[27. kumulatív frissítés](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery-ügynökök és a szolgáltatók (leírt módon az összegző) frissítése.
**A probléma javításokat/fejlesztései** | Javításai és továbbfejlesztései (leírt módon az összegző) száma.

#### <a name="azure-vms-disaster-recovery"></a>Az Azure virtuális gépek vészhelyreállítása 

Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | A Red Hat Enterprise Linux 7.5 támogatása.

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>A VMware virtuális gépek/fizikai kiszolgáló vészhelyreállítása 

Új lehetőségek a frissítést.

**Funkció** | **Részletek**
--- | ---
**Linux-támogatás** | A Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12 támogatása.



## <a name="next-steps"></a>További lépések

Naprakészen tartása a frissítések a [Azure frissítések](https://azure.microsoft.com/updates/?product=site-recovery) lapot.
