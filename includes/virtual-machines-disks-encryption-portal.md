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
ms.openlocfilehash: b8073240bdda38757a5e4feee66c9f54746966c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632047"
---
### <a name="portal"></a>Portál

Az ügyfél által felügyelt kulcsok beállítása a lemezekhez megköveteli, hogy az erőforrásokat egy adott sorrendben hozzon létre, ha az első alkalommal végzi el. Először létre kell hoznia és be kell állítania egy Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>A Azure Key Vault beállítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg és válassza ki a **kulcstárolókat**.

    [![SSE-Key-Vault-Portal-Search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Az Azure Key Vault, a lemezes titkosítási készlet, a virtuális gépek, a lemezek és a pillanatképek mindegyikének ugyanabban a régióban kell lennie, és az előfizetés a sikeres telepítéshez.

1. Új Key Vault létrehozásához válassza a **+ Hozzáadás** lehetőséget.
1. Hozzon létre egy új erőforráscsoportot.
1. Adja meg a kulcstároló nevét, válasszon ki egy régiót, és válasszon ki egy díjszabási szintet.
1. Válassza a **felülvizsgálat + létrehozás**lehetőséget, ellenőrizze a beállításokat, majd kattintson a **Létrehozás**gombra.

    ![Képernyőkép a Azure Key Vault létrehozási felületéről. A létrehozott megadott értékek megjelenítése](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Ha a Key Vault üzembe helyezése befejeződött, válassza ki.
1. A **Beállítások**területen válassza a **kulcsok** lehetőséget.
1. Válassza a **készítés/importálás**lehetőséget.

    ![Képernyőkép a Key Vault erőforrás-beállítások panelről. Megjeleníti a beállításokban a generált/importálás gombot.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Hagyja a **kulcs típusát** az **RSA** és az **RSA-kulcs méretének** értékeként **2048**-re.
1. Adja meg a többi kijelölést, majd válassza a **Létrehozás**lehetőséget.

    ![A kulcs létrehozása/importálása gomb kiválasztásakor megjelenő, a Key (létrehozás/importálás) gombra kattintva megjelenő képernyőkép](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>A lemez titkosítási készletének beállítása

1. Keresse meg a **lemezes titkosítási csoportokat** , és jelölje ki.
1. A **lemez titkosítási készletek** paneljén válassza a **+ Hozzáadás**lehetőséget.

    ![Képernyőkép a lemez titkosítási portálján Főképernyőről. A Hozzáadás gomb kiemelése](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Válassza ki az erőforráscsoportot, nevezze el a titkosítási csoportot, és válassza ki ugyanazt a régiót, mint a kulcstartó.
1. Válassza **a Key Vault és a kulcs**elemet.
1. Válassza ki a korábban létrehozott Key vaultot és kulcsot, valamint a verziót.
1. Kattintson a **kiválasztás**gombra.
1. Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás**lehetőséget.

    ![Képernyőkép a lemez titkosítás-létrehozási paneljéről. Az előfizetés, az erőforráscsoport, a lemez titkosítási készlet neve, a régió és a Key Vault + Key választó megjelenítése.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Nyissa meg a lemez titkosítási készletet a létrehozás befejeződése után, és válassza ki a felugró riasztást.

    ![Képernyőkép a riasztás előugró ablakáról: "lemez, lemezkép vagy pillanatkép lemezes titkosítási készlettel való hozzárendeléséhez engedélyeket kell biztosítania a kulcstartó számára". A folytatáshoz válassza ki ezt a riasztást](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Két értesítésnek kell megjelennie, és sikeresnek kell lennie. Ezzel lehetővé teszi a lemezes titkosítási készlet használatát a kulcstartóval.

![A Key Vault sikeres engedélyének és szerepkör-hozzárendelésének képernyőképe.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

Most, hogy létrehozta és beállította a kulcstartót és a lemez titkosítási készletét, a titkosítás használatával üzembe helyezhet egy virtuális gépet.
A virtuális gép telepítési folyamata hasonló a normál telepítési folyamathoz, az egyetlen különbség, hogy a virtuális gépet ugyanabban a régióban kell telepíteni, mint a többi erőforrást, és Ön úgy dönt, hogy az ügyfél által felügyelt kulcsot használja.

1. Keressen rá **Virtual Machines** , és válassza a **+ Hozzáadás** elemet a virtuális gép létrehozásához.
1. Az **alapszintű** lapon válassza ki ugyanazt a régiót, mint a lemez titkosítási készletét, és Azure Key Vault.
1. Adja meg a többi értéket az **Alap** lapon, ahogy szeretné.

    ![Képernyőfelvétel a virtuális gépek létrehozásának élményéről, a régió értékének kiemelésével.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. A **lemezek** lapon válassza a titkosítás inaktív állapotban lehetőséget **egy ügyfél által felügyelt kulccsal**.
1. Válassza ki a lemez titkosítási készletét a **lemez titkosítási készlete** legördülő menüből.
1. Végezze el a többi kijelölést úgy, ahogy szeretné.

    ![Képernyőkép a virtuális gép létrehozási felületéről, a lemezek panelről. A lemez titkosítási készletének legördülő menüje kiemelve.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Engedélyezés meglévő lemezen

> [!CAUTION]
> A virtuális géphez csatlakoztatott lemezeken a lemez titkosításának engedélyezéséhez le kell állítania a virtuális gépet.
    
1. Navigáljon egy olyan virtuális gépre, amely ugyanabban a régióban található, mint az egyik lemezes titkosítási csoport.
1. Nyissa meg a virtuális gépet, és válassza a **Leállítás**lehetőséget.

    ![Képernyőkép a példaként szolgáló virtuális gép fő átfedéséről. A Leállítás gomb kiemelve](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. A virtuális gép leállítása után válassza a **lemezek** lehetőséget, majd válassza ki a titkosítani kívánt lemezt.

    ![Képernyőkép a példaként szolgáló virtuális gépről a lemezek panel megnyitásával. Az operációsrendszer-lemez ki van emelve, mint például a kiválasztható lemez.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Válassza a **titkosítás** lehetőséget, majd válassza a titkosítás lehetőséget az **ügyfél által felügyelt kulccsal** , majd válassza ki a lemez titkosítási készletét a legördülő listából.
1. Kattintson a **Mentés** gombra.

    ![Képernyőkép a példa operációsrendszer-lemezéről. A titkosítás panel meg van nyitva, és az ügyfél által felügyelt kulccsal történő titkosítás van kiválasztva, valamint a példa Azure Key Vault. A beállítások megadása után a Mentés gomb van kiválasztva.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Ismételje meg ezt a folyamatot a titkosítani kívánt virtuális géphez csatlakoztatott bármely más lemez esetében.
1. Ha a lemezek befejezik a váltást az ügyfél által felügyelt kulcsokra, ha nincsenek olyan csatlakoztatott lemezek, amelyeket titkosítani szeretne, elindíthatja a virtuális gépet.
