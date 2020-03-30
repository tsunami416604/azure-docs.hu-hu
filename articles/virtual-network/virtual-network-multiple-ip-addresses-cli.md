---
title: Virtuális gép több IP-címmel az Azure CLI használatával
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan rendelhet több IP-címet egy virtuális géphez az Azure parancssori felület (CLI) használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: kumud
ms.openlocfilehash: 144f30463adb3dfbce1717e06548baccc8286f8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240222"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Több IP-cím hozzárendelése virtuális gépekhez az Azure CLI használatával

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy virtuális gépet (VM) az Azure Resource Manager üzembe helyezési modelljén keresztül az Azure CLI használatával. A klasszikus központi telepítési modellen keresztül létrehozott erőforrásokhoz nem rendelhető hozzá több IP-cím. Ha többet szeretne megtudni az Azure üzembe helyezési modellekről, olvassa el a [Telepítési modellek ismertetése](../resource-manager-deployment-model.md) című cikket.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

Az alábbi lépések ismertetik, hogyan hozhat létre egy példa virtuális gép több IP-cím, a forgatókönyvben leírtak szerint. Szükség szerint módosítsa a változóértékeket a "" és az IP-címtípusokban a megvalósításhoz. 

1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli) ha még nincs telepítve.
2. Hozzon létre egy SSH nyilvános és titkos kulcspárt linuxos virtuális gépekhez a [Linux os sSH nyilvános és titkos kulcspár létrehozása](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című lépés végrehajtásával.
3. Egy parancshéjból jelentkezzen be `az login` a paranccsal, és válassza ki a használt előfizetést.
4. Hozza létre a virtuális gép a következő parancsfájl végrehajtásával egy Linux vagy Mac számítógépen. A parancsfájl létrehoz egy erőforráscsoportot, egy virtuális hálózatot (VNet), egy hálózati adaptert három IP-konfigurációval, és egy virtuális gépet a két hálózati adapterrel. A hálózati adapternek, a nyilvános IP-címnek, a virtuális hálózatnak és a virtuális gép erőforrásainak ugyanazon a helyen és előfizetésben kell létezniük. Bár az erőforrások nem minden kell léteznie ugyanabban az erőforráscsoportban, a következő parancsfájlban teszik.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

A mellett, hogy 3 IP-konfigurációval rendelkező hálózati adapterrel rendelkező virtuális gép létrehozása mellett a parancsfájl létrehozza a következőket:

- Alapértelmezés szerint egyetlen prémium díjas felügyelt lemez, de a lemeztípusához más lehetőségek is rendelkezésre állnak. A részletekért olvassa el a [Linux os virtuális gép létrehozása az Azure CLI-cikk használatával.](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Egy alhálózattal és két nyilvános IP-címmel rendelkező virtuális hálózat. Másik lehetőségként használhatja a *meglévő* virtuális hálózati, alhálózati, hálózati adapter- vagy nyilvános IP-cím erőforrásokat. Ha meg szeretné tudni, hogyan használhatja a `az vm create -h`meglévő hálózati erőforrásokat ahelyett, hogy további erőforrásokat hozna létre, írja be a .

A nyilvános IP-címek névleges díjkötelesek. Ha többet szeretne megtudni az IP-cím díjszabásáról, olvassa el az [IP-cím díjszabási](https://azure.microsoft.com/pricing/details/ip-addresses) oldalát. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.

A virtuális gép létrehozása után `az network nic show --name MyNic1 --resource-group myResourceGroup` adja meg a parancsot a hálózati adapter konfigurációjának megtekintéséhez. Adja `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` meg a hálózati adapterhez társított IP-konfigurációk listájának megtekintéséhez.

Adja hozzá a privát IP-címeket a virtuális gép operációs rendszeréhez az operációs rendszer lépéseivégrehajtásával a [cikk IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszában.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-címek hozzáadása virtuális géphez

További privát és nyilvános IP-címeket adhat hozzá egy meglévő Azure-hálózati adapterhez a következő lépések végrehajtásával. A példák az ebben a cikkben ismertetett [forgatókönyvre](#scenario) épülnek.

1. Nyisson meg egy parancshéjat, és hajtsa végre a szakasz további lépéseit egyetlen munkameneten belül. Ha még nincs telepítve és konfigurálva az Azure CLI, hajtsa végre az [Azure CLI telepítési](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) cikkben leírt lépéseket, és jelentkezzen be az Azure-fiókjába a `az-login` paranccsal.

2. Hajtsa végre az alábbi szakaszok valamelyikének lépéseit az Ön igényei nek megfelelően:

    **Privát IP-cím hozzáadása**
    
    Ha privát IP-címet szeretne hozzáadni egy hálózati adapterhez, a következő paranccsal létre kell hoznia egy IP-konfigurációt. A statikus IP-címnek az alhálózat nem használt címének kell lennie.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Hozzon létre annyi konfigurációt, amennyire szüksége van, egyedi konfigurációnevek és privát IP-címek használatával (statikus IP-címekkel rendelkező konfigurációk esetén).

    **Nyilvános IP-cím hozzáadása**
    
    A nyilvános IP-cím hozzáadódik azáltal, hogy új IP-konfigurációhoz vagy meglévő IP-konfigurációhoz társítja azt. Végezze el a következő szakaszok egyikének lépéseit, ahogy azt szeretné.

    A nyilvános IP-címek névleges díjkötelesek. Ha többet szeretne megtudni az IP-cím díjszabásáról, olvassa el az [IP-cím díjszabási](https://azure.microsoft.com/pricing/details/ip-addresses) oldalát. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.

    - **Az erőforrás társítása új IP-konfigurációhoz**
    
        Amikor nyilvános IP-címet ad hozzá egy új IP-konfigurációban, saját IP-címet is hozzá kell adnia, mert minden IP-konfigurációnak privát IP-címmel kell rendelkeznie. Hozzáadhat egy meglévő nyilvános IP-címerőforrást, vagy létrehozhat egy újat. Új létrehozásához írja be a következő parancsot:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Ha statikus privát IP-címmel és a kapcsolódó *myPublicIP3* nyilvános IP-cím erőforrással rendelkező új IP-konfigurációt szeretne létrehozni, írja be a következő parancsot:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Az erőforrás társítása meglévő IP-konfigurációhoz** Nyilvános IP-cím erőforrás csak olyan IP-konfigurációhoz társítható, amelyhez még nem tartozik. A következő parancs megadásával meghatározhatja, hogy egy IP-konfigurációrendelkezik-e nyilvános IP-címmel:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Visszaadott kimenet:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Mivel az *IpConfig-3* **PublicIpAddressId** oszlopa üres a kimenetben, jelenleg nincs hozzá nyilvános IP-címerőforrás társítva. Az IpConfig-3 protokollhoz hozzáadhat egy meglévő nyilvános IP-címerőforrást, vagy a következő parancsot adhatja meg:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Írja be a következő parancsot a nyilvános IP-cím erőforrás társításához az *IPConfig-3*nevű meglévő IP-konfigurációhoz:
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. A hálózati adapterhez rendelt privát IP-címek és nyilvános IP-cím-azonosítók megtekintéséhez írja be a következő parancsot:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Visszaadott kimenet: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Adja hozzá a hálózati adapterhez hozzáadott privát IP-címeket a virtuális gép operációs rendszeréhez a cikk [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszában található utasításokat követve. Ne adja hozzá a nyilvános IP-címeket az operációs rendszerhez.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
