---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177026"
---
## <a name="supported-regions"></a>Támogatott régiók

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Első lépések

1. Jelentkezzen be az [Azure Portalra](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > A Azure Portal eléréséhez a [megadott hivatkozást](https://aka.ms/diskencryptionupdates) kell használnia. A kettős titkosítás jelenleg nem látható a nyilvános Azure Portal a hivatkozás használata nélkül.

1. Keresse meg és válassza ki a **lemezes titkosítási csoportokat**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Képernyőfelvétel a fő Azure Portalról a lemezes titkosítási csoportok ki vannak emelve a keresősáv számára.":::

1. Válassza a **+ Hozzáadás** lehetőséget.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Képernyőkép a lemez titkosítási készlet paneljéről, a + Hozzáadás elem ki van emelve.":::

1. Válasszon egyet a támogatott régiók közül.
1. A **titkosítás típusa**beállításnál válassza a **dupla titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal**lehetőséget.

    > [!NOTE]
    > Ha egy adott titkosítási típussal rendelkező lemezes titkosítási készletet hoz létre, az nem módosítható. Ha más titkosítási típust szeretne használni, létre kell hoznia egy új lemezes titkosítási készletet.

1. Adja meg a hátralévő információt.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Képernyőfelvétel: a lemez titkosítási készletének létrehozási panelje, a régiók és a kettős titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal kiemelve.":::

1. Válasszon ki egy Azure Key Vault és egy kulcsot, vagy hozzon létre egy újat, ha szükséges.

    > [!NOTE]
    > Ha Key Vault példányt hoz létre, engedélyeznie kell a Soft delete és a kiürítési védelmet. Ezek a beállítások akkor kötelezőek, ha Key Vault használ a felügyelt lemezek titkosításához, és a véletlen törlés miatt megóvja az adatok elvesztését.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Képernyőkép a Key Vault létrehozási paneljéről.":::

1. Válassza a **Létrehozás** lehetőséget.
1. Navigáljon a létrehozott lemezes titkosítási készlethez, és válassza ki a megjelenített hibát. Ezzel a beállítással beállíthatja, hogy a lemez titkosítása működjön.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Képernyőfelvétel: a lemez titkosítási készlete hibaüzenetet jelenít meg, a hiba szövege: egy lemez, lemezkép vagy pillanatkép ehhez a lemezes titkosítási készlethez való hozzárendeléséhez engedélyeket kell adnia a kulcstartónak.":::

    Egy értesítésnek a beugró és sikeres. Ezzel lehetővé teszi a lemezes titkosítási készlet használatát a kulcstartóval.
    
    ![A Key Vault sikeres engedélyének és szerepkör-hozzárendelésének képernyőképe.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Navigáljon a lemezre.
1. Válassza a **titkosítás**lehetőséget.
1. A **titkosítás típusa**beállításnál válassza a **dupla titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal**lehetőséget.
1. Válassza ki a lemez titkosítási készletét.
1. Válassza a **Mentés**lehetőséget.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Képernyőkép a felügyelt lemez titkosítási paneljéről, a fenti titkosítási típus ki van emelve.":::

A felügyelt lemezen már engedélyezte a kettős titkosítást a REST-ben.
