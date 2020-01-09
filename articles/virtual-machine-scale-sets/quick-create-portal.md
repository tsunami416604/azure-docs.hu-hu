---
title: Rövid útmutató – virtuálisgép-méretezési csoport létrehozása a Azure Portalban
description: Ismerkedjen meg az üzemelő példányokkal, hogy megtanulja, hogyan hozhat létre gyorsan egy virtuális gépet a Azure Portal.
services: virtual-machine-scale-sets.
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: 5a44aad7471c29a387337a5ee988e66287fca3ca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351144"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Rövid útmutató: Virtuálisgép-méretezési csoport létrehozása az Azure Portalon

A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát beállíthatja manuálisan, de automatikus méretezési szabályokat is megadhat az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Egy Azure-terheléselosztó ezután elosztja a forgalmat a méretezési csoportban lévő virtuálisgép-példányok között. Ebben a rövid útmutatóban egy virtuálisgép-méretezési csoportot hoz létre az Azure Portalon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása

Az Azure [Load Balancer](../load-balancer/load-balancer-overview.md) az egészséges virtuálisgép-példányok között osztja el a bejövő forgalmat. 

Először hozzon létre egy nyilvános alapszintű Load Balancer a portál használatával. A létrehozott név és nyilvános IP-cím automatikusan be van állítva a terheléselosztó előtér-címére.

1. A keresőmezőbe írja be a **Load Balancer**kifejezést. A találatok között a **piactér** területen válassza a **Load balancert**.
1. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat:

    | Beállítás                 | Value (Díj)   |
    | ---| ---|
    | Előfizetés  | Válassza ki előfizetését.    |    
    | Erőforráscsoport | Válassza az **új létrehozása** lehetőséget, és írja be a *myVMSSResourceGroup* szöveget a szövegmezőbe.|
    | Név           | *myLoadBalancer*         |
    | Region (Régió)         | Válassza az **USA keleti régiója** lehetőséget.       |
    | Type (Típus)          | Válassza a **Nyilvános** lehetőséget.       |
    | SKU (Cikkszám)           | Válassza a **standard**lehetőséget.       |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím  | *MyPip*   |
    | Hozzárendelés| Statikus |

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet. 
1. Az érvényesítést követően válassza a **Létrehozás**lehetőséget. 

![Load Balancer létrehozása](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása
Méretezési csoportokat Windows Server-rendszerképpel vagy Linux-rendszerképpel (például RHEL, CentOS, Ubuntu vagy SLES) helyezhet üzembe.

1. Írja be a **méretezési csoport** kifejezést a keresőmezőbe. Az eredmények alatt, a **piactér**területen válassza a **virtuálisgép-méretezési**csoportok lehetőséget. Ekkor megnyílik a **virtuálisgép-méretezési csoport létrehozása** lap. 
1. Az **Alapok** fül **Projektadatok** részén győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, és válassza az **Új létrehozása** lehetőséget az Erőforráscsoport részen. Írja be a *myVMSSResourceGroup* nevet, majd kattintson az **OK gombra** . 
1. A méretezési csoport neveként írja be a *myScaleSet* nevet.
1. A **régió**területen válasszon ki egy régiót, amely a területhez közeledik.
1. Hagyja meg a **méretezési csoport virtuális gépek** alapértelmezett értékét a **Orchestrator**.
1. Válassza ki a **piactér rendszerképét.** Ebben a példában az *Ubuntu Server 18,04 LTS*-et választottuk.
1. Adja meg a kívánt felhasználónevet, és válasszon ki egy tetszőleges hitelesítési típust.
   - A **jelszónak** legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi feltétel közül háromnak: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter. További információkért lásd: [felhasználónév- és jelszókövetelmények](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Ha a Linux operációsrendszer-lemezt választja, választhatja az **SSH nyilvános kulcs** lehetőséget is. Csak a nyilvános kulcsot adja meg, például: *~/.ssh/id_rsa.pub*. [SSH-kulcsok létrehozásához és használatához](../virtual-machines/linux/mac-create-ssh-keys.md) használhatja a portál Azure Cloud Shell szolgáltatását is.
   
    ![Virtuálisgép-méretezési csoport létrehozása](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Kattintson a **tovább** gombra a többi oldal áthelyezéséhez. 
1. Hagyja meg az alapértelmezett értékeket a **példány** és a **lemezek** oldalain.
1. A **hálózatkezelés** lap terheléselosztás területén válassza az **Igen** lehetőséget a méretezési csoport példányainak **a terheléselosztó mögé**állításához. 
1. A **terheléselosztási beállítások**területen válassza az **Azure Load Balancer**elemet.
1. A **terheléselosztó kiválasztása**lapon válassza ki a korábban létrehozott *myLoadBalancer* .
1. A **háttérbeli készlet kiválasztásához**válassza az **új létrehozása**elemet, írja be a *myBackendPool*, majd a **Létrehozás**lehetőséget.
1. Ha elkészült, válassza a **felülvizsgálat + létrehozás**elemet. 
1. Az ellenőrzés elvégzése után válassza a **Létrehozás** lehetőséget a méretezési csoport telepítéséhez.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje az erőforráscsoportot, a méretezési csoportot és az összes kapcsolódó erőforrást. Ehhez válassza ki a méretezési csoporthoz tartozó erőforráscsoportot, majd válassza a **Törlés**lehetőséget.


## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban egy alapszintű méretezési csoportot hozott létre az Azure Portalon. Ha bővebb információra van szüksége, lépjen tovább az Azure-beli virtuálisgép-méretezési csoportok létrehozásáról és kezeléséről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli virtuálisgép-méretezési csoportok létrehozása és kezelése](tutorial-create-and-manage-powershell.md)
