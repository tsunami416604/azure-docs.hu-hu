---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 276ddf0a70fa450451cd3ddc78c7610c4ab1edc1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58495206"
---
A rendelkezésre állási csoport figyelőjének egy IP-cím és hálózati nevet, amelyet az SQL Server rendelkezésre állási csoport figyel. A rendelkezésre állási csoport figyelőjének létrehozásához tegye a következőket:

1. <a name="getnet"></a>A fürt hálózati erőforrás nevének beolvasása.

    a. RDP használatával kapcsolódni az Azure virtuális gépen, amelyen az elsődleges replika. 

    b. Nyissa meg a Feladatátvevőfürt-kezelőben.

    c. Válassza ki a **hálózatok** csomópont, és figyelje meg a fürt hálózati neve. Ezt a nevet használja a `$ClusterNetworkName` változót a PowerShell-parancsfájlt. Az alábbi ábrán a fürthálózat nevének van **fürt hálózati 1**:

   ![Fürthálózat neve](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Az ügyfél-hozzáférési pont hozzáadása.  
    Az ügyfél-hozzáférési pont a hálózatnév használó alkalmazásokat a rendelkezésre állási csoportban adatbázisaihoz való kapcsolódásra. Hozzon létre az ügyfél-hozzáférési pont a Feladatátvevőfürt-kezelőben.

    a. Bontsa ki a fürt nevét, és kattintson **szerepkörök**.

    b. Az a **szerepkörök** panelen kattintson a jobb gombbal a rendelkezésre állási csoport nevét, majd válassza ki **erőforrás hozzáadása** > **ügyfél-hozzáférési pont**.

   ![Ügyfél-hozzáférési pont](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Az a **neve** hozzon létre egy nevet az új figyelőt. 
   Az új figyelőt a neve: a hálózat nevét, amely az alkalmazások használata az SQL Server rendelkezésre állási csoportban adatbázisaihoz való kapcsolódásra.

    d. A figyelő létrehozásának befejezéséhez kattintson **tovább** kétszer, és kattintson a **Befejezés**. Nem hozza a figyelőt vagy erőforrás online ezen a ponton.

1. A rendelkezésre állási csoport fürtszerepkört a hálózatról. A **Feladatátvevőfürt-kezelőben** alatt **szerepkörök**, kattintson a jobb gombbal a szerepkört, és válassza ki **szerepkör leállítása**.

1. <a name="congroup"></a>Az IP-erőforrást a rendelkezésre állási csoport konfigurálása.

    a. Kattintson a **erőforrások** lapra, és ezután bontsa ki a létrehozott ügyfél-hozzáférési pontját.  
    Az ügyfél-csatlakozási pont kapcsolat nélküli üzemmódban.

   ![Ügyfél-hozzáférési pont](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Kattintson a jobb gombbal az IP-erőforrást, és kattintson a tulajdonságok. Tekintse meg az IP-cím nevére, és használhatja a `$IPResourceName` változót a PowerShell-parancsfájlt.

    c. A **IP-cím**, kattintson a **statikus IP-cím**. Az IP-címet beállítani: ugyanazt a címet, amelyet az Azure Portalon a load balancer cím beállításakor használt.

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Győződjön meg az SQL Server rendelkezésre állási csoport erőforrása az ügyfél hozzáférési pontjának függ.

    a. A Feladatátvevőfürt-kezelőben kattintson **szerepkörök**, majd kattintson a rendelkezésre állási csoport.

    b. Az a **erőforrások** lap **egyéb erőforrások**, kattintson a jobb gombbal az erőforrás rendelkezésre állási csoport, és kattintson **tulajdonságok**. 

    c. A Függőségek lap adja hozzá az ügyfél hozzáférési pont (figyelő) erőforrás nevét.

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Kattintson az **OK** gombra.

1. <a name="listname"></a>Ellenőrizze az ügyfél hozzáférési pont erőforrás függ az IP-címet.

    a. A Feladatátvevőfürt-kezelőben kattintson **szerepkörök**, majd kattintson a rendelkezésre állási csoport. 

    b. Az a **erőforrások** lapon kattintson a jobb gombbal az ügyfél hozzáférési pont erőforrásnál a **kiszolgálónév**, és kattintson a **tulajdonságok**. 

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Kattintson a **függőségek** fülre. Ellenőrizze, hogy az IP-cím egy függőséget. Ha nem így van, beállíthat egy függőséget az IP-címen található. Ha több erőforrás szerepel, győződjön meg arról, hogy az IP-címek vagy nem rendelkezik-e és, függőségeit. Kattintson az **OK** gombra. 

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Ellenőrizheti, hogy a függőségek megfelelően vannak-e konfigurálva. A Feladatátvevőfürt-kezelő, nyissa meg a szerepkörökhöz, kattintson a jobb gombbal a rendelkezésre állási csoportban, kattintson a **további műveletek**, és kattintson a **függőségi jelentés megjelenítése**. Ha a függőségek megfelelően vannak beállítva, a rendelkezésre állási csoport függ a hálózat nevét, és a hálózat neve az adott nyelvtől függ az IP-címet. 


1. <a name="setparam"></a>Állítsa a fürt paramétereit a PowerShellben.

   a. Másolja a következő PowerShell-parancsfájl egy SQL Server-példányon. Frissítse a változókat, az adott környezetben.

   - `$ListenerILBIP` az IP-cím, amelyet a rendelkezésre állási csoport figyelője az Azure load balancer a létrehozott van.
    
   - `$ListenerProbePort` Ez a port a rendelkezésre állási csoport figyelője az Azure load balancer a konfigurált.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. A fürt paraméterek beállítása a PowerShell-parancsprogram futtatásával egy, a fürtcsomópontok.  

   > [!NOTE]
   > Ha az SQL Server-példányok külön régiókban, kétszer a PowerShell-parancsfájl futtatásához szükséges. Az első alkalommal használja a `$ListenerILBIP` és `$ListenerProbePort` első régióban. A második alkalommal használja a `$ListenerILBIP` és `$ListenerProbePort` második régióban. A fürt hálózati és a fürt IP-erőforrás neve minden egyes régióban is eltérőek.

1. A rendelkezésre állási csoport fürtszerepkör online állapotba. A **Feladatátvevőfürt-kezelőben** alatt **szerepkörök**, kattintson a jobb gombbal a szerepkört, és válassza ki **szerepkör indítása**.

Ha szükséges, ismételje meg a fenti lépéseket, és állítsa be a fürt paramétereit a WSFC-fürt IP-cím.

1. Az IP-cím neve WSFC-fürt IP-címének beolvasása. A **Feladatátvevőfürt-kezelőben** alatt **fürt alapvető erőforrásai**, keresse meg **kiszolgálónév**.

1. Kattintson a jobb gombbal **IP-cím**, és válassza ki **tulajdonságok**.

1. Másolás a **neve** IP-cím. Ez lehet `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Állítsa a fürt paramétereit a PowerShellben.
  
   a. Másolja a következő PowerShell-parancsfájl egy SQL Server-példányon. Frissítse a változókat, az adott környezetben.

   - `$ClusterCoreIP` az az IP-cím az Azure load balancer a WSFC-core fürterőforrás amelyiken létrehozta. Ez különbözik a rendelkezésre állási csoport figyelőjének IP-címét.

   - `$ClusterProbePort` a port, az Azure load balancer for a WSFC állapotadat-mintavétel konfigurálva van. Eltér a mintavételi modul a rendelkezésre állási csoport figyelője.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. A fürt paraméterek beállítása a PowerShell-parancsprogram futtatásával egy, a fürtcsomópontok.  

>[!WARNING]
>A rendelkezésre állási csoport figyelőjének állapotát mintavételi portot nem lehet a fürt core IP-cím egészségügyi mintavételi port különbözik. Ezekben a példákban a figyelőjének portszámára 59999 pedig a fürt alapvető IP-cím 58888. Mindkét portnak szükséges egy engedélyezési bejövő tűzfalszabályt.