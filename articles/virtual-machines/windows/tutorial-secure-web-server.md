---
title: Oktatóanyag – Windows webkiszolgáló védelme SSL-tanúsítványokkal az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használhatja az Azure PowerShellt olyan Windows rendszerű virtuális gép védelmére, amely az IIS webkiszolgálót az Azure Key Vaultban tárolt SSL-tanúsítványokkal futtatja.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 76d1170f4696c4221233d2b3c1d358375adfe5c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189547"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>Oktatóanyag: Webkiszolgáló védelme a Key Vaultban tárolt SSL-tanúsítványokkal Windows rendszerű virtuális gépen az Azure-ban

A webkiszolgálók védelméhez egy Secure Sockets Layer- (SSL-) tanúsítvánnyal titkosítható a webes adatforgalom. Ezek az SSL-tanúsítványok az Azure Key Vaultban tárolhatók, és lehetővé teszik a tanúsítványok biztonságos üzembe helyezését a Windows rendszerű virtuális gépeken az Azure-ban. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Key Vault létrehozása;
> * tanúsítvány létrehozása vagy feltöltése a Key Vaultba;
> * Virtuális gép létrehozása és az IIS-webkiszolgáló telepítése
> * A tanúsítvány elhelyezése a virtuális gépen és az IIS konfigurálása SSL-kötéssel

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


## <a name="overview"></a>Áttekintés
Az Azure Key Vault megvédi a titkosítási kulcsokat és titkos kódokat, például a tanúsítványokat és jelszavakat. A Key Vault leegyszerűsíti a tanúsítványkezelési folyamatot, valamint lehetővé teszi a tanúsítványokhoz hozzáférő kulcsok feletti teljes körű felügyeletet. Létrehozhat egy önaláírt tanúsítványt a Key Vaultban, vagy feltölthet egy meglévő, megbízható tanúsítványt.

Beépített tanúsítványokat tartalmazó egyéni virtuális gép rendszerkép használata helyett a futó virtuális gépeken helyezi el a tanúsítványokat. Ez a folyamat biztosítja, hogy a legnaprakészebb tanúsítványok legyenek telepítve a webkiszolgálókon az üzembe helyezés alatt. Ha frissíti vagy lecseréli a tanúsítványt, nem kell új rendszerképet is létrehoznia a virtuális gépekről. A további virtuális gépek létrehozásakor a rendszer automatikusan alkalmazza a legújabb tanúsítványokat. A tanúsítványok a folyamat során egyszer sem hagyják el az Azure platformot, és nem jelennek meg szkriptekben, a parancssori előzményekben vagy sablonokban.


## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;
Mielőtt létrehozhatna egy Key Vaultot és a tanúsítványokat, létre kell hoznia egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példában létrehozunk egy *myResourceGroupSecureWeb* nevű erőforráscsoportot az *USA keleti régiója* helyen:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Ezután létrehozunk egy Key Vaultot a [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) paranccsal. Mindegyik Key Vaultnak egyedi névvel kell rendelkeznie, amely csak kisbetűkből állhat. Cserélje le a `mykeyvault` nevet a következő példában a saját egyedi Key Vault-névre:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Tanúsítvány létrehozása és tárolása a Key Vaultban
Éles környezetben importálnia kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt az [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate) paranccsal. Ebben az oktatóanyagban a következő példa mutatja be, hogyan hozhat létre olyan önaláírt tanúsítványt az [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) paranccsal, amely az alapértelmezett tanúsítványszabályzatot használja a [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy) parancsból. 

```azurepowershell-interactive
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
Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre az *USA keleti régiója* helyen. Ha még nem léteznek, létrejönnek a támogató hálózati erőforrások. A biztonságos webes forgalom engedélyezéséhez a parancsmag megnyitja a *443*-as portot is.

```azurepowershell-interactive
# Create a VM
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

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

A virtuális gép létrehozása néhány percig tart. Az utolsó lépés az egyéni Azure-szkriptek bővítményével telepíti az IIS-webkiszolgálót a [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension) paranccsal.


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Tanúsítvány hozzáadása egy virtuális géphez a Key Vaultból
Ha a tanúsítványt a Key Vaultból egy virtuális géphez szeretné adni, szerezze be a tanúsítvány azonosítóját a [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret) paranccsal. Adja a tanúsítványt a virtuális géphez az [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret) paranccsal:

```azurepowershell-interactive
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRmVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Az IIS konfigurálása a tanúsítvány használatára
Ismét az egyéni szkriptek bővítményének használatával frissítse az IIS-konfigurációt a [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) paranccsal. Ez a frissítés alkalmazza a Key Vaultból beszúrt tanúsítványt az IIS-re, és konfigurálja a webes kötést:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
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


### <a name="test-the-secure-web-app"></a>A biztonságos webalkalmazás tesztelése
Kérje le a virtuális gép nyilvános IP-címét a [Get-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress) paranccsal. A következő példa a korábban létrehozott `myPublicIP` IP-címét kéri le:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Most nyisson meg egy webböngészőt, és írja be a `https://<myPublicIP>` címet a címsorba. Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget:

![Webböngésző biztonsági figyelmeztetésének elfogadása](./media/tutorial-secure-web-server/browser-warning.png)

Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

![Futó biztonságos IIS-hely megtekintése](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban beállította egy IIS-webkiszolgáló védelmét az Azure Key Vaultban tárolt SSL-tanúsítvánnyal. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Key Vault létrehozása;
> * tanúsítvány létrehozása vagy feltöltése a Key Vaultba;
> * Virtuális gép létrehozása és az IIS-webkiszolgáló telepítése
> * A tanúsítvány elhelyezése a virtuális gépen és az IIS konfigurálása SSL-kötéssel

Kövesse ezt a hivatkozást az előre felépített virtuálisgép-szkriptek mintáinak megtekintéséhez.

> [!div class="nextstepaction"]
> [Windows rendszerű virtuális gép szkriptjeinek mintái](./powershell-samples.md)
