---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226704"
---
1. A Feladatátvevőfürt-kezelőben bontsa ki a **szerepkörök**, és jelölje ki a rendelkezésre állási csoport.  

2. Az a **erőforrások** fülre, kattintson a jobb gombbal a figyelő nevét, majd **tulajdonságok**.

3. Kattintson a **függőségek** fülre. Ha több erőforrás szerepel a listában, győződjön meg arról, hogy az IP-címek vagy nem rendelkezik-e és, függőségeit.  

4. Kattintson az **OK** gombra.

5. Kattintson a jobb gombbal a figyelő nevét, és kattintson **online állapotba hozás**.

6. Miután a figyelő online, a **erőforrások** fülre, kattintson a jobb gombbal a rendelkezésre állási csoportot, majd **tulajdonságai**.
   
    ![A rendelkezésre állási csoport erőforrása konfigurálása](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Függőség létrehozása a figyelő neve erőforráson (nem az IP-cím erőforrás neve), és kattintson a **OK**.
   
    ![Adja hozzá a függőségi figyelő neve](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Indítsa el az SQL Server Management Studiót, és kapcsolódjon az elsődleges replika.

9. Lépjen a **AlwaysOn magas rendelkezésre állás** > **rendelkezésre állási csoportok** > **\<AvailabilityGroupName\>**   >  **Rendelkezésre állási csoport figyelője**.  
    A figyelő nevét, amelyet a Feladatátvevőfürt-kezelő hozott üzenetnek kell megjelennie.

10. Kattintson a jobb gombbal a figyelő nevét, és kattintson **tulajdonságok**.

11. Az a **Port** mezőt, a korábban már használt $EndpointPort használatával adja meg a port számát a rendelkezésre állási csoport figyelőjét (ebben az oktatóanyagban a 1433-as volt az alapértelmezett), és kattintson a **OK**.

