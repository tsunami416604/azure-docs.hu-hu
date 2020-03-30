---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8f2e2081ee31e2ce7b21677bd261ed7eda705334
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485955"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portál

Növekményes pillanatképek létrehozásához és konfigurálásához a [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)globális Azure Portalon az alábbi hivatkozást kell használnia: . Növekményes pillanatkép létrehozása még nem érhető el a globális Azure Portalon.

1. Jelentkezzen be az [Azure Portalon](https://aka.ms/incrementalsnapshots) a megadott hivatkozással, és keresse meg a pillanatképet a lemezre.
1. A lemezen válassza a **Pillanatkép létrehozása lehetőséget.**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Screenshot. A lemez penge, a ** + Create snapshot ** kiemelve, mivel ez az, amit ki kell választani.":::

1. Jelölje ki a használni kívánt erőforráscsoportot, és adjon meg egy nevet.
1. Válassza a **Növekményes** lehetőséget, és válassza **a Véleményezés + Létrehozás lehetőséget.**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Screenshot. Hozzon létre egy pillanatkép-panelt, töltse ki a nevet, és válassza a növekményes lehetőséget, majd hozza létre a pillanatképet.":::

1. Válassza a **Létrehozás lehetőséget**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Screenshot. A pillanatkép érvényesítési lapja, erősítse meg a beállításokat, majd hozza létre a pillanatképet.":::

## <a name="next-steps"></a>További lépések

Ha a .NET használatával a növekményes pillanatképek különbözeti képességét bemutató mintakódot szeretne látni, olvassa el az [Azure felügyelt lemezek biztonsági másolata egy másik régióba a növekményes pillanatképek különbözeti képességével rendelkező témakört.](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
