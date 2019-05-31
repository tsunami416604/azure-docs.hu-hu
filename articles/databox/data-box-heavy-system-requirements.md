---
title: A Microsoft Azure Data Box nehéz követelményei |} A Microsoft Docs
description: Ismerje meg a szoftver- és az Azure Data Box nagy hálózati követelményei
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: 8cd2f96954cde367eb99d89e89bcf672b53dd590
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247994"
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

## <a name="next-steps"></a>További lépések

* [Az Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
