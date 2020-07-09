---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79128998"
---
## <a name="limitations"></a>Korlátozások

- A virtuális gépek méretezési csoportjai jelenleg nem támogatottak a dedikált gazdagépeken.
- A dedikált gazdagépek számára elérhető méretek és hardver típusok régiónként eltérőek. További információért tekintse meg a gazdagép [díjszabását ismertető oldalt](https://aka.ms/ADHPricing) .

## <a name="create-a-host-group"></a>Gazda csoport létrehozása

A **gazda-csoport** egy új erőforrás, amely dedikált gazdagépek gyűjteményét jelöli. Egy adott régióban és egy rendelkezésre állási zónában hozhat létre egy gazdagépet, és hozzáadhat gazdagépeket. A magas rendelkezésre állás tervezése során további lehetőségek is rendelkezésre állnak. A dedikált gazdagépekhez a következő lehetőségek közül választhat: 
- Több rendelkezésre állási zónára kiterjedő span. Ebben az esetben minden használni kívánt zónában rendelkeznie kell egy gazdagép-csoporttal.
- Több tartalék tartomány között, amelyek fizikai állványokra vannak leképezve. 
 
Mindkét esetben meg kell adnia a gazdagép-csoport tartalék tartományának darabszámát. Ha nem szeretné a csoportban lévő tartalék tartományokat kivonni, használja az 1. számú tartalék tartományt. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használja. 

Ebben a példában egy 1 rendelkezésre állási zónát és 2 tartalék tartományt használó gazdagépet hozunk létre. 


1. Nyissa meg az Azure [Portalt](https://portal.azure.com).
1. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Keresse meg a **gazdagépet** , majd válassza az eredmények közül a **gazdagép csoportok** lehetőséget.
1. A **gazdagép-csoportok** lapon válassza a **Létrehozás**lehetőséget.
1. Válassza ki a használni kívánt előfizetést, majd válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához.
1. A név mezőbe írja be a *MyDedicatedHostsRG* **nevet** , majd kattintson az **OK gombra**.
1. A **gazdagép-csoport neve**mezőbe írja be a következőt: *myHostGroup*.
1. A hely mezőben válassza az **USA keleti** **régiója**lehetőséget.
1. A **rendelkezésre állási zóna**beállításnál válassza az **1**lehetőséget.
1. A tartalék **tartományok száma**beállításnál válassza a **2**lehetőséget.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd várja meg az érvényesítést.
1. Ha megjelenik az **átadott érvényesítési** üzenet, válassza a **Létrehozás** lehetőséget a gazda csoport létrehozásához.

Csak néhány percet vehet igénybe a gazda csoport létrehozásakor.

## <a name="create-a-dedicated-host"></a>Dedikált gazdagép létrehozása

Most hozzon létre egy dedikált gazdagépet a gazdagép csoportban. A gazdagép neve mellett meg kell adnia a gazdagéphez tartozó SKU-t is. A gazdagép SKU rögzíti a támogatott virtuálisgép-sorozatot, valamint a dedikált gazdagép hardveres generációját.

A gazdagép SKU-ról és a díjszabásról további információt az [Azure dedikált gazdagép díjszabása](https://aka.ms/ADHPricing)című témakörben talál.

Ha a gazdagéphez a tartalék tartományokat állítja be, a rendszer megkéri, hogy adja meg a gazdagép tartalék tartományát.  

1. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Keressen rá a **dedikált gazdagépre** , majd válassza ki a **dedikált gazdagépeket** az eredmények közül.
1. A **dedikált gazdagépek** lapon válassza a **Létrehozás**lehetőséget.
1. Válassza ki a használni kívánt előfizetést.
1. Válassza az *myDedicatedHostsRG* elemet az **erőforráscsoport**elemnél.
1. A **példány részletei**mezőbe írja *myHost* be a myHost **nevet** , és válassza az *USA keleti* régiója lehetőséget a helyhez.
1. A **hardverprofil**területen válassza a *standard Es3 Family-Type 1* elemet a **család méretéhez**, válassza a *MyHostGroup* lehetőséget **, majd válassza** az *1* értéket a tartalék **tartomány**számára. Hagyja meg az alapértelmezett értékeket a többi mezőnél.
1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** lehetőséget, és várja meg az érvényesítést.
1. Ha megjelenik az **átadott érvényesítési** üzenet, válassza a **Létrehozás** lehetőséget a gazdagép létrehozásához.


