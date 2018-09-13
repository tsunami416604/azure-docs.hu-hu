---
title: Ismételt üzembe helyezése egy virtuális Géphez az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Ismerje meg, hogyan ismételt üzembe helyezése az Azure DevTest Labs szolgáltatásban virtuális gép (áthelyezése az Azure egyik csomópontról a másikra).
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
ms.openlocfilehash: 273b0f1105d8b71b90a06e2627e201b97f12a754
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646163"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Ismételt üzembe helyezése egy virtuális Géphez az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
Ha nem tud csatlakozni a virtuális gép (VM) egy távoli asztali kapcsolaton keresztül tesztkörnyezetben, a virtuális gép ismételt üzembe, és próbálja meg újra azt conencting. Ha egy virtuális gép újbóli telepítése, DevTest Labs a virtuális gép a csomópont, amelyen fut az Azure infrastruktúráján belül egy új csomópont helyezi át. Ezután elindítja a virtuális gépek megtartja a konfigurációs beállításokat és a kapcsolódó erőforrásokat. Ez a funkció menti, töltött idő a hibaelhárítást a távoli asztali kapcsolat vagy a Windows-alapú virtuális gépeket a tesztkörnyezetben történő alkalmazás-hozzáférés. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>A lab-ben a virtuális gép ismételt üzembe lépései 
Ismételt üzembe helyezése egy virtuális Géphez az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet, hajtsa végre az alábbi lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a labor létrehozása, amely tartalmazza a virtuális gép újra szeretné.  
4. A bal oldali panelen válassza ki a **saját virtuális gépek**. 
5. Virtuális gépek listájából válasszon ki egy virtuális Gépet.
6. A virtuális géphez a virtuális gép oldalon válassza ki a **ismételt üzembe helyezése** alatt **műveletek** a bal oldali menüben.

    ![Ismételt üzembe helyezés](media/devtest-lab-redeploy-vm/redeploy.png)
7. Olvassa el a lapon található információkat, és válassza ki **ismételt üzembe helyezése** gombra. 9. A művelet ismételt üzembe helyezés állapotának ellenőrzéséhez a **értesítések** ablak.

    ![Ismételt üzembe helyezés állapota](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan méretezze át egy virtuális Gépet az Azure DevTest Labs szolgáltatásban, lásd: [virtuális gép átméretezése](devtest-lab-resize-vm.md).


