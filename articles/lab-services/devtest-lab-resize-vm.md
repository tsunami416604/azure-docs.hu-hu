---
title: Virtuális gép átméretezése egy tesztkörnyezetben az Azure DevTest Labsben
description: Ismerje meg, hogyan módosíthatja a virtuális gép (VM) méretét az Azure DevTest Labs-ben a PROCESSZOR, a hálózat vagy a lemez teljesítményének igényei alapján.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bf7c425766a97aaa1d143133f04502a0aa3c36cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756177"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép átméretezése egy tesztkörnyezetben az Azure DevTest Labsben
Az Azure virtuális gépek egyik fontos jellemzője, hogy lehetővé teszi a virtuális gép (VM) méretének módosítását a PROCESSZOR, a hálózat vagy a lemez teljesítményének igényei alapján. Az Azure DevTest Labs támogatja ezt a funkciót a virtuális gépek egy laborban most. Az átméretezési funkció megfelel a labor szabályzat a laborban engedélyezett virtuálisgép-méretek. Ez azt, hogy módosíthatja a virtuális gép méretét, hogy csak a laborban engedélyezett méretek. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Virtuális gép átméretezése laborban 
Virtuális gép átméretezése egy tesztkörnyezetben az Azure DevTest Labs, az alábbi lépésekkel: 

> [!NOTE]
> Ha egy távoli asztali munkameneten (RDP) keresztül csatlakozik a virtuális géphez, mentse a munkáját, és az átméretezés előtt bontsa a kapcsolatot a virtuális géppel.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki azt a tesztkörnyezetet, amely tartalmazza az átméretezni kívánt virtuális gép.  
4. A bal oldali panelen válassza a **Saját virtuális gépek**lehetőséget . 
5. A virtuális gépek listájából válasszon ki egy virtuális gépet.
6. Válassza a **Leállítás** lehetőséget az eszköztáron, ha a virtuális gép fut. Ellenőrizze a művelet állapotát az **Értesítések** ablakban. Várjon, amíg a virtuális gép leáll, majd zárja be az **Értesítések ablakot.** 

    ![A virtuális gép leállítása](media/devtest-lab-resize-vm/stop-vm.png)
1. A virtuális gép virtuális gépének lapján válassza a Bal oldali menü **Beállítások csoport Beállítások** **parancsát.**

    ![Méret menü](media/devtest-lab-resize-vm/size-menu.png)
1. A **Méret kiválasztása** ablakban tallózással és a virtuális gép méretének kiválasztásával kattintson a **Kijelölés gombra.**     
1. Ellenőrizze az átméretezési művelet állapotát az **Értesítések** ablakban.

    ![Állapot átméretezése](media/devtest-lab-resize-vm/resize-status.png)
10. Az átméretezési művelet sikeres stafára zárja be az **Értesítések ablakot.** 
11. Válassza a bal oldali menü **Áttekintés parancsát,** majd az eszköztár **Újraindítás parancsát** a virtuális gép újraindításához. 

## <a name="next-steps"></a>További lépések
Az Azure virtuális gépek által támogatott átméretezési funkcióról a [Virtuális gépek átméretezése](https://azure.microsoft.com/blog/resize-virtual-machines/)című témakörben talál részletes információt.


