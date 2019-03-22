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
ms.openlocfilehash: 371cbcc50b574f95e8d9ba4efe79058b2b25a8ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58016680"
---
**Configuration/Process server-követelmények**

**Összetevő** | **Követelmény** 
--- | ---
**HARDVERBEÁLLÍTÁSOK** | 
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | 3., beleértve az operációs rendszer lemez, a folyamatkiszolgálói gyorsítótárlemez és az adatmegőrzési meghajtó a feladat-visszavételhez 
Szabad lemezterület (folyamatkiszolgálói gyorsítótár) | 600 GB
Szabad területe (adatmegőrzési lemez) | 600 GB
 | 
**SZOFTVERFRISSÍTÉSI BEÁLLÍTÁSOK** | 
Operációs rendszer | Windows Server 2012 R2 <br> Windows Server 2016
Operációs rendszer területi beállítása | Angol (en-us)
Windows Server-szerepkörök | Ezek a szerepkörök nem engedélyezi: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | Ezek a szabályzatok csoport nem engedélyezi: <br> -Hozzáférés megakadályozása a parancssorba. <br> -A beállításjegyzék szerkesztőeszközeihez való hozzáférés letiltása. <br> -Megbízhatósági logika fájlmellékletekhez. <br> – Kapcsolja be a parancsfájl végrehajtása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Nincs már meglévő alapértelmezett webhelye <br> – Nincs már létező webhely vagy alkalmazás 443-as porton <br>-Engedélyezése [a névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás 
| 
**HÁLÓZATI BEÁLLÍTÁSOK** | 
IP-cím típusa | Statikus 
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel) 
Hálózati adapter típusa | VMXNET3 (Ha a konfigurációs kiszolgáló VMware virtuális gép)
 |
**Internet-hozzáférés** (a kiszolgáló el kell érnie a következő URL-címek – közvetlen vagy proxyn keresztül):|
\*.backup.windowsazure.com | A replikált adatok átvitel és összehangoláshoz
\*.store.core.windows.net | A replikált adatok átvitel és összehangoláshoz
\*.blob.core.windows.net | A replikált adatokat tároló tárfiók eléréséhez használt
\*.hypervrecoverymanager.windowsazure.com | Replikációkezelési műveletekhez és összehangoláshoz
https:\//management.azure.com | Replikációkezelési műveletekhez és összehangoláshoz 
*.services.visualstudio.com | Telemetria célra (nem kötelező)
time.nist.gov | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
time.windows.com | A rendszer és a globális idő közötti időszinkronizálás ellenőrzéséhez.
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF-állítsa be a következő URL-címekhez hozzá kell férnie. Hozzáférés-vezérlés és az identitás használják az Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL letöltéshez
|
**SZOFTVER TELEPÍTÉSE** | 
A VMware vSphere PowerCLI | [A PowerCLI 6.0-s verzió](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) kell telepíteni, ha fut a konfigurációs kiszolgáló VMware virtuális gép.
MYSQL | MySQL kell telepíteni. Manuálisan is telepítheti, vagy a Site Recovery telepíthetik azt. (Tekintse meg [beállításainak konfigurálása](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) további információ)

**A méretezési követelmények Configuration/Process server**

**CPU** | **Memória** | **Gyorsítótárlemez** | **A módosult adatok aránya** | **Replikált gépek**
--- | --- | --- | --- | ---
8 Vcpu<br/><br/> 2 sockets * 4 mag \@ 2,5 GHz-es | 16GB | 300 GB | 500 GB vagy kevesebb | < 100 gépek
12 vcpu-k<br/><br/> 2 socks * 6 magok \@ 2,5 GHz-es | 18 GB | 600 GB | 500 GB-1 TB | 100-150 gépek
16 vcpu-k<br/><br/> 2 socks * 8 magos \@ 2,5 GHz-es | 32 GB | 1 TB | 1-2 TB | 150-200 gépek

