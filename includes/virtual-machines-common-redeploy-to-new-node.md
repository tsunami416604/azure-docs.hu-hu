---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179124"
---
## <a name="use-the-azure-portal"></a>Az Azure Portal használata
1. Válassza ki az újratelepíteni kívánt virtuális gép, majd válassza ki az *újratelepítés* gombot a *Beállítások* panelen. Előfordulhat, hogy lefelé kell görgetnie a **"Újratelepítés" gombot** tartalmazó Támogatás és hibaelhárítás szakasz megtekintéséhez, ahogy az a következő példában látható:
   
    ![Az Azure Virtuálisgép-panel](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. A művelet megerősítéséhez válassza az *Újratelepítés* gombot:
   
    ![Virtuálisgép-panel újratelepítése](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. A virtuális gép **állapota** *frissül,* ahogy a virtuális gép újraüzembe helyezésre készül, ahogy az a következő példában látható:
   
    ![Virtuális gép frissítése](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. Az **állapot** ezután *elindul,* ahogy a virtuális gép elindul egy új Azure-gazdagépen, ahogy az a következő példában látható:
   
    ![Virtuális gép indítása](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Miután a virtuális gép befejezte a rendszerindítási folyamatot, az **állapot** ezután visszatér *a Futás*állapotba, jelezve, hogy a virtuális gép telepítése sikeresen megtörtént:
   
    ![A virtuális gép fut](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

