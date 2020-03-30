---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3ba5d74aa245fbcd9d43f2b4398387d7f59e202c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299504"
---
### <a name="portal"></a>Portál

Ha a lemezekhez ügyfél által felügyelt kulcsokat állít be, akkor az erőforrásokat adott sorrendben kell létrehoznia, ha először teszi meg. Először létre kell hoznia és be kell állítania egy Azure Key Vaultot.

#### <a name="setting-up-your-azure-key-vault"></a>Az Azure Key Vault beállítása

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com/) és keressen a Key Vaultra
1. Keresse meg és válassza a **Key Vaults lehetőséget.**

    [![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Az Azure key vault, a lemeztitkosítási készlet, a virtuális gép, a lemezek és a pillanatképek mind ugyanabban a régióban kell lennie, és előfizetés a telepítés sikeres legyen.

1. Új kulcstartó létrehozásához válassza a **+Hozzáadás** lehetőséget.
1. Új erőforráscsoport létrehozása
1. Adja meg a kulcstartó nevét, válasszon ki egy régiót, és válasszon ki egy tarifacsomagot.
1. Válassza **a Véleményezés + Létrehozás**lehetőséget, ellenőrizze a beállításokat, majd válassza a **Létrehozás gombot.**

    ![Képernyőkép az Azure Key Vault létrehozási élményéről. A létrehozott értékek megjelenítése](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Miután a key vault telepítése befejeződik, válassza ki azt.
1. Válassza a **Billentyűk lehetőséget** a **Beállítások csoportban.**
1. **Létrehozás/importálás** kiválasztása

    ![Képernyőkép a Key Vault erőforrás-beállítási ablaktáblájáról. A létrehozás/importálás gomb megjelenítése a beállításokon belül.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Hagyja, hogy a **kulcstípus** **rsa** és **RSA kulcsméret** beállítása **is 2080-ra**van állítva.
1. Töltse ki a fennmaradó kijelöléseket tetszés szerint, majd válassza a **Létrehozás gombot.**

    ![Képernyőkép a létrehozás/importálás után megjelenő billentyűpanel létrehozásáról](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>A lemeztitkosítási készlet beállítása

Lemeztitkosítási készletek létrehozásához és konfigurálásához a https://aka.ms/diskencryptionsetskövetkező hivatkozást kell használnia: . Ha a Microsoft Azure Government régióban van, akkor [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)a következő hivatkozást kell használnia: . A lemeztitkosítási készlet létrehozása még nem érhető el a globális Azure Portalon.

1. Nyissa meg a régiónak megfelelő lemeztitkosítási készletkapcsolatot:

    Nyilvános régiók:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Az Azure Government régiói:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Válassza **a +Hozzáadás lehetőséget.**

    ![Képernyőkép a lemeztitkosítási portál főképernyőjéről. A Hozzáadás gomb kiemelése](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Válassza ki az erőforráscsoportot, nevezze el a titkosítási készletet, és válassza ki ugyanazt a régiót, mint a kulcstartó.
1. Válassza a **Kulcstartó és kulcs**lehetőséget.
1. Válassza ki a korábban létrehozott kulcstartót és kulcsot, valamint a verziót.
1. Nyomja **le a Kijelölés gombot.**
1. Válassza **a Véleményezés + Létrehozás,** majd a **Létrehozás lehetőséget.**

    ![Képernyőkép a lemeztitkosítás létrehozása panelről. Az előfizetés, az erőforráscsoport, a lemeztitkosítási készlet neve, a régió és a kulcstartó + kulcsválasztó megjelenítése.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Nyissa meg a lemeztitkosítási készletet, miután befejezte a létrehozást, és válassza ki a megjelenő riasztást.

    ![Képernyőkép a riasztási előugró ablakról: "Lemez, lemezkép vagy pillanatkép lemeztitkosítási készlethez való társításához engedélyeket kell adnia a key vaultnak". Válassza ki ezt a riasztást a folytatáshoz](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Két értesítés nek meg kell jelennie, és sikeresnek kell lennie. Ezzel lehetővé teszi, hogy a kulcstartóhoz beállított lemeztitkosítási készletet használja.

![Képernyőkép a kulcstartó sikeres engedély- és szerepkör-hozzárendeléséről.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

Most, hogy létrehozta és beállította a kulcstartót és a lemeztitkosítási készletet, üzembe helyezhet egy virtuális számítógépet a titkosítás használatával.
A virtuális gép központi telepítési folyamata hasonló a szabványos központi telepítési folyamathoz, az egyetlen különbség az, hogy a virtuális gép üzembe helyezéséhez ugyanabban a régióban, mint a többi erőforrás, és úgy dönt, hogy egy ügyfél által felügyelt kulcsot használ.

1. Nyissa meg a régiónak megfelelő lemeztitkosítási készletkapcsolatot:

    Nyilvános régiók:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Az Azure Government régiói:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. Keressen **meg virtuális gépeket,** és válassza **a + Hozzáadás** lehetőséget a virtuális gép létrehozásához.
1. Az **Alapszintű** lapon válassza ki ugyanazt a régiót, mint a lemeztitkosítási készlet és az Azure Key Vault.
1. Tetszés szerint töltse ki az **Alap** lap egyéb értékeit.

    ![Képernyőkép a virtuális gép létrehozásáról, a régió értéke kiemelve.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. A **Lemezek** lapon válassza **a Titkosítás inaktívként egy ügyfél által felügyelt kulccsal**lehetőséget.
1. Válassza ki a lemeztitkosítási készletet a **Lemeztitkosításkészlet** legördülő menüben.
1. A fennmaradó beállításokat tetszés szerint elkell készítenie.

    ![Képernyőkép a virtuális gép létrehozásának élményéről, a lemezek panel. A lemeztitkosítási készlet legördülő menüjének kiemelésével.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Engedélyezés meglévő lemezen

A lemeztitkosítás meglévő lemezeken történő kezeléséhez és konfigurálásához https://aka.ms/diskencryptionsetsa következő hivatkozást kell használnia: . Az ügyfél által felügyelt kulcsok engedélyezése a meglévő lemezeken még nem érhető el a globális Azure Portalon.

> [!CAUTION]
> A virtuális géphez csatlakoztatott lemezeken a lemeztitkosítás engedélyezése megköveteli a virtuális gép leállítását.

1. Nyissa meg a régiónak megfelelő lemeztitkosítási készletkapcsolatot:

    Nyilvános régiók:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Az Azure Government régiói:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Keresse meg a virtuális gép, amely ugyanabban a régióban van, mint a lemez titkosítási készletek.
1. Nyissa meg a virtuális gép, és válassza **leállítás**.

    ![Képernyőkép a példa virtuális gép fő átfedéséről. Kiemelt Leállítás gomb](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Miután a virtuális gép leállítása befejeződött, válassza **a Lemezek** lehetőséget, majd jelölje ki a titkosítani kívánt lemezt.

    ![Képernyőkép a példa virtuális gép, a Lemezek panel nyitva. Az operációs rendszer lemeze ki van emelve, példaként a kiválasztható lemez.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Válassza **a Titkosítás** lehetőséget, és válassza az **inaktív titkosítás tikuláns kulcsot egy ügyfél által kezelt kulccsal,** majd válassza ki a lemeztitkosítási készletet a legördülő listában.
1. Kattintson a **Mentés** gombra.

    ![Képernyőkép a példa operációsrendszer-lemezről. A titkosítási panel nyitott, az ügyfél által felügyelt kulccsal inaktív titkosítás, valamint az Azure Key Vault példa van kiválasztva. A kijelölés után a Mentés gomb lesz kiválasztva.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Ismételje meg ezt a folyamatot a titkosítani kívánt virtuális géphez csatlakoztatott egyéb lemezek esetében.
1. Ha a lemezek befejezik az ügyfél által felügyelt kulcsokra való váltást, ha nincs más csatlakoztatott lemez, amelyet titkosítani szeretne, elindíthatja a virtuális gépét.