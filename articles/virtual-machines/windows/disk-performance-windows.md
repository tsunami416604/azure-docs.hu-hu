---
title: Virtuálisgép- és lemezteljesítmény
description: További információ arról, hogy a virtuális gépek és a csatlakoztatott lemezek hogyan működnek együtt a teljesítményhez
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 88cf9c28a9d325d617f4b049015f0cd238a2fb31
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016459"
---
# <a name="virtual-machine-and-disk-performance"></a>Virtuálisgép- és lemezteljesítmény
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>A virtuális gép nem gyorsítótárazott és gyorsítótárazott korlátai
 A Premium Storage-t és a Premium Storage-gyorsítótárazást engedélyező virtuális gépek esetében két különböző tárolási sávszélességre vonatkozó korlát van. Folytassa a Standard_D8s_v3 virtuális gép példájának megtekintésével. A [Dsv3-sorozat](../dv3-dsv3-series.md) dokumentációja és a Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Futtasson egy teljesítményteszt-tesztet ezen a virtuális gépen és a lemeznek az IO-tevékenység létrehozásához szükséges kombinációján [here](disks-benchmarks.md) A benchmarking eszközből láthatja, hogy a virtuális gép és a lemez kombinációja elérheti a 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]