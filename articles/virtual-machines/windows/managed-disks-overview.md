---
title: Az Azure Disk Storage Windows virtuális gépek felügyelt lemez – áttekintés |} A Microsoft Docs
description: Áttekintés az Azure managed disks használatát, amely kezeli a storage-fiókok, az Azure Windows virtuális gépek használatakor
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fb1ee8556935b141dfee6a18c96ecafb476aa584
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725813"
---
# <a name="introduction-to-azure-managed-disks"></a>Bevezetés az Azure-ba, felügyelt lemezek

Egy Azure által felügyelt lemez egy virtuális merevlemezt (VHD). Felfoghatók úgy, mint egy helyszíni kiszolgálón, de, virtualizált fizikai lemezt. Azure managed disksbe tárolódnak, a lapblobokhoz, amelyek a rendszer egy véletlenszerű i/o-tárolóobjektum az Azure-ban. Felügyelt lemez "felügyelt" absztrakciós, mert a lapblobok, blobtárolók és az Azure storage-fiókok nevezzük. Felügyelt lemezekkel rendelkező mindössze meg kell rendelkezni a lemez, és az Azure gondoskodik a többiről.

Amikor kiválaszt Azure által felügyelt lemezek a számítási feladatokat, az Azure létrehozza és felügyeli a lemezt Ön helyett. A lemezek a rendelkezésre álló típusok a következők: Ultranagy tartós állapot-meghajtók (SSD) (előzetes verzió), a prémium szintű SSD, a Standard SSD és a szabványos merevlemez-meghajtókon (HDD). Minden egyes lemeztípus kapcsolatos további információkért lásd: [válassza ki a lemez típusát az IaaS virtuális gépek](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Válassza ki a lemez típusát IaaS virtuális gépekhez](disks-types.md)