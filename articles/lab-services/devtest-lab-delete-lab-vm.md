---
title: Tesztkörnyezet vagy virtuális gép törlése a laborban Azure DevTest Labs
description: Ez a cikk bemutatja, hogyan törölheti a labort, vagy törölhet egy virtuális gépet egy laborban a Azure Portal (Azure DevTest Labs) használatával.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270809"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Tesztkörnyezet vagy virtuális gép törlése a laborban Azure DevTest Labs
Ebből a cikkből megtudhatja, hogyan törölhet laboratóriumi vagy virtuális gépet egy laborban.

## <a name="delete-a-lab"></a>Tesztkörnyezet törlése
Ha töröl egy DevTest Labs-példányt egy erőforráscsoporthoz, a DevTest Labs szolgáltatás a következő műveleteket hajtja végre: 

- A rendszer automatikusan törli az összes, a labor létrehozásakor létrehozott erőforrást. Maga az erőforráscsoport nem törlődik. Ha kézzel hozta létre az erőforráscsoport erőforrásait, a szolgáltatás nem törli őket. 
- A laborban és az ezekhez a virtuális gépekhez társított erőforráscsoportok összes virtuális gépe automatikusan törlődik. Amikor laborban hoz létre virtuális gépet, a szolgáltatás erőforrásokat (lemezt, hálózati adaptert, nyilvános IP-címet stb.) hoz létre a virtuális géphez egy külön erőforráscsoporthoz. Ha azonban a további erőforrásokat manuálisan hozza létre ezekben az erőforráscsoportokban, a DevTest Labs szolgáltatás nem törli ezeket az erőforrásokat és az erőforráscsoportot. 

Labor törléséhez hajtsa végre a következő műveleteket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a bal oldali menü **minden erőforrás** elemét, válassza ki a **DevTest Labs** lehetőséget a szolgáltatás típusához, és válassza ki a labort.

    ![Válassza ki a labort](media/devtest-lab-delete-lab-vm/select-lab.png)
3. A **DevTest labor** lapon kattintson a **Törlés** elemre az eszköztáron. 

    ![Törlés gomb](media/devtest-lab-delete-lab-vm/delete-button.png)
4. A **jóváhagyás** lapon adja meg a labor **nevét** , és válassza a **Törlés**lehetőséget. 

    ![Jóváhagyás](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. A művelet állapotának megtekintéséhez válassza az **értesítések** ikon (harang) lehetőséget. 

    ![Értesítések](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Virtuális gép törlése tesztkörnyezetben
Ha egy laborban törölek egy virtuális gépet, a labor létrehozásakor létrehozott egyes erőforrások (nem az összes) törlődnek. A következő erőforrások nem törlődnek: 

-   Key Vault a fő erőforráscsoporthoz
-   Rendelkezésre állási csoport, terheléselosztó, nyilvános IP-cím a virtuális gép erőforráscsoporthoz. Ezeket az erőforrásokat az erőforráscsoport több virtuális gépe osztja meg. 

A virtuális gép, a hálózati adapter és a virtuális géphez társított lemez törölve lett. 

A laborban lévő virtuális gépek törléséhez hajtsa végre a következő műveleteket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a bal oldali menü **minden erőforrás** elemét, válassza ki a **DevTest Labs** lehetőséget a szolgáltatás típusához, és válassza ki a labort.

    ![Válassza ki a labort](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Válassza a **... lehetőséget. (három pont)** a virtuális gépek listájában, és válassza a **Törlés**lehetőséget. 

    ![Virtuális gép törlése a menüben](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. A **megerősítő** párbeszédpanelen kattintson **az OK gombra**. 
5. A művelet állapotának megtekintéséhez válassza az **értesítések** ikon (harang) lehetőséget. 

Ha törölni szeretne egy virtuális **gépet a virtuális gép lapról**, válassza a **Törlés** lehetőséget az eszköztárról az alábbi ábrán látható módon:

![Virtuális gép törlése a virtuális gép lapról](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>További lépések
Ha labort szeretne létrehozni, tekintse meg a következő cikkeket: 

- [Labor létrehozása](devtest-lab-create-lab.md)
- [Virtuális gép hozzáadása a tesztkörnyezethez](devtest-lab-add-vm.md)