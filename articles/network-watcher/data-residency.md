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
ms.openlocfilehash: a6be3a7fd19b43bd1b18af05d0dbfaf5053fb181
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682961"
---
# <a name="data-residency-for-azure-network-watcher"></a>Adattárolás az Azure Network Watcher
A Csatlakozáskezelő (előzetes verzió) szolgáltatás kivételével az Azure Network Watcher nem tárolja az ügyféladatokat.


## <a name="connection-monitor-preview-data-residency"></a>A Csatlakozáskezelő (előzetes verzió) adattárolási szolgáltatás
A Csatlakozáskezelő (előzetes verzió) szolgáltatás az ügyféladatokat tárolja. Ezeket az adatfájlokat a Network Watcher automatikusan tárolja egyetlen régióban. Így a kapcsolat figyelője (előzetes verzió) automatikusan megfelel a régión belüli adattárolási követelményeknek, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net/)megadott követelményeket is.

## <a name="data-residency"></a>Adattárolási hely
Az Azure-ban az ügyféladatok egyetlen régióban való tárolását lehetővé tevő funkció jelenleg csak az Ázsia és a Csendes-óceáni térség geo és Dél-Brazília (Sao Paulo állam) régiójában, a brazíliai geo régióban érhető el. Az összes többi régió esetében az ügyféladatokat a Geo tárolja. További információ: [Adatvédelmi központ](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Következő lépések

* Olvassa el a [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)áttekintése című témakört.
