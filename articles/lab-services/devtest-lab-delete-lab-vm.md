---
title: A labor- vagy virtuális gép törlése az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan labor- vagy virtuális gép törlése a tesztkörnyezetben.
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
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: 99caf04698226de8daa9cfb8f60662e5cb0f8b49
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449826"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet egy labor- vagy virtuális gép törlése
Ez a cikk bemutatja, hogyan labor- vagy virtuális gép törlése a tesztkörnyezetben.

## <a name="delete-a-lab"></a>Tesztkörnyezet törlése
Ha töröl egy erőforráscsoportot egy DevTest Labs-példányt, a DevTest Labs szolgáltatás a következő műveleteket hajtja végre: 

- A labor létrehozásának időpontjában automatikusan létrehozott összes erőforrást a rendszer automatikusan törli. Nem törlődik automatikusan magát az erőforráscsoportot. Ha korábban manuálisan létrehozott erőforrásokat az erőforráscsoport, a szolgáltatás nem törli őket. 
- Ezek a virtuális gépek társított a labor- és erőforrás csoportokban lévő összes virtuális gép automatikusan törlődnek. Ha egy virtuális Gépet hoz létre a tesztkörnyezetben, a szolgáltatás egy külön erőforráscsoportot a virtuális géphez hoz létre erőforrások (lemez, hálózati adapter, nyilvános IP-cím, stb.). Azonban ha manuálisan hoz létre egyéb erőforrásokat az ezeket az erőforráscsoportokat, a DevTest Labs szolgáltatás nem törli ezeket az erőforrásokat és az erőforráscsoportot. 

Labor törléséhez tegye a következőket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **összes erőforrás** válassza a bal oldali menü **DevTest Labs** típusának megfelelő szolgáltatás, és válassza a labor létrehozása.

    ![Válassza ki a labor](media\devtest-lab-delete-lab-vm\select-lab.png)
3. Az a **fejlesztési és tesztelési labor** kattintson **törlése** az eszköztáron. 

    ![Törlés gomb](media\devtest-lab-delete-lab-vm\delete-button.png)
4. Az a **megerősítő** lap, adja meg a **neve** a labor, és válassza ki a **törlése**. 

    ![Megerősítés](media\devtest-lab-delete-lab-vm\confirm-delete.png)
5. A művelet állapotának megtekintéséhez válassza ki a **értesítések** ikonra (harang). 

    ![Értesítések](media\devtest-lab-delete-lab-vm\delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>A labor virtuális gép törlése
Ha a labor virtuális gépek törléséhez egyes erőforrások (összes) a labor létrehozásakor létrehozott törlődnek. A következő erőforrások nem törlődnek: 

-   Kulcstartó-fő az erőforráscsoportban
-   Rendelkezésre állási beállítása, a terheléselosztó nyilvános IP-címet a virtuális gép az erőforráscsoportban. Ezeket az erőforrásokat egy erőforráscsoportba tartozó több virtuális gép vannak megosztva. 

Virtuális gép, hálózati adapter és a virtuális Géphez tartozó lemez törlődnek. 

A labor virtuális gépek törléséhez tegye a következőket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **összes erőforrás** válassza a bal oldali menü **DevTest Labs** típusának megfelelő szolgáltatás, és válassza a labor létrehozása.

    ![Válassza ki a labor](media\devtest-lab-delete-lab-vm\select-lab.png)
3. Válassza ki **... három (pont)**  listájában, a virtuális gépeket, és válassza ki a virtuális gép **törlése**. 

    ![A menüben a virtuális gép törlése](media\devtest-lab-delete-lab-vm\delete-vm-menu-in-list.png)
4. Az a **megerősítő** párbeszédpanelen jelölje ki **Ok**. 
5. A művelet állapotának megtekintéséhez válassza ki a **értesítések** ikonra (harang). 

A virtuális gép törlése a **virtuális gép**válassza **törlése** az eszköztárról, a következő képen látható módon:

![Virtuális gép törlése a virtuális gép oldalról](media\devtest-lab-delete-lab-vm\delete-from-vm-page.png) 


## <a name="next-steps"></a>További lépések
Ha azt szeretné, labor létrehozásához a következő cikkekben talál: 

- [Labor létrehozása](devtest-lab-create-lab.md)
- [A labor virtuális gép hozzáadása](devtest-lab-add-vm.md)