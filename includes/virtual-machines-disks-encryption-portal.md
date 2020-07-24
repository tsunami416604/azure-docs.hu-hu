---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a8702f14bfb9aed5a3595203dde1d86ea56a151b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133847"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

Most, hogy létrehozta és beállította a kulcstartót és a lemez titkosítási készletét, a titkosítás használatával üzembe helyezhet egy virtuális gépet.
A virtuális gép telepítési folyamata hasonló a normál telepítési folyamathoz, az egyetlen különbség, hogy a virtuális gépet ugyanabban a régióban kell telepíteni, mint a többi erőforrást, és Ön úgy dönt, hogy az ügyfél által felügyelt kulcsot használja.

1. Keressen rá **Virtual Machines** , és válassza a **+ Hozzáadás** elemet a virtuális gép létrehozásához.
1. Az **alapszintű** panelen válassza ki ugyanazt a régiót, mint a lemez titkosítási készletét, és Azure Key Vault.
1. Adja meg a többi értéket az **alapszintű** panelen, ahogy szeretné.

    ![Képernyőfelvétel a virtuális gépek létrehozásának élményéről, a régió értékének kiemelésével.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. A **lemezek** panelen válassza a titkosítás inaktív állapotban lehetőséget **egy ügyfél által felügyelt kulccsal**.
1. Válassza ki a lemez titkosítási készletét a **lemez titkosítási készlete** legördülő menüből.
1. Végezze el a többi kijelölést úgy, ahogy szeretné.

    ![Képernyőkép a virtuális gép létrehozási felületéről, a lemezek panelről. A lemez titkosítási készletének legördülő menüje kiemelve.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Engedélyezés meglévő lemezen

> [!CAUTION]
> A virtuális géphez csatlakoztatott lemezeken a lemez titkosításának engedélyezéséhez le kell állítania a virtuális gépet.
    
1. Navigáljon egy olyan virtuális gépre, amely ugyanabban a régióban található, mint az egyik lemezes titkosítási csoport.
1. Nyissa meg a virtuális gépet, és válassza a **Leállítás**lehetőséget.

    ![Képernyőkép a példaként szolgáló virtuális gép fő átfedéséről. A Leállítás gomb kiemelve](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. A virtuális gép leállítása után válassza a **lemezek** lehetőséget, majd válassza ki a titkosítani kívánt lemezt.

    ![Képernyőkép a példaként szolgáló virtuális gépről a lemezek panel megnyitásával. Az operációsrendszer-lemez ki van emelve, mint például a kiválasztható lemez.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Válassza a **titkosítás** lehetőséget, majd válassza a titkosítás lehetőséget az **ügyfél által felügyelt kulccsal** , majd válassza ki a lemez titkosítási készletét a legördülő listából.
1. Válassza a **Mentés** lehetőséget.

    ![Képernyőkép a példa operációsrendszer-lemezéről. A titkosítás panel meg van nyitva, és az ügyfél által felügyelt kulccsal történő titkosítás van kiválasztva, valamint a példa Azure Key Vault. A beállítások megadása után a Mentés gomb van kiválasztva.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Ismételje meg ezt a folyamatot a titkosítani kívánt virtuális géphez csatlakoztatott bármely más lemez esetében.
1. Ha a lemezek befejezik a váltást az ügyfél által felügyelt kulcsokra, ha nincsenek olyan csatlakoztatott lemezek, amelyeket titkosítani szeretne, elindíthatja a virtuális gépet.
