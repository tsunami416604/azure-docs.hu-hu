---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227151"
---
Ebben a lépésben, manuálisan hozzon létre a rendelkezésre állási csoport figyelőjének a Feladatátvevőfürt-kezelő és az SQL Server Management Studióval.

1. Nyissa meg a Feladatátvevőfürt-kezelőben a csomópont, amelyen az elsődleges replika.

2. Válassza ki a **hálózatok** csomópont, és figyelje meg a fürt hálózati neve. Ez a név használatban van a PowerShell-parancsfájlt a $ClusterNetworkName változót.

3. Bontsa ki a fürt nevét, és kattintson **szerepkörök**.

4. Az a **szerepkörök** panelen kattintson a jobb gombbal a rendelkezésre állási csoport nevét, majd válassza ki **erőforrás hozzáadása** > **ügyfél-hozzáférési pont**.
   
    ![Adja hozzá az ügyfél-hozzáférési pont rendelkezésre állási csoport](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Az a **neve** mezőben hozzon létre egy nevet az új figyelőt, kattintson a **tovább** kétszer, és kattintson a **Befejezés**.  
    Nem hozza a figyelőt vagy erőforrás online ezen a ponton.

6. Kattintson a **erőforrások** lapra, és ezután bontsa ki az imént létrehozott ügyfél-hozzáférési pont. 
    Az IP-cím erőforrás a fürtben lévő minden egyes fürt hálózati jelenik meg. Ha ez egy csak az Azure-megoldás, csak egy IP-cím erőforrás jelenik meg.

7. A következő lehetőségek közül választhat:
   
   * Hibrid megoldás konfigurálása:
     
        a. Kattintson a jobb gombbal a helyszíni alhálózatnak megfelelő IP-cím erőforrás, és válassza **tulajdonságok**. Megjegyzés: az IP-cím neve és a hálózat nevét.
   
        b. Válassza ki **statikus IP-cím**, fel nem használt IP-címet hozzárendelni, és kattintson **OK**.
 
   * Egy csak az Azure-megoldás konfigurálása:

        a. Kattintson a jobb gombbal az IP-cím erőforrás, amely megfelel az Azure-alhálózaton, és válassza **tulajdonságok**.
       
       > [!NOTE]
       > Ha a figyelő később sikertelen egy ütköző IP-címet DHCP által kiválasztott miatt online állapotba, konfigurálhatja egy érvényes statikus IP-címet a Tulajdonságok ablakban.
       > 
       > 

       b. Az egyazon **IP-cím** tulajdonságok ablakban módosítsa a **IP-cím neve**.  
        Ezt a nevet használja a PowerShell-parancsfájl $IPResourceName változóban. Ha a megoldás több Azure virtuális hálózat is kiterjed, ismételje meg ezt a lépést minden IP-erőforráshoz.

