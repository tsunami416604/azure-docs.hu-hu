---
title: Microsoft Azure Data Box rendszerkövetelményei | Microsoft Docs
description: Az Azure Data Box szoftveres és hálózati követelményeinek ismertetése
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380580"
---
# <a name="azure-data-box-system-requirements"></a>Rendszerkövetelmények Azure Data Box

Ez a cikk a Microsoft Azure Data Box fontos rendszerkövetelményeit, valamint a Data Boxhoz csatlakozó ügyfeleket ismerteti. Javasoljuk, hogy körültekintően tekintse át az információkat a Data Box üzembe helyezése előtt, majd az üzembe helyezés és az azt követő művelet során szükség szerint küldje vissza.

A rendszerkövetelmények a következők:

* A **Data boxhoz csatlakozó gazdagépek szoftveres követelményei** – ismerteti a támogatott platformokat, a helyi webes felhasználói felület, az SMB-ügyfelek és a Data boxhoz csatlakozni képes gazdagépek további követelményeit.
* **A Data Box hálózati követelményei** – információt nyújt a Data Box optimális működéséhez szükséges hálózati követelményekről.


## <a name="software-requirements"></a>Szoftverkövetelmények

A szoftverre vonatkozó követelmények tartalmazzák a támogatott operációs rendszerekkel, a helyi webes felhasználói felület és az SMB-ügyfelek által támogatott böngészőkkel kapcsolatos információkat.

### <a name="supported-operating-systems-for-clients"></a>Az ügyfelekhez támogatott operációs rendszerek

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Támogatott fájlrendszerek Linux rendszerű ügyfelek számára

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Támogatott tárolási típus

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Támogatott webböngészők

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Hálózati követelmények

Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha egy 10 GbE-kapcsolat nem érhető el, az adatmásoláshoz egy 1 GbE adatkapcsolat használható, de a másolási sebességek is hatással vannak.

### <a name="port-requirements"></a>Portra vonatkozó követelmények

A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon, hogy engedélyezze az SMB-vagy NFS-forgalmat. A (z) vagy a *bejövő* tábla a bejövő ügyfél által az eszközhöz való hozzáférést kérő irányt jelöli. A *kimenő vagy kimenő* *állapot arra utal* , hogy a Data Box-eszköz hogyan küldi el az adatokat külsőleg, az üzembe helyezésen kívül: például az internet felé.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Következő lépések

* [A Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
