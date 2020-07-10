---
title: Lemezes tárolás – áttekintés
description: Az Azure Managed Disks áttekintése, amely a virtuális gépek használatakor kezelheti a Storage-fiókokat.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d3932047422571399ad0562b2f9f2d19c3e6799b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145306"
---
# <a name="introduction-to-azure-managed-disks"></a>Bevezetés az Azure-beli felügyelt lemezek használatába

Az Azure Managed Disks az Azure által felügyelt és az Azure Virtual Machines-t használó, blokk szintű tárolási kötetek. A felügyelt lemezek olyanok, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizált. A felügyelt lemezek esetében mindössze annyit kell tennie, hogy megadja a lemez méretét, a lemez típusát és a lemez kiépítését. A lemez kiépítése után az Azure kezeli a többiet.

A lemezek elérhető típusai a következők: ultra Disks, Premium SSD-meghajtók (SSD), standard SSD-k és standard merevlemez-meghajtók (HDD). Az egyes lemezek típusával kapcsolatos további információkért lásd: [IaaS virtuális gépekhez tartozó lemez típusának kiválasztása](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [IaaS-alapú virtuális gépek lemeztípusának kiválasztása](disks-types.md)