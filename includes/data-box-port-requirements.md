---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839766"
---
| Port száma| Be- vagy ki | Port hatóköre| Kötelező| Megjegyzések |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|A|LAN|Igen|Ez a port a Data Box Blog tároló REST API-k HTTP-n keresztül való csatlakoztatására szolgál. Ha nem csatlakozik a REST API-khoz, ez automatikusan átirányítja a helyi webes felhasználói felületre a 8443-as felett. |
| TCP 443 (HTTPS)|A|LAN|Igen|Ez a port a Data Box Blog rest API-k HTTPS-en keresztüli tárolásához való csatlakozásra szolgál. Ha nem csatlakozik a REST API-khoz, ez automatikusan átirányítja a helyi webes felhasználói felületre a 8443-as felett. |
| TCP 8443 (HTTPS-Alt)|A|LAN|Igen|Ez egy alternatív port https és akkor használatos, amikor csatlakozik a helyi webes felhasználói felület eszközkezelés. |
| TCP 445 (SMB)|Ki/be|LAN|Egyes esetekben<br>Lásd a jegyzeteket|Ez a port csak akkor szükséges, ha SMB-n keresztül csatlakozik. |
| TCP 2049 (NFS)|Ki/be|LAN|Egyes esetekben<br>Lásd a jegyzeteket|Ez a port csak akkor szükséges, ha NFS-en keresztül csatlakozik. |
| TCP 111 (NFS)|Ki/be|LAN|Egyes esetekben<br>Lásd a jegyzeteket|Ez a port az rpcbind/port leképezéshez használatos, és csak akkor szükséges, ha NFS-en keresztül csatlakozik.  |
