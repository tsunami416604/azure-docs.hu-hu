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
ms.openlocfilehash: 972223815810917684f35c4e99f04e1ab5b882c8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952120"
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

    - DR. a SCVMM által felügyelt Hyper-V virtuális gépek között az ügyfél által birtokolt helyek között: a replikálások továbbra is működni fognak, mivel a Hyper-V replika mögöttes funkciója továbbra is működni fog, de az ügyfelek nem tudják megtekinteni, kezelni vagy elvégezni a ny DR-vel kapcsolatos műveleteit a Azure Portal Azure-beli helyreállítási felületén keresztül. 
    - A SCVMM által az Azure-ba felügyelt Hyper-V virtuális gépek DR. a meglévő replikálások megszakadnak, és az ügyfelek nem tekinthetik meg, kezelhetik és nem hajthatják végre a ny DR kapcsolódó műveleteit a Azure Site Recoveryon keresztül


## <a name="recommended-actions-to-be-taken"></a>Végrehajtandó javasolt műveletek

Az alábbi alternatív lehetőségekkel biztosíthatja, hogy az ügyfél ne befolyásolja a DR-stratégiáját, ha a forgatókönyv elavult. 

- A [Hyper-V-gazdagépeken található virtuális gépekhez tartozó Dr célként megkezdheti az Azure használatát](hyper-v-azure-tutorial.md).

> [!IMPORTANT]
> Vegye figyelembe, hogy a helyszíni környezet továbbra is rendelkezhet SCVMMM, de az ASR-t csak a Hyper-V-gazdagépekre mutató hivatkozásokkal konfigurálhatja.

- Válassza a helyek közötti replikálás folytatását, de használja a mögöttes [Hyper-V replika megoldást](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), de nem fogja tudni kezelni a Dr-konfigurációkat a Azure Portal Azure site Recovery használatával. 


## <a name="next-steps"></a>Következő lépések
Tervezze meg az elavult alkalmazást, és válasszon egy alternatív lehetőséget, amely az infrastruktúra és a vállalat számára legmegfelelőbb. Ha bármilyen kérdése van, kérjük, lépjen kapcsolatba Microsoft ügyfélszolgálata

