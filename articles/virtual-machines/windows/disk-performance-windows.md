---
title: A virtuális gép és a lemez teljesítménye – Windows
description: További információ arról, hogy a virtuális gépek és a csatlakoztatott lemezek hogyan működnek együtt a Windows teljesítményével.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584119"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Virtuális gép és lemez teljesítménye (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>A virtuális gép nem gyorsítótárazott és gyorsítótárazott korlátai
 A Premium Storage-t és a Premium Storage-gyorsítótárazást engedélyező virtuális gépek esetében két különböző tárolási sávszélességre vonatkozó korlát van. Folytassa a Standard_D8s_v3 virtuális gép példájának megtekintésével. A [Dsv3-sorozat](../dv3-dsv3-series.md) dokumentációja és a Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Futtasson egy teljesítményteszt-tesztet ezen a virtuális gépen és a lemeznek az IO-tevékenység létrehozásához szükséges kombinációján [](disks-benchmarks.md) A benchmarking eszközből láthatja, hogy a virtuális gép és a lemez kombinációja elérheti a 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]