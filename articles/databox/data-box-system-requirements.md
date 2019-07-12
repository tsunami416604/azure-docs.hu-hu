---
title: A Microsoft Azure Data Box követelményei |} A Microsoft Docs
description: Az Azure Data Box szoftveres és hálózati követelményeinek ismertetése
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839749"
---
# <a name="azure-data-box-system-requirements"></a>Az Azure Data Box rendszerre vonatkozó követelmények

Ez a cikk ismerteti a rendszer fontos szemben támasztott követelmények, a Microsoft Azure Data Box és a Data boxra kívánó ügyfelek számára. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt üzembe helyezése a Data Box, majd tekintse át biztonsági, szükség szerint a telepítési és az ezt követő művelet során.

A rendszer követelményei a következők:

* **A gazdagép csatlakozik a Data Box szoftverkövetelményei** – ismerteti a támogatott platformok, a helyi webes felhasználói felület böngészők, SMB-kliensek és esetleges további követelményeket, amely képes kapcsolódni a Data Box-gazdagépek.
* **A Data Box hálózatkezelési követelményei** – a Data Box optimális működéséhez a hálózati követelményeket ismerteti.


## <a name="software-requirements"></a>Szoftverkövetelmények

A szoftverkövetelményeknek tartalmazza a támogatott operációs rendszerek, a helyi webes felhasználói felület által támogatott böngészők és SMB-ügyfelek az információkat.

### <a name="supported-operating-systems-for-clients"></a>Ügyfelek támogatott operációs rendszerek

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Támogatott Linux-ügyfelek fájlrendszereket

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Támogatott tárolási típus

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Támogatott webböngészők

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Hálózati követelmények

Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. 10-GbE kapcsolatot nem érhető el, ha egy 1-GbE adatkapcsolat másolja az adatokat, de a sebesség érintett példány használható.

### <a name="port-requirements"></a>Port követelményei

Az alábbi táblázat felsorolja a portot, amelyet meg kell nyitni a tűzfalon az SMB vagy NFS-forgalom engedélyezése. Ebben a táblázatban *a* vagy *bejövő* irányát hivatkozik, mely a bejövő kérések ügyfélelérési az eszközre. *Ki* vagy *kimenő* hivatkozik, amelyben a Data Box-eszköz kívülről, adatokat küld a központi telepítés meghaladja a irány: például kimenő internetkapcsolattal.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>További lépések

* [Az Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
