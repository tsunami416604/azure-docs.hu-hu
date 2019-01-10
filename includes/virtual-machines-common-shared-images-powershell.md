---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3ec5b9c6357f0d075ddd9b0fd5c8a88ee2846209
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192217"
---
## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="preview-register-the-feature"></a>Előzetes verzió: A funkció regisztrálása

Megosztott kép katalógusok előzetes verzióban érhető el, de szeretne regisztrálni a szolgáltatást ahhoz is használhatja. A megosztott kép katalógusok funkció regisztrálása:

```azurepowershell-interactive
Register-AzureRmProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>A felügyelt lemezkép beolvasása

Láthatja, hogy egy erőforrás csoport az elérhető rendszerképek listájának [Get-AzureRmImage](/powershell/module/AzureRM.Compute/get-azurermimage). Ha már tudja, hogy a rendszerkép nevének és milyen erőforráscsoport, akkor használhatja `Get-AzureRmImage` újra, hogy a kép objektum, és a későbbi használat céljából változóban tárolja. Ebben a példában beolvassa-nevű rendszerképet *myImage* a "myResourceGroup" erőforráscsoportot, és hozzárendeli azt a változót *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzureRmImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Lemezkép-katalógus létrehozása 

Lemezkép-katalógus, hogy az elsődleges erőforrás használt lemezkép megosztása. Katalógus egyedieknek kell lenniük az előfizetésben. Hozzon létre egy rendszerképet katalógus [New-AzureRmGallery](/powershell/module/AzureRM.Compute/new-azurermgallery). A következő példában létrehozunk egy katalógusban nevű *myGallery* a a *myGalleryRG* erőforráscsoportot.

```azurepowershell-interactive
$resourceGroup = New-AzureRMResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzureRmGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Kép definíció létrehozása 

Létrehozhatja a katalógus kép definíció [New-AzureRmGalleryImageDefinition](/powershell/module/azurerm.compute/new-azurermgalleryimageversion). Ebben a példában a katalógus-lemezkép neve *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzureRmGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

Egy soron következő kiadásban is elérheti a személyes meghatározott használandó **-közzétevő**, **-ajánlat** és **- termékváltozat** értékek keresése, és adjon meg egy lemezkép-definíciót, majd a virtuális gép létrehozása a megfelelő rendszerkép-definíció legfrissebb rendszerképverzió használatával. Ha például az alábbiakban három rendszerkép-definíciók és azok értékeit:

|Rendszerkép-definíció|Közzétevő|Ajánlat|SKU|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|Tesztelés|standardOffer|testSku|

Az alábbi három egyedi értékek amelyekre. Egy későbbi kiadásban lesz úgy, hogy ezek az értékek annak érdekében, hogy a kérelem egy adott rendszerkép legújabb verzióját. 

```powershell
# The following should set the source image as myImage1 from the table above
$vmConfig = Set-AzureRmVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

Ez hasonlít hogyan jelenleg megadhatja ezeket [Azure Marketplace-rendszerképek](../articles/virtual-machines/windows/cli-ps-findimage.md) hozhat létre virtuális Gépet. Ezt szem minden rendszerkép definíciójában rendelkeznie kell egy egyedi készletét ezeket az értékeket. Lemezkép verziója, amelyek egy vagy két, de nem minden három érték lehet. 

##<a name="create-an-image-version"></a>Hozzon létre egy lemezkép verziója

Hozzon létre egy lemezkép verziója egy felügyelt rendszerképet az [New-AzureRmGalleryImageVersion](/powershell/module/AzureRM.Compute/new-azurermgalleryimageversion) . Ebben a példában a rendszerkép verziószáma *1.0.0-s* , és mindkét replikálás *USA nyugati középső Régiója* és *USA déli középső Régiójában* adatközpontokban.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzureRmGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Eltarthat egy ideig a kép replikálása összes a célrégiók, ezért létrehoztunk egy feladatot, hogy nyomon követheti a folyamat állapotát. A feladat előrehaladásának megtekintéséhez írja be a `$job.State`.

```azurepowershell-interactive
$job.State
```

