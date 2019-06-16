---
title: Az Azure virtuális gépek migrálása felügyelt lemezekre |} A Microsoft Docs
description: A storage-fiókok nem felügyelt lemezek használatával felügyelt lemezeket használni létrehozott Azure virtuális gépeket áttelepíteni.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7a390c5231f715ce778c0cc267211b8bfb5934d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418399"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Az Azure virtuális gépek migrálása felügyelt lemezekre az Azure-ban

Az Azure Managed Disks leegyszerűsíti a tárolókezelési külön felügyelnie tárfiókokat eltávolításával.  Is áttelepítheti a meglévő Azure virtuális gépek számára, hogy nagyobb megbízhatóságot, rendelkezésre állási csoportban lévő virtuális gépek felügyelt lemezekre. Ez biztosítja, hogy a rendelkezésre állási csoport különböző virtuális lemezek hibaérzékeny pont hibák elkerülése érdekében hibapontok. A rendelkezésre állási csoport a különböző tárolási skálázási egységeket (stampek), amely korlátozza a egyetlen skálázási egységek hibáival oka a hardver és szoftver hibák különböző virtuális lemezek automatikusan helyezi.
Igényei alapján választhat a négy típusú tárolási lehetőségeket. A rendelkezésre álló lemeztípusok kapcsolatos további információkért tekintse meg ezt a cikket [lemez típusának kiválasztása](disks-types.md)

## <a name="migration-scenarios"></a>Áttelepítési forgatókönyvek

Áttelepítheti a Managed Disks a következő esetekben:

|Forgatókönyv  |Cikk  |
|---------|---------|
|Különálló virtuális gépek és rendelkezésreállási csoportban lévő virtuális gépek konvertálása felügyelt lemezekké     |[Felügyelt lemezek használata virtuális gépek konvertálása](convert-unmanaged-to-managed-disks.md)         |
|Egyetlen virtuális gép átalakítása Kapcsolatcsoportok módosítása klasszikusról Resource Manager felügyelt lemezeken     |[Virtuális gép létrehozása a klasszikus virtuális merevlemezből](create-vm-specialized-portal.md)         |
|Konvertálás egy virtuális hálózaton lévő összes virtuális gép a klasszikusból Resource Manager felügyelt lemezeken     |[IaaS-erőforrások áttelepítése klasszikusról Resource Manager](migration-classic-resource-manager-ps.md) , majd [virtuális gép átalakítása nem felügyeltről felügyelt lemezessé](convert-unmanaged-to-managed-disks.md)         |
|Standard szintű nem felügyelt lemezek virtuális gépekhez prémium felügyelt lemezekkel rendelkező virtuális gépek frissítése     | Először [Windows virtuális gép átalakítása nem felügyeltről felügyelt felügyelt lemezekre](convert-unmanaged-to-managed-disks.md). Ezután [frissítse a tárolás típusát, egy felügyelt lemez](convert-disk-storage.md).         |

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Managed Disks](managed-disks-overview.md)
- Tekintse át a [a felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).
