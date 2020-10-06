---
title: Microsoft Azure Data Box rendszerkövetelményei | Microsoft Docs
description: Ismerje meg a Azure Data Box fontos rendszerkövetelményeit, valamint a Data Boxhoz csatlakozó ügyfeleket.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767874"
---
# <a name="azure-data-box-system-requirements"></a>Rendszerkövetelmények Azure Data Box

Ez a cikk a Microsoft Azure Data Box és a Data Boxhoz kapcsolódó ügyfelek fontos rendszerkövetelményeit ismerteti. Javasoljuk, hogy körültekintően tekintse át az információkat, mielőtt üzembe helyezi a Data Box, majd az üzembe helyezés és a művelet során szükség szerint tekintse át.

A rendszerkövetelmények a következők:

* A **szoftverre vonatkozó követelmények:** A Data Boxhoz csatlakozó gazdagépek a helyi webes felhasználói felület támogatott operációs rendszereit, a fájlátviteli protokollokat, a Storage-fiókokat, a tárolási típusokat és a böngészőket ismertetik.
* **Hálózati követelmények:** A Data Box esetében a Data Box optimális működéséhez szükséges hálózati kapcsolatok és portokra vonatkozó követelmények ismertetése.


## <a name="software-requirements"></a>Szoftverkövetelmények

A szoftverre vonatkozó követelmények közé tartoznak a támogatott operációs rendszerek, a fájlátviteli protokollok, a Storage-fiókok, a tárolási típusok és a helyi webes felhasználói felület böngészők.

### <a name="supported-operating-systems-for-clients"></a>Az ügyfelekhez támogatott operációs rendszerek

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Az ügyfelek által támogatott fájlátviteli protokollok

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Az Data Box-megosztásokhoz való kapcsolódás nem támogatott az exportálási rendeléseken a REST használatával. 

### <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Támogatott tárolótípusok

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Támogatott webböngészők

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Hálózati követelmények

Az adatközpontnak nagy sebességű hálózattal kell rendelkeznie. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha egy 10 GbE-kapcsolat nem érhető el, a rendszer egy GbE adatkapcsolatot használ az adatmásoláshoz, de a másolási sebességekre is hatással van.

### <a name="port-requirements"></a>Portra vonatkozó követelmények

A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon, hogy engedélyezze az SMB-vagy NFS-forgalmat. Ebben *a táblázatban a (* *bejövő*) arra utal, hogy a bejövő ügyfél milyen irányba kér hozzáférést az eszközhöz. Az *out* (vagy a *kimenő*) arra az irányra vonatkozik, amelyben a Data Box eszköz külsőleg küld adatokat, az üzembe helyezésen túl: például az internet felé.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Következő lépések

* [A Azure Data Box üzembe helyezése](data-box-deploy-ordered.md)
