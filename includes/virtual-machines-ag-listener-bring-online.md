---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179070"
---
1. A Feladatátvevőfürt-kezelőben bontsa ki a **Szerepkörök**csomópontot, majd jelölje ki az elérhetőségi csoportot.  

2. Az **Erőforrások** lapon kattintson a jobb gombbal a figyelő nevére, majd kattintson a **Tulajdonságok parancsra.**

3. Kattintson a **Függőségek** fülre. Ha több erőforrás szerepel a listában, ellenőrizze, hogy az IP-címek OR, nem ÉS, függőségekkel rendelkeznek-e.  

4. Kattintson az **OK** gombra.

5. Kattintson a jobb gombbal a figyelő nevére, majd kattintson **az Online állapot ra.**

6. Miután a figyelő online állapotba került, az **Erőforrások** lapon kattintson a jobb gombbal az elérhetőségi csoportra, majd kattintson a **Tulajdonságok parancsra.**
   
    ![Az elérhetőségi csoport erőforrásának konfigurálása](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Hozzon létre egy függőséget a figyelő néverőforrását (nem az IP-cím erőforrások nevét), majd kattintson az **OK**gombra.
   
    ![Függőség hozzáadása a figyelő nevétől](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Indítsa el az SQL Server Management Studio alkalmazást, majd csatlakozzon az elsődleges kópiához.

9. Nyissa meg **az AlwaysOn magas rendelkezésre állási rendelkezésre állási** > **csoportok** > **\<elérhetőségi csoportfigyelőit.\>****Availability Group Listeners** >   
    Meg kell jelenjenie a feladatátvevői fürtkezelőben létrehozott figyelőnévnek.

10. Kattintson a jobb gombbal a figyelő nevére, majd kattintson **a Tulajdonságok parancsra.**

11. A **Port** mezőben adja meg a rendelkezésre állási csoportfigyelő portszámát a korábban használt $EndpointPort használatával (ebben az oktatóanyagban az 1433 volt az alapértelmezett), majd kattintson az **OK**gombra.

