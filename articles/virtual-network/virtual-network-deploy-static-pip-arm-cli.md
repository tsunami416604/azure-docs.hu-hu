---
title: "Virtuális gép létrehozása egy statikus nyilvános IP-cím - Azure CLI 2.0 |} Microsoft Docs"
description: "Útmutató: virtuális gép létrehozása az Azure parancssori felület (CLI) 2.0 használatával statikus nyilvános IP-cím."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4c32694949880037f01bb2b6b9779d2cbb9809c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli-20"></a>Virtuális gép létrehozása az Azure CLI 2.0 használatával statikus nyilvános IP-cím

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI 2.0](virtual-network-deploy-static-pip-arm-cli.md)
> * [Azure CLI 1.0](virtual-network-deploy-static-pip-cli-nodejs.md)
> * [Sablon](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a Microsoft azt javasolja, hogy a klasszikus üzembe helyezési modellel helyett az új telepítések esetén a Resource Manager telepítési modell használatát bemutatja.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>A virtuális gép létrehozása

Hajthatja végre ezt a feladatot az Azure CLI 2.0 (Ez a cikk) vagy a [Azure CLI 1.0](virtual-network-deploy-static-pip-cli-nodejs.md). Az értékeket a "" a következő lépések a változók létre erőforrásokat tudja kihozni a forgatókönyvből beállításokkal. Módosítsa az értékeket, a környezetének megfelelő.

1. Telepítse a [Azure CLI 2.0](/cli/azure/install-az-cli2) Ha még nincs telepítve.
2. Az SSH nyilvános és titkos kulcsból álló kulcspárt létrehozása Linux virtuális gépek; Ehhez hajtsa végre a lépéseket a [az SSH nyilvános és titkos kulcsból álló kulcspárt létrehozása Linux virtuális gépek](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Jelentkezzen be a parancsot a parancssorba a `az login`.
4. A virtuális gép létrehozása az alábbi Linux vagy a Mac számítógépen parancsfájl. Az Azure nyilvános IP-cím, a virtuális hálózati, a hálózati adapter és a Virtuálisgép-erőforrások összes léteznie kell a helyre. Bár az erőforrásokat ugyanabban az erőforráscsoportban szerepel a nincs, a következő parancsfájlban tehetik.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
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
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

Virtuális gép létrehozása mellett a parancsfájl hoz létre:
- Egyszeri támogatás lemez felügyelt alapértelmezés szerint, de más beállítások érhetők el a lemez típusát is létrehozhat. Olvassa el a [Linux virtuális gép létrehozása az Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikkben alább.
- Virtuális hálózati alhálózat, a hálózati adapter és nyilvános IP-cím erőforrás. Másik megoldásként használhatja *meglévő* virtuális hálózat, alhálózat, a hálózati adapter vagy nyilvános IP-cím erőforrás. Megtudhatja, hogyan használja a további erőforrások létrehozása helyett a meglévő hálózati erőforrások, írja be a következőt `az vm create -h`.

## <a name = "validate"></a>Virtuális gép létrehozása és a nyilvános IP-cím ellenőrzése

1. Adja meg a parancsot `az resource list --resouce-group IaaSStory --output table` hozta létre a parancsfájl erőforrások listájának megjelenítéséhez. Nem kell öt erőforrások visszaadott kimenet: hálózati kapcsolat, lemez, nyilvános IP-cím, virtuális hálózati és a virtuális gép.
2. Adja meg a parancsot `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. A visszaadott kimeneti jegyezze fel a értékének **IP-cím** , és hogy értékének **PublicIpAllocationMethod** van *statikus*.
3. A következő parancs végrehajtása előtt távolítsa el a <>, cserélje le *felhasználónév* használt névvel a **felhasználónév** változó a parancsfájlt, és cserélje le a *IP-cím*rendelkező a **IP-cím** az előző lépésben. A következő parancsot a virtuális Géphez való kapcsolódáshoz: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Távolítsa el a virtuális gép és a kapcsolódó erőforrások

Javasoljuk, hogy törölje-e az erőforrások létrehozott ebben a gyakorlatban, ha nem használja őket éles környezetben. Virtuális gép, a nyilvános IP-cím és a lemezerőforrásokat függő díj terheli, mindaddig, amíg azok van üzembe helyezve. A gyakorlat során létrehozott erőforrások eltávolításához kövesse az alábbi lépéseket:

1. Az erőforrások az erőforráscsoportban megtekintéséhez futtassa a `az resource list --resource-group IaaSStory` parancsot.
2. Ellenőrizze, hogy nincsenek erőforrások az erőforráscsoportban, az ebben a cikkben a parancsfájl által létrehozott erőforrások eltérő. 
3. Ebben a gyakorlatban létrehozott összes erőforrást törli, futtassa a `az group delete -n IaaSStory` parancsot. A parancs törli az erőforráscsoportot és a benne található összes erőforrást.

## <a name="next-steps"></a>Következő lépések

A hálózati forgalommal áramolhasson felé és felől a virtuális gép létrehozása az ebben a cikkben. Megadhatja a bejövő és kimenő szabályok belül egy NSG korlátozó áramolhasson az a hálózati adapter vagy az alhálózat érkező vagy oda irányuló forgalmat. Ha többet szeretne megtudni az NSG-k, olvassa el a [NSG áttekintése](virtual-networks-nsg.md) cikk.