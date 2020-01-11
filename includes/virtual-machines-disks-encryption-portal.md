---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 165a115f35810c1bfe463a571affb2e44ed74205
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893732"
---
### <a name="portal"></a>Portál

Az ügyfél által felügyelt kulcsok beállítása a lemezekhez megköveteli, hogy az erőforrásokat egy adott sorrendben hozzon létre, ha az első alkalommal végzi el. Először létre kell hoznia és be kell állítania egy Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>A Azure Key Vault beállítása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és keressen rá Key Vault
1. Keresse meg és válassza ki a **kulcstárolókat**.

![SSE-Key-Vault-Portal-Search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)

> [!IMPORTANT]
> Az Azure Key Vault, a lemezes titkosítási készlet, a virtuális gépek, a lemezek és a pillanatképek mindegyikének ugyanabban a régióban kell lennie, és az előfizetés a sikeres telepítéshez.

1. Új Key Vault létrehozásához válassza a **+ Hozzáadás** lehetőséget.
1. Új erőforráscsoport létrehozása
1. Adja meg a kulcstároló nevét, válasszon ki egy régiót, és válasszon ki egy díjszabási szintet.
1. Válassza a **felülvizsgálat + létrehozás**lehetőséget, ellenőrizze a beállításokat, majd kattintson a **Létrehozás**gombra.

![SSE-Create-a-Key-Vault. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Ha a Key Vault üzembe helyezése befejeződött, válassza ki.
1. A **Beállítások**területen válassza a **kulcsok** lehetőséget.
1. **Előállítási/importálási** lehetőség kiválasztása

![SSE-Key-Vault-Generate-Settings. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Tartsa meg mindkét **kulcs típusát** az **RSA** és az **RSA-kulcs méretének** beállítása **2080**értékre.
1. Adja meg a többi kijelölést, majd válassza a **Létrehozás**lehetőséget.

![SSE-Create-a-Key-Generate. png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>A lemez titkosítási készletének beállítása

A lemezes titkosítási készletek létrehozásához és konfigurálásához a következő hivatkozást kell használnia: https://aka.ms/diskencryptionsets. A lemez titkosítási készletének létrehozása még nem érhető el a nyilvános Azure Portalban.

1. Nyissa meg a [lemez titkosítási készletek hivatkozását](https://aka.ms/diskencryptionsets).
1. Válassza a **+ Hozzáadás**lehetőséget.

![SSE-Create-Disk-Encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Válassza ki az erőforráscsoportot, nevezze el a titkosítási csoportot, és válassza ki ugyanazt a régiót, mint a kulcstartó.
1. Válassza **a Key Vault és a kulcs**elemet.
1. Válassza ki a korábban létrehozott Key vaultot és kulcsot, valamint a verziót.
1. Kattintson a **kiválasztás**gombra.
1. Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás**lehetőséget.

![SSE-Disk-ENC-set-Blade-Key. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Nyissa meg a lemez titkosítási készletet a létrehozás befejeződése után, és válassza ki a felugró riasztást.

![SSE-Disk-ENC-Alert-fix. png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Két értesítésnek kell megjelennie, és sikeresnek kell lennie. Ezzel lehetővé teszi, hogy a készletet a kulcstartóval használja.

![Disk-ENC-Notification-success. png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

Most, hogy létrehozta és beállította a kulcstartót és a lemez titkosítási készletét, a titkosítás használatával üzembe helyezhet egy virtuális gépet.
A virtuális gép telepítési folyamata hasonló a normál telepítési folyamathoz, az egyetlen különbség, hogy a virtuális gépet ugyanabban a régióban kell telepíteni, mint a többi erőforrást, és Ön úgy dönt, hogy az ügyfél által felügyelt kulcsot használja.

1. Keressen rá **Virtual Machines** , és válassza a **+ Hozzáadás** elemet a virtuális gép létrehozásához.
1. Az **alapszintű** lapon válassza ki ugyanazt a régiót, mint a lemez titkosítási készletét, és Azure Key Vault.
1. Adja meg a többi értéket az **Alap** lapon, ahogy szeretné.

![SSE-Create-a-VM-Region. png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. A **lemezek** lapon válassza a titkosítás inaktív állapotban lehetőséget az **ügyfél által felügyelt kulcs**lehetőséggel.
1. Válassza ki a lemez titkosítási készletét a **lemez titkosítási készlete** legördülő menüből.
1. Végezze el a többi kijelölést úgy, ahogy szeretné.

![SSE-Create-VM-Select-CMK-encryption-set. png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)