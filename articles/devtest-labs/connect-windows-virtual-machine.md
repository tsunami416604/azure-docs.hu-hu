---
title: Kapcsolódjon a Windows rendszerű virtuális gépekhez Azure DevTest Labs
description: Megtudhatja, hogyan csatlakozhat a Windows rendszerű virtuális géphez egy laborban (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86540677"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>Kapcsolódás Windows rendszerű virtuális géphez a laborban (Azure DevTest Labs)
Ez a cikk bemutatja, hogyan csatlakozhat egy Windows rendszerű virtuális géphez a laborban. 

## <a name="connect-to-a-windows-vm"></a>Csatlakozás Windows rendszerű virtuális géphez
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A keresési sávban keresse meg és válassza ki a **DevTest Labs**elemet. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="DevTest Labs keresése és kiválasztása":::    
1. A Labs listából válassza ki a **labort**.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="DevTest Labs keresése és kiválasztása":::            
1. A labor kezdőlapján válassza ki a Windows rendszerű virtuális GÉPET a **saját virtuális gépek** listájából. 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="DevTest Labs keresése és kiválasztása":::                
1. A **virtuális gép virtuálisgép** -lapján válassza a **kapcsolat** lehetőséget az eszköztáron. Ha a virtuális gép le van állítva, kattintson az első **lépések** elemre a virtuális gép elindításához.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="DevTest Labs keresése és kiválasztása":::                    
1. Ha az Edge böngészőt használja, a böngésző alján található **letöltött RDP-fájlra** mutató hivatkozás jelenik meg. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="DevTest Labs keresése és kiválasztása":::                        
1. Nyissa meg az RDP-fájlt, és adja meg a virtuális gép létrehozásakor beírt virtuálisgép-hitelesítő adatokat. Most csatlakoznia kell a Windows rendszerű virtuális géphez. 

## <a name="next-steps"></a>További lépések
[Útmutató: Kapcsolódás Linux rendszerű virtuális géphez](connect-linux-virtual-machine.md)
