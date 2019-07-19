---
title: A Windows rendszerű virtuális gépek felügyelt lemezének áttekintése Azure Disk Storage | Microsoft Docs
description: Az Azure Managed Disks áttekintése, amely az Azure Windows rendszerű virtuális gépek használatakor kezeli a Storage-fiókokat
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
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "64725813"
---
# <a name="introduction-to-azure-managed-disks"></a>Bevezetés az Azure Managed Disks használatába

Az Azure Managed Disk egy virtuális merevlemez (VHD). Úgy gondolhatja, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizált. Az Azure Managed Disks szolgáltatás blobként tárolódik, amely az Azure-ban egy véletlenszerű IO Storage-objektum. A felügyelt lemezeket nevezzük felügyelt, mivel ez egy absztrakt lap Blobok, blob-tárolók és Azure Storage-fiókok esetében. A felügyelt lemezekkel mindössze annyit kell tennie, hogy kiépíti a lemezt, az Azure pedig gondoskodik a többiről.

Ha kijelöli az Azure Managed Disks használatát a számítási feladatokkal, az Azure létrehozza és felügyeli a lemezt. A lemezek elérhető típusai az SSD-k (előzetes verzió), prémium SSD, standard SSD és a szabványos merevlemez-meghajtók (HDD-k). Az egyes lemezek típusával kapcsolatos további információkért lásd: [IaaS virtuális gépekhez tartozó lemez típusának kiválasztása](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Válassza ki a IaaS virtuális gépekhez tartozó lemez típusát](disks-types.md)