---
title: Azure-beli virtuális gépek migrálása a Managed Disksba
description: Telepítse át a nem felügyelt lemezekkel létrehozott Azure-beli virtuális gépeket a Storage-fiókokban a Managed Disks használatához.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d88792f50e0e79dd0313694cf979761054551eac
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487524"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Azure-beli virtuális gépek migrálása az Azure-ba való Managed Disks

Az Azure Managed Disks leegyszerűsíti a tárolási felügyeletet, hiszen nem kell külön kezelnie a Storage-fiókokat.  A meglévő Azure-beli virtuális gépeket is áttelepítheti Managed Disks a rendelkezésre állási csoportba tartozó virtuális gépek jobb megbízhatóságának kihasználása érdekében. Biztosítja, hogy a rendelkezésre állási csoportokban lévő különböző virtuális gépek lemezei elég elszigeteltek legyenek egymástól, hogy elkerülje az egyes meghibásodási pontokat. A szolgáltatás automatikusan áthelyezi a különböző virtuális gépek lemezeit egy rendelkezésre állási csoportba a különböző tárolási skálázási egységekben (bélyegzők), ami korlátozza a hardveres és a szoftver meghibásodása miatti egyetlen tárolási skálázási egység meghibásodásának hatását.
Az igények alapján négyféle tárolási lehetőség közül választhat. A rendelkezésre álló lemezek típusairól a [lemez típusának kiválasztása](../disks-types.md) című cikkből tájékozódhat.

## <a name="migration-scenarios"></a>Migrálási forgatókönyvek

A következő helyzetekben telepítheti át Managed Disksre:

|Használati példa  |Cikk  |
|---------|---------|
|Különálló virtuális gépek és rendelkezésreállási csoportban lévő virtuális gépek konvertálása felügyelt lemezekké     |[Virtuális gépek konvertálása felügyelt lemezek használatára](convert-unmanaged-to-managed-disks.md)         |
|Egyetlen virtuális gép konvertálása klasszikusról Resource Managerre felügyelt lemezeken     |[Virtuális gép létrehozása klasszikus VHD-ből](create-vm-specialized-portal.md)         |
|A vNet lévő összes virtuális gép konvertálása a Klasszikusból a Resource Managerbe a felügyelt lemezeken     |[IaaS-erőforrások migrálása a Klasszikusból a Resource Managerbe](../migration-classic-resource-manager-ps.md) , majd [a virtuális gép konvertálása nem felügyelt lemezekről a felügyelt lemezekre](convert-unmanaged-to-managed-disks.md)         |
|Szabványos nem felügyelt lemezekkel rendelkező virtuális gépek frissítése felügyelt prémium szintű lemezekkel     | Először [alakítsa át a Windows rendszerű virtuális gépeket a nem felügyelt lemezekről a felügyelt lemezekre](convert-unmanaged-to-managed-disks.md). Ezután [frissítse a felügyelt lemez tárolási típusát](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>További lépések

- További információ a [Managed Disks](../managed-disks-overview.md)
- Tekintse át a [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks/).