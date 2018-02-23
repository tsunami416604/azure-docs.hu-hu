---
title: "Indítsa újra a virtuális gép egy tesztkörnyezetben, a Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "Útmutató: a virtuális gép újraindításához a Azure DevTest Labs szolgáltatásban"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: v-craic
ms.openlocfilehash: e784b6ebffa215fc090af0f915900a41972fb778
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Indítsa újra a virtuális gép egy tesztkörnyezetben, a Azure DevTest Labs szolgáltatásban
Gyorsan és egyszerűen a virtuális gép újraindításához a DevTest Labs szolgáltatásban Ez a cikk utasításait követve. Vegye figyelembe a következő virtuális gép újraindítása előtt:

- A virtuális gép futnia kell, újraindítás funkció engedélyezését.
- A felhasználó egy futó virtuális géphez csatlakoztatva van, amikor egy újrainduljon, ha azok csatlakoznia kell a virtuális gép biztonsági mentése elindulásakor.
- Ha egy közbülső végzi, a virtuális gép újraindításakor, figyelmeztetést, hogy az összetevő nem alkalmazható. 

    ![Az összetevők érvényesítésekor újraindításakor figyelmeztetés](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Ha a virtuális gép összetevő érvényesítésekor elakad, használhatja a indítsa újra a virtuális gép szolgáltatás egyik lehetséges módja a probléma megoldásához.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Indítsa újra a virtuális gépek a Azure DevTest Labs szolgáltatásban egy tesztkörnyezetben lépései
1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Labs listában jelölje ki a labor, amely tartalmazza a virtuális gép újraindítja.  
1. A bal oldali panelen válassza ki a **saját virtuális gépek**. 
1. A virtuális gépek listájából válassza ki a virtuális gép.
1. Válassza ki a virtuális gép felügyeleti ablaktábla felső részén **indítsa újra a**.  

    ![Indítsa újra a virtuális gép gomb](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Az újraindítás állapotának figyelése kiválasztásával a **értesítések** ikonra a bal felső sarkában az ablak.

    ![A virtuális gép újraindítási állapotának megtekintése](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

A virtuális gép indítsa újra a listából történő kiválasztásakor a három ponttal (…) által **saját virtuális gépek**.

![Indítsa újra a virtuális gép folytatást jelző pontokra keresztül](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>További lépések
* Az újraindítás után újra csatlakozhat a virtuális gép kiválasztásával **Connect** a a a felügyeleti panelt.
* Megismerkedhet a [DevTest Labs Azure Resource Manager gyorsindítási sablonok gyűjteménye](https://github.com/Azure/azure-devtestlab/tree/master/Samples)
