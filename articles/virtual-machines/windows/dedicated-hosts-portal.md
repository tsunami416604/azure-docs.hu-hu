---
title: Azure dedikált gazdagépek üzembe helyezése a portál használatával
description: Virtuális gépek üzembe helyezése dedikált gazdagépeken a portál használatával.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: b6f5e155b76535c4d9e0080983d5f54cec3adb01
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086949"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Virtuális gépek üzembe helyezése dedikált gazdagépeken a portál használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre egy dedikált Azure- [gazdagépet](dedicated-hosts.md) a virtuális gépek (VM-EK) üzemeltetéséhez. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozása** lehetőséget.
1. Az **új** lap **népszerű**területén válassza a **Windows Server 2016 Datacenter**elemet.
1. Az **alapvető beállítások** lap **projekt részletei**területén ellenőrizze, **hogy a megfelelő**előfizetés van-e kiválasztva, majd válassza a *myDedicatedHostsRG* lehetőséget. 
1. A **Példány részletei** területen írja a *myVM* nevet a **Virtuális gép neve** mezőbe, majd a *Régió* menüjéből válassza ki az **USA keleti régiója** lehetőséget.
1. A **rendelkezésre állási beállítások** területen válassza a **rendelkezésre állási zóna**elemet, majd a legördülő listából válassza az *1* lehetőséget.
1. A méret beállításnál válassza a **méret módosítása**lehetőséget. Az elérhető méretek listájában válasszon egyet a Esv3 sorozatból, például a **standard E2s v3**-as verzióban. Előfordulhat, hogy törölnie kell a szűrőt, hogy az összes rendelkezésre álló méretet meg lehessen tekinteni.
1. A **Rendszergazdai fiók**, területen adjon meg egy felhasználónevet (pl. *azureuser*) és egy jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. A **bejövő portszabályok**területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** lehetőséget a legördülő menüből.
1. A lap tetején válassza a **speciális** fület, majd a **gazdagép** szakaszban **válassza a** *MyHostGroup* lehetőséget **a gazdagéphez és a** *myHost* . 
    ![válassza ki a gazda csoportot és a gazdagépet](./media/dedicated-hosts-portal/advanced.png)
1. Hagyja változatlanul a többi alapértelmezett beállítást, és kattintson a **Áttekintés + létrehozás** gombra a lap alján.
1. Amikor megjelenik az érvényesítés által átadott üzenet, válassza a **Létrehozás**lehetőséget.

## <a name="add-an-existing-vm"></a>Meglévő virtuális gép hozzáadása 

A kilépő virtuális gépet egy dedikált gazdagéphez is hozzáadhatja, de a virtuális gépnek először Stop\Deallocated. kell lennie. Mielőtt áthelyezi a virtuális gépet egy dedikált gazdagépre, győződjön meg arról, hogy a virtuális gép konfigurációja támogatott:

- A virtuális gép méretének azonos méretűnek kell lennie, mint a dedikált gazdagépnek. Ha például a dedikált gazdagép DSv3, akkor a virtuális gép mérete Standard_D4s_v3, de nem lehet Standard_A4_v2. 
- A virtuális gépnek ugyanabban a régióban kell lennie, ahol a dedikált gazdagép található.
- A virtuális gép nem lehet a közelségi elhelyezési csoport része. A dedikált gazdagépre való áthelyezés előtt távolítsa el a virtuális gépet a közelségi csoportból. További információ: [virtuális gép áthelyezése a közelségi csoportból](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- A virtuális gép nem lehet rendelkezésre állási készletben.
- Ha a virtuális gép egy rendelkezésre állási zónában van, akkor a gazdagép-csoporttal megegyező rendelkezésre állási zónának kell lennie. A virtuális gép rendelkezésre állási zónájának beállításai és a gazdagép csoportjának egyeznie kell.

Helyezze át a virtuális gépet egy dedikált gazdagépre a [portál](https://portal.azure.com)használatával.

1. Nyissa meg a virtuális gép lapját.
1. A virtuális gép stop\deallocate válassza a **Leállítás** lehetőséget.
1. A bal oldali menüben válassza a **konfiguráció** lehetőséget.
1. Válasszon ki egy gazdagépet és egy gazdagépet a legördülő menükből.
1. Ha elkészült, válassza a **Mentés** lehetőséget az oldal tetején.
1. Miután hozzáadta a virtuális gépet a gazdagéphez, válassza az **Áttekintés** lehetőséget a bal oldali menüben.
1. A lap tetején kattintson a **Start** gombra a virtuális gép újraindításához.

## <a name="next-steps"></a>Következő lépések

- További információt a [dedikált gazdagépek](dedicated-hosts.md) áttekintése című témakörben talál. 

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A dedikált gazdagépeket [Azure PowerShell](dedicated-hosts-powershell.md)használatával is üzembe helyezheti.
