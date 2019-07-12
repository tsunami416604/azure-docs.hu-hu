---
title: A Microsoft Azure Data Box nehéz követelményei |} A Microsoft Docs
description: Ismerje meg a szoftver- és az Azure Data Box nagy hálózati követelményei
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839782"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Az Azure Data Box (nagy erőforrásigényű) rendszerre vonatkozó követelmények

Ez a cikk ismerteti a fontos követelmények az Azure Data Box nehéz eszköz és a kapcsolódó ügyfelek számára az eszközre. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt üzembe helyezése a Data Box (nagy erőforrásigényű), majd tekintse át biztonsági, szükség szerint a telepítési és az ezt követő művelet során.

A rendszer követelményei a következők:

* **A gazdagép csatlakozik a Data Box nehéz szoftverkövetelményei** – ismerteti a támogatott platformok, a helyi webes felhasználói felület böngészők, SMB-kliensek és esetleges további követelményeket, amely képes kapcsolódni a Data Box-gazdagépek.
* **A Data Box nehéz hálózatkezelési követelményei** – a Data Box nehéz eszköz optimális működéséhez a hálózati követelményeket ismerteti.

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

Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. A leggyorsabb másolási megbízhatóbbak két 40-GbE kapcsolatot, amellyel párhuzamos (a csomópontonként egy). Ha nem rendelkezik elérhető 40-GbE, azt javasoljuk, hogy legalább két 10-GbE kapcsolatot (egy csomópontonkénti).

### <a name="port-requirements"></a>Port követelményei

Az alábbi táblázat felsorolja a portot, amelyet meg kell nyitni a tűzfalon az SMB vagy NFS-forgalom engedélyezése. Ebben a táblázatban *a* vagy *bejövő* irányát hivatkozik, mely a bejövő kérések ügyfélelérési az eszközre. *Ki* vagy *kimenő* hivatkozik, amelyben a Data Box nehéz eszköz kívülről, adatokat küld a központi telepítés meghaladja a irány: például kimenő internetkapcsolattal.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>További lépések

* [Az Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
