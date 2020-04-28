---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179068"
---
Ebben a lépésben manuálisan hozza létre a rendelkezésre állási csoport figyelőjét Feladatátvevőfürt-kezelő és SQL Server Management Studio.

1. Nyissa meg Feladatátvevőfürt-kezelő az elsődleges replikát futtató csomópontból.

2. Válassza ki a **hálózatok** csomópontot, és jegyezze fel a fürt hálózati nevét. A rendszer ezt a nevet használja a PowerShell-parancsfájl $ClusterNetworkName változójában.

3. Bontsa ki a fürt nevét, majd kattintson a **szerepkörök**elemre.

4. A **szerepkörök** ablaktáblán kattintson a jobb gombbal a rendelkezésre állási csoport nevére, majd válassza az **erőforrás** > -**ügyfél-hozzáférési pont**hozzáadása lehetőséget.
   
    ![Ügyfél-hozzáférési pont hozzáadása a rendelkezésre állási csoporthoz](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. A **név** mezőben hozzon létre egy nevet az új figyelőhöz, kattintson kétszer a **tovább** gombra, majd kattintson a **Befejezés**gombra.  
    Ezen a ponton ne helyezze online állapotba a figyelőt vagy az erőforrást.

6. Kattintson a **Resources (erőforrások** ) lapra, majd bontsa ki az imént létrehozott ügyfél-hozzáférési pontot. 
    Megjelenik a fürtben lévő minden egyes fürt IP-címének erőforrása. Ha ez egy csak Azure-megoldás, akkor csak egy IP-cím típusú erőforrás jelenik meg.

7. A következő lehetőségek közül választhat:
   
   * Hibrid megoldás konfigurálása:
     
        a. Kattintson a jobb gombbal a helyszíni alhálózatnak megfelelő IP-cím erőforrásra, majd válassza a **Tulajdonságok**lehetőséget. Jegyezze fel az IP-cím nevét és a hálózat nevét.
   
        b. Válassza a **statikus IP-cím**lehetőséget, rendeljen hozzá egy nem használt IP-címet, majd kattintson **az OK**gombra.
 
   * Csak Azure-alapú megoldás konfigurálása:

        a. Kattintson a jobb gombbal az Azure-alhálózatnak megfelelő IP-cím erőforrásra, majd válassza a **Tulajdonságok**lehetőséget.
       
       > [!NOTE]
       > Ha a figyelő később nem tud online állapotba állítani, mert a DHCP által kiválasztott ütköző IP-cím van beállítva, akkor ebben a Tulajdonságok ablakban érvényes statikus IP-címet is beállíthat.
       > 
       > 

       b. Az IP- **cím** tulajdonságai ablakban módosítsa az **IP-cím nevét**.  
        Ezt a nevet a PowerShell-parancsfájl $IPResourceName változója használja. Ha a megoldás több Azure-beli virtuális hálózatot is felölel, ismételje meg ezt a lépést minden IP-erőforrás esetében.

