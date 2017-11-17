---
title: "Virtuális gép több IP-címek az Azure parancssori felülettel |} Microsoft Docs"
description: "Útmutató több IP-címek hozzárendelése egy virtuális gép az Azure parancssori felület (CLI) használatával."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: jimdial
ms.openlocfilehash: aa0f84299dcb4800cd332d8276785f6b08152060
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Több IP-címek hozzárendelése a virtuális gépek az Azure parancssori felület használatával

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk ismerteti (VM) virtuális gép létrehozása az Azure Resource Manager deployment használatával az Azure parancssori felület használatával. Több IP-cím nem lehet hozzárendelni a klasszikus üzembe helyezési modell használatával létrejött erőforrásokat. Az Azure üzembe helyezési modellel kapcsolatos további tudnivalókért olvassa el a [üzembe helyezési modellel megértéséhez](../resource-manager-deployment-model.md) cikk.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Több IP-címekkel rendelkező virtuális gép létrehozása

A következő lépések bemutatják egy példa virtuális gép létrehozása több IP-címmel, a forgatókönyvben leírtak szerint. A változók értékeinek módosítása "" és az IP-cím típusok a végrehajtásához szükség szerint. 

1. Telepítse a [Azure CLI 2.0](/cli/azure/install-az-cli2) Ha még nincs telepítve.
2. Az SSH nyilvános és titkos kulcsból álló kulcspárt létrehozása Linux virtuális gépek; Ehhez hajtsa végre a lépéseket a [az SSH nyilvános és titkos kulcsból álló kulcspárt létrehozása Linux virtuális gépek](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Jelentkezzen be a parancsot a parancssorba a `az login` , és válassza ki az előfizetést használ.
4. A virtuális gép létrehozása az alábbi Linux vagy a Mac számítógépen parancsfájl. A parancsfájl egy erőforráscsoportot, egy virtuális hálózathoz (VNet), a három IP-konfiguráció egyetlen hálózati Adapterrel és egy virtuális Gépet hoz létre a két hálózati adapter nem csatlakoztatható. A hálózati adapter, nyilvános IP-cím, virtuális hálózatot és Virtuálisgép-erőforrások összes léteznie kell a helyre és az előfizetés. Bár az erőforrásokat ugyanabban az erőforráscsoportban szerepel a nincs, a következő parancsfájlban tehetik.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP adresses unique to each Azure region. Download and view the file from
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

azure network nic ip-config create \
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
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes rticle. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location estcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the utput returned by entering the
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

Mellett egy hálózati 3 IP-konfigurációk a virtuális gép létrehozása, a parancsfájl hoz létre:

- Egyszeri támogatás lemez felügyelt alapértelmezés szerint, de más beállítások érhetők el a lemez típusát is létrehozhat. Olvassa el a [Linux virtuális gép létrehozása az Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikkben alább.
- Egy virtuális hálózatot egyetlen alhálózattal és két nyilvános IP-címet. Másik megoldásként használhatja *meglévő* virtuális hálózat, alhálózat, a hálózati adapter vagy nyilvános IP-cím erőforrás. Megtudhatja, hogyan használja a további erőforrások létrehozása helyett a meglévő hálózati erőforrások, írja be a következőt `az vm create -h`.

Nyilvános IP-címek névleges díjat kell. IP-cím árazással kapcsolatos további tudnivalókért olvassa el a [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses) lap. Nyilvános IP-címek egy előfizetésben használható száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.

A virtuális gép létrehozása után adja meg a `az network nic show --name MyNic1 --resource-group myResourceGroup` parancs használatával tekintheti meg a hálózati konfigurációt. Adja meg a `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` pufferében társított IP-konfigurációk listájának megtekintése

A privát IP-címek hozzáadása a virtuális gép operációs rendszer, az operációs rendszernek a lépések végrehajtásával a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát.

## <a name="add"></a>IP-címek hozzáadása a virtuális gépek

További privát és nyilvános IP-címeket adhat hozzá egy meglévő hálózati adapterre a következő lépések végrehajtásával. A példák épül a [forgatókönyv](#Scenario) a cikkben.

1. Nyissa meg a parancssorba, és hajtsa végre a fennmaradó lépéseit ebben a szakaszban egy munkameneten belül. Ha még nem rendelkezik Azure CLI telepítése és konfigurálása, hajtsa végre a a [Azure CLI 2.0 telepítési](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk és az Azure bejelentkezési fiók az a `az-login` parancsot.

2. Hajtsa végre a következő részeiből, a követelmények alapján:

    **Adja hozzá a magánhálózati IP-cím**
    
    A magánhálózati IP-cím hozzáadása a hálózati adapter, létre kell hoznia egy IP-konfiguráció a következő parancs használatával. A statikus IP-cím az alhálózat nem használt címnek kell lennie.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Tetszőleges számú konfigurációk összes kívánt beállítást, egyedi konfigurációs nevek és privát IP-címek használata (a statikus IP-címekkel rendelkező konfigurációk) létrehozása.

    **A nyilvános IP-cím hozzáadása**
    
    A nyilvános IP-cím jelenik meg egy új IP-konfigurációt vagy egy meglévő IP-konfiguráció társításával. Hajtsa végre a következő szakaszok, egyikében összes kívánt beállítást.

    Nyilvános IP-címek névleges díjat kell. IP-cím árazással kapcsolatos további tudnivalókért olvassa el a [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses) lap. Nyilvános IP-címek egy előfizetésben használható száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.

    - **Társítsa az erőforrást egy új IP-konfiguráció**
    
        Egy nyilvános IP-címet ad hozzá egy új IP-konfiguráció, amikor is hozzá kell magánhálózati IP-cím, mert az összes IP-konfiguráció magánhálózati IP-címnek kell rendelkeznie. Egy meglévő nyilvános IP-cím erőforrás hozzáadása, vagy hozzon létre egy újat. Hozzon létre egy újat, írja be a következő parancsot:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Új IP-konfiguráció létrehozása hozzá statikus magánhálózati IP-cím és a társított *myPublicIP3* nyilvános IP-cím erőforrás címet, adja meg a következő parancsot:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Társítsa az erőforrást egy meglévő IP-konfiguráció** egy nyilvános IP-cím erőforrás csak lehet társítva, amely még nincs társított IP-konfigurációt. Segítségével meghatározhatja, hogy rendelkezik-e IP-konfigurációt tartozó nyilvános IP-cím a következő parancs beírásával:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Kimeneti adott vissza:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Mivel a **PublicIpAddressId** oszlopában *IpConfig-3* van a kimenetben üres, nem nyilvános IP-cím erőforrás jelenleg társítva. Egy meglévő nyilvános IP-cím erőforrás hozzáadása IpConfig-3, vagy hozzon létre egyet a következő parancsot írja be:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Adja meg a következő parancsot a meglévő IP-konfiguráció a nyilvános IP-cím erőforrás hozzárendelni *IPConfig-3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. A privát IP-címek és a nyilvános IP-cím erőforrás a következő parancs beírásával a hálózati adapterhez társított azonosítók megtekintése:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Kimeneti adott vissza: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Adja hozzá a magánhálózati IP-címek utasításait követve hozzáadni a hálózati Adaptert a virtuális gép operációs rendszerre a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegyen fel a nyilvános IP-címeket az operációs rendszer.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
