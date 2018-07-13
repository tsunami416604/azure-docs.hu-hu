---
title: Virtuális gép létrehozása egy statikus nyilvános IP-cím – Azure CLI-vel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy virtuális gép statikus nyilvános IP-címet az Azure parancssori felület (CLI) használatával.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
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
ms.openlocfilehash: bd44971162a79e53b731c5c89316f14e8bb0a1a6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651959"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Virtuális gép létrehozása egy statikus nyilvános IP-címet az Azure CLI használatával

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk ismerteti a Microsoft azt javasolja, a klasszikus üzemi modell helyett új telepítések esetén a Resource Manager üzemi modell használatával.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>A virtuális gép létrehozása

Az értékeket a "" a változók a következő lépések az erőforrások a forgatókönyvet a beállításokkal hozzon létre. Módosítsa az értékeket, ha a környezet szükséges.

1. Telepítse a [Azure CLI 2.0](/cli/azure/install-az-cli2) Ha már nincs telepítve.
2. SSH nyilvános és titkos kulcspárok létrehozása Linux rendszerű virtuális gépek által ismertetett lépéseket követve a [SSH nyilvános és titkos kulcspárok létrehozása Linux rendszerű virtuális gépek](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Egy parancs-rendszerhéjból, jelentkezzen be a parancs `az login`.
4. A virtuális gép létrehozásához hajtsa végre a parancsprogramot, a Linux vagy Mac számítógépen a következő. Az Azure nyilvános IP-címet, a virtuális hálózat, a hálózati adapter és a Virtuálisgép-erőforrások összes léteznie kell ugyanazon a helyen. Bár az erőforrásokat ugyanabban az erőforráscsoportban található, nincs, az alábbi szkriptben ezekre is vonatkoznak.

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

Amellett, hogy egy virtuális Gépet létrehozni, a szkriptet hoz létre:
- Egy egyetlen prémium szintű felügyelt lemez alapértelmezés szerint, de más beállítások érhetők el a lemez típusát is létrehozhat. Olvassa el a [Linux rendszerű virtuális gép létrehozása az Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) részleteivel.
- Virtuális hálózat, alhálózat, hálózati és nyilvános IP-cím erőforrás. Másik lehetőségként használhatja *meglévő* virtuális hálózat, alhálózat, hálózati adapter vagy nyilvános IP-cím erőforrás. Ismerje meg, hogyan használhatja a további erőforrások létrehozásához helyett a meglévő hálózati erőforrásokhoz, írja be a következőt `az vm create -h`.

## <a name = "validate"></a>A virtuális gép létrehozása és a nyilvános IP-cím ellenőrzése

1. Adja meg a parancsot `az resource list --resouce-group IaaSStory --output table` a parancsfájl által létrehozott erőforrások listájának megtekintéséhez. Kell lennie öt erőforrások a visszaadott kimenetet: hálózati adapter, lemez, nyilvános IP-cím, virtuális hálózat és egy virtuális gépet.
2. Adja meg a parancsot `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. A visszaadott kimenetet, jegyezze fel az értékét **IP-cím** , és hogy értékét **PublicIpAllocationMethod** van *statikus*.
3. Mielőtt végrehajtja a következő parancsot, távolítsa el a <>, cserélje le *felhasználónév* használt nevű a **felhasználónév** változó a parancsfájlt, és cserélje le a *IP-cím*együtt a **IP-cím** az előző lépésben. Futtassa a következő parancsot a virtuális Géphez való csatlakozáshoz: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Távolítsa el a virtuális gép és a kapcsolódó erőforrások

Javasoljuk, hogy biztosan törölje az ebben a gyakorlatban, ha nem használja éles környezetben létrehozott erőforrásokat. Virtuális gép nyilvános IP-cím és lemezes erőforrásaira díjkötelesek, mindaddig, amíg azok üzemelnek. Ez a gyakorlat során létrehozott erőforrások eltávolításához kövesse az alábbi lépéseket:

1. Az erőforráscsoportban lévő erőforrásokat megtekintéséhez futtassa a `az resource list --resource-group IaaSStory` parancsot.
2. Ellenőrizze, hogy nincsenek erőforrások az erőforráscsoportban, nem az ebben a cikkben a parancsfájl által létrehozott erőforrásokat. 
3. A gyakorlat során létrehozott összes erőforrás törléséhez futtassa a `az group delete -n IaaSStory` parancsot. A parancs törli az erőforráscsoportot és a benne található összes erőforrást.
 
## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím. Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges, ha például [több IP-címek hozzárendelése virtuális géphez Windows](virtual-network-multiple-ip-addresses-cli.md). Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, érdekében, hogy az Azure-ban rendelt magánhálózati IP-cím megegyező címre [hálózati adapter](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. Tudjon meg többet [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait.

## <a name="next-steps"></a>További lépések

Hálózati forgalmat, és a virtuális gépről a jelen cikkben létrehozott áramolhasson. Bejövő és kimenő biztonsági szabályok egy hálózati biztonsági csoporton belül, amely korlátozza a forgalmat, amely a hálózati adapter vagy az alhálózaton, illetve határozhatja meg. Hálózati biztonsági csoportokkal kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoportok áttekintése](security-overview.md).