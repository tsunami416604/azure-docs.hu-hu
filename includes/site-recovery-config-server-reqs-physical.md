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
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179345"
---
**A fizikai kiszolgálók replikálásához szükséges konfiguráció/folyamat kiszolgálói követelmények**

**Összetevő** | **Követelmény** 
--- | ---
**HARDVERBEÁLLÍTÁSOK** | 
Processzormagok | 8 
RAM | 16 GB
Lemezek száma | 3, beleértve az operációsrendszer-lemezt, a feldolgozási kiszolgáló gyorsítótárának lemezét és a feladat-visszavételi meghajtót 
Szabad lemezterület (folyamat kiszolgálójának gyorsítótára) | 600 GB
Szabad lemezterület (adatmegőrzési lemez) | 600 GB
 | 
**SZOFTVERBEÁLLÍTÁSOK** | 
Operációs rendszer | Windows Server 2012 R2 <br> Windows Server 2016
Operációs rendszer területi beállítása | Angol (en-us)
Windows Server-szerepkörök | Ne engedélyezze ezeket a szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V 
Csoportházirendek | Ne engedélyezze ezeket a csoportházirendeket: <br> – A parancssor elérésének tiltása. <br> – A beállításjegyzék szerkesztési eszközeihez való hozzáférés megakadályozása. <br> – A fájlmellékletek megbízhatósági logikája. <br> – A parancsfájlok végrehajtásának bekapcsolása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Nincs előre meglévő alapértelmezett webhely <br> – Nincs előre létező webhely/alkalmazás a 443-as porton <br>– [Névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) engedélyezése <br> – Engedélyezze a [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -beállítást.
IP-cím típusa | Statikus 
| 
**HOZZÁFÉRÉSI BEÁLLÍTÁSOK** | 
MYSQL | A MySQL-t telepíteni kell a konfigurációs kiszolgálóra. Manuálisan is telepítheti, vagy Site Recovery telepítheti az üzembe helyezés során. Site Recovery a telepítéshez győződjön meg arról, hogy a gép http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msiel tudja érni.
URL-címek | A konfigurációs kiszolgálónak hozzá kell férnie ezekhez az URL-címekhez (közvetlenül vagy proxyn keresztül):<br/><br/> Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`;`*.accesscontrol.windows.net`<br/><br/> Replikálási adatok átvitele: `*.backup.windowsazure.com`;`*.backup.windowsazure.us`<br/><br/> Replikáció kezelése: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`;`*.services.visualstudio.com`<br/><br/> Tárolási hozzáférés: `*.blob.core.windows.net`;`*.blob.core.usgovcloudapi.net`<br/><br/> Idő szinkronizálása `time.nist.gov`:;`time.windows.com`<br/><br/> Telemetria (nem kötelező):`dc.services.visualstudio.com`
Tűzfal | Az IP-cím alapú tűzfalszabályok lehetővé teszik az Azure URL-címekkel való kommunikációt. Az IP-címtartományok egyszerűsítése és korlátozása érdekében javasoljuk, hogy használjon URL-szűrést.<br/><br/>**Kereskedelmi IP-címek esetén:**<br/><br/>– Engedélyezi az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653)és a HTTPS (443) portot.<br/><br/> – Engedélyezi az USA nyugati régiójában lévő IP-címtartományok használatát (Access Control és Identitáskezelés esetén használatos).<br/><br/> – Engedélyezze az előfizetés Azure-régiójának IP-címtartományok használatát a Azure Active Directory, a biztonsági mentéshez, a replikáláshoz és a tároláshoz szükséges URL-címek támogatásához.<br/><br/> **Kormányzati IP-címek esetén:**<br/><br/> -Engedélyezi a Azure Government adatközpont IP-tartományait és a HTTPS-portot (443).<br/><br/> – Engedélyezi az IP-címtartományok használatát minden US Gov régióban (Virginia, Texas, Arizona és Iowa), hogy támogassa a Azure Active Directoryhoz, a biztonsági mentéshez, a replikáláshoz és a tároláshoz szükséges URL-címeket.
Portok | 443 engedélyezése (vezérlési csatorna összehangolása)<br/><br/> 9443 engedélyezése (adatátviteli szolgáltatás) 


**Konfiguráció/folyamat kiszolgáló méretezési követelményei**

**CPU** | **Memory (Memória)** | **Lemez gyorsítótára** | **Adatváltozási arány** | **Replikált gépek**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 szoftvercsatorna * 4 mag \@ 2,5 GHz | 16GB | 300 GB | 500 GB vagy kevesebb | < 100 gép
12 vCPU<br/><br/> 2 SOCKS * 6 \@ mag 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | 100 – 150 gép
16 vCPU<br/><br/> 2 zokni * 8 mag \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 – 200 gép

