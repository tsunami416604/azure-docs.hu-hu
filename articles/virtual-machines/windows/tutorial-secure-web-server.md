---
title: "Az Azure SSL-tanúsítványok IIS védelme |} Microsoft Docs"
description: "Megtudhatja, mennyire biztonságos a Windows Azure-ban az SSL-tanúsítványokat az IIS-webkiszolgáló"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6567853e9ef3cad63595dc0afe7a793bdc5d972c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>Biztonságos Windows virtuális gépre az Azure-ban SSL-tanúsítványokat az IIS-webkiszolgáló
Biztonságos webkiszolgálók, a Secure Sockets később (SSL) tanúsítvány használható webes forgalom titkosításához. E SSL-tanúsítványokat tárolhatja az Azure Key Vault, és biztonságos telepítéshez a Windows virtuális gépek (VM) tanúsítványok engedélyezése az Azure-ban. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Egy Azure-tároló létrehozása
> * Hozza létre, vagy a Key Vault tanúsítvány feltöltése
> * Hozzon létre egy virtuális Gépet, és az IIS-webkiszolgáló telepítése
> * A tanúsítvány szúrjon be a virtuális Gépet, és SSL-kötést az IIS konfigurálása

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.


## <a name="overview"></a>Áttekintés
Az Azure Key Vault megvédi a titkosítási kulcsok és titkos kulcsok, ilyen tanúsítványokat vagy jelszavakat. Key Vault segít a felügyeleti folyamat egyszerűsítésére, és lehetővé teszi a kulcsok ezeknek a tanúsítványoknak elérő irányítást tarthat fenn. Hozzon létre egy önaláírt tanúsítványt Key Vault belül, vagy töltsön fel egy meglévő, a megbízható tanúsítványt, amely már Ön a tulajdonosa.

Ahelyett, hogy az egyéni Virtuálisgép-lemezkép tanúsítványokat tartalmaz bővíthetőség a tanúsítványok behelyezése egy futó virtuális Gépet. Ez a folyamat biztosítja, hogy a legfrissebb tanúsítványok telepítve vannak a webkiszolgáló üzembe helyezése során. Újítsa meg, vagy cserélje le a tanúsítványt, ha nem is kell létrehoznia egy új egyéni Virtuálisgép-lemezkép. A legújabb tanúsítványok automatikusan vannak olyanok, további virtuális gépek létrehozásakor. A teljes folyamat során a tanúsítványok soha ne hagyja meg az Azure platformon, vagy egy parancsfájl, a parancssori előzmények vagy a sablon ki vannak téve.


## <a name="create-an-azure-key-vault"></a>Egy Azure-tároló létrehozása
Tanúsítványok és a kulcstároló létrehozásához, hozzon létre egy erőforráscsoportot, a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupSecureWeb* a a *USA keleti régiója* helye:

```powershell
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Ezután hozzon létre egy rendelkező Key Vaultból [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Minden Key Vault szükséges egy egyedi nevet, és minden kisbetű kell lennie. Cserélje le `<mykeyvault>` az alábbi példában a saját egyedi kulcstároló nevével:

```powershell
$keyvaultName="<mykeyvault>"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Egy tanúsítvány jön létre, és tárolja a Key Vault
Az éles környezetben való használathoz, importálnia kell a egy érvényes tanúsítvány megbízható-szolgáltató által aláírt [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). Ebben az oktatóanyagban a következő példa bemutatja, hogyan hozhat létre egy önaláírt tanúsítványt [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) , amely használja az alapértelmezett tanúsítvány-házirend [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```powershell
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Állítsa a virtuális gép a rendszergazda felhasználónév és jelszó [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Most a virtuális Géphez a hozhat létre [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példa hoz létre a szükséges virtuális hálózati összetevők, az operációs rendszer konfigurációja, és létrehoz egy nevű virtuális gép *myVM*:

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myVnet" `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleRDP"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 443
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleWWW"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name "myNic" `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2" | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2016-Datacenter" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

A virtuális gép létrehozásához néhány percet vesz igénybe. Az utolsó lépést az Azure egyéni parancsprogramok futtatására szolgáló bővítmény használja az IIS webkiszolgálóra telepítendő [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>A tanúsítvány felvétele a virtuális gép a Key Vault
A tanúsítvány hozzáadása a Key Vault egy virtuális géphez, szerezze be a tanúsítvány Azonosítóját [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). A tanúsítvány felvétele a virtuális Géphez a [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret):

```powershell
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRMVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Konfigurálja az IIS a tanúsítvány használatára
Használja az egyéni parancsprogramok futtatására szolgáló bővítmény újra [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) az IIS-konfiguráció frissítése. A frissítés a tanúsítványt az IIS a Key Vault beszúrta vonatkozik, és a webes kötés konfigurálja:

```powershell
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Biztonságos webes alkalmazás tesztelése
A nyilvános IP-címet a virtuális gép az beszerzése [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). Az alábbi példa beolvassa az IP-címek `myPublicIP` korábban létrehozott:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIP" | select "IpAddress"
```

Most nyisson meg egy webböngészőt, és írja be `https://<myPublicIP>` a böngésző címsorába. Válassza ki a biztonsági figyelmeztetést, ha egy önaláírt tanúsítványt használt elfogadásához **részletek** , majd **nyissa meg a képernyőn látható weblapon**:

![Fogadja el a webes böngésző biztonsági figyelmeztetés](./media/tutorial-secure-web-server/browser-warning.png)

A biztonságos IIS-webhelyet akkor jelenik meg, az alábbi példában látható módon:

![Futó biztonságos IIS webhely megtekintése](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy IIS webkiszolgálón az Azure Key Vault tárolt SSL-tanúsítvánnyal védett. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egy Azure-tároló létrehozása
> * Hozza létre, vagy a Key Vault tanúsítvány feltöltése
> * Hozzon létre egy virtuális Gépet, és az IIS-webkiszolgáló telepítése
> * A tanúsítvány szúrjon be a virtuális Gépet, és SSL-kötést az IIS konfigurálása

Kövesse a hivatkozásra kattintva megtekintheti az előre elkészített virtuálisgép-parancsprogram minták.

> [!div class="nextstepaction"]
> [Windows virtuális gép parancsfájl minták](./powershell-samples.md)