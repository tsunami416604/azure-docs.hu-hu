---
title: fájl belefoglalása
description: fájl belefoglalása
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 1aaec104e9130eeef723c6505e04e3317271566b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234215"
---
**A konfigurációs és folyamatkiszolgálókövetelményei**


## <a name="hardware-requirements"></a>Hardverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | 3, beleértve az operációs rendszer lemezét, a kiszolgáló gyorsítótárának feldolgozását és a feladat-visszavétel megőrzési meghajtóját 
Szabad lemezterület (folyamatkiszolgáló gyorsítótára) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
 | 

## <a name="software-requirements"></a>Szoftverkövetelmények

**Összetevő** | **Követelmény** 
--- | ---
Operációs rendszer | Windows Server 2012 R2 <br> Windows Server 2016
Operációs rendszer területi beállítása | Angol (en-*)
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> - A parancssorhoz való hozzáférés megakadályozása. <br> - Hozzáférés megakadályozása a rendszerleíró adatbázis szerkesztő eszközök. <br> - Megbízhatósági logika a fájlmellékletekhez. <br> - Kapcsolja be a Script Execution. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nincs már létező alapértelmezett webhely <br> - Nincs már létező weboldal / alkalmazás hallgatása a 443-as porton <br>- Engedélyezése [névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás 
FIPS (szövetségi információfeldolgozási szabványok) | Fips mód nem engedélyezése
|

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

**Összetevő** | **Követelmény** 
--- | --- 
IP-cím típusa | Statikus 
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel) 
Hálózati adapter típusa | VMXNET3 (ha a konfigurációs kiszolgáló VMware VM)
 |
**Internet-hozzáférés** (a kiszolgálónak a következő URL-címekhez kell hozzáférnie, közvetlenül vagy proxyn keresztül):|
\*.backup.windowsazure.com | Replikált adatátvitelhez és koordinációhoz használatos
\*.store.core.windows.net | Replikált adatátvitelhez és koordinációhoz használatos
\*.blob.core.windows.net | Replikált adatokat tároló tárfiók eléréséhez
\*.hypervrecoverymanager.windowsazure.com | Replikációkezelési műveletekhez és koordinációhoz használatos
https:\//management.azure.com | Replikációkezelési műveletekhez és koordinációhoz használatos 
*.services.visualstudio.com | Telemetriai célokra használva (nem kötelező)
time.nist.gov | A rendszer és a globális idő közötti időszinkronizálás ellenőrzésére szolgál
time.windows.com | A rendszer és a globális idő közötti időszinkronizálás ellenőrzésére szolgál
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | Az OVF beállításához hozzá kell férni ezekhez az URL-címekhez. Az Azure Active Directory hozzáférés-vezérlési és identitáskezelési célokra használja őket.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | A MySQL letöltésének befejezéséhez. </br> Néhány régióban előfordulhat, hogy a letöltés átlesz irányítva a CDN URL-címére. Szükség esetén győződjön meg arról, hogy a CDN URL-címe is szerepel a listán.
|

## <a name="required-software"></a>Szükséges szoftverek

**Összetevő** | **Követelmény** 
--- | ---
VMware vSphere PowerCLI | [A PowerCLI 6.0-s verzióját](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) telepíteni kell, ha a konfigurációs kiszolgáló vmware virtuális gépen fut.
Mysql | MySQL kell telepíteni. Manuálisan is telepítheti, vagy a Site Recovery is telepítheti. (További információért olvassa el a [beállítások konfigurálása](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) című pontot)
|

## <a name="sizing-and-capacity-requirements"></a>Méretezési és kapacitási követelmények

Az alábbi táblázat a konfigurációs kiszolgáló kapacitáskövetelményeit foglalja össze. Ha több VMware virtuális gépet replikál, tekintse át a [kapacitástervezési szempontokat,](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) és futtassa az [Azure Site Recovery Deployment Planner eszközt.](../articles/site-recovery/site-recovery-deployment-planner.md)


**Cpu** | **Memory (Memória)** | **Lemez gyorsítótára** | **Adatváltozási sebesség** | **Replikált gépek**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 foglalat * \@ 4 mag 2,5 GHz | 16 GB | 300 GB | 500 GB vagy kevesebb | < 100 gép
12 vCPU<br/><br/> 2 zokni * \@ 6 mag 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100-150 gép
16 vCPU<br/><br/> 2 zokni * \@ 8 mag 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 gép
|

