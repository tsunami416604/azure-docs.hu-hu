---
title: Az Azure rendszerkép-készítő használata Windows rendszerű virtuális gépekhez készült rendszerkép-katalógussal (előzetes verzió)
description: Hozzon létre Azure Shared Gallery-rendszerképeket az Azure rendszerkép-készítő és a Azure PowerShell használatával.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263353"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Előzetes verzió: Windows-rendszerkép létrehozása és terjesztése megosztott képgyűjteménybe 

Ebből a cikkből megtudhatja, hogyan használhatja az Azure-rendszerkép-szerkesztőt, Azure PowerShell és hogyan hozhat létre egy rendszerkép-verziót egy [megosztott rendszerkép](shared-image-galleries.md)-katalógusban, majd globálisan terjesztheti a rendszerképet. Ezt az [Azure CLI](../linux/image-builder-gallery.md)használatával is elvégezheti.

A rendszerkép konfigurálásához egy. JSON sablont fogunk használni. Az általunk használt. JSON fájl a következő: [armTemplateWinSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). A sablon helyi verziójának letöltése és szerkesztése folyamatban van, ezért ez a cikk a helyi PowerShell-munkamenet használatával íródik.

A rendszerkép megosztott képgyűjteménybe való terjesztéséhez a sablon a [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) használja a sablon `distribute` szakaszának értékeként.

Az Azure rendszerkép-készítő automatikusan futtatja a Sysprep programot a rendszerkép általánosítása érdekében, ez egy általános Sysprep-parancs, amelyet szükség esetén [felül lehet bírálni](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) . 

Vegye figyelembe, hogy a réteg testreszabása hányszor történik. A Sysprep parancsot akár 8 alkalommal is futtathatja egyetlen Windows-rendszerképben. A Sysprep 8 alkalommal történő futtatása után újra létre kell hoznia a Windows-rendszerképet. További információ: [korlátozások a Sysprep futtatásának](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)hányszor. 

> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>A szolgáltatások regisztrálása
Ha az előzetes verzióban szeretné használni az Azure képszerkesztőt, regisztrálnia kell az új szolgáltatást.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

A szolgáltatás regisztrációjának állapotát vizsgálja meg.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Várjon, amíg a `RegistrationState` `Registered` a következő lépésre való áttérés előtt.

Keresse meg a szolgáltató regisztrációját. Győződjön meg arról, hogy minden `Registered`visszaadja.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Ha `Registered`nem adnak vissza, a következő paranccsal regisztrálhatók a szolgáltatók:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Változók létrehozása

Többször is fogjuk használni az adatokat, így az adatok tárolására néhány változót fogunk létrehozni. Cserélje le a változók értékeit, például a `username` és a `vmpassword`t a saját adataival.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, és adja meg az Azure rendszerkép-szerkesztőnek, hogy erőforrásokat hozzon létre az adott erőforráscsoporthoz.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>A megosztott Képtár létrehozása

Ha közös rendszerkép-katalógussal szeretné használni a képszerkesztőt, rendelkeznie kell egy meglévő képtárat és képdefiníciót tartalmazó képpel. A rendszerkép-szerkesztő nem hozza létre az Ön számára a rendszerkép-gyűjteményt és a rendszerkép definícióját.

Ha még nem rendelkezik a használni kívánt gyűjtemény-és képdefinícióval, először hozza létre őket. Először hozzon létre egy képtárat.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>A sablon letöltése és konfigurálása

Töltse le a. JSON sablont, és konfigurálja a változókkal.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

A sablont el kell küldenie a szolgáltatásnak, ezzel letölti az összes függő összetevőt, például a parancsfájlokat, és tárolja azokat az átmeneti erőforráscsoporthoz, a *IT_* előtaggal.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

A "Run" meghívásához szükséges rendszerkép létrehozásához a sablonon.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

A rendszerkép létrehozása és replikálása mindkét régióban eltarthat egy ideig. Várjon, amíg ez a rész be nem fejeződik a virtuális gép létrehozásához való továbblépés előtt.

A rendszerkép-Build állapotának automatizálására vonatkozó beállításokkal kapcsolatos további információkért tekintse meg a sablonhoz tartozó [Readme fájlt](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) a githubon.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az Azure-rendszerkép-szerkesztő által létrehozott rendszerkép-verzióból.

Szerezze be a létrehozott rendszerkép-verziót.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Hozza létre a virtuális gépet a második régióban, melyet a rendszerkép replikált.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>A Testreszabás ellenőrzése
Hozzon létre egy Távoli asztal-csatlakozást a virtuális géphez a virtuális gép létrehozásakor beállított Felhasználónév és jelszó használatával. A virtuális gépen nyisson meg egy parancssort, és írja be a következőt:

```console
dir c:\
```

A rendszerkép testreszabása során létrehozott `buildActions` nevű könyvtárat kell megjelennie.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha most újra testre szeretné szabni a rendszerkép verzióját, hogy ugyanazon rendszerkép új verzióját hozza létre, **hagyja ki ezt a lépést** , és folytassa az [Azure rendszerkép-készítő használatával egy másik rendszerkép-verzió létrehozásához](image-builder-gallery-update-image-version.md).


Ezzel törli a létrehozott rendszerképet, valamint az összes többi erőforrás-fájlt is. Az erőforrások törlése előtt ellenőrizze, hogy befejeződött-e az üzemelő példány.

Először törölje az erőforráscsoport-sablont, ellenkező esetben a AIB által használt átmeneti erőforráscsoport (*IT_* ) nem lesz törölve.

A Képsablon ResourceID beolvasása. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Sablon törlése.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Törölje az erőforráscsoportot.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan frissítheti a létrehozott rendszerkép-verziót, tekintse meg az [Azure rendszerkép-készítő használata egy másik rendszerkép-verzió létrehozásához](image-builder-gallery-update-image-version.md)című témakört.
