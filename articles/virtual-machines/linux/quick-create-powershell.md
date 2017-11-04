---
title: "Azure gyors üzembe helyezés – Virtuális gépek létrehozása PowerShell használatával | Microsoft Docs"
description: "Gyorsan megismerheti a Linux rendszerű virtuális gépek PowerShell segítségével történő létrehozásának módját."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/13/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fdd83f2386055fa9fac1ad50f4b01bf4419342b5
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="create-a-linux-virtual-machine-with-powershell"></a>Linux rendszerű virtuális gép létrehozása PowerShell segítségével

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató részletesen bemutatja, hogyan lehet egy Ubuntu Servert futtató virtuális gépet üzembe helyezni az Azure PowerShell-modullal. A kiszolgáló üzembe helyezése után a rendszer létrehoz egy SSH-kapcsolatot, és telepít egy NGINX-webkiszolgálót.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

A gyorsútmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Utolsó lépésként a Windows-beli felhasználói profiljának *.ssh* mappájába kell menteni az *id_rsa.pub* nevű nyilvános SSH-kulcsot. Az Azure-hoz tartozó SSH-kulcsok létrehozásával kapcsolatos részletes információkért olvassa el az [SSH-kulcsok az Azure-hoz történő létrehozását](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertető cikket.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
```

## <a name="create-networking-resources"></a>Hálózati erőforrások létrehozása

Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális gép hálózati csatlakoztatásának biztosítására, illetve az internethez csatlakoztatására használatosak.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus `
-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Hozzon létre egy hálózati biztonsági csoportot és egy hálózati biztonsági csoportszabályt. A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális gépet. Ebben az esetben létrejön egy bejövő szabály a 22-es porthoz, amely lehetővé teszi a bejövő SSH-kapcsolatokat. Egy bejövő szabályt is létre szeretnénk hozni a 80-as porthoz, amely lehetővé teszi a bejövő webes forgalmat.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Hozzon létre egy hálózati kártyát a virtuális géphez a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) parancsmaggal. A hálózati kártya csatlakoztatja a virtuális gépet egy alhálózathoz, egy hálózati biztonsági csoporthoz és egy nyilvános IP-címhez.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuálisgép-konfigurációt. Ebben a konfigurációban a virtuális gép üzembe helyezése során használt beállítások szerepelnek, például a virtuális gép rendszerképe, mérete és hitelesítési konfigurációja.

```powershell
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName myVM -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName Canonical -Offer UbuntuServer -Skus 16.04-LTS -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy SSH-kapcsolatot a virtuális géppel.

A virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Ha olyan rendszert használ, amelyre telepítve van az SSH, a következő paranccsal csatlakozhat a virtuális géphez. Ha Windows rendszert használ, a [Putty](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-ssh-from-windows?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-private-key-for-putty) segítségével hozhatja létre a kapcsolatot. 

```bash 
ssh <Public IP Address>
```

Ha a rendszer kéri, adja meg a következő bejelentkezési felhasználónevet: *azureuser*. Ha megadott egy hozzáférési kódot az SSH-kulcsok létrehozásakor, azt is meg kell adnia.


## <a name="install-nginx"></a>Az NGINX telepítése

Az alábbi parancsokkal csomag adatforrások frissítése, és telepítse a legújabb NGINX-csomagot. 

```bash 
# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Most, hogy az NGINX telepítve van, és a 80-as port meg van nyitva a virtuális gépén az internet irányából, tetszőleges böngészőt használhat az alapértelmezett NGINX-kezdőlap megtekintéséhez. Ügyeljen arra, hogy az alapértelmezett oldalt a fentebb dokumentált nyilvános IP-cím használatával keresse fel. 

![Alapértelmezett NGINX-webhely](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, egy hálózati biztonsági csoport szabályát, valamint telepített egy webkiszolgálót. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)
