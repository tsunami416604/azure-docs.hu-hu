---
title: fájl belefoglalása
description: fájl belefoglalása
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179345"
---
**A fizikai kiszolgáló replikációjának konfigurációs/feldolgozási kiszolgálóra vonatkozó követelményei**

**Összetevő** | **Követelmény** 
--- | ---
**HARDVERBEÁLLÍTÁSOK** | 
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | 3, beleértve az operációs rendszer lemezét, a kiszolgáló gyorsítótárának feldolgozását és a feladat-visszavétel megőrzési meghajtóját 
Szabad lemezterület (folyamatkiszolgáló gyorsítótára) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
 | 
**SZOFTVERBEÁLLÍTÁSOK** | 
Operációs rendszer | Windows Server 2012 R2 <br> Windows Server 2016
Operációs rendszer területi beállítása | Angol (en-us)
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> - A parancssorhoz való hozzáférés megakadályozása. <br> - Hozzáférés megakadályozása a rendszerleíró adatbázis szerkesztő eszközök. <br> - Megbízhatósági logika a fájlmellékletekhez. <br> - Kapcsolja be a Script Execution. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nincs már létező alapértelmezett webhely <br> - Nincs már létező weboldal / alkalmazás hallgatása port 443 <br>- Engedélyezése [névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - A [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás engedélyezése.
IP-cím típusa | Statikus 
| 
**HOZZÁFÉRÉSI BEÁLLÍTÁSOK** | 
Mysql | A MySQL-t telepíteni kell a konfigurációs kiszolgálóra. Manuálisan is telepíthető, vagy a Site Recovery telepítheti a telepítés során. A Site Recovery telepítéséhez ellenőrizze, http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msihogy a készülék el tudja-e érni a .
 URL-címek | A konfigurációs kiszolgálónak hozzáférésre van szüksége ezekhez az URL-címekhez (közvetlenül vagy proxyn keresztül):<br/><br/> Azure Hirdetés: `login.microsoftonline.com`; `login.microsoftonline.us`;`*.accesscontrol.windows.net`<br/><br/> Replikációs adatátvitel: `*.backup.windowsazure.com`;`*.backup.windowsazure.us`<br/><br/> Replikáció kezelése: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`;`*.services.visualstudio.com`<br/><br/> Tárolási hozzáférés: `*.blob.core.windows.net`;`*.blob.core.usgovcloudapi.net`<br/><br/> Időszinkronizálás: `time.nist.gov`;`time.windows.com`<br/><br/> Telemetria (nem kötelező):`dc.services.visualstudio.com`
Tűzfal | AZ IP-címalapú tűzfalszabályoknak lehetővé kell tenniük az Azure URL-címekkel való kommunikációt. Az IP-tartományok egyszerűsítése és korlátozása érdekében javasoljuk az URL-szűrés használatát.<br/><br/>**Kereskedelmi IP-k esetében:**<br/><br/>- Az [Azure Datacenter IP-tartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653)és a HTTPS (443) port engedélyezése.<br/><br/> - IP-címtartományok engedélyezése az USA nyugati részén (hozzáférés-vezérléshez és identitáskezeléshez használatos).<br/><br/> - Ip-címtartományok engedélyezése az előfizetés Azure-régiójához, az Azure Active Directoryhoz, a biztonsági mentéshez, a replikációhoz és a tároláshoz szükséges URL-címek támogatásához.<br/><br/> **Kormányzati IP-k esetében:**<br/><br/> - Engedélyezze az Azure Government Datacenter IP-tartományok, és a HTTPS (443) port.<br/><br/> - Ip-címtartományok engedélyezése az összes amerikai kormányrégióban (Virginia, Texas, Arizona és Iowa) az Azure Active Directoryhoz, a biztonsági mentéshez, a replikációhoz és a tároláshoz szükséges URL-címek támogatásához.
Portok | 443 engedélyezése (Csatorna vezénylésének vezérlése)<br/><br/> 9443 engedélyezése (Adatátvitel) 


**A konfigurációs/folyamatkiszolgáló méretezési követelményei**

**Cpu** | **Memory (Memória)** | **Lemez gyorsítótára** | **Adatváltozási sebesség** | **Replikált gépek**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 foglalat * \@ 4 mag 2,5 GHz | 16 GB | 300 GB | 500 GB vagy kevesebb | < 100 gép
12 vCPU<br/><br/> 2 zokni * \@ 6 mag 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100-150 gép
16 vCPU<br/><br/> 2 zokni * \@ 8 mag 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 gép

