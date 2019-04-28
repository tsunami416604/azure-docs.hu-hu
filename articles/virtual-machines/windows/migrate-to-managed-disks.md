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
ms.date: 01/03/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12cd1caa4cb96dbd5862776589d4a34aeb294ca1
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763759"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Az Azure virtuális gépek migrálása felügyelt lemezekre az Azure-ban

Az Azure Managed Disks leegyszerűsíti a tárolókezelési külön felügyelnie tárfiókokat eltávolításával.  Is áttelepítheti a meglévő Azure virtuális gépek számára, hogy nagyobb megbízhatóságot, rendelkezésre állási csoportban lévő virtuális gépek felügyelt lemezekre. Ez biztosítja, hogy a rendelkezésre állási csoport különböző virtuális lemezek hibaérzékeny pont hibák elkerülése érdekében hibapontok. A rendelkezésre állási csoport a különböző tárolási skálázási egységeket (stampek), amely korlátozza a egyetlen skálázási egységek hibáival oka a hardver és szoftver hibák különböző virtuális lemezek automatikusan helyezi.
Igényei alapján választhat a négy típusú tárolási lehetőségeket. A rendelkezésre álló lemeztípusok kapcsolatos további információkért tekintse meg ezt a cikket [lemez típusának kiválasztása](disks-types.md)

## <a name="migrate-scenarios"></a>Forgatókönyvek áttelepítése

Áttelepítheti a Managed Disks a következő esetekben:

| **Áttelepítése...**                                            | **Dokumentáció-hivatkozás**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Különálló virtuális gépek és rendelkezésreállási csoportban lévő virtuális gépek konvertálása felügyelt lemezekké   | [Felügyelt lemezek használata virtuális gépek konvertálása](convert-unmanaged-to-managed-disks.md) |
| Egyetlen virtuális gép klasszikusról Resource Manager-alapú környezetbe felügyelt lemezeken     | [Virtuális gép létrehozása a klasszikus virtuális merevlemezből](create-vm-specialized-portal.md)  | 
| Virtuális hálózaton lévő összes virtuális gép klasszikusról Resource Manager-alapú környezetbe felügyelt lemezeken     | [IaaS-erőforrások áttelepítése klasszikusról Resource Manager](migration-classic-resource-manager-ps.md) , majd [virtuális gép átalakítása nem felügyeltről felügyelt lemezessé](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Managed Disks](managed-disks-overview.md)
- Tekintse át a [a felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).
