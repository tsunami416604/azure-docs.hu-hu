---
title: Az Azure dedikált állomásainak üzembe helyezése az Azure Portalhasználatával
description: Virtuális gépek üzembe helyezése dedikált állomásokaz Azure Portalhasználatával.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086765"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Virtuális gépek telepítése dedikált állomásokra a portál használatával

Ez a cikk végigvezeti, hogyan hozhat létre egy Azure [dedikált gazdagépet](dedicated-hosts.md) a virtuális gépek (Virtuális gépek) üzemeltetéséhez. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában az **Erőforrás létrehozása** gombra.
1. Az Azure Marketplace-erőforrások fölött lévő keresőmezőben keressen az **Ubuntu Server 16.04 LTS** (Canonical) elemre, és válassza ki, majd válassza a **Létrehozás** lehetőséget.
1. Az **Alapok lapon** a **Project részletei**csoportban győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, majd válassza a *myDedicatedHostsRG* lehetőséget **erőforráscsoportként.** 
1. A **Példány részletei** területen írja a *myVM* nevet a **Virtuális gép neve** mezőbe, majd a **Régió** menüjéből válassza ki az *USA keleti régiója* lehetőséget.
1. Az **Elérhetőségi beállítások csoportban** válassza a **Rendelkezésre állási zóna**lehetőséget, válassza ki az *1* elemet a legördülő menüből.
1. A mérethez válassza a **Méret módosítása**lehetőséget. A rendelkezésre álló méretek listájában válasszon egyet az Esv3 sorozatból, például **a Standard E2s v3.** Előfordulhat, hogy törölnie kell a szűrőt, hogy láthassa az összes rendelkezésre álló méretet.
1. A **Rendszergazdai fiók** részen válassza a **Nyilvános SSH-kulcs**, lehetőséget, írja be a felhasználónevét, majd illessze be a nyilvános kulcsát a szövegmezőbe. Távolítsa el a kezdő vagy záró szóközöket a nyilvános kulcsból.

    ![Rendszergazdai fiók](./media/quick-create-portal/administrator-account.png)

1. A **Bejövő portszabályok** > **Csoportban A bejövő portok nyilvános lehívása csoportban**válassza a Kijelölt **portok engedélyezése lehetőséget,** majd válassza az **SSH (22)** elemet a legördülő menüből. 
1. A lap tetején válassza a **Speciális** lapot, majd a **Host (Gazdagép)** szakaszban válassza a *myHostGroup* for **Host group** és a *myHost* for the **Host**lehetőséget. 
    ![Állomáscsoport és gazdagép kiválasztása](./media/dedicated-hosts-portal/advanced.png)
1. Hagyja meg a többi alapértelmezett beállítást, és kattintson a **Felülvizsgálat + létrehozás** gombra a lap alján.
1. Amikor megjelenik az érvényesítési üzenet, válassza a **Létrehozás gombot.**

A virtuális gép üzembe helyezése eltarthat néhány percig.

## <a name="add-an-existing-vm"></a>Meglévő virtuális gép hozzáadása 

Hozzáadhat egy kilépő virtuális gép egy dedikált gazdagép, de a virtuális gép először le kell állítani\Felszabadított. Mielőtt áthelyezne egy virtuális gép egy dedikált gazdagép, győződjön meg arról, hogy a virtuális gép konfigurációja támogatott:

- A virtuális gép méretének ugyanolyan méretűnek kell lennie, mint a dedikált gazdagépnek. Például ha a dedikált gazdagép DSv3, majd a virtuális gép mérete lehet Standard_D4s_v3, de nem lehet Standard_A4_v2. 
- A virtuális gép nek ugyanabban a régióban kell lennie, mint a dedikált gazdagép.
- A virtuális gép nem lehet része a közelség elhelyezési csoport. Távolítsa el a virtuális gép a közelség elhelyezési csoport, mielőtt áthelyezi egy dedikált gazdagép. További információ: [Virtuális gép áthelyezése a közelségelhelyezési csoportból](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- A virtuális gép nem lehet egy rendelkezésre állási csoportban.
- Ha a virtuális gép egy rendelkezésre állási zónában van, akkor a gazdagépcsoportokkal azonos rendelkezésre állási zónának kell lennie. A virtuális gép és a gazdagépcsoport rendelkezésre állási zóna beállításainak egyezniük kell.

Helyezze át a virtuális gép egy dedikált állomás a [portál](https://portal.azure.com)használatával.

1. Nyissa meg a virtuális gép lapját.
1. Válassza **a Leállítás** lehetőséget a virtuális gép felszabadításának leállításához.
1. Válassza a bal oldali menü **Konfiguráció** parancsát.
1. Válasszon ki egy gazdagépcsoportot és egy állomást a legördülő menüből.
1. Ha végzett, válassza a **Mentés** lehetőséget a lap tetején.
1. Miután a virtuális gép hozzá lett adva a gazdagéphez, válassza a bal oldali menü **Áttekintés parancsát.**
1. A lap tetején válassza az **Indítás lehetőséget** a virtuális gép újraindításához.

## <a name="next-steps"></a>További lépések

- További információt a [Dedikált állomások](dedicated-hosts.md) áttekintése című témakörben talál.

- Van egy mintasablon, amely zónákat és tartalék tartományokat is használ a maximális rugalmasság érdekében egy régióban. [here](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)

- Az [Azure CLI](dedicated-hosts-cli.md)használatával dedikált gazdatst is üzembe helyezhet.



