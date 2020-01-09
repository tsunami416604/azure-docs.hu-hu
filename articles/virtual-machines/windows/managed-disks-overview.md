---
title: A Windows rendszerű virtuális gépek Azure Disk Storage áttekintése
description: Az Azure Managed Disks áttekintése, amely az Azure Windows rendszerű virtuális gépek használatakor a Storage-fiókokat kezeli
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b2c2c1289a40d63b2f396ee59000c3aedb14c3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460045"
---
# <a name="introduction-to-azure-managed-disks"></a>Bevezetés az Azure Managed Disks használatába

Az Azure Managed Disks az Azure által felügyelt és az Azure Virtual Machines-t használó, blokk szintű tárolási kötetek. A felügyelt lemezek olyanok, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizált. A felügyelt lemezek esetében mindössze annyit kell tennie, hogy megadja a lemez méretét, a lemez típusát és a lemez kiépítését. A lemez kiépítése után az Azure kezeli a többiet.

A lemezek elérhető típusai a következők: ultra Disks, Premium SSD-meghajtók (SSD), standard SSD-k és standard merevlemez-meghajtók (HDD). Az egyes lemezek típusával kapcsolatos további információkért lásd: [IaaS virtuális gépekhez tartozó lemez típusának kiválasztása](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Válassza ki a IaaS virtuális gépekhez tartozó lemez típusát](disks-types.md)
