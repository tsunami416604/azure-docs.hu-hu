---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67839766"
---
| Port nem.| Be vagy ki | Port hatóköre| Kötelező| Jegyzetek |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|In|LAN|Yes|Ezzel a porttal csatlakozhat a Data Box blog Storage REST API-khoz HTTP-n keresztül. Ha nem csatlakozik a REST API-khoz, a rendszer automatikusan átirányítja a helyi webes felhasználói felületre a 8443-es kapcsolaton keresztül. |
| TCP 443 (HTTPS)|In|LAN|Yes|Ez a port a Data Box blog Storage REST API-k HTTPS-kapcsolaton keresztüli elérésére szolgál. Ha nem csatlakozik a REST API-khoz, a rendszer automatikusan átirányítja a helyi webes felhasználói felületre a 8443-es kapcsolaton keresztül. |
| TCP 8443 (HTTPS-Alt)|In|LAN|Yes|Ez egy másik HTTPS-port, amelyet a rendszer a helyi webes felhasználói felülethez való csatlakozáskor használ az eszközök kezeléséhez. |
| TCP 445 (SMB)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Erre a portra csak akkor van szükség, ha az NFS-en keresztül csatlakozik. |
| TCP 111 (NFS)|Ki/be|LAN|Bizonyos esetekben<br>Lásd: megjegyzések|Ez a port a rpcbind/port hozzárendeléshez használatos, és csak akkor szükséges, ha az NFS-en keresztül csatlakozik.  |
