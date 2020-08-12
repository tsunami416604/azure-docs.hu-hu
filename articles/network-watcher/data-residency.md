---
title: Adattárolás az Azure Network Watcherhoz | Microsoft Docs
description: A Network Watcher szolgáltatás adattárolásának megismerése
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117835"
---
# <a name="data-residency-for-azure-network-watcher"></a>Adattárolás az Azure Network Watcher
Az Azure Network Watcher a Csatlakozáskezelő (előzetes verzió) szolgáltatás kivételével nem tárolja az ügyféladatokat.


## <a name="connection-monitor-preview-data-residency"></a>A Csatlakozáskezelő (előzetes verzió) adattárolási szolgáltatás
A *Csatlakozáskezelő (előzetes verzió)* szolgáltatás az ügyféladatokat tárolja. Ezeket az adatfájlokat a Network Watcher automatikusan tárolja egyetlen régióban. Így a *kapcsolat figyelője (előzetes verzió)* automatikusan megfelel a régión belüli adattárolási követelményeknek, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net/)megadott követelményeket is.

## <a name="singapore-data-residency"></a>Szingapúri adattárolás

Az Azure-ban az ügyféladatok egyetlen régióban való tárolását lehetővé tevő funkció jelenleg csak a Ázsia és a Csendes-óceáni térség geo Délkelet-ázsiai régiójában (Szingapúr) érhető el. Az összes többi régió esetében az ügyféladatokat a Geo tárolja. További információ: [megbízhatósági központ](https://azuredatacentermap.azurewebsites.net/).

> [!Note]
> **Korábbi replikálás** Az ügyfeleknek lehetősége van arra, hogy a végfelhasználói IP-címeket a Network Watcher-példánnyal tárolják, így Network Watcher a végfelhasználói IP-címekkel kapcsolatos elérhetőségi, késési és hálózati topológiai változásokat is megfigyelheti. Ezeket a végfelhasználói IP-címeket ügyféladatokként lehet besorolni. 2020. július 15-én a Network Watcher egyetlen régióban tárolta ezeket az adatmennyiséget. (Az ügyféladatok már nem replikálódnak a HK szolgáltatásba.) Ezek az adathalmazok már nem replikálódnak a HK-re. A vásárlói adatok és a titkosítása inaktív állapotban van.
> 
> Ha az ügyféladatok egy harmadik fél számára elérhetővé válnak, az lehetővé tenné a harmadik fél számára, hogy megismerje az IP-címet, de nem engedélyezi a harmadik fél számára az IP-címhez tartozó számítógép vagy eszköz hozzáférését. Network Watcher úgy véli, hogy egyetlen harmadik fél sem fér hozzá ezen ügyféladatok HK-ben.

## <a name="next-steps"></a>További lépések

* Olvassa el a [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)áttekintése című témakört.
