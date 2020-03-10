---
title: Lemezes tárolás – áttekintés
description: Az Azure Managed Disks áttekintése, amely a virtuális gépek használatakor kezelheti a Storage-fiókokat.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: edcde9c5192791e88da57d158bab3e79b171bfc8
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944767"
---
# <a name="introduction-to-azure-managed-disks"></a>Bevezetés az Azure Managed Disks használatába

Az Azure Managed Disks az Azure által felügyelt és az Azure Virtual Machines-t használó, blokk szintű tárolási kötetek. A felügyelt lemezek olyanok, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizált. A felügyelt lemezek esetében mindössze annyit kell tennie, hogy megadja a lemez méretét, a lemez típusát és a lemez kiépítését. A lemez kiépítése után az Azure kezeli a többiet.

A lemezek elérhető típusai a következők: ultra Disks, Premium SSD-meghajtók (SSD), standard SSD-k és standard merevlemez-meghajtók (HDD). Az egyes lemezek típusával kapcsolatos további információkért lásd: [IaaS virtuális gépekhez tartozó lemez típusának kiválasztása](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Válassza ki a IaaS virtuális gépekhez tartozó lemez típusát](disks-types.md)