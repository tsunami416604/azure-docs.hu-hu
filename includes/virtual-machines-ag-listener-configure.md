---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 73ba78eca710f0b98b2a209494519cb8003e554b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75467096"
---
A rendelkezésre állási csoport figyelője egy IP-cím és hálózati név, amelyet az SQL Server rendelkezésre állási csoport figyel. A rendelkezésre állási csoport figyelőjének létrehozásához tegye a következőket:

1. <a name="getnet"></a>A fürt hálózati erőforrásának bekéselése.

    a. Az RDP használatával csatlakozzon az elsődleges replikát tartalmazó Azure virtuális géphez. 

    b. Nyissa meg a feladatátvevőfürt-kezelőt.

    c. Jelölje ki a **Hálózatok** csomópontot, és jegyezze fel a fürthálózat nevét. Használja ezt a `$ClusterNetworkName` nevet a változóban a PowerShell-parancsfájlban. Az alábbi képen a fürt hálózatának neve **Fürthálózat 1**:

   ![Fürthálózat neve](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Adja hozzá az ügyfél hozzáférési pontját.  
    Az ügyfél-hozzáférési pont az a hálózati név, amelyet az alkalmazások egy rendelkezésre állási csoport adatbázisaihoz való csatlakozáshoz használnak. Hozza létre az ügyfél-hozzáférési pontot a Feladatátvevőfürt-kezelőben.

    a. Bontsa ki a fürt nevét, majd kattintson a **Szerepkörök gombra.**

    b. A **Szerepkörök** ablaktáblán kattintson a jobb gombbal az elérhetőségi csoport nevére, majd válassza **az Erőforrás-ügyfél** > hozzáférési pontjának hozzáadása**parancsot.**

   ![Ügyfél-hozzáférési pont](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. A **Név** mezőben hozzon létre egy nevet az új figyelőnek. 
   Az új figyelő neve az a hálózati név, amelyet az alkalmazások az SQL Server rendelkezésre állási csoportjában lévő adatbázisokhoz való csatlakozáshoz használnak.

    d. A figyelő létrehozásának befejezéséhez kattintson kétszer a **Tovább** gombra, majd a **Befejezés**gombra. Ne hozza a figyelő vagy erőforrás online ezen a ponton.

1. A rendelkezésre állási csoport fürtszerepkörének offline állapotba helyezése. A **Feladatátvevőfürt-kezelő** **szerepkörök**csoportjában kattintson a jobb gombbal a szerepkörre, és válassza a **Szerepkör leállítása parancsot.**

1. <a name="congroup"></a>Konfigurálja az IP-erőforrást a rendelkezésre állási csoporthoz.

    a. Kattintson az **Erőforrások** fülre, majd bontsa ki a létrehozott ügyfél-hozzáférési pontot.  
    Az ügyfél-hozzáférési pont offline állapotban van.

   ![Ügyfél-hozzáférési pont](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Kattintson a jobb gombbal az IP-erőforrásra, majd kattintson a tulajdonságok parancsra. Vegye figyelembe az IP-cím nevét, `$IPResourceName` és használja azt a változóban a PowerShell-parancsfájlban.

    c. Az **IP-cím csoportban**kattintson a **Statikus IP-cím gombra.** Állítsa be az IP-címet, mint ugyanazt a címet, amelyet az Azure Portalon a terheléselosztó címének beállításakor használt.

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Az SQL Server rendelkezésre állási csoportjának erőforrásának függővé tételéhez függjön az ügyfél hozzáférési pontja.

    a. A Feladatátvevőfürt-kezelőben kattintson a **Szerepkörök**elemre, majd az elérhetőségi csoportra.

    b. Az **Erőforrások** lap **Egyéb erőforrások**csoportjában kattintson a jobb gombbal az elérhetőségi erőforráscsoportra, majd kattintson a **Tulajdonságok parancsra.** 

    c. A függőségek lapon adja hozzá az ügyfél-hozzáférési pont (a figyelő) erőforrás nevét.

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Kattintson az **OK** gombra.

1. <a name="listname"></a>Az ügyfél-hozzáférési pont erőforrásának függővé az IP-címtől függővé.

    a. A Feladatátvevőfürt-kezelőben kattintson a **Szerepkörök**elemre, majd az elérhetőségi csoportra. 

    b. Az **Erőforrások** lapon kattintson a jobb gombbal az ügyfélelérési pont erőforrására a **Kiszolgáló neve területen,** majd kattintson a **Tulajdonságok parancsra.** 

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Kattintson a **Függőségek** fülre. Ha nem, állítsa be az IP-cím függőségét. Ha több erőforrás szerepel a listában, ellenőrizze, hogy az IP-címek OR, nem ÉS, függőségekkel rendelkeznek-e. Kattintson az **OK** gombra. 

   ![IP-erőforrás](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Ellenőrizheti, hogy a függőségek megfelelően vannak-e konfigurálva. A Feladatátvevőfürt-kezelőben nyissa meg a Szerepkörök, kattintson a jobb gombbal az elérhetőségi csoportra, kattintson a **További műveletek parancsra,** majd kattintson a **Függőségi jelentés megjelenítése parancsra.** Ha a függőségek megfelelően vannak konfigurálva, a rendelkezésre állási csoport a hálózat nevétől függ, a hálózat neve pedig az IP-címtől függ. 


1. <a name="setparam"></a>Állítsa be a fürt paramétereit a PowerShellben.

   a. Másolja a következő PowerShell-parancsfájlt az SQL Server egyik példányába. Frissítse a környezet változóit.

   - `$ListenerILBIP`Az Azure load balancer a rendelkezésre állási csoport figyelője által létrehozott IP-cím.
    
   - `$ListenerProbePort`a port konfigurált az Azure terheléselosztó a rendelkezésre állási csoport figyelője.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Állítsa be a fürt paramétereit a PowerShell-parancsfájl futtatásával a fürtcsomópontok egyikén.  

   > [!NOTE]
   > Ha az SQL Server-példányok külön régiókban vannak, kétszer kell futtatnia a PowerShell-parancsfájlt. Az első alkalommal `$ListenerILBIP` használja `$ListenerProbePort` a és az első régióból. A második alkalommal `$ListenerILBIP` használja `$ListenerProbePort` a és a második régióból. A fürthálózat neve és a fürt IP-erőforrásneve régiónként is eltérő.

1. Hozza online állapotba a rendelkezésre állási csoport fürtszerepkörét. A **Feladatátvevőfürt-kezelő** **szerepkör öklében**kattintson a jobb gombbal a szerepkörre, és válassza a **Szerepkör indítása parancsot.**

Ha szükséges, ismételje meg a fenti lépéseket a WSFC-fürt IP-címének fürtparamétereinek beállításához.

1. A WSFC-fürt IP-címének beszerezése. A **Fürtalapvető erőforrások**csoport **Feladatátvevőfürt-kezelőjében** keresse meg a **Kiszolgálónevét.**

1. Kattintson a jobb gombbal **az IP-cím**elemre, és válassza **a Tulajdonságok parancsot.**

1. Másolja az IP-cím **nevére.** Lehet, `Cluster IP Address`hogy . 

1. <a name="setwsfcparam"></a>Állítsa be a fürt paramétereit a PowerShellben.
  
   a. Másolja a következő PowerShell-parancsfájlt az SQL Server egyik példányába. Frissítse a környezet változóit.

   - `$ClusterCoreIP`A WSFC core cluster erőforrás Azure-terheléselosztóján létrehozott IP-cím. Ez eltér a rendelkezésre állási csoport figyelőip-címét.

   - `$ClusterProbePort`a WSFC-állapotminta Azure-terheléselosztóján konfigurált port. Ez eltér a rendelkezésre állási csoport figyelője a mintavétel.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Állítsa be a fürt paramétereit a PowerShell-parancsfájl futtatásával a fürtcsomópontok egyikén.  

>[!WARNING]
>A rendelkezésre állási csoport figyelő állapotmintavételi portnak különböznie kell a fürt magjának IP-cím állapotmintavételi portjától. Ezekben a példákban a figyelő port 59999 és a fürt magja IP-cím állapot mintavételi port 58888. Mindkét porthoz bejövő tűzfalszabály szükséges.
