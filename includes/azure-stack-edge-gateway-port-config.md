---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: baf18ae0263215e6ff83570557255d06c3117fd4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084040"
---
| Port nem.| Be vagy ki | Port hatóköre| Kötelező|   Jegyzetek |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Ki|WAN |Nem|A rendszer a kimenő portot használja az internet-hozzáféréshez a frissítések lekéréséhez. <br>A kimenő webes proxy a felhasználó által konfigurálható. |
| TCP 443 (HTTPS)|Ki|WAN|Igen|A kimenő port a felhőben tárolt adatok elérésére szolgál.<br>A kimenő webes proxy a felhasználó által konfigurálható.|
| UDP 123 (NTP)|Ki|WAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha Internet-alapú NTP-kiszolgálót használ.  |   
| UDP 53 (DNS)|Ki|WAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha Internet alapú DNS-kiszolgálót használ.<br>Javasoljuk, hogy használjon helyi DNS-kiszolgálót. |
| TCP 5985 (WinRM)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Ez a port a távoli PowerShell-lel HTTP-n keresztül történő csatlakozáshoz szükséges.  |
| UDP 67 (DHCP)|Ki|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha helyi DHCP-kiszolgálót használ.  |
| TCP 80 (HTTP)|Ki/be|LAN|Igen|Ez a port a helyi kezelőfelület helyi felhasználói felületének bejövő portja az eszközön. <br>A helyi felhasználói felület HTTP-n keresztüli elérése automatikusan a HTTPS-re lesz átirányítva.  |
| TCP 443 (HTTPS)|Ki/be|LAN|Igen|Ez a port a helyi kezelőfelület helyi felhasználói felületének bejövő portja az eszközön. Ez a port Azure Resource Manager csatlakoztatására is használható az eszköz helyi API-khoz, a blob Storage REST API-kon keresztüli csatlakoztatásához, valamint a biztonsági jogkivonat szolgáltatáshoz (STS) a hozzáférés-és frissítési jogkivonatok használatával történő hitelesítéshez.|
| TCP 445 (SMB)|In|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|In|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az NFS-en keresztül csatlakozik. |


