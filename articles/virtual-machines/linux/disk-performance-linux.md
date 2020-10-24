---
title: Virtuálisgép- és lemezteljesítmény
description: További információ arról, hogy a virtuális gépek és a csatlakoztatott lemezek hogyan működnek együtt a teljesítményhez
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 092368bb66784a00d5116da0b6be6513f8ebb261
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518065"
---
# <a name="virtual-machine-and-disk-performance"></a>Virtuálisgép- és lemezteljesítmény
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>A virtuális gép nem gyorsítótárazott és gyorsítótárazott korlátai
A Premium Storage-hoz és a Premium Storage-gyorsítótárazáshoz engedélyezett virtuális gépeknek két különböző tárolási sávszélessége van. Tekintsük át például a Standard_D8s_v3 virtuális gépet. A [Dsv3 sorozat](../dv3-dsv3-series.md) és a Standard_D8s_v3 dokumentációja:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Futtasson egy teljesítményteszt-tesztet ezen a virtuális gépen és az i/o-tevékenységet létrehozó lemez-kombináción. Ha szeretné megtudni, hogyan lehet az Azure-on alapuló adatforgalomra vonatkozó teljesítménytesztet megismerni, tekintse [meg az alkalmazás az Azure Disk Storage](disks-benchmarks.md) A benchmarking eszközből láthatja, hogy a virtuális gép és a lemez kombinációja elérheti a 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
