---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 0e7a814c1607b15e3af0e76a5ae6dfad1594a3b3
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474261"
---
## <a name="limitations"></a>Korlátozások

- A virtuális gépek méretezési csoportjai jelenleg nem támogatottak a dedikált gazdagépeken.
- A kezdeti kiadás a következő virtuálisgép-sorozatot támogatja: DSv3, ESv3, FSv2, LSv2 és MSv2. 

## <a name="create-a-host-group"></a>Gazdagépcsoport létrehozása

A **gazda-csoport** egy új erőforrás, amely dedikált gazdagépek gyűjteményét jelöli. Egy adott régióban és egy rendelkezésre állási zónában hozhat létre egy gazdagépet, és hozzáadhat gazdagépeket. A magas rendelkezésre állás tervezése során további lehetőségek is rendelkezésre állnak. A dedikált gazdagépekhez a következő lehetőségek közül választhat: 
- Több rendelkezésre állási zónára kiterjedő span. Ebben az esetben minden használni kívánt zónában rendelkeznie kell egy gazdagép-csoporttal.
- Több tartalék tartomány között, amelyek fizikai állványokra vannak leképezve. 
 
Mindkét esetben meg kell adnia a gazdagép-csoport tartalék tartományának darabszámát. Ha nem szeretné a csoportban lévő tartalék tartományokat kivonni, használja az 1. számú tartalék tartományt. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használja. 

Ebben a példában egy 1 rendelkezésre állási zónát és 2 tartalék tartományt használó gazdagépet hozunk létre. 


1. Nyissa meg az Azure [Portalt](https://portal.azure.com).
1. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Keresse meg a **gazdagépet** , majd válassza az eredmények közül a **gazdagép csoportok** lehetőséget.

    ![A gazdagép-csoportok keresési eredménye.](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. A **gazdagép-csoportok** lapon válassza a **Létrehozás**lehetőséget.
1. Válassza ki a használni kívánt előfizetést, majd válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához.
1. A név mezőbe írja be a *MyDedicatedHostsRG* **nevet** , majd kattintson az **OK gombra**.
1. A **gazdagép-csoport neve**mezőbe írja be a következőt: *myHostGroup*.
1. A hely mezőben válassza az **USA keleti** **régiója**lehetőséget.
1. A **rendelkezésre állási zóna**beállításnál válassza az **1**lehetőséget.
1. A tartalék **tartományok száma**beállításnál válassza a **2**lehetőséget.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd várja meg az érvényesítést.

    ![Gazda-csoport beállításai](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. Ha megjelenik az **átadott érvényesítési** üzenet, válassza a **Létrehozás** lehetőséget a gazda csoport létrehozásához.

Csak néhány percet vehet igénybe a gazda csoport létrehozásakor.

## <a name="create-a-dedicated-host"></a>Dedikált gazdagép létrehozása

Most hozzon létre egy dedikált gazdagépet a gazdagép csoportban. A gazdagép neve mellett meg kell adnia a gazdagéphez tartozó SKU-t is. A gazdagép SKU rögzíti a támogatott virtuálisgép-sorozatot, valamint a dedikált gazdagép hardveres generációját.

A gazdagép SKU-ról és a díjszabásról további információt az [Azure dedikált gazdagép díjszabása](https://aka.ms/ADHPricing)című témakörben talál.

Ha a gazdagéphez a tartalék tartományokat állítja be, a rendszer megkéri, hogy adja meg a gazdagép tartalék tartományát.  

1. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Keressen rá a **dedikált gazdagépre** , majd válassza ki a **dedikált gazdagépeket** az eredmények közül.

    ![A gazdagép-csoportok keresési eredménye.](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. A **dedikált gazdagépek** lapon válassza a **Létrehozás**lehetőséget.
1. Válassza ki a használni kívánt előfizetést.
1. Válassza az *myDedicatedHostsRG* elemet az **erőforráscsoport**elemnél.
1. A **példány részletei**mezőbe írja be a myHost **nevet** , és válassza az *USA keleti* régiója lehetőséget a helyhez.
1. A **hardverprofil**területen válassza a *standard Es3 Family-Type 1* elemet a **család méretéhez**, válassza a *MyHostGrup* lehetőséget **, majd válassza** az *1* értéket a tartalék **tartomány**számára. Hagyja meg az alapértelmezett értékeket a többi mezőnél.
1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** lehetőséget, és várja meg az érvényesítést.

    ![Gazdagép beállításai](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. Ha megjelenik az **átadott érvényesítési** üzenet, válassza a **Létrehozás** lehetőséget a gazdagép létrehozásához.


