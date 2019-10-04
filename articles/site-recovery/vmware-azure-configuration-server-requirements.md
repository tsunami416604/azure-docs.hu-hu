---
title: A konfigurációs kiszolgáló követelményei az Azure-ba történő VMware vész-helyreállításhoz Azure Site Recovery használatával | Microsoft Docs
description: Ez a cikk az Azure-ba történő VMware vész-helyreállítási konfigurációs kiszolgáló üzembe helyezésének támogatását és követelményeit ismerteti Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 86fa817128dc89eb97bee18f4f8a6de1f650c265
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814301"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Az Azure-ba történő VMware vész-helyreállítási konfigurációs kiszolgálói követelmények

Ha [Azure site Recoveryt](site-recovery-overview.md) használ a VMWare virtuális gépek és a fizikai kiszolgálók Azure-ba történő helyreállításához, a helyszíni konfigurációs kiszolgálót is üzembe kell helyeznie.

- A konfigurációs kiszolgáló koordinálja a helyszíni VMware és az Azure közötti kommunikációt. Emellett az adatreplikálást is kezeli.
- [További](vmware-azure-architecture.md) információ a konfigurációs kiszolgáló összetevőiről és folyamatairól.

## <a name="configuration-server-deployment"></a>Konfigurációs kiszolgáló telepítése

A VMware virtuális gépek Azure-ba való vész-helyreállításához a konfigurációs kiszolgálót VMware VM-ként kell telepíteni.

- A Site Recovery egy olyan PETESEJT-sablont biztosít, amelyet a Azure Portalból tölt le, és az vCenter Serverba importálhatja a konfigurációs kiszolgáló virtuális gépe beállításához.
- Ha a konfigurációs kiszolgálót a petesejtek sablonnal telepíti, a virtuális gép automatikusan megfelel a jelen cikkben felsorolt követelményeknek.
- Javasoljuk, hogy a konfigurációs kiszolgálót a petesejtek sablonnal állítsa be. Ha azonban a VMware virtuális gépek esetében vész-helyreállítást állít be, és nem tudja használni a petesejtek sablont, a konfigurációs kiszolgálót a [megadott utasítások](physical-azure-set-up-source.md)alapján is telepítheti.
- Ha a konfigurációs kiszolgálót a helyszíni fizikai gépek Azure-ba való vész-helyreállítására telepíti, kövesse a [jelen cikkben](physical-azure-set-up-source.md)található utasításokat. 


## <a name="hardware-requirements"></a>Hardverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | 3, beleértve az operációsrendszer-lemezt, a feldolgozási kiszolgáló gyorsítótárának lemezét és a feladat-visszavételi meghajtót 
Szabad lemezterület (folyamat kiszolgálójának gyorsítótára) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB

## <a name="software-requirements"></a>Szoftverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Operációs rendszer | Windows Server 2012 R2 <br> Windows Server 2016
Operációs rendszer területi beállítása | Angol (en-us)
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> – A parancssor elérésének tiltása. <br> – A beállításjegyzék szerkesztési eszközeihez való hozzáférés megakadályozása. <br> – A fájlmellékletek megbízhatósági logikája. <br> – A parancsfájlok végrehajtásának bekapcsolása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Nincs előre meglévő alapértelmezett webhely <br> – Nincs előre létező webhely/alkalmazás a 443-as porton <br>– [Névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) engedélyezése <br> – [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -beállítás engedélyezése 

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

**Összetevő** | **Követelmény** 
--- | --- 
IP-cím típusa | Statikus tartalom 
Internetelérés | A kiszolgálónak hozzá kell férnie ezekhez az URL-címekhez (közvetlenül vagy proxyn keresztül): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> -https:\//Management.Azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> A OVF emellett a következő URL-címekhez is hozzá kell férnie: <br> -https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> -https:\//login.Live.com  <br> -https:\//auth.gfx.MS <br> -https:\//Graph.Windows.net <br> -https:\//login.Windows.net <br> -https:\//www.Live.com <br> -https:\//www.microsoft.com <br> -https:\//dev.mysql.com/get/downloads/MySQLInstaller/MySQL-Installer-Community-5.7.20.0.msi 
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel) 
Hálózati adapter típusa | VMXNET3 (ha a konfigurációs kiszolgáló egy VMware virtuális gép)

## <a name="required-software"></a>Szükséges szoftverek

**Összetevő** | **Követelmény** 
--- | ---
VMware vSphere PowerCLI | A [PowerCLI 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) -es verzióját kell telepíteni, ha a konfigurációs kiszolgáló VMWare virtuális gépen fut.
MYSQL | Telepíteni kell a MySQL-t. Manuálisan is telepítheti, vagy Site Recovery telepítheti.

## <a name="sizing-and-capacity-requirements"></a>Méretezési és kapacitási követelmények

A következő táblázat összefoglalja a konfigurációs kiszolgáló kapacitásának követelményeit. Több VMware virtuális gép replikálásakor tekintse át a [kapacitás tervezésével kapcsolatos szempontokat](site-recovery-plan-capacity-vmware.md), és futtassa az [Azure site Recovery Deployment Planner](site-recovery-deployment-planner.md) eszközt a VMware-replikációhoz. olvasás 

**Összetevő** | **Követelmény** 
--- | ---

| **CPU** | **Memória** | **Lemez gyorsítótára** | **Adatváltozási arány** | **Replikált gépek** |
| --- | --- | --- | --- | --- |
| 8 vCPU<br/><br/> 2 szoftvercsatorna * 4 mag \@ 2,5 GHz | 16 GB | 300 GB | 500 GB vagy kevesebb | 100-nál több gépen |
| 12 vCPU<br/><br/> 2 SOCKS * 6 \@ mag 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | 100 – 150 gép |
| 16 vCPU<br/><br/> 2 zokni * 8 mag \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 gép | 



## <a name="next-steps"></a>További lépések
Állítsa be a [VMWare virtuális gépek](vmware-azure-tutorial.md) vész-helyreállítását az Azure-ba.
