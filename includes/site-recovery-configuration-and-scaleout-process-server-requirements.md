---
title: fájlbefoglalás
description: fájlbefoglalás
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/23/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: c8c51d671cd98a606c11a39b6cf489aa288d71b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85292177"
---
**A konfigurációs és a feldolgozási kiszolgáló követelményei**


## <a name="hardware-requirements"></a>Hardverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | 3, beleértve az operációsrendszer-lemezt, a feldolgozási kiszolgáló gyorsítótárának lemezét és a feladat-visszavételi meghajtót 
Szabad lemezterület (folyamat kiszolgálójának gyorsítótára) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
 | 

## <a name="software-requirements"></a>Szoftverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Operációs rendszer | Windows Server 2012 R2 <br> Windows Server 2016
Operációs rendszer területi beállítása | Angol (en-*)
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> – A parancssor elérésének tiltása. <br> – A beállításjegyzék szerkesztési eszközeihez való hozzáférés megakadályozása. <br> – A fájlmellékletek megbízhatósági logikája. <br> – A parancsfájlok végrehajtásának bekapcsolása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Nincs előre meglévő alapértelmezett webhely <br> – Nincs már meglévő webhely/alkalmazás a 443-es porton <br>– [Névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) engedélyezése <br> – [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -beállítás engedélyezése 
FIPS (Federal Information Processing Standards) | FIPS mód engedélyezése
|

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

**Összetevő** | **Követelmény** 
--- | --- 
IP-cím típusa | Statikus 
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel) 
Hálózati adapter típusa | VMXNET3 (ha a konfigurációs kiszolgáló egy VMware virtuális gép)
 |
**Internet-hozzáférés** (a kiszolgálónak közvetlenül vagy proxyn keresztül kell hozzáférnie a következő URL-címekhez):|
\*.backup.windowsazure.com | A replikált adatátvitelhez és a koordinációhoz használatos
\*.blob.core.windows.net | A replikált adattárolást tároló fiók elérésére szolgál. Megadhatja a gyorsítótárbeli Storage-fiók adott URL-címét.
\*.hypervrecoverymanager.windowsazure.com | Replikációs felügyeleti műveletekhez és koordináláshoz használatos
https:\//login.microsoftonline.com | Replikációs felügyeleti műveletekhez és koordináláshoz használatos 
time.nist.gov | A rendszer és a globális idő közötti idő-szinkronizálás vizsgálatára szolgál.
time.windows.com | A rendszer és a globális idő közötti idő-szinkronizálás vizsgálatára szolgál.
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https: \/ /login.Live.com </li><li> https: \/ /Graph.Windows.net </li><li> https:\//login.windows.net </li><li> *. services.visualstudio.com (nem kötelező) </li><li> https: \/ /www.Live.com </li><li> https: \/ /www.microsoft.com </li></ul> | A OVF-telepítőnek hozzá kell férnie a további URL-címekhez. A hozzáférés-vezérléshez és az identitások kezeléséhez használják Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | A MySQL letöltésének befejezéséhez. </br> Néhány régióban a letöltés átirányítva lesz a CDN URL-címére. Szükség esetén győződjön meg arról, hogy a CDN URL-címe is engedélyezett.
|

## <a name="required-software"></a>Szükséges szoftverek

**Összetevő** | **Követelmény** 
--- | ---
VMware vSphere PowerCLI | A [PowerCLI 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) -es verzióját kell telepíteni, ha a konfigurációs kiszolgáló VMWare virtuális gépen fut.
MYSQL | Telepíteni kell a MySQL-t. Manuálisan is telepítheti, vagy Site Recovery telepítheti. (További információ a [beállítások konfigurálása](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) című témakörben találhatók)
|

## <a name="sizing-and-capacity-requirements"></a>Méretezési és kapacitási követelmények

A következő táblázat összefoglalja a konfigurációs kiszolgáló kapacitásának követelményeit. Több VMware virtuális gép replikálásakor tekintse át a [kapacitás megtervezésével kapcsolatos szempontokat](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) , és futtassa az [Azure site Recovery Deployment Planner eszközt](../articles/site-recovery/site-recovery-deployment-planner.md).


**CPU** | **Memory (Memória)** | **Lemez gyorsítótára** | **Adatváltozási arány** | **Replikált gépek**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 szoftvercsatorna * 4 mag \@ 2,5 GHz | 16 GB | 300 GB | 500 GB vagy kevesebb | < 100 gép
12 vCPU<br/><br/> 2 SOCKS * 6 mag \@ 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | 100 – 150 gép
16 vCPU<br/><br/> 2 zokni * 8 mag \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 – 200 gép
|

