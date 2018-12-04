---
title: Konfigurációs kiszolgáló követelményei a VMware-vészhelyreállításhoz az Azure-bA az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk ismerteti támogatása és a követelmények az Azure-bA az Azure Site Recovery VMware-vészhelyreállításhoz használt konfigurációs kiszolgáló telepítése során
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: 506f5102d38191e20e18e395f3a59ac12d951ab7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850618"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Konfigurációs kiszolgáló követelményei a VMware-vészhelyreállításhoz az Azure-bA

Egy helyszíni konfigurációs kiszolgálót telepít, használatakor [Azure Site Recovery](site-recovery-overview.md) vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA.

- A konfigurációs kiszolgáló koordináták kommunikációhoz között a helyszíni VMware és az Azure. Azt is felügyeli az adatreplikációt.
- A konfigurációs kiszolgáló koordináták kommunikációhoz között a helyszíni VMware és az Azure. Azt is felügyeli az adatreplikációt.
- [További](vmware-azure-architecture.md) a konfigurációs kiszolgáló összetevőit és folyamatokról.

## <a name="configuration-server-deployment"></a>Konfigurációs kiszolgáló telepítése

A VMware virtuális gépek vészhelyreállítása az Azure-bA a konfigurációs kiszolgáló VMware virtuális gépként üzembe.

- A Site recoveryben egy OVA-sablon letöltése az Azure Portalról, és importálja a vCenter-kiszolgálóhoz a konfigurációs kiszolgáló virtuális gép beállításához.
- A konfigurációs kiszolgáló, az OVA-sablon használatával helyez üzembe, a virtuális gép automatikusan megfelel az ebben a cikkben felsorolt követelmények listáját.
- Javasoljuk, hogy beállította a konfigurációs kiszolgáló, az OVA-sablon használatával. Azonban ha VMware virtuális gépek katasztrófa utáni helyreállítás beállítása és az OVA sablon nem használható, a konfigurációs kiszolgáló használatával telepíthet [ezeket az utasításokat a megadott](physical-azure-set-up-source.md).
- Ha a használt konfigurációs kiszolgáló, valamint a helyszíni fizikai gépeket az Azure-bA telepíti, kövesse a [Ez a cikk](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Hardverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | 3., beleértve az operációs rendszer lemez, a folyamatkiszolgálói gyorsítótárlemez és az adatmegőrzési meghajtó a feladat-visszavételhez 
Szabad lemezterület (folyamatkiszolgálói gyorsítótár) | 600 GB
Szabad területe (adatmegőrzési lemez) | 600 GB

## <a name="software-requirements"></a>Szoftverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Operációs rendszer | Windows Server 2012 R2 <br> Windows Server 2016
Operációs rendszer területi beállítása | Angol (en-us)
Windows Server-szerepkörök | Ezek a szerepkörök nem engedélyezi: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | Ezek a szabályzatok csoport nem engedélyezi: <br> -Hozzáférés megakadályozása a parancssorba. <br> -A beállításjegyzék szerkesztőeszközeihez való hozzáférés letiltása. <br> -Megbízhatósági logika fájlmellékletekhez. <br> – Kapcsolja be a parancsfájl végrehajtása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Nincs már létező alapértelmezett webhelye <br> – Nincs már létező webhely vagy alkalmazás 443-as porton <br>-Engedélyezése [a névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás 

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

**Összetevő** | **Követelmény** 
--- | --- 
IP-cím típusa | Statikus 
Internetelérés | A kiszolgáló URL-hozzáférésre van szüksége (közvetlenül vagy proxyn keresztül): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> – https:\//management.azure.com <br> -*. services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF is el kell érnie a következő URL-címek: <br> – https:\//login.microsoftonline.com <br> – https:\//secure.aadcdn.microsoftonline-p.com <br> – https:\//login.live.com  <br> – https:\//auth.gfx.ms <br> – https:\//graph.windows.net <br> – https:\//login.windows.net <br> – https:\//www.live.com <br> – https:\//www.microsoft.com <br> – https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel) 
Hálózati adapter típusa | VMXNET3 (Ha a konfigurációs kiszolgáló VMware virtuális gép)

## <a name="required-software"></a>Szükséges szoftverek

**Összetevő** | **Követelmény** 
--- | ---
A VMware vSphere PowerCLI | [A PowerCLI 6.0-s verzió](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) kell telepíteni, ha fut a konfigurációs kiszolgáló VMware virtuális gép.
MYSQL | MySQL kell telepíteni. Manuálisan is telepítheti, vagy a Site Recovery telepíthetik azt.

## <a name="sizing-and-capacity-requirements"></a>Méretezés és a kapacitásbeli követelmények

A következő táblázat összefoglalja a konfigurációs kiszolgáló követelményeiről. Ha több VMware virtuális gépeket replikál, tájékozódjon a [kapacitástervezésének szempontjai](site-recovery-plan-capacity-vmware.md), és futtassa a [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) eszköz a VMWare replication.read 

**Összetevő** | **Követelmény** 
--- | ---
**CPU** | **Memória** | **Gyorsítótárlemez** | **A módosult adatok aránya** | **Replikált gépek**
--- | --- | --- | --- | ---
8 Vcpu<br/><br/> 2 sockets * 4 mag \@ 2,5 GHz-es | 16 GB | 300 GB | 500 GB vagy kevesebb | Les, mint 100 gépek
12 vcpu-k<br/><br/> 2 socks * 6 magok \@ 2,5 GHz-es | 18 GB | 600 GB | 500 GB – 1 TB | 100-150 gépek
16 vcpu-k<br/><br/> 2 socks * 8 magos \@ 2,5 GHz-es | 32 GB | 1 TB | 1 – 2 TB | 150 – 200 – gépek



## <a name="next-steps"></a>További lépések
A vészhelyreállítás beállítása [VMware virtuális gépek](vmware-azure-tutorial.md) az Azure-bA.
