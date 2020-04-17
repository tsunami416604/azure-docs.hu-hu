---
title: 'Oktatóanyag: TLS-/SSL-tanúsítványokkal rendelkező Windows-webkiszolgáló biztonságossá tétele az Azure-ban'
description: Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure PowerShellt egy Olyan Windows virtuális gép biztonságossá, amely az IIS-webkiszolgálót az Azure Key Vaultban tárolt TLS/SSL-tanúsítványokkal futtatja.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2795d45cd5bba7aab33b06350faee23e83189c30
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455580"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Oktatóanyag: Webkiszolgáló biztonságossá tétele az Azure-ban lévő Windows virtuális gépen a Key Vaultban tárolt TLS/SSL-tanúsítványokkal

> [!NOTE]
> Jelenleg ez a dokumentum csak akkor működik, általános képek. Ha az oktatóanyag ot Specialized lemezzel próbálja meg használni, hibaüzenetet fog kapni. 

A webkiszolgálók, a Transport Layer Security (TLS), korábbi nevén Secure Sockets Layer (SSL) biztonságos sáfására vonatkozó tanúsítvány használható a webes forgalom titkosítására. Ezek a TLS/SSL-tanúsítványok tárolhatók az Azure Key Vaultban, és lehetővé teszik a tanúsítványok biztonságos üzembe helyezését az Azure-beli Windows virtuális gépek (VM-ek) számára. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Key Vault létrehozása;
> * tanúsítvány létrehozása vagy feltöltése a Key Vaultba;
> * Virtuális gép létrehozása és az IIS-webkiszolgáló telepítése
> * Adja be a tanúsítványt a virtuális gépbe, és konfigurálja az IIS-t TLS-kötéssel


## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/powershell)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="overview"></a>Áttekintés
Az Azure Key Vault megvédi a titkosítási kulcsokat és titkos kódokat, például a tanúsítványokat és jelszavakat. A Key Vault leegyszerűsíti a tanúsítványkezelési folyamatot, valamint lehetővé teszi a tanúsítványokhoz hozzáférő kulcsok feletti teljes körű felügyeletet. Létrehozhat egy önaláírt tanúsítványt a Key Vaultban, vagy feltölthet egy meglévő, megbízható tanúsítványt.

Beépített tanúsítványokat tartalmazó egyéni virtuális gép rendszerkép használata helyett a futó virtuális gépeken helyezi el a tanúsítványokat. Ez a folyamat biztosítja, hogy a legnaprakészebb tanúsítványok legyenek telepítve a webkiszolgálókon az üzembe helyezés alatt. Ha frissíti vagy lecseréli a tanúsítványt, nem kell új rendszerképet is létrehoznia a virtuális gépekről. A további virtuális gépek létrehozásakor a rendszer automatikusan alkalmazza a legújabb tanúsítványokat. A tanúsítványok a folyamat során egyszer sem hagyják el az Azure platformot, és nem jelennek meg szkriptekben, a parancssori előzményekben vagy sablonokban.


## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;
Mielőtt létrehozhatna egy Key Vaultot és tanúsítványokat, hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)segítségével. A következő példában létrehozunk egy *myResourceGroupSecureWeb* nevű erőforráscsoportot az *USA keleti régiója* helyen:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Ezután hozzon létre egy Key Vault ot a [New-AzKeyVault segítségével.](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) Mindegyik Key Vaultnak egyedi névvel kell rendelkeznie, amely csak kisbetűkből állhat. Cserélje le a `mykeyvault` nevet a következő példában a saját egyedi Key Vault-névre:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Tanúsítvány létrehozása és tárolása a Key Vaultban
Éles használatra importálnia kell egy érvényes tanúsítványt, amelyet megbízható szolgáltató írt alá [az Import-AzKeyVaultCertificate paranccsal.](https://docs.microsoft.com/powershell/module/az.keyvault/import-azkeyvaultcertificate) Ebben az oktatóanyagban a következő példa bemutatja, hogyan hozhat létre önaláírt tanúsítványt [az Add-AzKeyVaultCertificate tanúsítványsal,](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultcertificate) amely a [New-AzKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy)alapértelmezett tanúsítványházirendjét használja. 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gép [a New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre az *USA keleti régiója* helyen. Ha még nem léteznek, létrejönnek a támogató hálózati erőforrások. A biztonságos webes forgalom engedélyezéséhez a parancsmag megnyitja a *443*-as portot is.

```azurepowershell-interactive
# Create a VM
New-AzVm `
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
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

A virtuális gép létrehozása néhány percig tart. Az utolsó lépés az Azure Custom Script Extension használatával telepíti az IIS webkiszolgálót a [Set-AzVmExtension segítségével.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension)


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Tanúsítvány hozzáadása egy virtuális géphez a Key Vaultból
Ha hozzá szeretné adni a tanúsítványt a Key Vaultból egy virtuális géphez, szerezze be a tanúsítvány azonosítóját a [Get-AzKeyVaultSecret](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvaultsecret)segítségével. Adja hozzá a tanúsítványt az [Add-AzVMSecret](https://docs.microsoft.com/powershell/module/az.compute/add-azvmsecret)szolgáltatással rendelkező virtuális géphez:

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Az IIS konfigurálása a tanúsítvány használatára
Az IIS-konfiguráció frissítéséhez használja ismét az egyéni parancsfájl-bővítményt a [Set-AzVMExtension alkalmazással.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) Ez a frissítés alkalmazza a Key Vaultból beszúrt tanúsítványt az IIS-re, és konfigurálja a webes kötést:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>A biztonságos webalkalmazás tesztelése
Szerezze be a virtuális gép nyilvános IP-címét a [Get-AzPublicIPAddress segítségével.](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) A következő példa a korábban létrehozott `myPublicIP` IP-címét kéri le:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Most nyisson meg egy webböngészőt, és írja be a `https://<myPublicIP>` címet a címsorba. Ha önaláírt tanúsítványt használ, a biztonsági figyelmeztetést a Részletek lehetőséget **választja,** majd **lépjen a weblapra**:

![Webböngésző biztonsági figyelmeztetésének elfogadása](./media/tutorial-secure-web-server/browser-warning.png)

Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

![Futó biztonságos IIS-hely megtekintése](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban egy IIS-webkiszolgálót biztosított az Azure Key Vaultban tárolt TLS/SSL-tanúsítvánnyal. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Key Vault létrehozása;
> * tanúsítvány létrehozása vagy feltöltése a Key Vaultba;
> * Virtuális gép létrehozása és az IIS-webkiszolgáló telepítése
> * Adja be a tanúsítványt a virtuális gépbe, és konfigurálja az IIS-t TLS-kötéssel

Kövesse ezt a hivatkozást az előre felépített virtuálisgép-szkriptek mintáinak megtekintéséhez.

> [!div class="nextstepaction"]
> [Windows rendszerű virtuális gép szkriptjeinek mintái](./powershell-samples.md)
