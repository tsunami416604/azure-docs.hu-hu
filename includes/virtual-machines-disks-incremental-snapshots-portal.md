---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80628431"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portál


1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és navigáljon ahhoz a lemezhez, amelyről pillanatképet szeretne készíteni.
1. A lemezen válassza a **pillanatkép létrehozása** lehetőséget.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Képernyőkép. A lemez paneljén, a * * + pillanatkép létrehozása * * elem kiemelve, ahogy ezt ki kell választania.":::

1. Válassza ki a használni kívánt erőforráscsoportot, és adjon meg egy nevet.
1. Válassza a **növekményes** lehetőséget, és válassza a **felülvizsgálat + létrehozás** lehetőséget

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Képernyőkép. A lemez paneljén, a * * + pillanatkép létrehozása * * elem kiemelve, ahogy ezt ki kell választania.":::

1. Kattintson a **Létrehozás** elemre.

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Képernyőkép. A lemez paneljén, a * * + pillanatkép létrehozása * * elem kiemelve, ahogy ezt ki kell választania.":::

## <a name="next-steps"></a>További lépések

Ha szeretné megtekinteni a növekményes Pillanatképek különbözeti képességét bemutató mintakód használatát, tekintse meg az [Azure-Managed Disks biztonsági mentések másolása másik régióba a növekményes Pillanatképek különbözeti képességével](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)című témakört.
