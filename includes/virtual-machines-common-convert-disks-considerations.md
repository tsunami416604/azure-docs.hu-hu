---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 091e4dc0759ce1b227349d5d1b6b08b9eda66ee0
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84237665"
---
* Az átalakításhoz újra kell indítani a virtuális gépet, ezért ütemezze a virtuális gépek migrálását egy meglévő karbantartási időszakra. 

* Az átalakítás nem vonható vissza. 

* Vegye figyelembe, hogy a virtuálisgép-közreműködő szerepkörrel rendelkező felhasználók nem változtathatják meg a [virtuális gép](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) méretét (ahogy a konverziót előkészítik). Ennek az az oka, hogy a felügyelt lemezekkel rendelkező virtuális gépeken a felhasználónak rendelkeznie kell a Microsoft. számítás/lemezek/írási engedéllyel az operációsrendszer-lemezeken.

* Mindenképpen tesztelje az átalakítást. Migráljon egy tesztcélú virtuális gépet, mielőtt végrehajtja a migrálást éles környezetben.

* Az átalakítás közben felszabadítja a virtuális gépet. A virtuális gép új IP-címet kap, amikor elindul az átalakítás után. Szükség esetén [hozzárendelhet egy statikus IP-címet](../articles/virtual-network/public-ip-addresses.md) a virtuális géphez.

* Tekintse át az Azure-beli virtuálisgép-ügynöknek az átalakítási folyamat támogatásához szükséges minimális verzióját. Az ügynök verziójának vizsgálatával és frissítésével kapcsolatos információkért lásd: virtuálisgép [-ügynökök minimális verziójának támogatása az Azure-ban](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)