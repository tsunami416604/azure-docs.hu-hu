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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58887015"
---
**A fizikai kiszolgáló replikációjához Configuration/Process server-követelményei**

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
IIS | – Nincs már létező alapértelmezett webhelye <br> – Nincs már létező webhely vagy alkalmazás 443-as porton <br>-Engedélyezése [a névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás.
IP-cím típusa | Statikus 
| 
**HOZZÁFÉRÉSI BEÁLLÍTÁSOK** | 
MYSQL | A konfigurációs kiszolgáló MySQL kell telepíteni. Manuálisan is telepítheti, vagy üzembe helyezés során a Site Recovery is telepíthet. A Site Recovery telepítése, ellenőrizze, hogy elérje a gép http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
URLs | A konfigurációs kiszolgáló URL-hozzáférésre van szüksége (közvetlenül vagy proxyn keresztül):<br/><br/> Az Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`; `*.accesscontrol.windows.net`<br/><br/> Replikációs adatátvitelhez: `*.backup.windowsazure.com`; `*.backup.windowsazure.us`<br/><br/> Replikációs felügyeleti: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`; `*.services.visualstudio.com`<br/><br/> Storage-hozzáférés: `*.blob.core.windows.net`; `*.blob.core.usgovcloudapi.net`<br/><br/> Időszinkronizálás: `time.nist.gov`; `time.windows.com`<br/><br/> A telemetriai adatok (nem kötelező): `dc.services.visualstudio.com`
Tűzfal | IP-cím-alapú tűzfalszabályainak kell engedélyezi a kommunikációt az Azure URL-címekhez. Az egyszerűbb, és korlátozza az IP-címtartományok, javasoljuk hogy URL-CÍMEK szűrése.<br/><br/>**Kereskedelmi IP-címek:**<br/><br/>– Engedélyezze a [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653), és a HTTPS (443) portot.<br/><br/> – Engedélyezze a IP-címtartományok esetében az USA nyugati RÉGIÓJA (hozzáférés-vezérléshez és Identitáskezeléshez használt).<br/><br/> – Engedélyezze a IP-címtartományok tartozó Azure-régió az előfizetés, az URL-címek támogatása szükséges az Azure Active Directory, biztonsági mentési és replikációs és storage.<br/><br/> **A kormányzati szerveknek nyújtott IP-címek:**<br/><br/> – Engedélyezze az Azure Government adatközpont IP-címtartományok, és a HTTPS (443) portot.<br/><br/> – Engedélyezze a IP-címtartományok minden US Gov régió (Virginia, Texas, Arizona és Iowa), az URL-címeket támogatja a szükséges az Azure Active Directory, biztonsági mentési és replikációs és storage.
Portok | 443 (vezérlőcsatorna-vezénylés) engedélyezése<br/><br/> 9443 (adatátvitel) engedélyezése 


**A méretezési követelmények Configuration/Process server**

**CPU** | **Memória** | **Gyorsítótárlemez** | **A módosult adatok aránya** | **Replikált gépek**
--- | --- | --- | --- | ---
8 Vcpu<br/><br/> 2 sockets * 4 mag \@ 2,5 GHz-es | 16GB | 300 GB | 500 GB vagy kevesebb | < 100 gépek
12 vcpu-k<br/><br/> 2 socks * 6 magok \@ 2,5 GHz-es | 18 GB | 600 GB | 500 GB-1 TB | 100-150 gépek
16 vcpu-k<br/><br/> 2 socks * 8 magos \@ 2,5 GHz-es | 32 GB | 1 TB | 1-2 TB | 150-200 gépek

