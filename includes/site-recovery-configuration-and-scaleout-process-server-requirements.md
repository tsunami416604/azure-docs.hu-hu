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
ms.openlocfilehash: 601819756b78ffe8762bdfbfd5f802bc2d76e9c5
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35268053"
---
**Konfiguráció/folyamat kiszolgáló követelményei**

**Összetevő** | **Követelmény** 
--- | ---
**HARDVERBEÁLLÍTÁSOK** | 
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | 3., beleértve az operációs rendszer lemez, a folyamat kiszolgáló gyorsítótár lemez és a feladat-visszavételi adatmegőrzési meghajtó 
Szabad lemezterület (folyamat kiszolgáló gyorsítótárának) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
 | 
**SZOFTVERFRISSÍTÉSI BEÁLLÍTÁSOK** | 
Operációs rendszer | Windows Server 2012 R2 <br> Windows Server 2016
Operációs rendszer területi beállítása | Angol (en-us)
Windows Server-szerepkörök | Ezek a szerepkörök nem engedélyezi: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | A csoportházirendek nem engedélyezi: <br> -Tagadni a hozzáférést a parancssorba. <br> -A hozzáférés megakadályozása a beállításjegyzék szerkesztésével eszközök. <br> – Megbízható vonatkozó logikát. <br> -Parancsfájl végrehajtása bekapcsolása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Nincs elérésű, korábban létező alapértelmezett webhely <br> -Egyetlen elérésű, korábban létező webhely vagy alkalmazás figyeli a 443-as port <br>-Engedélyezése [névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás 
| 
**HÁLÓZATI BEÁLLÍTÁSOK** | 
IP-cím típusa | Statikus 
Internetelérés | A kiszolgáló URL-hozzáférésre van szüksége (közvetlen vagy proxyn keresztül) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (Ha a konfigurációs kiszolgáló beállítása) <br> - time.nist.gov <br> - time.windows.com 
Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel) 
A hálózati adapter típusa | VMXNET3 (Ha a konfigurációs kiszolgáló VMware virtuális gép)
 | 
**SZOFTVER TELEPÍTÉSE** | 
VMware vSphere PowerCLI | [PowerCLI 6.0-s verzió](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) kell telepíteni, ha a VMware virtuális gép működik a konfigurációs kiszolgáló.
MYSQL | MySQL kell telepíteni. A Site Recovery telepítheti, vagy manuálisan telepíthet.

**Konfiguráció/folyamatkiszolgáló követelmények méretezése**

**CPU** | **Memória** | **Gyorsítótár-lemez** | **Adatváltozási sebesség** | **Replikált gépek**
--- | --- | --- | --- | ---
8 Vcpu<br/><br/> 2 sockets * @ 2,5 GHz, 4 mag | 16GB | 300 GB | 500 GB vagy kevesebb | < 100 gépek
12 Vcpu<br/><br/> 2 socks * @ 2,5 GHz-es 6 mag | 18 GB | 600 GB | 500 GB - 1 TB-OS | 100-150 gépek
16 Vcpu<br/><br/> 2 socks * @ 2,5 GHz-es 8 magos | 32 GB | 1 TB | 1-2 TB | 150-200 gépek

