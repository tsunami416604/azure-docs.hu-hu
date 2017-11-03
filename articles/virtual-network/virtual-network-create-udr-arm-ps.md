---
title: "Hozzon létre egy felhasználó által megadott útvonalat az útvonal hálózati forgalmat a hálózati virtuális készülék - a PowerShell használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy hálózati virtuális készüléken keresztül hálózati forgalom útválasztási útválasztást Azure alapértelmezett felülbírálásához felhasználói útvonalat."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 9696a74ac02688f9004156f6f16b39b37756751d
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-user-defined-route---powershell"></a>Felhasználó által megadott útvonal - PowerShell létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre útvonalakat felhasználói irányíthatja a forgalmat a kettő közötti [virtuális hálózati](virtual-networks-overview.md) alhálózat egy hálózati virtuális készüléken keresztül. A hálózati virtuális készülék valójában egy hálózati alkalmazás, például egy tűzfal futtató virtuális gép. Virtuális készülékek előre konfigurált hálózati központilag telepíthető egy Azure virtuális hálózatban kapcsolatos további tudnivalókért tekintse meg a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Alhálózatok virtuális hálózatban, létrehozásakor az Azure létrehoz alapértelmezett [rendszerútvonalak](virtual-networks-udr-overview.md#system-routes) , amelyek lehetővé teszik az erőforrások kommunikálnak egymással, hogy az összes alhálózat az alábbi ábrán látható módon:

![Alapértelmezett útvonalak](./media/create-user-defined-route/default-routes.png)

Ebben az oktatóanyagban létrehozhat egy virtuális hálózati nyilvános, magán és DMZ-alhálózatok, az alábbi képen látható módon. Általában webkiszolgálók nyilvános alhálózathoz telepíthető, és egy alkalmazás vagy az adatbázis-kiszolgáló telepíthető titkos alhálózathoz. A hálózati virtuális készülék DMZ alhálózaton meghatalmazottjaként járhatnak el egy virtuális gép létrehozása, és szükség esetén hozzon létre egy virtuális gép minden alhálózatban a hálózati virtuális készülék keresztül kommunikáló. A nyilvános és titkos alhálózatok közötti összes forgalom a készülék keresztül történik, az alábbi ábrán látható módon:

![Felhasználó által megadott útvonalak](./media/create-user-defined-route/user-defined-routes.png)

A cikkben a Resource Manager telepítési modell, amely a felhasználó által definiált útvonalak létrehozásakor használata javasolt üzembe helyezési modellel egy felhasználó által megadott útvonal létrehozásának lépéseit. Ha egy felhasználó által megadott útvonal (klasszikus) létrehozásához szüksége, tekintse meg [hozzon létre egy felhasználó által megadott útvonal (klasszikus)](virtual-network-create-udr-classic-ps.md). Ha nem ismeri az Azure üzembe helyezési modellel, lásd: [megértéséhez Azure üzembe helyezési modellel](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Felhasználó által definiált útvonalak kapcsolatos további információkért lásd: [felhasználó által definiált útvonalak áttekintése](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Útvonalak és hálózati virtuális készülék létrehozása

Telepítheti és konfigurálhatja a PowerShell legújabb verziójának [AzureRM](https://www.powershellgallery.com/packages/AzureRM/) a számítógépen, vagy kattintson a modul a **kipróbálás** bármely, a parancsfájlok végrehajtása az Azure-felhő rendszerhéj-parancsfájlok gombra. A felhő rendszerhéj a PowerShell-AzureRM modul telepítve van.
 
1. **Előfeltételek**: két vnettel rendelkező virtuális hálózat létrehozása a lépések végrehajtásával [hozzon létre egy virtuális hálózatot](#create-a-virtual-network).
2. Ha fut a PowerShell a számítógépről, jelentkezzen be Azure-bA a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) használatával a `login-azurermaccount` parancsot. A felhő-rendszerhéj használata, ha automatikusan jelentkezett be. A felhő rendszerhéj újra kell indítania a működéséhez szükséges virtuális hálózat létrehozásakor használt rendszerhéjakba módosítani.
3. A lépések során használt néhány változók megadása:

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. Hozzon létre egy DMZ-alhálózatot a meglévő virtuális hálózatban:

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. Hozzon létre egy statikus nyilvános IP-címet a hálózati virtuális készülék virtuális gépet.

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. A NVA virtuális gép létrehozásához. Az NVA a Linux vagy a Windows operációs rendszert futtató virtuális gép is lehet. A virtuális gép létrehozásához, másolja a parancsfájlt vagy operációs rendszerhez, és illessze be a PowerShell-munkamenethez. Ha egy Windows virtuális gép létrehozása illessze be a parancsfájlt egy szövegszerkesztőbe, a "érték" $cred változó módosítsa, majd illessze be a PowerShell-munkamenethez módosított:

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. Alapértelmezés szerint az Azure útvonalak forgalmat a virtuális hálózaton belül minden alhálózatok között. Hozzon létre módosíthatja úgy, hogy a forgalom útválasztási Azure alapértelmezett útvonalat a *nyilvános* alhálózati annak biztosítására, hogy az NVA ahelyett, hogy közvetlenül a *titkos* alhálózati.

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. Hozzon létre egy útvonaltábla a *nyilvános* alhálózat.

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. A nyilvános alhálózathoz útvonaltábla társítani. Megfelelően az útvonalakat az alhálózat minden kimenő forgalom továbbításához az útvonaltáblát az Azure közötti társítás egy útválasztási táblázatot az alhálózathoz okoz. Egy útválasztási táblázatot lehet társítva a következőhöz: nulla vagy több alhálózatból, mivel alhálózat lehetnek nulla, vagy egy útválasztási táblázatot társítva.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. Hozzon létre egy útvonal forgalmát a *titkos* alhálózatában ahhoz, hogy a *nyilvános* keresztül az NVA virtuális gép.

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. Az útvonaltábla létrehozása a *titkos* alhálózat.

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. Az útvonaltábla társítsa a *titkos* alhálózat.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **Választható lehetőség:** a nyilvános és Magánfelhő alhálózatban lévő virtuális gép létrehozása, és ellenőrizze, hogy a virtuális gépek közötti kommunikáció áthalad a hálózati virtuális készülék a lépések végrehajtásával [útválasztásiellenőrzése](#validate-routing).
15. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez végrehajtásához a [törli az erőforrást](#delete-resources).

## <a name="validate-routing"></a>Útválasztás ellenőrzése

1. Ha még nem tette meg, hajtsa végre a [útvonalak és hálózati virtuális készülék létrehozása](#create-routes-and-network-virtual-appliance).
2. Kattintson a **kipróbálás** a csoportban, amely a következő, amely az Azure-felhő rendszerhéj megnyitása gombra. Ha a rendszer kéri, jelentkezzen be az Azure használatával a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p). Az Azure-felhő rendszerhéj a szabad rendszerhéjakba az Azure parancssori felületével előre telepítve. 

    Az alábbi parancsfájlok, hozzon létre két virtuális gép, egy-egy a *nyilvános* alhálózatot és egy-egy a *titkos* alhálózat. A parancsfájlok IP-továbbítás ahhoz, hogy az operációs rendszer NVA operációs rendszerében a hálózati adapter irányíthatja a forgalmat a hálózati kapcsolaton keresztül is engedélyezheti. Egy éles NVA általában megvizsgálja a forgalom útválasztási azt megelőzően, de ebben az oktatóanyagban az egyszerű NVA csak irányítja a forgalmat tanulmányozza az nélkül. 

    Kattintson a **másolási** gombra a **Linux** vagy **Windows** parancsfájlok, hajtsa végre, és illessze be a parancsfájl tartalmát egy szövegszerkesztőben. Módosítsa a jelszavát a *adminPassword* változót, majd illessze be a parancsfájl az Azure felhőalapú rendszerhéjat. Futtassa a parancsfájlt az operációs rendszer, a 7. lépésben a hálózati virtuális készülék létrehozásakor kijelölt [útvonalak és hálózati virtuális készülék létrehozása](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Ellenőrizze a nyilvános és Magánfelhő alhálózatban lévő virtuális gépek közötti kommunikáció. 

    - Nyissa meg egy [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) vagy [távoli asztal](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) a nyilvános IP-címe (Windows) kapcsolatot a *myVm nyilvános* virtuális gépet.
    - A parancssorból a *myVm nyilvános* virtuális gépet, adja meg `ping myVm-Private`. Mivel az NVA irányítja a forgalmat a nyilvánosság elől a titkos alhálózathoz választ kap.
    - Az a *myVm nyilvános* virtuális gépet, a nyilvános és titkos alhálózatokat a virtuális gépek közötti nyomkövetést futtatni. Adja meg a megfelelő parancsot, amely a következő, az operációs rendszertől függően telepítette, akkor a virtuális gépek használata a nyilvános és Magánfelhő alhálózatok:
        - **Windows**: egy parancssorból futtassa a `tracert myvm-private` parancsot.
        - **Ubuntu**: futtassa a `tracepath myvm-private` parancsot.
      Forgalom átmegy 10.0.2.4 (NVA) (a virtuális gép személyes alhálózaton) 10.0.1.4 elérése előtt. 
    - Az előző lépéseket csatlakozva a *myVm InPrivate-* virtuális gép és a pingelés a *myVm nyilvános* virtuális gépet. A nyomkövetési útvonal 10.0.0.4 (a virtuális gép nyilvános alhálózaton) elérése előtt 10.0.2.4 keresztül utazás kommunikációs jeleníti meg.
    - **Opcionálisan**: Azure hálózati figyelőt a következő ugrás képességét segítségével ellenőrizheti azokat a következő Ugrás az Azure két virtuális gépek között. Hálózati figyelőt használatához először [hozzon létre egy Azure hálózati figyelőt példányt](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) számára a használni kívánt régiót. Ebben az oktatóanyagban az Amerikai keleti terület szolgál. Miután engedélyezte a hálózati figyelőt példánya a régió, adja meg a nyilvános és Magánfelhő alhálózatban lévő virtuális gépek között a következő ugrás adatainak megtekintéséhez a következő parancsot:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       A kimeneti adja vissza *10.0.2.4* , a **nextHopIpAddress** és *VirtualAppliance* , a **nextHopType**.

> [!NOTE]
> Ebben az oktatóanyagban olvassanak, nyilvános IP-címek hozzá vannak rendelve a virtuális gépek használata a nyilvános és titkos alhálózatok, és minden hálózati port engedélyezve a hozzáférés az Azure mindkét virtuális gépekhez. Éles környezetben való használathoz a virtuális gépek létrehozásakor lehet, hogy nem nyilvános IP-címek kiosztása őket, és szűkítheti a hálózati forgalmat a magánhálózati alhálózathoz takarja el a hálózati virtuális készülék telepítésével, illetve a hálózati biztonsági csoportok hozzárendelése az alhálózatok hálózati adapter, vagy mindkettőt. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Ebben az oktatóanyagban egy meglévő virtuális hálózatot, két alhálózattal igényel. Kattintson a **kipróbálás** a mezőbe, amely gyorsan a virtuális hálózat létrehozása a következő gombra. Kattintson a **kipróbálás** gombra kattint, megnyílik a [Azure Cloud rendszerhéj](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Bár a felhőalapú rendszerhéj a PowerShell vagy a Bash rendszerhéjat ebben a szakaszban fut, a virtuális hálózat létrehozása a rendszerhéjakba szolgál. A rendszerhéjakba az Azure parancssori felület telepítve van. Ha a felhő rendszerhéj kéri, jelentkezzen be az Azure használatával a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p). A jelen oktatóanyagban használt virtuális hálózat létrehozásához kattintson a **másolási** gombra kattint, a következő mezőbe, majd illessze be a parancsfájl az Azure-felhő rendszerhéj:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Virtuális hálózat létrehozása a portálon, a PowerShell vagy az Azure Resource Manager-sablonok használatával kapcsolatos további tudnivalókért lásd: [hozzon létre egy virtuális hálózatot](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Erőforrások törlése

Ez az oktatóanyag befejezése után előfordulhat, hogy törölni kívánja az erőforrásokat, amelyek hozott létre, így használati költségek. Erőforráscsoport törlésével, az erőforráscsoport összes erőforrást is törli. A PowerShell-ablakba írja be a következő parancsot:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

- Hozzon létre egy [magas rendelkezésre állású hálózati virtuális készülék](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuális hálózati berendezések gyakran rendelkezik, több hálózati adapterrel és IP-címek hozzárendelve. Megtudhatja, hogyan [hálózati adapterek hozzáadása egy meglévő virtuális gép](virtual-network-network-interface-vm.md#vm-add-nic) és [IP-címek hozzáadása a meglévő hálózati illesztő](virtual-network-network-interface-addresses.md#add-ip-addresses). Bár az összes virtuálisgép-méretek lehet kapcsolódik legalább két hálózati adapterrel, minden virtuális gép méretét támogatja a hálózati adapterek maximális száma. Hány hálózati adapterek minden egyes virtuális gép mérete által támogatott, lásd: [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek méretét. 
- Kényszerített bújtatás forgalom helyszíni keresztül felhasználói útvonal létrehozása egy [pont-pont VPN-kapcsolat](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
