---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 948f5399ceb931d701341c2cf378482ede94979d
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200326"
---
| Port nem.| Be vagy ki | Port hatóköre| Kötelező| Megjegyzések |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|In|LAN|Igen|Ezzel a porttal csatlakozhat a Data Box blog Storage REST API-khoz HTTP-n keresztül. Ha nem csatlakozik a REST API-khoz, a rendszer automatikusan átirányítja a helyi webes felhasználói felületre a 8443-es kapcsolaton keresztül. |
| TCP 443 (HTTPS)|In|LAN|Igen|Ez a port a Data Box blog Storage REST API-k HTTPS-kapcsolaton keresztüli elérésére szolgál. Ha nem csatlakozik a REST API-khoz, a rendszer automatikusan átirányítja a helyi webes felhasználói felületre a 8443-es kapcsolaton keresztül. |
| TCP 8443 (HTTPS-Alt)|In|LAN|Igen|Ez egy másik HTTPS-port, amelyet a rendszer a helyi webes felhasználói felülethez való csatlakozáskor használ az eszközök kezeléséhez. |
| TCP 445 (SMB)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az NFS-en keresztül csatlakozik. |
| TCP 111 (NFS)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Ez a port a rpcbind/port hozzárendeléshez használatos, és csak akkor szükséges, ha az NFS-en keresztül csatlakozik.  |
