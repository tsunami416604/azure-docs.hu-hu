---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839766"
---
| Port nem.| És leskálázása | Port hatókör| Szükséges| Megjegyzések |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80-AS (HTTP)|A|LAN|Igen|Ezt a portot használja HTTP-n keresztül kapcsolódni a Data Box-Blog storage REST API-k. Nem csatlakozik a REST API-kat, ha ez automatikusan átirányítja a helyi webes felhasználói felület több mint 8443. |
| TCP 443 (HTTPS)|A|LAN|Igen|Ezt a portot használja HTTPS-kapcsolaton keresztül csatlakozni a Data Box-Blog storage REST API-k. Nem csatlakozik a REST API-kat, ha ez automatikusan átirányítja a helyi webes felhasználói felület több mint 8443. |
| TCP 8443 (HTTPS-Alt)|A|LAN|Igen|Ez egy másik portot a HTTPS-hez és az eszközfelügyelethez a helyi webes felhasználói felületen való csatlakozáskor használja. |
| A TCP 445-ÖS (SMB)|Out/In|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha az SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|Out/In|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|A port szükség, csak akkor, ha NFS-n keresztül csatlakozik. |
| TCP 111 (NFS)|Out/In|LAN|Bizonyos esetekben<br>Lásd: a megjegyzések|Ez a port rpcbind és portját leképezést, és csak akkor szükséges, ha, NFS-n keresztül csatlakozik.  |
