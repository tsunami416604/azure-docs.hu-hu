---
title: Gyorsútmutató – Virtuálisgép-méretezési csoport létrehozása az Azure Portalon
description: Az üzembe helyezésekkel ismerkedhet meg az Azure Portal gyors méretezésével kapcsolatos virtuális gépek gyors létrehozásával.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: mvc, H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: jushiman
ms.openlocfilehash: 5855c53a73ac94985f34f229fbe5ab77067ef17d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680422"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Rövid útmutató: Virtuálisgép-méretezési csoport létrehozása az Azure Portalon

A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát beállíthatja manuálisan, de automatikus méretezési szabályokat is megadhat az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Egy Azure-terheléselosztó ezután elosztja a forgalmat a méretezési csoportban lévő virtuálisgép-példányok között. Ebben a rövid útmutatóban egy virtuálisgép-méretezési csoportot hoz létre az Azure Portalon.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása

Az Azure [terheléselosztó](../load-balancer/load-balancer-overview.md) elosztja a bejövő forgalmat a kifogástalan virtuálisgép-példányok között. 

Először hozzon létre egy nyilvános standard terheléselosztót a portál használatával. A létrehozott név és nyilvános IP-cím automatikusan konfigurálva lesz a terheléselosztó előtérként.

1. A keresőmezőbe írja be a **terheléselosztót**. A keresési eredmények között a **Piactér** csoportban válassza a **Terheléselosztó lehetőséget.**
1. A **Terheléselosztó létrehozása** lap **Alapjai** lapján adja meg vagy válassza ki a következő adatokat:

    | Beállítás                 | Érték   |
    | ---| ---|
    | Előfizetés  | Válassza ki előfizetését.    |    
    | Erőforráscsoport | Válassza **az Új létrehozása lehetőséget,** és írja be a *myVMSSResourceGroup* parancsot a szövegmezőbe.|
    | Name (Név)           | *myLoadBalancer*         |
    | Régió         | Válassza az **USA keleti régiója** lehetőséget.       |
    | Típus          | Válassza a **Nyilvános** lehetőséget.       |
    | SKU           | Válassza a **Normál**lehetőséget.       |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím  | *MyPip*   |
    | Hozzárendelés| Statikus |

1. Ha elkészült, válassza **a Véleményezés + létrehozás lehetőséget** 
1. Miután átment az ellenőrzésen, válassza a **Létrehozás gombot.** 

![Terheléselosztó létrehozása](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása
Méretezési csoportokat Windows Server-rendszerképpel vagy Linux-rendszerképpel (például RHEL, CentOS, Ubuntu vagy SLES) helyezhet üzembe.

1. Írja be a **Méretezés készletkifejezést** a keresőmezőbe. Az eredmények a **Piactér csoportban**válassza **a Virtuálisgép-méretezési csoportok**lehetőséget. Megnyílik **a Virtuálisgép méretezési csoport ának létrehozása** lap. 
1. Az **Alapok** fül **Projektadatok** részén győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, és válassza az **Új létrehozása** lehetőséget az Erőforráscsoport részen. Írja be a *myVMSSResourceGroup* nevet a névhez, majd kattintson az **OK gombra.** 
1. Írja be a *myScaleSet-et* a méretezési csoport neveként.
1. A **Régió területen**válassza ki a terület hez közeli területet.
1. Hagyja meg az **Orchestrator** **ScaleSet virtuális gépek** alapértelmezett értékét.
1. Válasszon ki egy piactérképet a **Kép**hez. Ebben a példában az *Ubuntu Server 18.04 LTS-t*választottuk.
1. Adja meg a kívánt felhasználónevet, és válasszon ki egy tetszőleges hitelesítési típust.
   - A **jelszónak** legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi feltétel közül háromnak: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter. További információkért lásd: [felhasználónév- és jelszókövetelmények](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Ha a Linux operációsrendszer-lemezt választja, választhatja az **SSH nyilvános kulcs** lehetőséget is. Csak a nyilvános kulcsot adja meg, például: *~/.ssh/id_rsa.pub*. [SSH-kulcsok létrehozásához és használatához](../virtual-machines/linux/mac-create-ssh-keys.md) használhatja a portál Azure Cloud Shell szolgáltatását is.
   
    ![Virtuálisgép-méretezési csoport létrehozása](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Válassza a **Tovább** gombot a többi oldal áthelyezéséhez. 
1. Hagyja meg a **Példány** és a Lemezek lap alapértelmezett **beállításait.**
1. A **Hálózatkezelés** lap **Terheléselosztás csoportban**válassza az **Igen** lehetőséget, ha a méretezési csoport példányait egy terheléselosztó mögé szeretné helyezni. 
1. A **Terheléselosztási beállításokban**válassza az **Azure terheléselosztó**lehetőséget.
1. A **Válassza ki a terheléselosztót,** válassza ki a korábban létrehozott *myLoadBalancer* lehetőséget.
1. A **Háttérkészlet kiválasztása**csoportban válassza **az Új létrehozása**lehetőséget, írja be a *myBackendPool*parancsot, majd válassza a **Létrehozás lehetőséget.**
1. Ha elkészült, válassza **a Véleményezés + create**lehetőséget. 
1. Miután átment az ellenőrzésen, válassza a **Létrehozás** lehetőséget a méretezési csoport üzembe helyezéséhez.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje az erőforráscsoportot, a méretezési csoportot és az összes kapcsolódó erőforrást. Ehhez jelölje ki a méretezési csoport erőforráscsoportját, majd kattintson a **Törlés gombra.**


## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban egy alapszintű méretezési csoportot hozott létre az Azure Portalon. Ha bővebb információra van szüksége, lépjen tovább az Azure-beli virtuálisgép-méretezési csoportok létrehozásáról és kezeléséről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli virtuálisgép-méretezési csoportok létrehozása és kezelése](tutorial-create-and-manage-powershell.md)
