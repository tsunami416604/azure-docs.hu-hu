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
ms.openlocfilehash: ade53ba29d165b3b33ef25dabda25c4e60022608
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "40133241"
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
IIS | – Nincs már létező alapértelmezett webhelye <br> – Nincs már létező webhely vagy alkalmazás 443-as porton <br>-Engedélyezése [a névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás 
| 
**HÁLÓZATI BEÁLLÍTÁSOK** | 
IP-cím típusa | Statikus 
Internetelérés | A kiszolgáló URL-hozzáférésre van szüksége (közvetlenül vagy proxyn keresztül): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> – https:\//management.azure.com <br> -*. services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF is el kell érnie a következő URL-címek: <br> – https:\//login.microsoftonline.com <br> – https:\//secure.aadcdn.microsoftonline-p.com <br> – https:\//login.live.com  <br> – https:\//auth.gfx.ms <br> – https:\//graph.windows.net <br> – https:\//login.windows.net <br> – https:\//www.live.com <br> – https:\//www.microsoft.com <br> – https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel) 
Hálózati adapter típusa | VMXNET3 (Ha a konfigurációs kiszolgáló VMware virtuális gép)
 | 
**SZOFTVER TELEPÍTÉSE** | 
A VMware vSphere PowerCLI | [A PowerCLI 6.0-s verzió](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) kell telepíteni, ha fut a konfigurációs kiszolgáló VMware virtuális gép.
MYSQL | MySQL kell telepíteni. Manuálisan is telepítheti, vagy a Site Recovery telepíthetik azt.

**A méretezési követelmények Configuration/Process server**

**CPU** | **Memória** | **Gyorsítótárlemez** | **A módosult adatok aránya** | **Replikált gépek**
--- | --- | --- | --- | ---
8 Vcpu<br/><br/> 2 sockets * 4 mag \@ 2,5 GHz-es | 16GB | 300 GB | 500 GB vagy kevesebb | < 100 gépek
12 vcpu-k<br/><br/> 2 socks * 6 magok \@ 2,5 GHz-es | 18 GB | 600 GB | 500 GB – 1 TB | 100-150 gépek
16 vcpu-k<br/><br/> 2 socks * 8 magos \@ 2,5 GHz-es | 32 GB | 1 TB | 1 – 2 TB | 150-200 gépek

