---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "66416064"
---
* Az átalakításhoz újra kell indítani a virtuális gépet, ezért ütemezze a virtuális gépek migrálását egy meglévő karbantartási időszakra. 

* Az átalakítás nem vonható vissza. 

* Ne feledje, hogy a [virtuálisgép közreműködői](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkörrel rendelkező felhasználók nem módosíthatják a virtuális gép méretét (mivel azok átalakítás előtt). Ennek az az oka, hogy a felügyelt lemezekkel rendelkező virtuális gépekhez a microsoft.Compute/disks/write engedéllyel kell rendelkeznie az operációs rendszer lemezein.

* Mindenképpen tesztelje az átalakítást. Migráljon egy tesztcélú virtuális gépet, mielőtt végrehajtja a migrálást éles környezetben.

* Az átalakítás közben felszabadítja a virtuális gépet. A virtuális gép új IP-címet kap, amikor elindul az átalakítás után. Szükség esetén [hozzárendelhet egy statikus IP-címet](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) a virtuális géphez.

* Tekintse át az Azure virtuálisgép-ügynök minimális verzióját, amely az átalakítási folyamat támogatásához szükséges. Az ügynökverziójának ellenőrzéséről és frissítésről az [Azure-beli virtuálisgép-ügynökök minimális verziótámogatása című témakörben](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) talál további információt.