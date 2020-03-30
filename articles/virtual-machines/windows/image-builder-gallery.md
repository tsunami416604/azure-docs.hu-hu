---
title: Az Azure Image Builder használata windowsos virtuális gépek képgalériájával (előzetes verzió)
description: Hozzon létre Azure megosztott galéria képverziók at Azure Image Builder és az Azure PowerShell használatával.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263353"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Előzetes verzió: Windows-lemezkép létrehozása és terjesztése megosztott képtárban 

Ez a cikk bemutatja, hogyan használhatja az Azure Image Builder, és az Azure PowerShell, egy lemezkép-verzió létrehozása a [megosztott képtárban,](shared-image-galleries.md)majd a lemezkép globális terjesztése. Ezt az Azure CLI használatával is [megteheti.](../linux/image-builder-gallery.md)

A lemezkép konfigurálásához .json sablont fogunk használni. Az általunk használt .json fájl itt van: [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). A sablon helyi verzióját fogjuk letölteni és szerkeszteni, így ez a cikk a helyi PowerShell-munkamenet használatával íródott.

A kép megosztott képtárba való terjesztéséhez a sablon a `distribute` [sharedImage-et](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) használja a sablon szakaszának értékeként.

Az Azure Image Builder automatikusan futtatja a sysprep-et a lemezkép általánosításához, ez egy általános sysprep parancs, amelyet szükség esetén [felülbírálhat.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) 

Ne feledje, hogy hányszor rétegezi a testreszabásokat. A Sysprep parancs akár 8 alkalommal is futtatható egyetlen Windows-lemezképen. A Sysprep 8-szor imitált futtatása után újra létre kell hoznia a Windows-lemezképet. További információt a [Sysprep futtatásának korlátozása](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)című témakörben talál. 

> [!IMPORTANT]
> Az Azure Image Builder jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="register-the-features"></a>A funkciók regisztrálása
Az Azure Image Builder az előzetes verzióban való használatához regisztrálnia kell az új funkciót.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Ellenőrizze a szolgáltatás regisztrációjának állapotát.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Várjon, amíg `RegistrationState` van, `Registered` mielőtt a következő lépésre.

Ellenőrizze a szolgáltató regisztrációit. Győződjön meg `Registered`róla, hogy minden visszatér .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Ha nem térnek vissza, `Registered`a szolgáltatók regisztrálásához használja az alábbiakat:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Változók létrehozása

Mi lesz használ néhány információt többször, így hozunk létre néhány változótárolják ezt az információt. Cserélje le a változók `username` értékeit, például és `vmpassword`a t.

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

Hozzon létre egy erőforráscsoportot, és adjon engedélyt az Azure Image Builder-nek az adott erőforráscsoportban lévő erőforrások létrehozásához.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>A megosztott képtár létrehozása

Ha megosztott képgalériával szeretné használni a Képszerkesztőt, rendelkeznie kell egy meglévő képgalériával és képdefinícióval. A Képszerkesztő nem hozza létre a képgalériát és a képdefiníciót.

Ha még nem rendelkezik katalógus- és képdefinícióval, először hozza létre őket. Először hozzon létre egy képgalériát.

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

Töltse le a .json sablont, és konfigurálja a változókkal.

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


## <a name="create-the-image-version"></a>A lemezkép verziójának létrehozása

A sablont el kell küldeni a szolgáltatásnak, ez letölti a függő összetevőket, például a parancsfájlokat, és tárolja őket az *IT_* előtaggal ellátott átmeneti erőforráscsoportban.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

A lemezkép létrehozásához meg kell hívnia a "Futtatás" metódust a sablonon.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

A lemezkép létrehozása és replikálása mindkét régióban eltarthat egy ideig. Várjon, amíg ez a rész befejeződik, mielőtt a virtuális gép létrehozása.

A lemezkép-összeállítási állapot automatizálási lehetőségeiről a GitHubon található Sablon [hoz készült képhez című témakörben](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) olvashat.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép a lemezkép-verzió, amely az Azure Image Builder által létrehozott.

A létrehozott lemezkép-verzió beszerezni.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Hozza létre a virtuális gép a második régióban, amely a rendszerkép replikált.

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

## <a name="verify-the-customization"></a>A testreszabás ellenőrzése
Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel a virtuális gép létrehozásakor megadott felhasználónév és jelszó használatával. A virtuális gépbelsejében nyisson meg egy cmd-s kérdést, és írja be a következőt:

```console
dir c:\
```

A lemezkép testreszabása során létrehozott könyvtárnak `buildActions` meg kell jelennie.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha most újra szeretné testreszabni a lemezkép-verziót ugyanannak a lemezképnek az új verziójának létrehozásához, hagyja ki ezt a **lépést,** és folytassa az [Azure Image Builder használatával egy másik lemezkép-verzió létrehozásához.](image-builder-gallery-update-image-version.md)


Ezzel törli a létrehozott lemezképet az összes többi erőforrásfájllal együtt. Győződjön meg arról, hogy befejezte ezt a központi telepítést az erőforrások törlése előtt.

Először törölje az erőforráscsoport sablont, különben az AIB által használt átmeneti erőforráscsoport (*IT_*) nem lesz törölve.

A lemezképsablon ResourceID azonosítójának beszereznie. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Képsablon törlése.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

törölje az erőforráscsoportot.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

A létrehozott lemezkép-verzió frissítésének módjáról az [Azure Image Builder használata egy másik lemezkép-verzió létrehozásához című](image-builder-gallery-update-image-version.md)témakörben olvashat.
