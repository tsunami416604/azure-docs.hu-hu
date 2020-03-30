---
title: Az Azure dedikált állomásainak üzembe helyezése a portál használatával
description: Telepítse a virtuális gépeket dedikált állomásokra a portál használatával.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 3d014014b540e5ea5959483427dec4b239ceaf7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476790"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Virtuális gépek telepítése dedikált állomásokra a portál használatával

Ez a cikk végigvezeti, hogyan hozhat létre egy Azure [dedikált gazdagépet](dedicated-hosts.md) a virtuális gépek (Virtuális gépek) üzemeltetéséhez. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozása** lehetőséget.
1. Az **Új** lap **Népszerű**csoportban válassza a **Windows Server 2016 Datacenter lehetőséget.**
1. Az **Alapok lapon** a **Project részletei**csoportban győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, majd válassza a *myDedicatedHostsRG* lehetőséget **erőforráscsoportként.** 
1. A **Példány részletei** területen írja a *myVM* nevet a **Virtuális gép neve** mezőbe, majd a **Régió** menüjéből válassza ki az *USA keleti régiója* lehetőséget.
1. Az **Elérhetőségi beállítások csoportban** válassza a **Rendelkezésre állási zóna**lehetőséget, válassza ki az *1* elemet a legördülő menüből.
1. A mérethez válassza a **Méret módosítása**lehetőséget. A rendelkezésre álló méretek listájában válasszon egyet az Esv3 sorozatból, például **a Standard E2s v3.** Előfordulhat, hogy törölnie kell a szűrőt, hogy láthassa az összes rendelkezésre álló méretet.
1. A **Rendszergazdai fiók**, területen adjon meg egy felhasználónevet (pl. *azureuser*) és egy jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. A **Bejövő portszabályok csoportban**válassza **a Kijelölt portok engedélyezése lehetőséget,** majd válassza az **RDP (3389)** elemet a legördülő menüből.
1. A lap tetején válassza a **Speciális** lapot, majd a **Host (Gazdagép)** szakaszban válassza a *myHostGroup* for **Host group** és a *myHost* for the **Host**lehetőséget. 
    ![Állomáscsoport és gazdagép kiválasztása](./media/dedicated-hosts-portal/advanced.png)
1. Hagyja meg a többi alapértelmezett beállítást, és kattintson a **Felülvizsgálat + létrehozás** gombra a lap alján.
1. Amikor megjelenik az érvényesítési üzenet, válassza a **Létrehozás gombot.**

## <a name="add-an-existing-vm"></a>Meglévő virtuális gép hozzáadása 

Hozzáadhat egy meglévő virtuális gép egy dedikált gazdagép, de a virtuális gép először le kell állítani\Felszabadított. Mielőtt áthelyezne egy virtuális gép egy dedikált gazdagép, győződjön meg arról, hogy a virtuális gép konfigurációja támogatott:

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

- Az [Azure PowerShell](dedicated-hosts-powershell.md)használatával dedikált gazdatina is üzembe helyezhet.
