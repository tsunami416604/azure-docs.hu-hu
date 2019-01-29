---
title: What's new in Azure Site Recovery |} A Microsoft Docs
description: Az Azure Site Recovery-ben bevezetett új funkciók összegzést tartalmaz
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: fef6dc7937b2c83e67a281c69267bdf29216793d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55199049"
---
# <a name="whats-new-in-site-recovery"></a>A Site Recovery újdonságai

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás frissített és továbbfejlesztett folyamatosan. Könnyebben naprakész, ez a cikk a legújabb kiadásaihoz, az új funkciók és az új tartalmakat talál információt. Ez az oldal rendszeresen frissül.

Ha javaslata van a Site Recovery szolgáltatásokat, szívesen [kíváncsiak a véleményére](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>Q1 2019

### <a name="linux-support"></a>Linux-támogatás

[32. kumulatív frissítés](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) frissítést biztosít a Site Recovery-ügynökök és a szolgáltatók. A frissítések ad hozzá a Linux-támogatás a következő:

- **Azure virtuális gépek vészhelyreállítása**: RedHat munkaállomás 6/7. új kernel-verzióknál az Ubuntu, a Debian és a SUSE
- **VMware virtuális gépek/fizikai kiszolgálók Azure-bA vész-helyreállítási**: RedHat Enterprise Linux 7.6; RedHat munkaállomás 6/7. Oracle Linux 6.10/7.6 új kernel-verzióknál Ubuntu, a Debian és a SUSE.



## <a name="q4-2018"></a>Q4 2018

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Az Azure-beli virtuális gépek vészhelyreállításához díjkalkulátor

Vészhelyreállítás az Azure virtuális gépek tekintetében a virtuális gép licencelési költségeit, és a hálózati és tárolási költségeit. Az Azure biztosít egy [díjkalkulátor](https://aka.ms/a2a-cost-estimator) segítségével számolhatja ki ezeket a díjakat. A Site Recovery most már biztosít egy [becslés díjszabási példa](https://aka.ms/a2a-cost-estimator) , amely egy üzembe helyezési minta alapján egy háromszintű alkalmazás hat virtuális gépek használata az 12 Standard HDD és prémium szintű SSD 6 lemezek díjai. A minta egy nap a standard szintű, és 20 GB prémium szint 10 GB-os adatmódosítást feltételezik. Az adott üzembe helyezés költségeinek kiszámításához a változókat is módosíthatja. Megadhatja, hogy a virtuális gépek száma és típusa, a felügyelt lemezek számát, és a várható teljes adatváltozási sebessége a virtuális gép között várt. Ezenkívül a sávszélességgel kapcsolatos költségek becslése érdekében tömörítési tényezőt is alkalmazhat. [Olvasási](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) hírt adtunk.

### <a name="support-for-azure-vms-in-zones"></a>Azure-beli virtuális gépek zónák támogatása

Az Azure rendelkezésre állási zónák egyedi fizikai helyszínek az Azure-régióban. Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. Most engedélyezze a replikációt egy Azure virtuális gép, és állítsa be a feladatátvételhez célként egyetlen Virtuálisgép-példány, egy virtuális Gépet egy rendelkezésre állási csoportban vagy egy virtuális Gépet egy rendelkezésre állási zónában. A beállítás nincs hatással a replikáció. [Olvasási](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) hírt adtunk.
 
### <a name="disaster-recovery-for-encrypted-vms"></a>Titkosított virtuális gépek vészhelyreállítása

A Site Recovery támogatja az Azure-beli virtuális gépek az Azure AD-alkalmazás az Azure Disk Encryption (ADE) titkosított. [További információk](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Vész-helyreállítási használó virtuális gépek gyorsított hálózatkezelést

Gyorsított hálózatkezelés engedélyezéséhez egygyökerű i/o-virtualizálás (SR-IOV) virtuális gépekhez, hálózati teljesítmény javításához. Amikor engedélyezi egy Azure virtuális gép replikációját, a Site Recovery észleli, hogy engedélyezve van-e a gyorsított hálózatkezelés. Ha van, miután a feladatátvétel a Site Recovery automatikusan konfigurálja a gyorsított hálózatkezelés a cél replika Azure virtuális Gépen, mindkét [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) és [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [További információk](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="automatic-updates-for-the-mobility-service-extension"></a>A mobilitási szolgáltatás bővítmény automatikus frissítések

A Site Recovery a mobilitási szolgáltatás bővítmény automatikus frissítési beállítást hozzá. A mobilitási szolgáltatás bővítmény telepítve van az egyes Azure virtuális Gépeken, a Site Recovery által replikált. Ha engedélyezi a replikációt, akkor megadhatja, hogy lehetővé teszik a bővítmény frissítéseit kezelheti a Site Recovery. Frissítések a virtuális gép újraindítása nem szükséges, és nem befolyásolják a replikáció. [További információk](azure-to-azure-autoupdate.md).

### <a name="support-for-standard-ssd-disks"></a>Standard SSD-lemez támogatása

Azure bevezetett [Standard tartós állapot-meghajtók (SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) lemezek egy költséghatékony tárolási megoldást nyújt alkalmazások, például webkiszolgálók, amelyeket az állandó teljesítmény érdekében szükséges, de nem rendelkezik magas lemez IOPS. Prémium szintű SSD és HDD a standard szintű lemezek elemek kombinálása azokat. A Site recoveryben vészhelyreállítás az Azure virtuális gépek Standard SSD-lemez használatával. Alapértelmezés szerint a lemez típusát a célrégióban a feladatátvételt követően megmaradnak.

### <a name="support-for-azure-storage-firewall"></a>Az Azure storage-tűzfal támogatása

Hálózatok meghatározott Azure storage-fiókok bekapcsolásával tűzfalszabályok a fiók biztonságát. A forgalom megtagadásához belső hálózatok és az internetről alapértelmezés szerint a tárfiókok konfigurálása, majd számára a hozzáférést biztosítani a forgalom a meghatározott virtuális hálózatok. A Site Recovery replikációs támogatja a virtuális gépek nem felügyelt lemezek a tárfiókokhoz firewa engedélyezve van egy másodlagos régióba. A célrégióban, nem felügyelt lemezek storage-fiókok kiválaszthatja a tűzfal engedélyezve van. Hozzáférés programot is korlátozhatja a gyorsítótárfiókba hálózati hozzáférés korlátozása csak a hálózatot, amelyben a forrás virtuális gépek találhatók. Vegye figyelembe, hogy csak [hozzáférést](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) megbízható Microsoft-szolgáltatásokhoz.

## <a name="q3-2018"></a>Q3 2018 

### <a name="linux-support"></a>Linux-támogatás

#### <a name="update-rollup-28"></a>28. kumulatív frissítés

[28. kumulatív frissítés](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) frissítést biztosít a Site Recovery-ügynökök és a szolgáltatók. A frissítések ad hozzá a Linux-támogatás a következő:

- **Azure virtuális gépek vészhelyreállítása**: RedHat Enterprise Linux 6.10; CentOS 6.10; Linux-alapú virtuális gépek használatát a GUID partíciós tábla (GPT) partícióval, ha az örökölt BIOS-kompatibilitási mód mostantól támogatottak.
- **VMware virtuális gépek/fizikai kiszolgálók Azure-bA vész-helyreállítási**: RedHat Enterprise Linux 6.10; CentOS 6.10; Linux-alapú virtuális gépek használatát a GUID partíciós tábla (GPT) partícióval, ha az örökölt BIOS-kompatibilitási mód mostantól támogatottak.

#### <a name="update-rollup-27"></a>27. kumulatív frissítés

[28. kumulatív frissítés](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) frissítést biztosít a Site Recovery-ügynökök és a szolgáltatók. A frissítések ad hozzá a Linux-támogatás a következő:

- **Azure virtuális gépek vészhelyreállítása**: Red Hat Enterprise Linux 7.5
- **VMware virtuális gépek/fizikai kiszolgálók Azure-bA vész-helyreállítási**: SUSE Linux Enterprise Server 12, Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>A Windows Server 2016 rendszert futtató Azure virtuális gépek támogatása

Windows Server 2016 rendszeren futó Azure virtuális gépek replikálhatók az Azure Site Recoveryvel Azure-régiók között.

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>A közvetlen tárolóhelyek futtató Azure virtuális gépek támogatása

[A közvetlen tárolóhelyek](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) (elérhető a Windows Server 2016 és újabb verziók esetében) meghajtók együtt csoportosítja a tárolókészletet, és majd használja a kapacitás a készlet tárolóhelyek létrehozása. A tárolóhelyek használható önálló virtuális gép, egy [vendégfürt Azure-beli virtuális](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) helyi tárhely, a fürt minden csomópontján vagy a megosztott tároló használatával a fürtön.

## <a name="next-steps"></a>További lépések

Naprakészen tartása a frissítések a [Azure frissítések](https://azure.microsoft.com/updates/?product=site-recovery) lapot.


