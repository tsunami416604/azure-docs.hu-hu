---
title: A Microsoft Azure Data Box követelményei |} A Microsoft Docs
description: Az Azure Data Box szoftveres és hálózati követelményeinek ismertetése
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 469d553d60eb7c5cdfac3bc16f1fb479bd84cb70
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886206"
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
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Támogatott Linux-ügyfelek fájlrendszereket

| **Protokollok** | **Verziók** | 
| --- | --- | 
| SMB |2.x-es és újabb verziók |
| NFS | 4.1 including az összes verzió|

### <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Íme a támogatott tárfiókok és a tárolási típusokat a Data Box-eszköz listáját. Összes különböző típusú tárfiókok és a teljes képességek teljes listáját lásd: [a tárfiókok típusai](/azure/storage/common/storage-account-overview.md#types-of-storage-accounts).

| **Storage-fiók / támogatott tárolási típusok** | **Blokkblob** |**Lapblob*** |**Az Azure Files** |**Megjegyzések**|
| --- | --- | -- | -- | -- |
| Klasszikus Standard | I | I | I |
| Általános célú v1 Standard  | I | I | I | Gyakori és ritka elérésű egyaránt támogatottak.|
| Általános célú v1-támogatás  |  | I| | |
| Általános célú v2 Standard  | I | I | I | Gyakori és ritka elérésű egyaránt támogatottak.|
| General-purpose v2 Premium  |  |I | | |
| A BLOB storage Standard |I | | |Gyakori és ritka elérésű egyaránt támogatottak. |

\* *– Lapblobok feltöltött adatok igazítva, például VHD-k, 512 bájt kell lennie.*

>[!NOTE]
> Az Azure Data Lake Storage 2. generációs gyűjtések fiókok nem támogatottak.


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
| FireFox | Legújabb verziója | A FireFox tesztelése|
| Internet Explorer |Legújabb verziója |Ha nem tud bejelentkezni, ellenőrizze a cookie-k és a Javascript engedélyezve vannak-e. A Kezelőfelület elérésének engedélyezéséhez vegye fel az eszköz IP-cím **adatvédelmi műveletek** úgy, hogy az eszköz létesíthet-e a cookie-k. |


## <a name="networking-requirements"></a>Hálózati követelmények

Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. 10-GbE kapcsolatot nem érhető el, ha egy 1-GbE adatkapcsolat másolja az adatokat, de a sebesség érintett példány használható.

## <a name="next-step"></a>Következő lépés

* [Az Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)

