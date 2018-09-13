---
title: Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet virtuális gép átméretezése |} A Microsoft Docs
description: Ismerje meg, az Azure DevTest Labs szolgáltatásban virtuális gép átméretezése
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
ms.openlocfilehash: 9b9a1839bf4b028aec13b764b4de66385de4189e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646364"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet virtuális gép átméretezése
Azure-beli virtuális gépek fontos jellemzőit egyik célja, hogy lehetővé teszi a virtuális gép (VM) a CPU-, hálózati vagy lemez teljesítménye az igények alapján méretének módosítása. Az Azure DevTest Labs támogatja ezt a szolgáltatást a virtuális gépek tesztkörnyezetben most. Az átméretezési funkció engedélyezett Virtuálisgép-méretek a lab-ben a labor házirend csatlakozik. Azt jelenti módosíthatja a virtuális gép méretét csak engedélyezett méretek a tesztkörnyezetben. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>A lépéseket a labor virtuális gépek átméretezése 
Egy virtuális Géphez az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet méretezéséhez hajtsa végre az alábbi lépéseket: 

> [!NOTE]
> Ha a virtuális géphez távoli asztali kapcsolat (RDP) keresztül kapcsolódik, mentse a munkáját, és a virtuális gép átméretezése előtt válassza le.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a labor létrehozása, amely tartalmazza a virtuális gép átméretezése szeretné.  
4. A bal oldali panelen válassza ki a **saját virtuális gépek**. 
5. Virtuális gépek listájából válasszon ki egy virtuális Gépet.
6. Válassza ki **leállítása** az eszköztáron, ha a virtuális gép fut-e. A művelet állapotának ellenőrzéséhez a **értesítések** ablak. Várjon, amíg a virtuális gép leállt, majd zárja be a **értesítések** ablak. 

    ![A virtuális gép leállítása](media/devtest-lab-resize-vm/stop-vm.png)
1. A virtuális géphez a virtuális gép oldalon válassza ki a **mérete** alatt **beállítások** a bal oldali menüben.

    ![Méret menü](media/devtest-lab-resize-vm/size-menu.png)
1. Az a **méret kiválasztása** ablakban keresse meg és válassza ki a kívánt méretet a virtuális gép, és kattintson **kiválasztása**.     
1. Az átméretezés az állapotának ellenőrzéséhez a **értesítések** ablak.

    ![Méretezze át a állapota](media/devtest-lab-resize-vm/resize-status.png)
10. Az átméretezést követően a művelet sikeres, zárja be a **értesítések** ablak. 
11. Válassza ki **áttekintése** a bal oldali menüben, és válassza a **indítsa újra a** gombra az eszköztárban, indítsa újra a virtuális Gépet. 

## <a name="next-steps"></a>További lépések
Az Azure-beli virtuális gépek által támogatott átméretezése funkcióival kapcsolatos részletes információkért lásd: [méretezze át a virtuális gépek](https://azure.microsoft.com/blog/resize-virtual-machines/).


