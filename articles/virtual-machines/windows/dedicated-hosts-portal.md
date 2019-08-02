---
title: Az Azure dedikált gazdagépek üzembe helyezése a Azure Portal használatával | Microsoft Docs
description: A virtuális gépeket a Azure Portal használatával dedikált gazdagépekre helyezheti üzembe.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/26/2019
ms.author: cynthn
ms.openlocfilehash: 0a3cb76d1e28559727957c1007b0992fb8e90f2b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700560"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Előzetes verzió: Virtuális gépek üzembe helyezése dedikált gazdagépeken a portál használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre egy [dedikált](dedicated-hosts.md) Azure-gazdagépet a virtuális gépek (VM-EK) üzemeltetéséhez. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozása** lehetőséget.
1. Az **új** lap **népszerű**területén válassza a **Windows Server 2016 Datacenter**elemet.
1. Az **alapvető beállítások** lap **projekt részletei**területén ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva, majd válassza a *myDedicatedHostsRG* **lehetőséget.** 
1. A **Példány részletei** területen írja a *myVM* nevet a **Virtuális gép neve** mezőbe, majd a **Régió** menüjéből válassza ki az *USA keleti régiója* lehetőséget.
1. A **rendelkezésre állási beállítások** területen válassza a **rendelkezésre állási zóna**elemet, majd a legördülő listából válassza az *1* lehetőséget.
1. A méret beállításnál válassza a **méret módosítása**lehetőséget. Az elérhető méretek listájában válasszon egyet a Esv3 sorozatból, például a **standard E2s v3**-as verzióban. Előfordulhat, hogy törölnie kell a szűrőt, hogy az összes rendelkezésre álló méretet meg lehessen tekinteni.
1. A **Rendszergazdai fiók**, területen adjon meg egy felhasználónevet (pl. *azureuser*) és egy jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. A **bejövő portszabályok**területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** lehetőséget a legördülő menüből.
1. A lap tetején válassza a **speciális** fület, majd a **gazdagép** szakaszban válassza a *myHostGroup* lehetőséget a gazdagéphez **és a** *myHost* . 
    ![A gazda csoport és a gazdagép kiválasztása](./media/dedicated-hosts-portal/advanced.png)
1. Hagyja változatlanul a többi alapértelmezett beállítást, és kattintson a **Áttekintés + létrehozás** gombra a lap alján.
1. Amikor megjelenik az érvényesítés által átadott üzenet, válassza a **Létrehozás**lehetőséget.


## <a name="next-steps"></a>További lépések

- További információt a [dedikált gazdagépek](dedicated-hosts.md) áttekintése című témakörben talál. 

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A dedikált gazdagépeket [Azure PowerShell](dedicated-hosts-powershell.md)használatával is üzembe helyezheti.