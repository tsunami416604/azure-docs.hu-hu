---
title: Virtuális gép újbóli üzembe helyezése laborban Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan telepítheti át a virtuális gépeket (az egyik Azure-csomópontról a másikra) Azure DevTest Labs.
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
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898723"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép újbóli üzembe helyezése laborban Azure DevTest Labs
Ha nem tud csatlakozni egy virtuális géphez a laborban távoli asztali kapcsolaton keresztül, telepítse újra a virtuális gépet, és próbálkozzon újra a csatlakozással. Egy virtuális gép újratelepítésekor a DevTest Labs áthelyezi a virtuális gépet abból a csomópontból, amelyen az Azure-infrastruktúrán belüli új csomópontra fut. Ezután elindítja a virtuális gépet, miközben megőrzi az összes konfigurációs beállítást és a kapcsolódó erőforrásokat. Ez a szolgáltatás időt takarít meg a távoli asztali kapcsolat vagy az alkalmazás Windows-alapú virtuális gépekhez való hozzáférésének hibaelhárítása során. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>A virtuális gépek laboratóriumi üzembe helyezésének lépései 
Egy virtuális gép Azure DevTest Labs tesztkörnyezetben való újratelepítéséhez hajtsa végre a következő lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A Labs listából válassza ki azt a labort, amely tartalmazza az újratelepíteni kívánt virtuális gépet.  
4. A bal oldali panelen válassza a **saját Virtual Machines**elemet. 
5. A virtuális gépek listájából válasszon ki egy virtuális gépet.
6. A virtuális gép virtuálisgép-lapján válassza a bal oldali menü **műveletek** területén található **újratelepítés** lehetőséget.

    ![Ismételt üzembe helyezés](media/devtest-lab-redeploy-vm/redeploy.png)
7. Olvassa el az oldalon található információkat, majd kattintson az újbóli **üzembe helyezés** gombra. 9. Az ismételt üzembe helyezési művelet állapotát az **értesítések** ablakban tekintheti meg.

    ![Állapot újbóli üzembe helyezése](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan méretezheti át a virtuális gépeket a Azure DevTest Labsban: [virtuális gép](devtest-lab-resize-vm.md)átméretezése.


