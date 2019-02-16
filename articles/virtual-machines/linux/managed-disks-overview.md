---
title: Az Azure Disk Storage felügyelt lemez áttekintése Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: Áttekintés az Azure managed disks használatát, amely kezeli a storage-fiókok, Linux rendszerű virtuális gépek használatakor
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/11/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d8394121019338b53c87f7229d0ccf52fbdf8f21
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327027"
---
# <a name="introduction-to-azure-managed-disks"></a>Bevezetés az Azure-ba, felügyelt lemezek

Egy Azure által felügyelt lemez egy virtuális merevlemezt (VHD). Felfoghatók úgy, mint egy helyszíni kiszolgálón, de, virtualizált fizikai lemezt. Azure managed disksbe tárolódnak, a lapblobokhoz, amelyek a rendszer egy véletlenszerű i/o-tárolóobjektum az Azure-ban. Felügyelt lemez "felügyelt" absztrakciós, mert a lapblobok, blobtárolók és az Azure storage-fiókok nevezzük. Felügyelt lemezekkel rendelkező mindössze meg kell rendelkezni a lemez, és az Azure gondoskodik a többiről.

Amikor kiválaszt Azure által felügyelt lemezek a számítási feladatokat, az Azure létrehozza és felügyeli a lemezt Ön helyett. A rendelkezésre álló típusú lemezek ultranagy lemezek (előzetes verzió), premium tartós állapotú meghajtókhoz (SSD), standard SSD és a szabványos merevlemez-(HDD) meghajtók. Minden egyes lemeztípus kapcsolatos további információkért lásd: [válassza ki a lemez típusát az IaaS virtuális gépek](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Válassza ki a lemez típusát IaaS virtuális gépekhez](disks-types.md)
