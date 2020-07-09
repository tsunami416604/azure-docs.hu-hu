---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179124"
---
## <a name="use-the-azure-portal"></a>Az Azure Portal használata
1. Válassza ki azt a virtuális gépet, amelyet újra telepíteni kíván, majd kattintson az újbóli *üzembe helyezés* gombra a *Beállítások* panelen. Előfordulhat, hogy le kell görgetni a **támogatás és hibaelhárítás** szakaszt, amely tartalmazza az "újbóli üzembe helyezés" gombot, ahogy az alábbi példában látható:
   
    ![Azure-beli virtuális gép panel](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. A művelet megerősítéséhez kattintson az újbóli *üzembe helyezés* gombra:
   
    ![Virtuális gép panel újbóli üzembe helyezése](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. A virtuális gép **állapota** *frissítésre* módosul, mivel a virtuális gép előkészíti az újraüzembe helyezést, ahogyan az a következő példában látható:
   
    ![Virtuális gép frissítése](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. Az **állapot** akkor változik, *Amikor a* virtuális gép elindul egy új Azure-gazdagépen, ahogy az az alábbi példában is látható:
   
    ![Virtuális gép indítása](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Miután a virtuális gép befejezte a rendszerindítási folyamatot, az **állapot** a *futtatásra*tér vissza, amely azt jelzi, hogy a virtuális gép sikeresen újratelepítve lett:
   
    ![Virtuális gép fut](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

