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
ms.openlocfilehash: 64aa01995460837c820c90010d7c4e3f3d78e6a2
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300231"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>portál

A növekményes Pillanatképek létrehozásához és konfigurálásához a globális Azure Portal a következő hivatkozást kell használnia: [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots). A növekményes pillanatkép létrehozása még nem érhető el a globális Azure Portalban.

1. Jelentkezzen be a [Azure Portalba](https://aka.ms/incrementalsnapshots) a megadott hivatkozással, és navigáljon arra a lemezre, amelyről pillanatképet szeretne készíteni.
1. A lemezen válassza a **pillanatkép létrehozása** lehetőséget.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text=" ":::

1. Válassza ki a használni kívánt erőforráscsoportot, és adjon meg egy nevet.
1. Válassza a **növekményes** lehetőséget, és válassza a **felülvizsgálat + létrehozás** lehetőséget

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text=" ":::

1. Kattintson a **Létrehozás** elemre.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text=" ":::

## <a name="next-steps"></a>További lépések

Ha szeretné megtekinteni a növekményes Pillanatképek különbözeti képességét bemutató mintakód használatát, tekintse meg az [Azure-Managed Disks biztonsági mentések másolása másik régióba a növekményes Pillanatképek különbözeti képességével](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)című témakört.
