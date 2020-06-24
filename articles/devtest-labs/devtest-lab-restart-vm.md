---
title: Virtuális gép újraindítása Azure DevTest Labs laborban | Microsoft Docs
description: Ez a cikk azokat a lépéseket ismerteti, amelyekkel gyorsan és egyszerűen újraindítható a virtuális gépek (VM) a Azure DevTest Labs.
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
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898184"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép újraindítása Azure DevTest Labs tesztkörnyezetben
A virtuális gépeket gyorsan és egyszerűen újraindíthatja a DevTest Labs szolgáltatásban a jelen cikkben ismertetett lépéseket követve. A virtuális gép újraindítása előtt vegye figyelembe a következőket:

- Az újraindítás funkció engedélyezéséhez a virtuális gépnek futnia kell.
- Ha a felhasználó egy futó virtuális géphez csatlakozik, amikor újraindítást végez, újra kell csatlakozniuk a virtuális géphez a biztonsági mentés elindítása után.
- Ha a virtuális gép újraindításakor egy összetevőt alkalmaz a rendszer, akkor a rendszer figyelmeztetést küld arról, hogy az összetevő nem lesz alkalmazva.

    ![Figyelmeztetés az összetevők alkalmazása során történő újraindításkor](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Ha a virtuális gép elakadt egy összetevő alkalmazása közben, a probléma megoldásához használhatja a virtuális gép újraindítása funkciót.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>A virtuális gép újraindításának lépései Azure DevTest Labs tesztkörnyezetben
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
1. A Labs listából válassza ki azt a labort, amely tartalmazza az újraindítani kívánt virtuális gépet.
1. A bal oldali panelen válassza a **saját Virtual Machines**elemet.
1. A virtuális gépek listájából válassza ki a futó virtuális gépet.
1. A virtuális gép kezelése panel felső részén válassza az **Újraindítás**lehetőséget.

    ![Virtuális gép újraindítása gomb](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Az újraindítás állapotának figyeléséhez válassza az ablak jobb felső sarkában található **értesítések** ikont.

    ![A virtuális gép újraindításának állapotának megtekintése](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

A futó virtuális gépeket a **Virtual Machines**listájában lévő három pont (...) kiválasztásával is újraindíthatja.

![Virtuális gép újraindítása ellipszisekkel](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>További lépések
* Az újraindítás után újra csatlakozhat a virtuális géphez a felügyeleti paneljén a **Kapcsolódás** lehetőség kiválasztásával.
* Ismerkedés a [DevTest Labs Azure Resource Manager a rövid útmutató sablonjának galériájában](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
