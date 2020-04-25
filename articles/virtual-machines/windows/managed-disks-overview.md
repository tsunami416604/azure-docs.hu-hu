---
title: Azure Disk Storage áttekintése
description: Azure-beli felügyelt lemezek áttekintése, amelyek kezelik az Azure-beli virtuális gépek használatakor használt Storage-fiókokat
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4b3a66ce00582a3ef05e77f65acdc46fbde8ce72
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148083"
---
# <a name="introduction-to-azure-managed-disks"></a>Az Azure-beli felügyelt lemezek bemutatása

Az Azure Managed Disks az Azure által felügyelt és az Azure Virtual Machines-t használó, blokk szintű tárolási kötetek. A felügyelt lemezek olyanok, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizált. A felügyelt lemezek esetében mindössze annyit kell tennie, hogy megadja a lemez méretét, a lemez típusát és a lemez kiépítését. A lemez kiépítése után az Azure kezeli a többiet.

A lemezek elérhető típusai a következők: ultra Disks, Premium SSD-meghajtók (SSD), standard SSD-k és standard merevlemez-meghajtók (HDD). Az egyes lemezek típusával kapcsolatos további információkért lásd: [IaaS virtuális gépekhez tartozó lemez típusának kiválasztása](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [IaaS-alapú virtuális gépek lemeztípusának kiválasztása](disks-types.md)
