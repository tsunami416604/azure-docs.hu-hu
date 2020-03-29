---
title: Az Azure virtuális gépei áttelepítése felügyelt lemezekre
description: Telepítse át a nem felügyelt lemezek használatával létrehozott Azure virtuális gépeket a felügyelt lemezek használatához.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e8f2753ac9062803a2d6252eca1829cb0b168f02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921351"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Az Azure-beli virtuális gépek áttelepítése felügyelt lemezekre az Azure-ban

Az Azure felügyelt lemezek leegyszerűsíti a tárkezelés azáltal, hogy megszünteti a tárfiókok külön-külön kezelésének szükségességét.  A meglévő Azure-beli virtuális gépek felügyelt lemezekre is áttelepítheti kondi-szolgáltatások, hogy kihasználhassa a virtuális gépek nagyobb megbízhatóságát egy rendelkezésre állási csoportban. Biztosítja, hogy a rendelkezésre állási csoportban lévő különböző virtuális gépek lemezei eléggé elkülönüljenek egymástól, hogy elkerüljék a hibák egypontját. A különböző virtuális gépek lemezeit automatikusan elhelyezi egy rendelkezésre állási csoportban a különböző tárolási méretezési egységekben (bélyegzőkben), amelyek korlátozzák a hardver- és szoftverhibák miatt okozott egyetlen tárolási méretezési egység hibáinak hatását.
Az igényeinek megfelelően négyféle tárolási lehetőség közül választhat. A rendelkezésre álló lemeztípusokról a [Lemeztípus kiválasztása](disks-types.md) című cikkben olvashat.

## <a name="migration-scenarios"></a> Áttelepítési forgatókönyvek

A felügyelt lemezekre a következő esetekben telepítheti át a következő eseteket:

|Forgatókönyv  |Cikk  |
|---------|---------|
|Különálló virtuális gépek és rendelkezésreállási csoportban lévő virtuális gépek konvertálása felügyelt lemezekké     |[Virtuális gépek konvertálása felügyelt lemezek használatára](convert-unmanaged-to-managed-disks.md)         |
|Egyetlen virtuális gép konvertálása klasszikusról erőforrás-kezelőre felügyelt lemezeken     |[Virtuális gép létrehozása klasszikus virtuális merevlemezről](create-vm-specialized-portal.md)         |
|A virtuális hálózat összes virtuális gépének konvertálása klasszikusról erőforrás-kezelőre felügyelt lemezeken     |[IaaS-erőforrások áttelepítése klasszikusról erőforrás-kezelőre,](migration-classic-resource-manager-ps.md) majd [virtuális gép konvertálása nem felügyelt lemezekről felügyelt lemezekre](convert-unmanaged-to-managed-disks.md)         |
|A szabványos nem felügyelt lemezekkel rendelkező virtuális gépek frissítése felügyelt prémium szintű lemezekkel rendelkező virtuális gépekre     | Először [konvertáljon egy Windows-virtuális gépet nem felügyelt lemezekből felügyelt lemezekké.](convert-unmanaged-to-managed-disks.md) Ezután [frissítse a felügyelt lemez tárolási típusát.](convert-disk-storage.md)         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>További lépések

- További információ a [felügyelt lemezekről](managed-disks-overview.md)
- Tekintse át a [felügyelt lemezek díjszabását.](https://azure.microsoft.com/pricing/details/managed-disks/)
