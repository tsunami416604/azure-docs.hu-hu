---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179068"
---
Ebben a lépésben manuálisan hozza létre a rendelkezésre állási csoport figyelőjét a Feladatátvevő fürtkezelőben és az SQL Server Management Studio-ban.

1. Nyissa meg a feladatátvevőfürt-kezelőt az elsődleges kópiát tartalmazó csomópontról.

2. Jelölje ki a **Hálózatok** csomópontot, majd jegyezze fel a fürthálózat nevét. Ez a név a PowerShell-parancsfájl $ClusterNetworkName változójában használatos.

3. Bontsa ki a fürt nevét, majd kattintson a **Szerepkörök gombra.**

4. A **Szerepkörök** ablaktáblán kattintson a jobb gombbal az elérhetőségi csoport nevére, majd válassza **az Erőforrás-ügyfél** > hozzáférési pontjának hozzáadása**parancsot.**
   
    ![Ügyfél-hozzáférési pont hozzáadása az elérhetőségi csoporthoz](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. A **Név** mezőben hozzon létre egy nevet az új figyelőnek, kattintson kétszer a **Tovább** gombra, majd kattintson a **Befejezés gombra.**  
    Ne hozza a figyelő vagy erőforrás online ezen a ponton.

6. Kattintson az **Erőforrások** fülre, majd bontsa ki az imént létrehozott ügyfél-hozzáférési pontot. 
    Megjelenik a fürt minden egyes fürthálózatának IP-címerőforrása. Ha ez egy csak Azure-alapú megoldás, csak egy IP-címerőforrás jelenik meg.

7. A következő lehetőségek közül választhat:
   
   * Hibrid megoldás konfigurálása:
     
        a. Kattintson a jobb gombbal a helyszíni alhálózatnak megfelelő IP-címerőforrásra, majd válassza a **Tulajdonságok parancsot.** Jegyezze fel az IP-cím nevét és a hálózat nevét.
   
        b. Válassza **a Statikus IP-cím**lehetőséget, rendeljen hozzá egy nem használt IP-címet, majd kattintson **az OK**gombra.
 
   * Csak Azure-alapú megoldás konfigurálása:

        a. Kattintson a jobb gombbal az Azure-alhálózatnak megfelelő IP-címerőforrásra, majd válassza a **Tulajdonságok parancsot.**
       
       > [!NOTE]
       > Ha a figyelő később nem sikerül online állapotba lépnie a DHCP által kiválasztott ütköző IP-cím miatt, ebben a tulajdonságablakban érvényes statikus IP-címet állíthat be.
       > 
       > 

       b. Ugyanabban az **IP-cím** tulajdonságok ablakban módosítsa az **IP-cím nevét**.  
        Ez a név a PowerShell-parancsfájl $IPResourceName változójában használatos. Ha a megoldás több Azure virtuális hálózatra is kiterjed, ismételje meg ezt a lépést minden egyes IP-erőforrás esetében.

