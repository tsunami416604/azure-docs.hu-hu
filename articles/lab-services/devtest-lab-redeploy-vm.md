---
title: Virtuális gép újratelepítése egy tesztkörnyezetben az Azure DevTest Labsben | Microsoft dokumentumok
description: Ismerje meg, hogyan helyezheti üzembe újra egy virtuális gépet (áthelyezés az egyik Azure-csomópontról a másikra) az Azure DevTest Labsben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561624"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép újratelepítése egy laborban az Azure DevTest Labsben
Ha nem tud csatlakozni egy virtuális géphez (VM) egy tesztkörnyezetben egy távoli asztali kapcsolaton keresztül, telepítse újra a virtuális gépet, és próbáljon meg újra csatlakozni hozzá. Virtuális gép újratelepítésekor a DevTest Labs áthelyezi a virtuális gép a csomópont, amelyen fut egy új csomópont az Azure-infrastruktúrán belül. Ezután elindítja a virtuális gép, miközben megtartja az összes konfigurációs beállítások és a kapcsolódó erőforrások. Ez a szolgáltatás megtakarítja a távoli asztali kapcsolat vagy a Windows-alapú virtuális gépek hez való hozzáférés hibaelhárításával töltött időt a laborban. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Virtuális gép laborban való újratelepítésének lépései 
Virtuális gép újratelepítése egy tesztkörnyezetben az Azure DevTest Labs, az alábbi lépésekkel: 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki azt a tesztkörnyezetet, amely tartalmazza az újratelepíteni kívánt virtuális gép.  
4. A bal oldali panelen válassza a **Saját virtuális gépek**lehetőséget . 
5. A virtuális gépek listájából válasszon ki egy virtuális gépet.
6. A virtuális gép virtuális gépe lapján válassza a bal oldali menü **MŰVELETEK** csoportban **az újratelepítés** lehetőséget.

    ![Ismételt üzembe helyezés](media/devtest-lab-redeploy-vm/redeploy.png)
7. Olvassa el a lapon található információkat, és válassza az **Újratelepítés gombot.** 9. Ellenőrizze az újraüzembe helyezési művelet állapotát az **Értesítések** ablakban.

    ![Újracsoportosítás állapota](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan méretezheti át a virtuális gépeket az Azure DevTest Labsben, olvassa el [a Virtuális gép átméretezése.](devtest-lab-resize-vm.md)


