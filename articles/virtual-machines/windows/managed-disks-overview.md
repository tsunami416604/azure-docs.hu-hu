---
title: Az Azure Disk Storage áttekintése Windows virtuális gépekhez
description: Az Azure által felügyelt lemezek áttekintése, amelyek az Azure Windows virtuális gépek használatakor kezelik a tárfiókokat
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b2c2c1289a40d63b2f396ee59000c3aedb14c3d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75460045"
---
# <a name="introduction-to-azure-managed-disks"></a>Az Azure-beli felügyelt lemezek bemutatása

Az Azure által felügyelt lemezek blokkszintű tárolókötetek, amelyeket az Azure kezel, és amelyeket az Azure virtuális gépekkel használnak. A felügyelt lemezek olyanok, mint egy fizikai lemez egy helyszíni kiszolgálón, de virtualizáltak. A felügyelt lemezek esetén mindössze meg kell adnia a lemez méretét, a lemez típusát, és ki kell építenie a lemezt. Miután kiépítetta a lemezt, az Azure kezeli a többit.

A rendelkezésre álló lemeztípusok az ultralemezek, a prémium szintű SSD-meghajtók(SSD), a szabványos SSD-k és a szabványos merevlemez-meghajtók (HDD). Az egyes lemeztípusokról az [IaaS virtuális gépek lemeztípusának kiválasztása](disks-types.md)című témakörben talál további információt.

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [IaaS-alapú virtuális gépek lemeztípusának kiválasztása](disks-types.md)
