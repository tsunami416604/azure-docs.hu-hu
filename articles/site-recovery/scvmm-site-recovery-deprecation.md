---
title: A Hyper-V és a SCVMM által az Azure-ba felügyelt helyek között a DR. Microsoft Docs
description: A DR a Hyper-V-t használó és a SCVMM által az Azure-ba és az alternatív lehetőségekkel felügyelt helyek közötti, az ügyfél által birtokolt webhelyek közötti közelgő elavult elavulás
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: c126d72720c16d4ba869156e86a6e60110b2c31b
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847481"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>A Hyper-V és a SCVMM által az Azure-ba felügyelt helyek között a DR.

Ez a cikk ismerteti a közelgő elavult tervet, a kapcsolódó következményeket és az ügyfelek számára elérhető alternatív lehetőségeket a Azure Site Recovery által támogatott következő esetekben. 

- DR a SCVMM által felügyelt Hyper-V virtuális gépek között az ügyfél tulajdonában lévő helyek között 
- A SCVMM által az Azure-ba felügyelt Hyper-V virtuális gépek DR.

> [!IMPORTANT]
> Ezek a forgatókönyvek jelenleg elavultnak vannak megjelölve, és az ügyfeleknek a lehető leghamarabb el kell végezniük a Szervizelési lépéseket a környezetük megszakadásának elkerülése érdekében. 
 

## <a name="what-changes-should-you-expect"></a>Milyen módosításokat kell elvárnia?

- November 2019-től kezdődően a rendszer nem engedélyezi az új felhasználók beszállását ezen forgatókönyvek esetén. A **meglévő replikálások és felügyeleti műveletek** , beleértve a feladatátvételt, a feladatátvételi tesztet, a figyelést stb. **nem érintik**.

- Ha a forgatókönyvek elavultak, a következő szempontok lesznek, kivéve, ha az ügyfél a javasolt lépéseket követi.

    - DR. a SCVMM által felügyelt Hyper-V virtuális gépek között az ügyfél által birtokolt helyek között: a replikálások továbbra is működni fognak, mivel a Hyper-V replika mögöttes funkciója továbbra is működni fog, de az ügyfelek nem tekinthetik meg, kezelhetik és nem hajtanak végre semmilyen DR kapcsolódó műveletet. a Azure Portal Azure-beli helyreállítási felületén keresztül. 
    - A SCVMM által az Azure-ba felügyelt Hyper-V virtuális gépek DR. a meglévő replikálások megszakadnak, és az ügyfelek nem tekinthetik meg, kezelhetik és nem hajthatják végre a DR kapcsolódó műveleteit a Azure Site Recoveryon keresztül.


## <a name="recommended-actions-to-be-taken"></a>Végrehajtandó javasolt műveletek

Az alábbi lehetőségek közül az ügyfélnek biztosítania kell, hogy a DR-stratégiájuk ne legyen hatással a forgatókönyv elavulttá váltása után. 

- A [Hyper-V-gazdagépeken található virtuális gépekhez tartozó Dr célként megkezdheti az Azure használatát](hyper-v-azure-tutorial.md).

> [!IMPORTANT]
> Vegye figyelembe, hogy a helyszíni környezet továbbra is rendelkezhet SCVMM, de az ASR-t csak a Hyper-V-gazdagépekre mutató hivatkozásokkal konfigurálhatja.

- Válassza a helyek közötti replikálás folytatását, de használja a mögöttes [Hyper-V replika megoldást](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), de nem fogja tudni kezelni a Dr-konfigurációkat a Azure Portal Azure site Recovery használatával. 


## <a name="next-steps"></a>Következő lépések
Tervezze meg az elavult alkalmazást, és válasszon egy alternatív lehetőséget, amely az infrastruktúra és a vállalat számára legmegfelelőbb. Ha bármilyen kérdése van, kérjük, lépjen kapcsolatba Microsoft ügyfélszolgálata

