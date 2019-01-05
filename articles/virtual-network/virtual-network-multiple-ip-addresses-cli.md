---
title: Több IP-cím az Azure CLI-vel rendelkező virtuális gép
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogy több IP-cím hozzárendelése a virtuális gépek az Azure parancssori felület (CLI) segítségével.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: jimdial
ms.openlocfilehash: b383bd601335e8c828c4c734ad8015b650fdc9f3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54055115"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Több IP-cím hozzárendelése a virtuális gépek az Azure CLI használatával

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre egy virtuális gépet (VM) az Azure Resource Manager-alapú üzemi modellel, az Azure CLI használatával. Több IP-cím nem lehet hozzárendelni a klasszikus üzemi modellel létrehozott erőforrásokat. Azure üzembehelyezési modellekkel kapcsolatos további tudnivalókért olvassa el a [üzembe helyezési modellek ismertetése](../resource-manager-deployment-model.md) cikk.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

A következő lépések azt ismertetik, hogyan hozzon létre egy példa virtuális gépet több IP-címet, a forgatókönyvben leírtak szerint. Módosíthatja a "" és az IP-cím-típusok, a végrehajtásához szükség szerint. 

1. Telepítse a [Azure CLI-vel](/cli/azure/install-azure-cli) Ha már nincs telepítve.
2. SSH nyilvános és titkos kulcspárok létrehozása Linux rendszerű virtuális gépek által ismertetett lépéseket követve a [SSH nyilvános és titkos kulcspárok létrehozása Linux rendszerű virtuális gépek](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Egy parancs-rendszerhéjból, jelentkezzen be a parancs `az login` , és válassza ki az előfizetést használ.
4. A virtuális gép létrehozásához hajtsa végre a parancsprogramot, a Linux vagy Mac számítógépen a következő. A parancsfájl egy erőforráscsoportot, egy virtuális hálózat (VNet), egy hálózati adapter három IP-konfigurációval és a egy virtuális Gépet hoz létre a csatlakoztatott két hálózati adapterrel. A hálózati adapter, a nyilvános IP-címet, a virtuális hálózat és a Virtuálisgép-erőforrások összes léteznie kell az azonos helyen és előfizetésen. Bár az erőforrásokat ugyanabban az erőforráscsoportban található, nincs, az alábbi szkriptben ezekre is vonatkoznak.

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

Mellett egy hálózati Adapterrel 3 IP-konfigurációval rendelkező virtuális Gépet létrehozni, a szkriptet hoz létre:

- Egy egyetlen prémium szintű felügyelt lemez alapértelmezés szerint, de más beállítások érhetők el a lemez típusát is létrehozhat. Olvassa el a [Linux virtuális gép létrehozása az Azure CLI](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) részleteivel.
- Egy virtuális hálózatot egyetlen alhálózattal és két nyilvános IP-címet. Másik lehetőségként használhatja *meglévő* virtuális hálózat, alhálózat, hálózati adapter vagy nyilvános IP-cím erőforrás. Ismerje meg, hogyan használhatja a további erőforrások létrehozásához helyett a meglévő hálózati erőforrásokhoz, írja be a következőt `az vm create -h`.

Nyilvános IP-címek egy névleges díj rendelkezik. IP-címek díjszabása kapcsolatos további információkért olvassa el a [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses) lapot. Egy előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.

Miután a virtuális gép létrejött, írja be a `az network nic show --name MyNic1 --resource-group myResourceGroup` parancs használatával megtekintheti a hálózati adapter konfigurációját. Adja meg a `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` a hálózati adaptert. társított IP-konfigurációk listájának megtekintése

A magánhálózati IP-címek hozzáadása a virtuális gép operációs rendszeréhez, az operációs rendszernek a lépéseket követve a [hozzáadása IP-címek a virtuális gép operációs rendszerre](#os-config) című szakaszát.

## <a name="add"></a>IP-címek hozzáadása egy virtuális Géphez

Az alábbi lépéseket követve adhat hozzá további privát és nyilvános IP-címek egy meglévő Azure-beli hálózati interfészhez. A példák útmutatóra a [forgatókönyv](#Scenario) ebben a cikkben leírt.

1. Nyisson meg egy parancs-rendszerhéjat, és a hátralévő lépéseket ebben a szakaszban egy egyetlen munkamenetben. Ha még nem rendelkezik Azure CLI telepítve és konfigurálva, hajtsa végre a a [Azure CLI telepítési](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk, és jelentkezzen be az Azure-fiók az a `az-login` parancsot.

2. Hajtsa végre az alábbi szakaszokban, igényei alapján lépéseit:

    **Magánhálózati IP-cím hozzáadása**
    
    A hálózati adapter magánhálózati IP-cím hozzá, létre kell hoznia egy IP-konfigurációt a következő parancs használatával. A statikus IP-címet kell lennie a nem használt cím az alhálózat.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Hozzon létre annyi konfigurációk, amennyi szükséges, egyedi konfigurációs nevek és magánhálózati IP-címek használata (a statikus IP-címekkel rendelkező konfigurációk).

    **Nyilvános IP-cím hozzáadása**
    
    Nyilvános IP-cím vagy egy új IP-konfigurációt, vagy egy meglévő IP-konfiguráció társításával egészül ki. Hajtsa végre a következő szakaszokban áttekintendő, egyik lépéseit, amennyi szükséges.

    Nyilvános IP-címek egy névleges díj rendelkezik. IP-címek díjszabása kapcsolatos további információkért olvassa el a [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses) lapot. Egy előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.

    - **Rendelje hozzá egy új IP-konfigurációhoz az erőforrást**
    
        Minden alkalommal, amikor egy új IP-konfiguráció nyilvános IP-címet ad hozzá, hozzá kell adnia is magánhálózati IP-cím, mert minden IP-konfigurációval kell rendelkeznie a magánhálózati IP-cím. Adjon hozzá egy meglévő nyilvános IP-cím erőforrás, vagy hozzon létre egy újat. Hozzon létre egy újat, adja meg a következő parancsot:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Új IP-konfiguráció létrehozása egy statikus magánhálózati IP-cím és a társított *myPublicIP3* nyilvános IP-cím erőforrás, adja meg a következő parancsot:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Rendelje hozzá az erőforrást egy meglévő IP-konfiguráció** egy nyilvános IP-cím erőforrás csak lehet társítva, amely még nincs ilyen társított IP-konfigurációval. Segítségével meghatározhatja, hogy rendelkezik-e egy IP-konfiguráció társított nyilvános IP-cím a következő parancs beírásával:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Kimeneti adta vissza:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Mivel a **PublicIpAddressId** oszlopában *IpConfig-3* van üres a kimenetben, nincs nyilvános IP-cím erőforrás jelenleg társítva. Adjon hozzá egy meglévő nyilvános IP-cím erőforrás IpConfig-3, vagy hozzon létre egyet a következő parancsot írja be:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Adja meg a következő parancsot a nyilvános IP-cím erőforrás a meglévő IP-konfigurációhoz nevű társítása *IPConfig-3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. A magánhálózati IP-címek és a nyilvános IP-cím erőforrás a következő parancs beírásával a hálózati adapterhez társított azonosítók megtekintése:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Kimeneti adta vissza: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Adja hozzá a magánhálózati IP-címet adta hozzá a hálózati adapterhez, a virtuális gép operációs rendszer utasításait a [hozzáadása IP-címek a virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegye fel a nyilvános IP-címek az operációs rendszer.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
