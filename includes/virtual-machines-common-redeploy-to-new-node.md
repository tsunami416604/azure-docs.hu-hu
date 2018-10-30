---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226493"
---
## <a name="use-the-azure-portal"></a>Az Azure Portal használata
1. Válassza ki a virtuális gép ismételt üzembe helyezése, majd válassza a kívánt a *ismételt üzembe helyezése* gombra a *beállítások* panelen. Szükség lehet legörgetve találja meg a **támogatás és hibaelhárítás** szakaszt, amely tartalmazza a "Ismételt üzembe helyezés" gomb az alábbi példában látható módon:
   
    ![Az Azure virtuális gép paneljén](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. A művelet megerősítéséhez jelölje ki a *ismételt üzembe helyezése* gombra:
   
    ![Ismételt üzembe helyezése egy virtuális gép paneljén](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. A **állapot** módosítja a virtuális gép *Updating* , a virtuális gép újbóli üzembe helyezéséhez, előkészíti, az alábbi példában látható módon:
   
    ![Virtuális gép frissítése](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. A **állapot** változik majd *indítása* , a virtuális gép elindul egy új Azure gazdagépen az alábbi példában látható módon:
   
    ![A virtuális gép elindul](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. A virtuális gép a rendszerindítási folyamat befejezése után a **állapot** visszahelyeződik a *futó*, jelezve a virtuális gép rendelkezik rendszer újratelepítése sikeresen megtörtént:
   
    ![A virtuális gép fut](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

