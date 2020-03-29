---
title: Virtuális gép újraindítása egy tesztkörnyezetben az Azure DevTest Labs ben | Microsoft dokumentumok
description: Ez a cikk lépéseket tartalmaz a virtuális gépek (VM) gyors és egyszerű újraindításához az Azure DevTest Labsben.
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
ms.openlocfilehash: 52d3b92909483a99eb82c86b727261bbeb5f8d46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759994"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép újraindítása egy tesztkörnyezetben az Azure DevTest Labsben
A devtest labsben gyorsan és egyszerűen újraindíthategy virtuális gépet a jelen cikkben ismertetett lépések végrehajtásával. A virtuális gép újraindítása előtt vegye figyelembe a következőket:

- Az újraindítási szolgáltatás engedélyezéséhez a virtuális gépnek futnia kell.
- Ha egy felhasználó csatlakozik egy futó virtuális gép, amikor egy újraindítás végrehajtása, újra kell csatlakoznia a virtuális gép hez, miután elindul a biztonsági másolatot.
- Ha egy műtermék van alkalmazva a virtuális gép újraindításakor, figyelmeztetést kap, hogy a műtermék nem alkalmazható.

    ![Figyelmeztetés az összetevők alkalmazása közbeni újraindításkor](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Ha a virtuális gép leállt egy műtermék alkalmazása közben, használhatja a virtuális gép újraindítása szolgáltatást a probléma megoldásának lehetséges módjaként.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép újraindításának lépései egy tesztkörnyezetben az Azure DevTest Labsben
1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
1. A laborok listájából válassza ki azt a tesztkörnyezetet, amely tartalmazza az újraindítani kívánt virtuális gép.
1. A bal oldali panelen válassza a **Saját virtuális gépek**lehetőséget .
1. A virtuális gépek listájából válasszon ki egy futó virtuális gépet.
1. A virtuális gép felügyeleti ablaktábla tetején válassza az **Újraindítás**lehetőséget.

    ![Virtuális gép újraindítása gomb](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Az újraindítás állapotának figyeléséhez kattintson az ablak jobb felső részén található **Értesítések** ikonra.

    ![A virtuális gép újraindításának állapotának megtekintése](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

A futó virtuális gépet úgy is újraindíthatja, hogy kiválasztja a három pontot (...) a **Virtuális gépek**listában.

![Virtuális gép újraindítása három ponton keresztül](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>További lépések
* Újraindítása után újracsatlakozhat a virtuális géphez a Felügyeleti ablaktáblán a **Csatlakozás** lehetőség kiválasztásával.
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsstartsablon-gyűjteményét](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
