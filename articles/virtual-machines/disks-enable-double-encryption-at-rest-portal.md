---
title: A kettős titkosítás engedélyezése a REST-Azure Portal által felügyelt lemezeken
description: A felügyelt lemez adataihoz a Azure Portal használatával engedélyezze a kettős titkosítást a nyugalmi állapotban.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 3882aae0fb1ecf330917f886555208c3937dd9a5
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358218"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>A Azure Portal használatával engedélyezze a kettős titkosítást a felügyelt lemezeken.

Azure Disk Storage támogatja a kettős titkosítást a felügyelt lemezeken. A kettős titkosítással kapcsolatos fogalmakról, valamint a felügyelt lemezes titkosítási típusokról további információt a lemezes titkosításról szóló cikkben, a [Rest-titkosítást](disk-encryption.md#double-encryption-at-rest) ismertető szakaszban talál.

## <a name="getting-started"></a>Első lépések

1. Jelentkezzen be az [Azure Portalra](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > A Azure Portal eléréséhez a [megadott hivatkozást](https://aka.ms/diskencryptionupdates) kell használnia. A kettős titkosítás jelenleg nem látható a nyilvános Azure Portal a hivatkozás használata nélkül.

1. Keresse meg és válassza ki a **lemezes titkosítási csoportokat**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Képernyőfelvétel a fő Azure Portalról a lemezes titkosítási csoportok ki vannak emelve a keresősáv számára.":::

1. Válassza a **+ Hozzáadás** lehetőséget.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Képernyőkép a lemez titkosítási készlet paneljéről, a + Hozzáadás elem ki van emelve.":::

1. Válasszon egyet a támogatott régiók közül.
1. A **titkosítás típusa** beállításnál válassza a **dupla titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal** lehetőséget.

    > [!NOTE]
    > Ha egy adott titkosítási típussal rendelkező lemezes titkosítási készletet hoz létre, az nem módosítható. Ha más titkosítási típust szeretne használni, létre kell hoznia egy új lemezes titkosítási készletet.

1. Adja meg a hátralévő információt.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Képernyőfelvétel: a lemez titkosítási készletének létrehozási panelje, a régiók és a kettős titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal kiemelve.":::

1. Válasszon ki egy Azure Key Vault és egy kulcsot, vagy hozzon létre egy újat, ha szükséges.

    > [!NOTE]
    > Ha Key Vault példányt hoz létre, engedélyeznie kell a Soft delete és a kiürítési védelmet. Ezek a beállítások akkor kötelezőek, ha Key Vault használ a felügyelt lemezek titkosításához, és a véletlen törlés miatt megóvja az adatok elvesztését.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Képernyőkép a Key Vault létrehozási paneljéről.":::

1. Kattintson a **Létrehozás** gombra.
1. Navigáljon a létrehozott lemezes titkosítási készlethez, és válassza ki a megjelenített hibát. Ezzel a beállítással beállíthatja, hogy a lemez titkosítása működjön.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Képernyőfelvétel: a lemez titkosítási készlete hibaüzenetet jelenít meg, a hiba szövege: egy lemez, lemezkép vagy pillanatkép ehhez a lemezes titkosítási készlethez való hozzárendeléséhez engedélyeket kell adnia a kulcstartónak.":::

    Egy értesítésnek a beugró és sikeres. Ezzel lehetővé teszi a lemezes titkosítási készlet használatát a kulcstartóval.
    
    ![A Key Vault sikeres engedélyének és szerepkör-hozzárendelésének képernyőképe.](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. Navigáljon a lemezre.
1. Válassza a **titkosítás** lehetőséget.
1. A **titkosítás típusa** beállításnál válassza a **dupla titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal** lehetőséget.
1. Válassza ki a lemez titkosítási készletét.
1. Válassza a **Mentés** lehetőséget.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Képernyőkép a felügyelt lemez titkosítási paneljéről, a fenti titkosítási típus ki van emelve.":::

A felügyelt lemezen már engedélyezte a kettős titkosítást a REST-ben.


## <a name="next-steps"></a>Következő lépések

- [Azure PowerShell – ügyfél által felügyelt kulcsok engedélyezése kiszolgálóoldali titkosítással felügyelt lemezekkel](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager sablon mintái](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Ügyfelek által felügyelt kulcsok engedélyezése kiszolgálóoldali titkosítással – példák](./linux/disks-enable-customer-managed-keys-cli.md#examples)