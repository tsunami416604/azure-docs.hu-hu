---
title: Adattárolás az Azure Network Watcherhoz | Microsoft Docs
description: Ez a cikk segítséget nyújt az Azure Network Watcher szolgáltatás adattárolásának megismeréséhez.
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
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706838"
---
# <a name="data-residency-for-azure-network-watcher"></a>Adattárolás az Azure Network Watcher
A Csatlakozáskezelő (előzetes verzió) szolgáltatás kivételével az Azure Network Watcher nem tárolja az ügyféladatokat.


## <a name="connection-monitor-preview-data-residency"></a>A Csatlakozáskezelő (előzetes verzió) adattárolási szolgáltatás
A Csatlakozáskezelő (előzetes verzió) szolgáltatás az ügyféladatokat tárolja. Ezeket az adatfájlokat a Network Watcher automatikusan tárolja egyetlen régióban. Így a kapcsolat figyelője (előzetes verzió) automatikusan megfelel a régión belüli adattárolási követelményeknek, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net/)megadott követelményeket is.

## <a name="singapore-data-residency"></a>Szingapúri adattárolás

Az Azure-ban az ügyféladatok egyetlen régióban való tárolását lehetővé tevő funkció jelenleg csak a Ázsia és a Csendes-óceáni térség geo Délkelet-ázsiai régiójában (Szingapúr) érhető el. Az összes többi régió esetében az ügyféladatokat a Geo tárolja. További információ: [Adatvédelmi központ](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Következő lépések

* Olvassa el a [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)áttekintése című témakört.
