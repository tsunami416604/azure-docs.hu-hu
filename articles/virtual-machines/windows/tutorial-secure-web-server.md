---
title: 'Oktatóanyag: Windows-webkiszolgáló biztonságossá tétele SSL-tanúsítványokkal az Azure-ban'
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használhatja az Azure PowerShellt olyan Windows rendszerű virtuális gép védelmére, amely az IIS webkiszolgálót az Azure Key Vaultban tárolt SSL-tanúsítványokkal futtatja.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6185ad4f0e043329c4e833b97a09922ba0238a82
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264237"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>Oktatóanyag: Webkiszolgáló védelme a Key Vaultban tárolt SSL-tanúsítványokkal Windows rendszerű virtuális gépen az Azure-ban

> [!NOTE]
> Jelenleg ez a doc csak általánosított lemezképek esetében működik. Ha ezt az oktatóanyagot egy speciális lemezzel próbálja meg használni, hibaüzenetet fog kapni. 

A webkiszolgálók védelméhez egy Secure Sockets Layer- (SSL-) tanúsítvánnyal titkosítható a webes adatforgalom. Ezek az SSL-tanúsítványok az Azure Key Vaultban tárolhatók, és lehetővé teszik a tanúsítványok biztonságos üzembe helyezését a Windows rendszerű virtuális gépeken az Azure-ban. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Key Vault létrehozása;
> * tanúsítvány létrehozása vagy feltöltése a Key Vaultba;
> * Virtuális gép létrehozása és az IIS-webkiszolgáló telepítése
> * A tanúsítvány elhelyezése a virtuális gépen és az IIS konfigurálása SSL-kötéssel


## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="overview"></a>Áttekintés
Az Azure Key Vault megvédi a titkosítási kulcsokat és titkos kódokat, például a tanúsítványokat és jelszavakat. A Key Vault leegyszerűsíti a tanúsítványkezelési folyamatot, valamint lehetővé teszi a tanúsítványokhoz hozzáférő kulcsok feletti teljes körű felügyeletet. Létrehozhat egy önaláírt tanúsítványt a Key Vaultban, vagy feltölthet egy meglévő, megbízható tanúsítványt.

Beépített tanúsítványokat tartalmazó egyéni virtuális gép rendszerkép használata helyett a futó virtuális gépeken helyezi el a tanúsítványokat. Ez a folyamat biztosítja, hogy a legnaprakészebb tanúsítványok legyenek telepítve a webkiszolgálókon az üzembe helyezés alatt. Ha frissíti vagy lecseréli a tanúsítványt, nem kell új rendszerképet is létrehoznia a virtuális gépekről. A további virtuális gépek létrehozásakor a rendszer automatikusan alkalmazza a legújabb tanúsítványokat. A tanúsítványok a folyamat során egyszer sem hagyják el az Azure platformot, és nem jelennek meg szkriptekben, a parancssori előzményekben vagy sablonokban.


## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;
Key Vault és tanúsítványok létrehozása előtt hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)használatával. A következő példában létrehozunk egy *myResourceGroupSecureWeb* nevű erőforráscsoportot az *USA keleti régiója* helyen:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Ezután hozzon létre egy Key Vaultt a [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault). Mindegyik Key Vaultnak egyedi névvel kell rendelkeznie, amely csak kisbetűkből állhat. Cserélje le a `mykeyvault` nevet a következő példában a saját egyedi Key Vault-névre:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Tanúsítvány létrehozása és tárolása a Key Vaultban
Éles használatra a megbízható szolgáltató által aláírt érvényes tanúsítványt kell importálnia az [import-AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/import-azkeyvaultcertificate)használatával. Ebben az oktatóanyagban a következő példa bemutatja, hogyan hozhat létre olyan önaláírt tanúsítványt az [Add-AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultcertificate) , amely a [New-AzKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy)alapértelmezett tanúsítvány-házirendjét használja. 

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

Most már létrehozhatja a virtuális gépet a [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)használatával. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre az *USA keleti régiója* helyen. Ha még nem léteznek, létrejönnek a támogató hálózati erőforrások. A biztonságos webes forgalom engedélyezéséhez a parancsmag megnyitja a *443*-as portot is.

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

A virtuális gép létrehozása néhány percig tart. Az utolsó lépés az Azure Custom script bővítmény használatával telepíti az IIS-webkiszolgálót a [set-AzVmExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Tanúsítvány hozzáadása egy virtuális géphez a Key Vaultból
A tanúsítvány Key Vaultból virtuális géphez való hozzáadásához szerezze be a tanúsítvány AZONOSÍTÓját a [Get-AzKeyVaultSecret](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvaultsecret)használatával. Adja hozzá a tanúsítványt a virtuális géphez az [Add-AzVMSecret](https://docs.microsoft.com/powershell/module/az.compute/add-azvmsecret)használatával:

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Az IIS konfigurálása a tanúsítvány használatára
Az egyéni szkriptek bővítményét újra használhatja a [set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) az IIS konfigurációjának frissítéséhez. Ez a frissítés alkalmazza a Key Vaultból beszúrt tanúsítványt az IIS-re, és konfigurálja a webes kötést:

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
Szerezze be a virtuális gép nyilvános IP-címét a [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). A következő példa a korábban létrehozott `myPublicIP` IP-címét kéri le:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Most nyisson meg egy webböngészőt, és írja be a `https://<myPublicIP>` címet a címsorba. Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget:

![Webböngésző biztonsági figyelmeztetésének elfogadása](./media/tutorial-secure-web-server/browser-warning.png)

Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

![Futó biztonságos IIS-hely megtekintése](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban beállította egy IIS-webkiszolgáló védelmét az Azure Key Vaultban tárolt SSL-tanúsítvánnyal. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Key Vault létrehozása;
> * tanúsítvány létrehozása vagy feltöltése a Key Vaultba;
> * Virtuális gép létrehozása és az IIS-webkiszolgáló telepítése
> * A tanúsítvány elhelyezése a virtuális gépen és az IIS konfigurálása SSL-kötéssel

Kövesse ezt a hivatkozást az előre felépített virtuálisgép-szkriptek mintáinak megtekintéséhez.

> [!div class="nextstepaction"]
> [Windows rendszerű virtuális gép szkriptjeinek mintái](./powershell-samples.md)
