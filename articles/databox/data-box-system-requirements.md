---
title: A Microsoft Azure Data Box követelményei |} A Microsoft Docs
description: Ismerje meg a szoftver- és az Azure Data Box hálózati követelményei
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: f97c6174adf454a031e94942843075c457236575
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982976"
---
# <a name="azure-data-box-system-requirements"></a>Az Azure Data Box rendszerre vonatkozó követelmények

Ez a cikk ismerteti a rendszer fontos szemben támasztott követelmények, a Microsoft Azure Data Box és a Data boxra kívánó ügyfelek számára. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt üzembe helyezése a Data Box, majd tekintse át biztonsági, szükség szerint a telepítési és az ezt követő művelet során.

A rendszer követelményei a következők:

* **A gazdagép csatlakozik a Data Box szoftverkövetelményei** – ismerteti a támogatott platformok, a helyi webes felhasználói felület böngészők, SMB-kliensek és esetleges további követelményeket, amely képes kapcsolódni a Data Box-gazdagépek.
* **A Data Box hálózatkezelési követelményei** – a Data Box optimális működéséhez a hálózati követelményeket ismerteti.


## <a name="software-requirements"></a>Szoftverkövetelmények

A szoftverkövetelményeknek tartalmazza a támogatott operációs rendszerek, a helyi webes felhasználói felület által támogatott böngészők és SMB-ügyfelek az információkat.

### <a name="supported-operating-systems-for-clients"></a>Ügyfelek támogatott operációs rendszerek

Itt van az adatmásolási műveletet keresztül az ügyfelek a Data Box-eszköz csatlakozik a támogatott operációs rendszerek listáját.

| **Operációs rendszer** | **Verziók** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2-BEN <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Támogatott Linux-ügyfelek fájlrendszereket

| **Protokollok** | **Verziók** | 
| --- | --- | 
| SMB |2.x-es és újabb verziók |
| NFS | 4.1 including az összes verzió|

### <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Íme a támogatott tárolási típusokat a Data Box-eszköz listáját.

| **Storage-fiók** | **Megjegyzések** |
| --- | --- |
| Klasszikus | Standard |
| Általános célú  |Standard; a V1 és V2 is támogatja. |
| Blob |Gyakori és ritka elérésű egyaránt támogatottak. |


### <a name="supported-storage-types"></a>Támogatott tárolási típus

Íme a támogatott tárolási típusokat a Data Box-eszköz listáját.

| **Fájlformátum** | **Megjegyzések** |
| --- | --- |
| Az Azure blokkblob | |
| Oldala az Azure blob  | Az adatok igazítva 512 bájt lehet.|
| Azure Files | |


### <a name="supported-web-browsers"></a>Támogatott webböngészők

Íme a helyi webes felhasználói felület támogatott böngészők listája.

| **Böngésző** | **Verziók** | **További követelmények és megjegyzések** |
| --- | --- | --- |
| Google Chrome |Legújabb verziója |A Chrome tesztelése|
| Microsoft Edge |Legújabb verziója | |
| A FireFox | Legújabb verziója | A FireFox tesztelése|
| Internet Explorer |Legújabb verziója |Ha nem tud bejelentkezni, ellenőrizze a cookie-k és a Javascript engedélyezve vannak-e. A Kezelőfelület elérésének engedélyezéséhez vegye fel az eszköz IP-cím **adatvédelmi műveletek** úgy, hogy az eszköz létesíthet-e a cookie-k. |


## <a name="networking-requirements"></a>Hálózati követelmények

Az Adatközpont rendelkeznie kell a nagy sebességű hálózat. Javasoljuk, hogy van legalább 10 GbE kapcsolatot. 10 GbE kapcsolatot nem érhető el, ha 1 GbE adatkapcsolat használható adatmásolásra, de az is hatással van a Másolás sebesség.

## <a name="next-step"></a>Következő lépés

* [Az Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)

