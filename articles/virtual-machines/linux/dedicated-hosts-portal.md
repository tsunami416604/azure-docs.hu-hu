---
title: Az Azure dedikált gazdagépek üzembe helyezése a Azure Portal használatával
description: A virtuális gépeket a Azure Portal használatával dedikált gazdagépekre helyezheti üzembe.
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 296c43c7c1345c67cc5b83749272e56c122ee535
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036428"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Előzetes verzió: virtuális gépek üzembe helyezése dedikált gazdagépeken a portál használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre egy dedikált Azure- [gazdagépet](dedicated-hosts.md) a virtuális gépek (VM-EK) üzemeltetéséhez. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában az **Erőforrás létrehozása** gombra.
1. Az Azure Marketplace-erőforrások fölött lévő keresőmezőben keressen az **Ubuntu Server 16.04 LTS** (Canonical) elemre, és válassza ki, majd válassza a **Létrehozás** lehetőséget.
1. Az **alapvető beállítások** lap **projekt részletei**területén ellenőrizze, **hogy a megfelelő**előfizetés van-e kiválasztva, majd válassza a *myDedicatedHostsRG* lehetőséget. 
1. A **Példány részletei** területen írja a *myVM* nevet a **Virtuális gép neve** mezőbe, majd a *Régió* menüjéből válassza ki az **USA keleti régiója** lehetőséget.
1. A **rendelkezésre állási beállítások** területen válassza a **rendelkezésre állási zóna**elemet, majd a legördülő listából válassza az *1* lehetőséget.
1. A méret beállításnál válassza a **méret módosítása**lehetőséget. Az elérhető méretek listájában válasszon egyet a Esv3 sorozatból, például a **standard E2s v3**-as verzióban. Előfordulhat, hogy törölnie kell a szűrőt, hogy az összes rendelkezésre álló méretet meg lehessen tekinteni.
1. A **Rendszergazdai fiók** részen válassza a **Nyilvános SSH-kulcs**, lehetőséget, írja be a felhasználónevét, majd illessze be a nyilvános kulcsát a szövegmezőbe. Távolítsa el a kezdő vagy záró szóközöket a nyilvános kulcsból.

    ![Rendszergazdai fiók](./media/quick-create-portal/administrator-account.png)

1. A **bejövő portszabályok** > **nyilvános bejövő portok**területen válassza a **kiválasztott portok engedélyezése** lehetőséget, majd válassza az **SSH (22)** lehetőséget a legördülő menüből. 
1. A lap tetején válassza a **speciális** fület, majd a **gazdagép** szakaszban **válassza a** *MyHostGroup* lehetőséget **a gazdagéphez és a** *myHost* . 
    ![válassza ki a gazda csoportot és a gazdagépet](./media/dedicated-hosts-portal/advanced.png)
1. Hagyja változatlanul a többi alapértelmezett beállítást, és kattintson a **Áttekintés + létrehozás** gombra a lap alján.
1. Amikor megjelenik az érvényesítés által átadott üzenet, válassza a **Létrehozás**lehetőséget.

A virtuális gép üzembe helyezése eltarthat néhány percig.

## <a name="next-steps"></a>Következő lépések

- További információt a [dedikált gazdagépek](dedicated-hosts.md) áttekintése című témakörben talál.

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A dedikált gazdagépeket az [Azure CLI](dedicated-hosts-cli.md)használatával is üzembe helyezheti.



