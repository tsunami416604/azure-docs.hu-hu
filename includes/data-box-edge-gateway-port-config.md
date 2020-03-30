---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179559"
---
| Port száma| Be- vagy ki | Port hatóköre| Kötelező|   Megjegyzések |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Ki|WAN |Nem|A kimenő port internet-hozzáférésre szolgál a frissítések lekéréséhez. <br>A kimenő webproxy a felhasználó által konfigurálható. |
| TCP 443 (HTTPS)|Ki|WAN|Igen|A kimenő port a felhőben lévő adatok elérésére szolgál.<br>A kimenő webproxy a felhasználó által konfigurálható.|
| UDP 123 (NTP)|Ki|WAN|Egyes esetekben<br>Lásd a jegyzeteket|Ez a port csak akkor szükséges, ha internetalapú NTP-kiszolgálót használ.  |   
| UDP 53 (DNS)|Ki|WAN|Egyes esetekben<br>Lásd a jegyzeteket|Ez a port csak akkor szükséges, ha internetalapú DNS-kiszolgálót használ.<br>Javasoljuk, hogy használjon helyi DNS-kiszolgálót. |
| TCP 5985 (WinRM)|Ki/be|LAN|Egyes esetekben<br>Lásd a jegyzeteket|Ez a port szükséges az eszközhöz való csatlakozáshoz a távoli PowerShell HTTP-n keresztül.  |
| UDP 67 (DHCP)|Ki|LAN|Egyes esetekben<br>Lásd a jegyzeteket|Ez a port csak akkor szükséges, ha helyi DHCP-kiszolgálót használ.  |
| TCP 80 (HTTP)|Ki/be|LAN|Igen|Ez a port a helyi felhasználói felület bejövő portja az eszközön a helyi felügyelethez. <br>A http-n keresztüli helyi felhasználói felület elérése automatikusan átirányítja a HTTPS-re.  |
| TCP 443 (HTTPS)|Ki/be|LAN|Igen|Ez a port a helyi felhasználói felület bejövő portja az eszközön a helyi felügyelethez. |
| TCP 445 (SMB)|A|LAN|Egyes esetekben<br>Lásd a jegyzeteket|Ez a port csak akkor szükséges, ha SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|A|LAN|Egyes esetekben<br>Lásd a jegyzeteket|Ez a port csak akkor szükséges, ha NFS-en keresztül csatlakozik. |
