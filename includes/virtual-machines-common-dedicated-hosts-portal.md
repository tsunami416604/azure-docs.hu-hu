---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128998"
---
## <a name="limitations"></a>Korlátozások

- A virtuálisgép-méretezési csoportok jelenleg nem támogatottak dedikált állomásokon.
- A dedikált gazdagépek számára elérhető méretek és hardvertípusok régiónként eltérőek lehetnek. További információ a gazdagép [díjszabási oldalán.](https://aka.ms/ADHPricing)

## <a name="create-a-host-group"></a>Gazdagépcsoport létrehozása

A **gazdagépcsoport** egy új erőforrás, amely dedikált gazdagépek gyűjteményét képviseli. Hozzon létre egy gazdagépcsoportot egy régióban és egy rendelkezésre állási zónát, és adjon hozzá állomásokat. A magas rendelkezésre állás tervezésekor további lehetőségek is rendelkezésre állnak. Az alábbi lehetőségek egyikét vagy mindkettőt használhatja a dedikált gazdagépekkel: 
- Több rendelkezésre állási zónára is kiterjedjen. Ebben az esetben minden használni kívánt zónában rendelkeznie kell egy gazdagépcsoporttal.
- Több tartalék tartományban, amelyek fizikai állványokra vannak leképezve. 
 
Mindkét esetben meg kell adnia a gazdagépcsoport tartaléktartomány-számát. Ha nem szeretné span tartalék tartományok a csoportban, használja a tartalék tartomány száma 1. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használja. 

Ebben a példában 1 rendelkezésre állási zóna és 2 tartalék tartomány használatával hozunk létre egy gazdagépcsoportot. 


1. Nyissa meg az Azure [Portalt.](https://portal.azure.com)
1. Válassza **az Erőforrás létrehozása lehetőséget** a bal felső sarokban.
1. Keressen **gazdagépcsoportot,** majd válassza a **Gazdagépcsoportok elemet** az eredmények közül.
1. A **Gazdagépcsoportok** lapon válassza a **Létrehozás gombot.**
1. Jelölje ki a használni kívánt előfizetést, majd válassza az **Új létrehozása lehetőséget** új erőforráscsoport létrehozásához.
1. Írja be *a myDedicatedHostsRG* **nevet,** majd válassza az **OK gombot.**
1. A **Gazdagépcsoport nevéhez**írja be a *myHostGroup (Állomáscsoport) mezőbe*a .
1. A **Hely területen**válassza az USA keleti **része**lehetőséget.
1. A **Rendelkezésre állási zóna területen**válassza az **1**lehetőséget.
1. A **tartaléktartomány-szám esetén**válassza a **2**lehetőséget.
1. Válassza **a Véleményezés + létrehozás lehetőséget,** majd várja meg az érvényesítést.
1. Miután megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás** gombot a gazdagépcsoport létrehozásához.

A gazdagépcsoport létrehozása csak néhány percet vesz igénybe.

## <a name="create-a-dedicated-host"></a>Dedikált gazdagép létrehozása

Most hozzon létre egy dedikált gazdatkéntaként a gazdagépcsoportban. A gazdagép neve mellett meg kell adnia a termékváltozatot is az állomásszámára. A gazdatermékváltozat rögzíti a támogatott virtuális gép sorozat, valamint a hardver-generálás a dedikált gazdagép.

A gazdagép számos szolgáltatásáról és díjszabásáról az [Azure dedikált gazdagépdíj-szabása](https://aka.ms/ADHPricing)című témakörben talál további információt.

Ha tartalék tartományszámot állít be a gazdagépcsoporthoz, a rendszer megkéri, hogy adja meg a gazdagép tartalék tartományát.  

1. Válassza **az Erőforrás létrehozása lehetőséget** a bal felső sarokban.
1. Keressen **rá a Dedikált gazdagépre,** majd válassza a **Dedikált állomások** lehetőséget az eredmények közül.
1. A **Dedikált állomások** lapon válassza a **Létrehozás lehetőséget.**
1. Válassza ki a használni kívánt előfizetést.
1. Válassza ki *a myDedicatedHostsRG-t* **erőforráscsoportként.**
1. Az **példány részletei**, írja be a *myHost* a **név,** és válassza *ki az USA keleti része* a helyhez.
1. A **Hardverprofilban**válassza a *Szabványos Es3 család – 1.* **Size family** *myHostGroup* **Host group** *1* **Fault domain** Hagyja meg az alapértelmezett értékeket a többi mezőre.
1. Ha elkészült, válassza **a Véleményezés + létrehozás** és várakozás az érvényesítésre lehetőséget.
1. Miután megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás** gombot az állomás létrehozásához.


