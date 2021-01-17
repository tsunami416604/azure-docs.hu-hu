---
title: Virtuálisgép- és lemezteljesítmény
description: További információ arról, hogy a virtuális gépek és a csatlakoztatott lemezek hogyan működnek együtt a teljesítményhez.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5e9f6ecc733eccf317e3013752ee2f5b0586ea78
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540639"
---
# <a name="virtual-machine-and-disk-performance"></a>Virtuálisgép- és lemezteljesítmény
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>A virtuális gép nem gyorsítótárazott és gyorsítótárazott korlátai
A Premium Storage-hoz és a Premium Storage-gyorsítótárazáshoz engedélyezett virtuális gépeknek két különböző tárolási sávszélessége van. Tekintsük át például a Standard_D8s_v3 virtuális gépet. A [Dsv3 sorozat](dv3-dsv3-series.md) és a Standard_D8s_v3 dokumentációja:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

Futtasson egy teljesítményteszt-tesztet ezen a virtuális gépen és az i/o-tevékenységet létrehozó lemez-kombináción. Ha szeretné megtudni, hogyan lehet az Azure-on alapuló adatforgalomra vonatkozó teljesítménytesztet megismerni, tekintse [meg az alkalmazás az Azure Disk Storage](disks-benchmarks.md) A benchmarking eszközből láthatja, hogy a virtuális gép és a lemez kombinációja elérheti a 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-3.md)]
