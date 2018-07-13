---
title: Rövid útmutató – Linux rendszerű virtuális gép létrehozása az Azure PowerShell használatával | Microsoft Docs
description: Ebből a rövid útmutatóból elsajátíthatja, hogyan használható az Azure PowerShell Linux rendszerű virtuális gépek létrehozására
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 45b1597d3b61a9386fc015b5a7272d948fa5772b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930946"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Rövid útmutató: Linux virtuális gép létrehozása az Azure-ban PowerShell-lel

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használja az Azure PowerShell modult Ubuntut futtató Linux rendszerű virtuális gép üzembe helyezésére az Azure-ban. A virtuális gép működésének ellenőrzéséhez ezután SSH-kapcsolaton keresztül csatlakozzon a géphez, és telepítse az NGINX-webkiszolgálót.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Utolsó lépésként a Windows-beli felhasználói profiljának *.ssh* mappájába kell menteni az *id_rsa.pub* nevű nyilvános SSH-kulcsot. Az SSH-kulcsok létrehozásáról és használatáról az [SSH-kulcsok Azure-hoz történő létrehozását](ssh-from-windows.md) ismertető cikkben talál részletes információt.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoportok olyan logikai tárolók, amelyekbe a rendszer üzembe helyezi az Azure-erőforrásokat és kezeli azokat:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Virtuális hálózati erőforrások létrehozása

Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális gép hálózati csatlakoztatásának biztosítására, illetve az internethez csatlakoztatására használatosak:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myVNET" -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Hozzon létre egy Azure hálózati biztonsági csoportot és egy adatforgalmi szabályt. A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális gépet. A következő példában létrehozunk egy bejövő forgalomra vonatkozó szabályt a 22-es TCP-porthoz, amely engedélyezi az SSH-kapcsolatokat. A bejövő webforgalom engedélyezéséhez létrehozunk egy bejövő forgalomra vonatkozó szabályt is a 80-as TCP-porthoz.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleSSH"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleWWW"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myNetworkSecurityGroup" -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Hozzon létre egy virtuális hálózati adaptert (NIC) a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) parancsmaggal. A virtuális hálózati kártya csatlakoztatja a virtuális gépet egy alhálózathoz, egy hálózati biztonsági csoporthoz és egy nyilvános IP-címhez.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name "myNic" -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A virtuális gép konfigurációja tartalmazza a virtuális gép üzembe helyezésekor használt beállításokat, például a virtuális gép rendszerképét, méretét és hitelesítési beállításait. Adja meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét az alábbiak szerint:

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName "myVM" -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Most kombinálhatja az előző konfigurációs definíciókat a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal való létrehozáshoz:

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az üzembe helyezés befejeződése után hozzon létre SSH-kapcsolatot a virtuális géphez. A virtuális gép működés közbeni megtekintéséhez telepítse az NGINX webkiszolgálót.

A virtuális gép nyilvános IP-címének megtekintésére használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Használjon egy SSH-ügyfelet a virtuális géphez való csatlakozásra. Használhatja az Azure Cloud Shellt egy webböngészőből, vagy ha Windows használ, akkor a [Putty](ssh-from-windows.md) alkalmazást vagy a [Linux rendszerhez készült Windows alrendszert](/windows/wsl/install-win10). Adja meg a virtuális gép nyilvános IP-címét:

```bash
ssh azureuser@IpAddress
```

Ha a rendszer kéri, adja meg a következő bejelentkezési felhasználónevet: *azureuser*. Ha jelszót használ az SSH-kulcsokkal, akkor azt a rendszer kérésére meg kell adnia.

## <a name="install-web-server"></a>A webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az NGINX-webkiszolgálót. A csomagforrások frissítéséhez és a legújabb NGINX-csomag telepítéséhez futtassa a következő parancsokat az SSH-munkamenetből:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Amikor elkészült, zárja be (`exit`) az SSH-munkamenetet

## <a name="view-the-web-server-in-action"></a>A webkiszolgáló megtekintése működés közben

Most, hogy az NGINX telepítve van, és a 80-as port meg van nyitva a virtuális gépén az internetről, tetszőleges böngészőt használhat az alapértelmezett NGINX-kezdőlap megtekintéséhez. Használja a virtuális gép korábbi lépésben beszerzett nyilvános IP-címét. A következő példában az alapértelmezett NGINX-webhely látható:

![Alapértelmezett NGINX-webhely](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) parancsmaggal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, létrehozott egy hálózati biztonsági csoportot és szabályt, valamint telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)
