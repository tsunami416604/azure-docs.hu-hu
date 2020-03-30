---
title: Labor vagy virtuális gép törlése egy tesztkörnyezetben az Azure DevTest Labsben
description: Ez a cikk bemutatja, hogyan törölheti a laborban, vagy törölheti a virtuális gép egy laborban az Azure Portalon (Azure DevTest Labs).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 28fb464714f464a4c0a8f5eaf304dcdd5d603c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270809"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Labor vagy virtuális gép törlése egy tesztkörnyezetben az Azure DevTest Labsben
Ez a cikk bemutatja, hogyan törölheti a labor vagy virtuális gép egy laborban.

## <a name="delete-a-lab"></a>Tesztkörnyezet törlése
Ha egy FejlesztőiTeszt Labs-példányt töröl egy erőforráscsoportból, a DevTest Labs szolgáltatás a következő műveleteket hajtja végre: 

- A tesztkörnyezet létrehozásakor automatikusan létrehozott összes erőforrás automatikusan törlődik. Maga az erőforráscsoport nem törlődik. Ha manuálisan hozta létre az erőforráscsoportot, a szolgáltatás nem törli azokat. 
- A tesztkörnyezetben lévő összes virtuális gép és az ezekhez a virtuális gépekhez társított erőforráscsoportok automatikusan törlődnek. Amikor létrehoz egy virtuális gép egy tesztkörnyezetben, a szolgáltatás erőforrásokat hoz létre (lemez, hálózati adapter, nyilvános IP-cím, stb) a virtuális gép egy külön erőforráscsoportban. Azonban ha manuálisan hoz létre további erőforrásokat ezekben az erőforráscsoportokban, a DevTest Labs szolgáltatás nem törli ezeket az erőforrásokat és az erőforráscsoportot. 

Labor törlése, tegye a következő műveleteket: 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza ki az **összes erőforrást** a bal oldali menüből, válassza a **DevTest Labs** lehetőséget a szolgáltatás típusához, és válassza ki a labort.

    ![Válassza ki a laborját](media/devtest-lab-delete-lab-vm/select-lab.png)
3. A **DevTest Lab** lapon kattintson a **Törlés** gombra az eszköztáron. 

    ![Törlés gomb](media/devtest-lab-delete-lab-vm/delete-button.png)
4. A **Megerősítés** lapon adja meg a tesztkörnyezet **nevét,** és válassza a **Törlés**lehetőséget. 

    ![Jóváhagyás](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. A művelet állapotának megtekintéséhez válassza az **Értesítések** ikont (Csengő). 

    ![Értesítések](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Virtuális gép törlése egy tesztkörnyezetben
Ha egy tesztkörnyezetben törlök egy virtuális gép, a labor létrehozásakor létrehozott erőforrások egy része (nem az összes) törlődik. A következő erőforrások nem törlődnek: 

-   Kulcstartó a fő erőforráscsoportban
-   Rendelkezésre állási készlet, terheléselosztó, nyilvános IP-cím a virtuális gép erőforráscsoportban. Ezeket az erőforrásokat egy erőforráscsoporttöbb virtuális gépe osztja meg. 

A virtuális géphez társított virtuális gép, hálózati csatoló és lemez törlődik. 

Virtuális gép törlése egy tesztkörnyezetben, tegye a következő műveleteket: 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza ki az **összes erőforrást** a bal oldali menüből, válassza a **DevTest Labs** lehetőséget a szolgáltatás típusához, és válassza ki a labort.

    ![Válassza ki a laborját](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Válassza ki **a ... (Ellipszis)** a virtuális gépek listájában a virtuális gépek listájában, és válassza a **Törlés gombot.** 

    ![Virtuális gép törlése a menüben](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. A **megerősítést kérő** párbeszédpanelen válassza az **Ok gombot.** 
5. A művelet állapotának megtekintéséhez válassza az **Értesítések** ikont (Csengő). 

Ha törölni szeretne egy virtuális gépet a **Virtuálisgép lapról,** válassza a **Törlés** lehetőséget az eszköztáron az alábbi képen látható módon:

![Virtuális gép törlése a virtuális gép lapról](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>További lépések
Ha labort szeretne létrehozni, olvassa el az alábbi cikkeket: 

- [Labor létrehozása](devtest-lab-create-lab.md)
- [Virtuális gép hozzáadása a tesztkörnyezethez](devtest-lab-add-vm.md)