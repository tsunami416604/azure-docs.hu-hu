---
title: Közelség-elhelyezési csoport létrehozása a portál használatával
description: Ismerje meg, hogyan hozhat létre közelségelhelyezési csoportot az Azure Portalhasználatával.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180250"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Közelség-elhelyezési csoport létrehozása a portál használatával

Ahhoz, hogy a virtuális gépek a lehető legközelebb legyenek, és a lehető legalacsonyabb késést érjeel el, telepítenie kell őket egy [közelségelhelyezési csoporton](co-location.md#proximity-placement-groups)belül.

A közelségelhelyezési csoport egy logikai csoportosítás, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag egymáshoz közel helyezkednek el. A közelségelhelyezési csoportok olyan számítási feladatokhoz hasznosak, ahol az alacsony késés követelmény.


## <a name="create-the-proximity-placement-group"></a>A közelségelhelyezési csoport létrehozása

1. Írja be a **közelségelhelyezési csoportot** a keresésbe.
1. A keresési eredmények között a **Szolgáltatások** csoportban válassza a **Közelségelhelyezési csoportok lehetőséget.**
1. A **Közelségelhelyezési csoportok** lapon válassza a **Hozzáadás**gombot.
1. Az **Alapok lapon** a **Project részletei**csoportban győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva.
1. Az **Erőforrás csoportban** válassza az **Új létrehozása lehetőséget** új csoport létrehozásához, vagy válasszon ki egy meglévő erőforráscsoportot a legördülő menüből.
1. A **Régióban** válassza ki azt a helyet, ahol létre szeretné hozni a közelség-elhelyezési csoportot.
1. A **Közelségelhelyezési csoport nevének** mezőbe írjon be egy nevet, majd válassza **a Véleményezés + létrehozás**lehetőséget.
1. Az érvényesítési fázisok után válassza a **Létrehozás** gombot a közelségelhelyezési csoport létrehozásához.

    ![Képernyőkép: közelségelhelyezési csoport létrehozása](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Virtuális gép létrehozása közben a portálon, nyissa meg a **Speciális** lapon. 
1. A **Közelség elhelyezési csoport** kiválasztása csoportban válassza ki a megfelelő elhelyezési csoportot. 

    ![Képernyőkép a közelségelhelyezési csoport szakaszáról, amikor új virtuális gépet hoz létre a portálon](./media/ppg/vm-ppg.png)

1. Ha végzett az összes többi szükséges kijelölés beállításával, válassza a **Véleményezés + létrehozás**lehetőséget.
1. Miután átment az ellenőrzésen, válassza **a Létrehozás** lehetőséget a virtuális gép elhelyezési csoportban való üzembe helyezéséhez.




## <a name="next-steps"></a>További lépések

Az [Azure PowerShell](proximity-placement-groups.md) használatával is létrehozhat közelségi elhelyezési csoportokat.

