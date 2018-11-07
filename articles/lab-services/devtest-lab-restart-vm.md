---
title: Indítsa újra a virtuális gép az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Ismerje meg, hogyan indítsa újra a virtuális gép az Azure DevTest Labs szolgáltatásban
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0c11f5f43e0ea96a0b4bc9b4ecec30850c39b023
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231974"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Indítsa újra a virtuális gép az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
Gyorsan és egyszerűen indítsa újra a virtuális gépek DevTest Labs szolgáltatásban Ez a cikk lépéseit követve. Vegye figyelembe a következő virtuális gép újraindítása előtt:

- A virtuális gép az újraindítás funkció engedélyezésének kell futnia.
- Egy felhasználó egy futó virtuális géphez csatlakoztatva van, amikor egy újrainduljon, ha azok kell csatlakozni a virtuális gép biztonsági mentése elindítása után.
- Ha egy összetevő alkalmazása folyamatban van, a virtuális gép újraindításakor, hogy az összetevő nem alkalmazható figyelmeztetés jelenik meg. 

    ![Összetevők alkalmazása közben újraindításához figyelmeztetés](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Ha a virtuális gép egy alkalmazása közben elakadt, használhatja a indítsa újra a virtuális gép szolgáltatás egyik lehetséges módja a probléma megoldásához.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Indítsa újra a virtuális gép az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet lépései
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a labor létrehozása, amely tartalmazza a virtuális Gépet újraindítja.  
1. A bal oldali panelen válassza ki a **saját virtuális gépek**. 
1. Virtuális gépek listájából válassza ki a futó virtuális gépek.
1. Válassza ki a virtuális gép kezelése panel tetején lévő **indítsa újra a**.  

    ![Indítsa újra a virtuális gép gomb](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Válassza az újraindítás állapotának figyelése a **értesítések** ikonra a felső, az ablak jobb.

    ![A virtuális gép újraindítási állapotának megtekintése](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Futó virtuális gépek listájában melletti három pontra (...) kiválasztásával is újraindíthatja **saját virtuális gépek**.

![Indítsa újra a virtuális Gépet keresztül három pont](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>További lépések
* Újraindul, miután újra csatlakozhat a virtuális gép kiválasztásával **Connect** a a saját felügyeleti panel.
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsindítási sablon galéria](https://github.com/Azure/azure-devtestlab/tree/master/Samples)
