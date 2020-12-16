---
title: Virtuális gép és lemez teljesítménye – Linux
description: További információ arról, hogy a virtuális gépek és a csatlakoztatott lemezek hogyan működnek együtt a Linuxon végzett teljesítményhez.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591854"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>Virtuális gép és lemez teljesítménye (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>A virtuális gép nem gyorsítótárazott és gyorsítótárazott korlátai
A Premium Storage-hoz és a Premium Storage-gyorsítótárazáshoz engedélyezett virtuális gépeknek két különböző tárolási sávszélessége van. Tekintsük át például a Standard_D8s_v3 virtuális gépet. A [Dsv3 sorozat](../dv3-dsv3-series.md) és a Standard_D8s_v3 dokumentációja:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Futtasson egy teljesítményteszt-tesztet ezen a virtuális gépen és az i/o-tevékenységet létrehozó lemez-kombináción. Ha szeretné megtudni, hogyan lehet az Azure-on alapuló adatforgalomra vonatkozó teljesítménytesztet megismerni, tekintse [meg az alkalmazás az Azure Disk Storage](disks-benchmarks.md) A benchmarking eszközből láthatja, hogy a virtuális gép és a lemez kombinációja elérheti a 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
