---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179070"
---
1. A Feladatátvevőfürt-kezelő bontsa ki a **szerepkörök**elemet, majd jelölje ki a rendelkezésre állási csoportot.  

2. Az **erőforrások** lapon kattintson a jobb gombbal a figyelő nevére, majd kattintson a **Tulajdonságok**elemre.

3. Kattintson a **függőségek** fülre. Ha több erőforrás van felsorolva, ellenőrizze, hogy az IP-címek rendelkeznek-e vagy, sem és, függőségekkel.  

4. Kattintson az **OK** gombra.

5. Kattintson a jobb gombbal a figyelő nevére, majd kattintson az **online állapotba hozás**lehetőségre.

6. Miután a figyelő online állapotú, az **erőforrások** lapon kattintson a jobb gombbal a rendelkezésre állási csoportra, majd kattintson a **Tulajdonságok**elemre.
   
    ![A rendelkezésre állási csoport erőforrásának konfigurálása](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Hozzon létre egy függőséget a figyelő neve erőforrásban (ne az IP-cím erőforrásainak nevével), majd kattintson **az OK**gombra.
   
    ![Függőség hozzáadása a figyelő nevéhez](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Indítsa el SQL Server Management Studio, majd kapcsolódjon az elsődleges replikához.

9. Nyissa meg a **AlwaysOn magas rendelkezésre**állási  >  **csoportok**  >  **\<AvailabilityGroupName\>**  >  **rendelkezésre állási csoportjának figyelőit**.  
    A Feladatátvevőfürt-kezelőban létrehozott figyelő nevét meg kell jeleníteni.

10. Kattintson a jobb gombbal a figyelő nevére, majd kattintson a **Tulajdonságok**elemre.

11. A **port** mezőben adja meg a rendelkezésre állási csoport figyelő portszámát a korábban használt $EndpointPort használatával (ebben az oktatóanyagban az 1433 volt az alapértelmezett), majd kattintson az **OK**gombra.

