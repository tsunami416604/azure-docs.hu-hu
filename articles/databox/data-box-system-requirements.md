---
title: A Microsoft Azure Data Box rendszerkövetelményei| Microsoft dokumentumok
description: Az Azure Data Box szoftveres és hálózati követelményeinek ismertetése
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259954"
---
# <a name="azure-data-box-system-requirements"></a>Az Azure Data Box rendszerkövetelményei

Ez a cikk ismerteti a fontos rendszerkövetelmények a Microsoft Azure Data Box és az ügyfelek csatlakozik az adatdobozhoz. Azt javasoljuk, hogy alaposan tekintse át az információkat, mielőtt üzembe helyezné az adatdobozt, majd szükség szerint hivatkozzon rá a központi telepítés és az azt követő művelet során.

A rendszerkövetelmények a következők:

* **A Data Boxhoz csatlakozó állomások szoftverkövetelményei** – ismertetik a támogatott platformokat, a helyi webes felhasználói felület böngészőit, az SMB-ügyfeleket, valamint az adatdobozhoz csatlakozni képes állomásokra vonatkozó további követelményeket.
* **A Data Box hálózati követelményei** - tájékoztatást nyújt a Data Box optimális működéséhez szükséges hálózati követelményekről.


## <a name="software-requirements"></a>Szoftverkövetelmények

A szoftverkövetelmények tartalmazzák a támogatott operációs rendszerekre, a helyi webes felhasználói felület támogatott böngészőire és az SMB-ügyfelekre vonatkozó információkat.

### <a name="supported-operating-systems-for-clients"></a>Az ügyfelekhez támogatott operációs rendszerek

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Támogatott fájlrendszerek Linux-ügyfelekszámára

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Támogatott tárolótípusok

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Támogatott webböngészők

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Hálózati követelmények

Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha nem áll rendelkezésre 10 GbE-kapcsolat, az adatok másolásához 1 GbE-s adatkapcsolat használható, de a másolási sebesség hatással van.

### <a name="port-requirements"></a>Kikötői követelmények

Az alábbi táblázat azokat a portokat sorolja fel, amelyeket meg kell nyitni a tűzfalon az SMB- vagy NFS-forgalom engedélyezéséhez. Ebben a *táblában a bejövő* vagy *bejövő* arra az irányra utal, amelyből a bejövő ügyfél hozzáférést kér az eszközhöz. *A* *kimenő* vagy kimenő eszköz arra az irányra utal, amelyben a Data Box eszköz a központi telepítésen kívül, például az internetre való kimenő adatokat küldi.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>További lépések

* [Az Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
