---
title: What's new in Azure Site Recovery |} A Microsoft Docs
description: Az Azure Site Recovery-ben bevezetett új funkciók összegzést tartalmaz
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: fece1adc9ad2f5844a7d6cc1e0e9cc92d44de6d8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892931"
---
# <a name="whats-new-in-site-recovery"></a>A Site Recovery újdonságai

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás frissített és továbbfejlesztett folyamatosan. Könnyebben naprakész, ez a cikk a legújabb kiadásaihoz, az új funkciók és az új tartalmakat talál információt. Ez az oldal rendszeresen frissül.

Ha javaslata van a Site Recovery szolgáltatásokat, szívesen [kíváncsiak a véleményére](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>Q1 2019 

### <a name="update-rollup-34-february-2019"></a>A kumulatív frissítések 34 (2019. február)

[34. kumulatív frissítés](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Egy frissítést a Site Recovery-ügynökök és a szolgáltatók (leírt módon a kumulatív frissítést)
**A probléma javítások** | Számos javításai és továbbfejlesztései (leírt módon a kumulatív frissítést)



### <a name="update-rollup-33-february-2019"></a>A kumulatív frissítések 33 (2019. február)

[33. kumulatív frissítés](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Egy frissítést a Site Recovery-ügynökök és a szolgáltatók (leírt módon a kumulatív frissítést)
**A probléma javítások** | Számos javításai és továbbfejlesztései (leírt módon a kumulatív frissítést)
**Hálózatleképezés** | Az Azure-beli virtuális gépek vészhelyreállításához mostantól használhatja bármely elérhető célhálózat Ha engedélyezi a replikációt. 
**Standard SSD** | Most már beállíthat vész-helyreállítása Azure virtuális gépek használatával [Standard SSD-lemez](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Közvetlen tárolóhelyek** | Beállíthatja az Azure-beli Virtuálisgép-alkalmazások futó alkalmazások vészhelyreállítása [a közvetlen tárolóhelyek](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) magas rendelkezésre állás érdekében.
**BRTFS fájlrendszer** | VMware virtuális gépek mellett az Azure virtuális gépek támogatják.<br/><br/> Nem támogatott, ha: Replikáció engedélyezése után a BTRFS alárendelt fájlrendszerkötet módosul, a fájlrendszer több lemez oszlik, vagy ha a BTRFS fájlrendszer támogatja a RAID.



### <a name="update-rollup-32-january-2019"></a>Kumulatív frissítés (január 2019) 32

[31. kumulatív frissítés](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Egy frissítést a Site Recovery-ügynökök és a szolgáltatók (leírt módon a kumulatív frissítést)
**A probléma javítások** | Számos javításai és továbbfejlesztései (leírt módon a kumulatív frissítést)
**Vész-helyreállítási linuxhoz** | **Az Azure virtuális gépek**: RedHat munkaállomás 6/7. Ubuntu, a Debian és a SUSE új kernel-verzióknál támogatása.<br/><br/> **VMware virtuális gépek/fizikai kiszolgálók**: Redhat Enterprise Linux 7.6; RedHat munkaállomás 6/7. Oracle Linux 6.10/7.6; az új kernel-verzióknál, az Ubuntu, a Debian és a SUSE sémaszerkezeteket.


### <a name="update-rollup-31-january-2019"></a>A kumulatív frissítések 31 (január 2019)

[31. kumulatív frissítés](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Egy frissítést a Site Recovery-ügynökök és a szolgáltatók (leírt módon a kumulatív frissítést)
**A probléma javítások** | Számos javításai és továbbfejlesztései (leírt módon a kumulatív frissítést)
**Vész-helyreállítási linuxhoz** | **Az Azure virtuális gépek**: Oracle Linux 6.8 és 6.9/7.0; UEK5 kernelekkel támogatása.<br/><br/> **VMware virtuális gépek/fizikai kiszolgálók**: Oracle Linux 6.8 és 6.9/7.0; UEK5 kernel támogatása.
**BRTFS fájlrendszer** | Az Azure virtuális gépek esetében támogatott.
**LVM** | Az LVM és LVM2 kötetek támogatása.<br/><br/> A gyökérpartíció könyvtár lemezpartíción, és a LVM kötetek használata támogatott.
**Címtárak** | Támogatása az alábbi könyvtárak seet külön partíciókon tárolják vagy fájlrendszereket, amelyek nem a rendszer ugyanazon a lemezen: / (gyökér), gyökérpartíció, usr, /usr/local, /var, a/etc.
**Windows Server 2008** | A dinamikus lemezek támogatása.
**VMware virtuális gép feladatátvételi** | Továbbfejlesztett feladatátvételi ideje a VMware virtuális gépekhez, storvsc és vsbus nem rendszerindító illesztőprogramokkal.
**UEFI-támogatás** | Az Azure virtuális gépek nem támogatják az UEFI típusú rendszerindítást. Az Azure Site recoveryvel most már áttelepítheti a helyszíni fizikai kiszolgálóknak az UEFI Felülettel rendelkező. A Site Recovery a kiszolgáló áttelepíti a rendszerindítási típus áttérve BIOS áttelepítés előtt. A Site Recovery korábban ez a konverzió a virtuális gépek csak támogatott. A rendszer a Windows Server 2012 rendszert futtató fizikai kiszolgálók számára érhető el vagy újabb verzióját támogatja.
**Azure virtuális gépek rendelkezésre állási zónában** | Az Azure virtuális gépek rendelkezésre állási zónában üzembe helyezett egy másik régióba replikációs engedélyezheti. szervezeti egység Ezután engedélyezze a replikációt egy Azure virtuális gép, és állítsa be a feladatátvételhez célként egyetlen Virtuálisgép-példány, egy virtuális Gépet egy rendelkezésre állási csoportban vagy egy virtuális Gépet egy rendelkezésre állási zónában. A beállítás nincs hatással a replikáció. [Olvasási](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) hírt adtunk.


## <a name="q4-2018"></a>Q4 2018

### <a name="update-rollup-30-october-2018"></a>Kumulatív frissítés (2018. október) 30

[30. kumulatív frissítés](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Egy frissítést a Site Recovery-ügynökök és a szolgáltatók (leírt módon a kumulatív frissítést)
**A probléma javítások** | Számos javításai és továbbfejlesztései (leírt módon a kumulatív frissítést)
**Régió támogatása** | Site Recovery támogatása az Ausztrália 1. középső régiója, Ausztrália 2. középső régiója és.
**Lemeztitkosítás támogatása** | Az Azure AD-alkalmazás az Azure Disk Encryption (ADE) titkosított Azure virtuális gépek vészhelyreállítása támogatása. [További információk](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Lemez kizárása** | Unitialized lemezek most már automatikusan kizárt Azure-beli Virtuálisgép-replikáció során.
**Tűzfal-t** | Támogatása az [tűzfal tárfiókok](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> A tárfiókokhoz tűzfal engedélyezve van egy másik Azure-régióba vész-helyreállítási nem felügyelt lemezekkel rendelkező Azure virtuális gépeket replikálhatja.<br/><br/> Tűzfal storage-fiókok nem felügyelt lemezek esetén használhatja cél tárfiókot.<br/><br/> Támogatott, csak a PowerShell használatával.


### <a name="update-rollup-29-october-2018"></a>Kumulatív frissítés (2018. október) 29

[29. kumulatív frissítés](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Egy frissítést a Site Recovery-ügynökök és a szolgáltatók (leírt módon a kumulatív frissítést)
**A probléma javítások** | Számos javításai és továbbfejlesztései (leírt módon a kumulatív frissítést)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>A mobilitási szolgáltatás bővítmény automatikus frissítések

A Site Recovery a mobilitási szolgáltatás bővítmény automatikus frissítési beállítást hozzá. A mobilitási szolgáltatás bővítmény telepítve van az egyes Azure virtuális Gépeken, a Site Recovery által replikált. Ha engedélyezi a replikációt, akkor megadhatja, hogy lehetővé teszik a bővítmény frissítéseit kezelheti a Site Recovery. Frissítések a virtuális gép újraindítása nem szükséges, és nem befolyásolják a replikáció. [További információk](azure-to-azure-autoupdate.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Vész-helyreállítási használó virtuális gépek gyorsított hálózatkezelést

Gyorsított hálózatkezelés engedélyezéséhez egygyökerű i/o-virtualizálás (SR-IOV) virtuális gépekhez, hálózati teljesítmény javításához. Amikor engedélyezi egy Azure virtuális gép replikációját, a Site Recovery észleli, hogy engedélyezve van-e a gyorsított hálózatkezelés. Ha van, miután a feladatátvétel a Site Recovery automatikusan konfigurálja a gyorsított hálózatkezelés a cél replika Azure virtuális Gépen, mindkét [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) és [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [További információk](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Az Azure-beli virtuális gépek vészhelyreállításához díjkalkulátor

Vészhelyreállítás az Azure virtuális gépek tekintetében a virtuális gép licencelési költségeit, és a hálózati és tárolási költségeit. Az Azure biztosít egy [díjkalkulátor](https://aka.ms/a2a-cost-estimator) segítségével számolhatja ki ezeket a díjakat. A Site Recovery most már biztosít egy [becslés díjszabási példa](https://aka.ms/a2a-cost-estimator) , amely egy üzembe helyezési minta alapján egy háromszintű alkalmazás hat virtuális gépek használata az 12 Standard HDD és prémium szintű SSD 6 lemezek díjai. A minta egy nap a standard szintű, és 20 GB prémium szint 10 GB-os adatmódosítást feltételezik. Az adott üzembe helyezés költségeinek kiszámításához a változókat is módosíthatja. Megadhatja, hogy a virtuális gépek száma és típusa, a felügyelt lemezek számát, és a várható teljes adatváltozási sebessége a virtuális gép között várt. Ezenkívül a sávszélességgel kapcsolatos költségek becslése érdekében tömörítési tényezőt is alkalmazhat. [Olvasási](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) hírt adtunk.



## <a name="q3-2018"></a>Q3 2018 


### <a name="update-rollup-28-august-2018"></a>A kumulatív frissítések 28 (2018 augusztus)

[28. kumulatív frissítés](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Egy frissítést a Site Recovery-ügynökök és a szolgáltatók (leírt módon a kumulatív frissítést)
**Vész-helyreállítási linuxhoz** | **Az Azure virtuális gépek**: RedHat Enterprise Linux 6.10; támogatott hozzáadva CentOS 6.10.<br/><br/> **VMware virtuális gépek**: RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> Linux-alapú virtuális gépek használatát a GUID partíciós tábla (GPT) partícióval, ha az örökölt BIOS-kompatibilitási mód mostantól támogatottak.
**Cloud support támogatás** | Vész-helyreállítási támogatott Azure-beli virtuális gépek a Németországi felhőben.
**Az előfizetések közötti vész-helyreállítási** | Támogatás az Azure-beli virtuális gépek egy adott régióban egy másik előfizetésben, azonos Azure Active Directory-bérlőben található másik régióban való replikálásához. [További információk](https://aka.ms/cross-sub-blog).
**Windows Server 2008** | Migrálás támogatása futó Windows Server 2008 R2 vagy 2008 64 bites és 32-bit-es gépek.<br/><br/> Áttelepítés csak (replikáció és feladatátvétel). Feladat-visszavétel nem támogatott.

### <a name="update-rollup-27-july-2018"></a>Kumulatív frissítés (2018 július) 27

[27. kumulatív frissítés](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) biztosít a következő frissítéseket.

**Update** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | Egy frissítést a Site Recovery-ügynökök és a szolgáltatók (leírt módon a kumulatív frissítést)
**Vész-helyreállítási linuxhoz** | **Az Azure virtuális gépek**: Red Hat Enterprise Linux 7.5<br/><br/> **VMware virtuális gépek/fizikai kiszolgálók**: Red Hat Enterprise Linux 7,5-öt, a SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>További lépések

Naprakészen tartása a frissítések a [Azure frissítések](https://azure.microsoft.com/updates/?product=site-recovery) lapot.




 









