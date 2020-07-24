---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c5ea31217bd3088ec123281b36f8578b08ea25b2
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136237"
---
## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Támogatott régiók

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a virtuális gépekhez vagy virtuálisgép-méretezési csoportokhoz titkosítást lehessen használni a gazdagépen, be kell szereznie a funkciót az előfizetésében. Küldjön egy e-mailt a következő címre: encryptionAtHost@microsoft. a com és az előfizetési azonosítók segítségével szerezze be a funkciót az előfizetések számára.

Jelentkezzen be a Azure Portalba a [megadott hivatkozás](https://aka.ms/diskencryptionupdates)használatával.

> [!IMPORTANT]
> A Azure Portal eléréséhez a [megadott hivatkozást](https://aka.ms/diskencryptionupdates) kell használnia. A gazdagépen lévő titkosítás jelenleg nem látható a nyilvános Azure Portal a hivatkozás használata nélkül.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Azure Key Vault és lemezes titkosítási csoport létrehozása

Ha a szolgáltatás engedélyezve van, be kell állítania egy Azure Key Vault és egy lemezes titkosítási készletet, ha még nem tette meg.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

Egy új virtuális gépet kell üzembe helyeznie, hogy engedélyezze a titkosítást a gazdagépen, a meglévő virtuális gépeken nem engedélyezhető.

1. Keressen rá **Virtual Machines** , és válassza a **+ Hozzáadás** elemet a virtuális gép létrehozásához.
1. Hozzon létre egy új virtuális gépet, válasszon ki egy megfelelő régiót és egy támogatott VM-méretet.
1. Adja meg a többi értéket az **alapszintű** panelen, majd lépjen a **lemezek** panelre.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Példa szövege":::

1. A **lemezek** panelen válassza az **Igen** lehetőséget a **titkosításhoz a gazdagépen**.
1. Végezze el a többi kijelölést úgy, ahogy szeretné.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Példa szövege":::

1. Fejezze be a virtuális gép telepítési folyamatát, és válassza ki a környezetnek megfelelő beállításokat.

Már telepített egy virtuális gépet, amelyen engedélyezve van a titkosítás a gazdagépen, az összes társított lemez titkosítása a gazdagépen titkosítva történik.