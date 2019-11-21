---
title: 'Tutorial: Load balance internet traffic to VMs - Azure portal'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató ismerteti, hogyan hozhat létre és felügyelhet egy Standard Load Balancert az Azure Portalon.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 4d4703ccb4ee96eb69a780f91eae1eb6da9e1578
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225184"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Tutorial: Load balance internet traffic to VMs using the Azure portal

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. In this tutorial, you learn about the different components of the Azure Standard Load Balancer that distribute internet traffic to VMs and provide high availability. Az alábbiak végrehajtásának módját ismerheti meg:


> [!div class="checklist"]
> * Create an Azure Load Balancer
> * Create Load Balancer resources
> * Virtuális gépek létrehozása és IIS-kiszolgáló telepítése
> * View Load Balancer in action
> * Add and remove VMs from a Load Balancer

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása

In this section, you create a Standard Load Balancer that helps load balance virtual machines. A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Amikor létrehoz egy Standard Load Balancert, létre kell hoznia egy új standard nyilvános IP-címet is, amely a Standard Load Balancer előtereként van konfigurálva (alapértelmezés szerint *LoadBalancerFrontend* néven). 

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **Terheléselosztó** elemre.
2. In the **Basics** tab of the **Create load balancer** page, enter or select the following information, accept the defaults for the remaining settings, and then select **Review + create**:

    | Beállítás                 | Value (Díj)                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Select **Create new** and type *myResourceGroupSLB* in the text box.|
    | Név                   | *myLoadBalancer*                                   |
    | Region (Régió)         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Type (Típus)          | Select **Public**.                                        |
    | SKU (Cikkszám)           | Select **Standard**.                          |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Public IP address name              | Type *myPublicIP* in the text box.   |
    |Availability zone| Select **Zone redundant**.    |

3. In the **Review + create** tab, click **Create**.

   ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Create Load Balancer resources

In this section, you configure Load Balancer settings for a backend address pool, a health probe, and specify a balancer rule.

### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

To distribute traffic to the VMs, a backend address pool contains the IP addresses of the virtual (NICs) connected to the Load Balancer. Create the backend address pool *myBackendPool* to include virtual machines for load-balancing internet traffic.

1. Select **All services** in the left-hand menu, select **All resources**, and then click **myLoadBalancer** from the resources list.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a **Hozzáadás** gombra.
3. On the **Add a backend pool** page, for name, type *myBackendPool*, as the name for your backend pool, and then select **Add**.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

To allow the Load Balancer to monitor the status of your app, you use a health probe. The health probe dynamically adds or removes VMs from the Load Balancer rotation based on their response to health checks. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Select **All services** in the left-hand menu, select **All resources**, and then click **myLoadBalancer** from the resources list.
2. A **Beállítások** területen kattintson az **Állapotminták** elemre, majd a **Hozzáadás** gombra.
3. Használja az alábbi értékeket az állapotminta létrehozásához:
     
    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Enter *myHealthProbe*. |
    | Protocol (Protokoll) | Select **HTTP**. |
    | Port | Enter *80*.|
    | Intervallum | Enter *15* for number of **Interval** in seconds between probe attempts. |
    | Unhealthy threshold | Select *2* for number of **Unhealthy threshold** or consecutive probe failures that must occur before a VM is considered unhealthy.|
    
4. Kattintson az **OK** gombra.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Create a Load Balancer rule *myLoadBalancerRuleWeb* for listening to port 80 in the frontend *FrontendLoadBalancer* and sending load-balanced network traffic to the backend address pool *myBackEndPool* also using port 80.

1. Select **All services** in the left-hand menu, select **All resources**, and then click **myLoadBalancer** from the resources list.
2. A **Beállítások** területen kattintson a **Terheléselosztási szabályok** elemre, majd a **Hozzáadás** gombra.
3. Use these values to configure the load-balancing rule:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Enter *myHTTPRule*. |
    | Protocol (Protokoll) | Select **TCP**. |
    | Port | Enter *80*.|
    | Backend port | Enter *80*. |
    | Backend pool | Select *myBackendPool*.|
    | Állapotadat-mintavétel | Select *myHealthProbe*. |
    
4. Tartsa meg az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

In this section, you create a virtual network, create three virtual machines for the backend pool of the Load Balancer, and then install IIS on the virtual machines to help test the Load Balancer.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. On the upper-left side of the screen, select **Create a resource** > **Networking** > **Virtual network**.
2. In **Create virtual network**, enter or select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *myVNet* értéket. |
    | Címtér | Enter *10.1.0.0/16*. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Select existing resource - *myResourceGroupSLB*. |
    | Földrajzi egység | Válassza a **Nyugat-Európa** régiót.|
    | Subnet - Name | Adja meg a *myBackendSubnet* értéket. |
    | Alhálózat – címtartomány | Enter *10.1.0.0/24*. |
    
3. Leave the rest of the defaults and select **Create**.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Standard Load Balancer only supports VMs with Standard IP addresses in the backend pool. In this section, you will create three VMs (*myVM1*, *myVM2*, and *myVM3*) with a Standard public IP address in three different zones (*Zone 1*, *Zone 2*, and *Zone 3*) that are added to the backend pool of the Standard Load Balancer that was created earlier.

1. On the upper-left side of the portal, select **Create a resource** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. In **Create a virtual machine**, type or select the following values in the **Basics** tab:
   - **Subscription** > **Resource Group**: Select **myResourceGroupSLB**.
   - **Instance Details** > **Virtual machine name**: Type *myVM1*.
   - **Instance Details** > **Region** > select **West Europe**.
   - **Instance Details** > **Availability Options** > Select **Availability zones**. 
   - **Instance Details** > **Availability zone** > Select **1**.
  
1. Select the **Networking** tab, or select **Next: Disks**, then **Next: Networking**. 
   
   - Make sure the following are selected:
       - **Virtual network**: **myVnet**
       - **Subnet**: **myBackendSubnet**
       - **Public IP** > select **Create new**, and in the **Create public IP address** window, for **SKU**, select **Standard**, and for **Availability zone**, select **Zone-redundant**
      
   - To create a new network security group (NSG), a type of firewall, under **Network Security Group**, select **Advanced**. 
       1. In the **Configure network security group** field, select **Create new**. 
       1. Type *myNetworkSecurityGroup*, and select **OK**.

   - To make the VM a part of the Load Balancer's backend pool, complete the following steps:
        - In **Load Balancing**, for **Place this virtual machine behind an existing load balancing solution?** , select **Yes**.
        - In **Load balancing settings**, for **Load balancing options**, select **Azure load balancer**.
        - For **Select a load balancer**, *myLoadBalancer*. 
1. Select the **Management** tab, or select **Next** > **Management**. Under **Monitoring**, set **Boot diagnostics** to **Off**. 
1. Válassza az **Áttekintés + létrehozás** lehetőséget.   
1. Review the settings, and then select **Create**.
1. Follow the steps to create two additional VMs - *myVM2* and *myVM3*, with a Standard SKU public IP address in **Availability zone** **2** and **3** respectively, and all the other settings the same as *myVM1*.  

### <a name="create-network-security-group-rule"></a>Hálózati biztonsági csoportszabály létrehozása

In this section, you create a network security group rule to allow inbound connections using HTTP.

1. Select **All services** in the left-hand menu, select **All resources**, and then from the resources list click **myNetworkSecurityGroup** that is located in the **myResourceGroupSLB** resource group.
2. A **Beállítások** területen kattintson a **Bejövő biztonsági szabályok** lehetőségre, majd a **Hozzáadás** gombra.
3. Adja meg az alábbi értékeket a *myHTTPRule* nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *80*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *100*
    - Név: *myHTTPRule*
    - Leírás: *HTTP engedélyezése*
4. Válassza a **Hozzáadás** lehetőséget.

### <a name="install-iis-on-vms"></a>Az IIS telepítése virtuális gépekre

1. Select **All services** in the left-hand menu, select **All resources**, and then from the resources list click **myVM1** that is located in the *myResourceGroupSLB* resource group.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. A **Csatlakozás virtuális géphez** előugró ablakban válassza az **RDP-fájl letöltése** lehetőséget, majd nyissa meg a letöltött RDP-fájlt.
4. A **Távoli asztali kapcsolat** ablakban kattintson a **Csatlakozás** gombra.
5. Jelentkezzen be a virtuális gépre az adott virtuális gép létrehozásakor megadott hitelesítő adatokkal. Ez elindít egy távoli asztali munkamenetet a *myVM1* virtuális géppel.
6. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Windows PowerShell** elemre.
7. A PowerShell-ablakban futtassa az alábbi parancsokat az IIS-kiszolgáló telepítéséhez, távolítsa el az alapértelmezett iisstart.htm fájlt, majd adjon hozzá egy új iisstart.htm fájt, amely megjeleníti a virtuális gép nevét:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Szüntesse meg a távoli asztali munkamenetet a *myVM1*-gyel.
7. Az 1–6. lépés megismétlésével telepítse az IIS-t és a frissített iisstart.htm fájlt a *myVM2* és *myVM3* gépeken.

## <a name="test-the-load-balancer"></a>Test the Load Balancer
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Select **All services** in the left-hand menu, select **All resources**, and then click **myPublicIP**.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

      ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

To see the Load Balancer distribute traffic across the three VMs running your app, you can force-refresh your web browser.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Virtuális gépek háttérkészletből való eltávolítása vagy hozzáadása
Előfordulhat, hogy karbantartás kell végeznie az alkalmazást futtató virtuális gépeken (például operációsrendszer-frissítést kell telepítenie). Az alkalmazás megnövekedett forgalmának kezeléséhez szükség lehet további virtuális gépek hozzáadására. This section shows you how to remove or add a VM (*myVM1*) from the Load Balancer.

### <a name="remove-vm-from-a-backend-pool"></a>Remove VM from a backend pool
To remove *myVM1* from the backend pool, complete the following steps:

1. Select **All services** in the left-hand menu, select **All resources**, and then click **myLoadBalancer** from the resources list.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a háttérkészlet listájában kattintson a **myBackendPool** elemre.
3. On the **myBackendPool** page, to remove *VM1* select the delete icon at the end of the row that displays *myVM1*, and then click **Save**.

Most, hogy a *myVM1* már nincs a címkészletben, bármilyen karbantartási feladatot elvégezhet a *myVM1* gépen, például telepíthet szoftverfrissítéseket. In the absence of *VM1*, the load is now balanced across *myVM2* and *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Add VM to a backend pool
To add *myVM1* back to the backend pool, complete the following steps:

1. Select **All services** in the left-hand menu, select **All resources**, and then select **myVM1** from the resources list.
2. In the **VM1** page, under **Settings**, select **Networking**.
3. In the **Networking** page, select the **Load balancing** tab, and then select **Add load balancing**.
4. In the **Add load balancing** page, do the following:
   1. For **Load balancing options**, select **Azure load balancer**.
   2. For **Select a load balancer**, select *myLoadBalancer*.
   3. For **Select a backend pool**, select *myBackendPool*. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

When they are no longer needed, delete the resource group, Load Balancer, and all related resources. To do so, select the *myResouceGroupSLB* resource group that contains the Load Balancer, and then select  **Delete**.

## <a name="next-steps"></a>Következő lépések

In this tutorial, you created a Standard Load Balancer, attached VMs to it, configured the Load Balancer traffic rule, health probe, and then tested the Load Balancer. Emellett eltávolított egy virtuális gépet az elosztott terhelésű készletből, és hozzáadta a virtuális gépet a háttércímkészlethez. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
