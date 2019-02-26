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
ms.openlocfilehash: c74a96e3dcce1394e0af5447c07ad38c54b960fa
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825546"
---
## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="preview-register-the-feature"></a>Előzetes verzió: A funkció regisztrálása

Megosztott kép katalógusok előzetes verzióban érhető el, de szeretne regisztrálni a szolgáltatást ahhoz is használhatja. A megosztott kép katalógusok funkció regisztrálása:

```azurepowershell-interactive
Register-AzProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>A felügyelt lemezkép beolvasása

Láthatja, hogy egy erőforrás csoport az elérhető rendszerképek listájának [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Ha már tudja, hogy a rendszerkép nevének és milyen erőforráscsoport, akkor használhatja `Get-AzImage` újra, hogy a kép objektum, és a későbbi használat céljából változóban tárolja. Ebben a példában beolvassa-nevű rendszerképet *myImage* a "myResourceGroup" erőforráscsoportot, és hozzárendeli azt a változót *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Lemezkép-katalógus létrehozása 

Lemezkép-katalógus, hogy az elsődleges erőforrás használt lemezkép megosztása. Katalógus egyedieknek kell lenniük az előfizetésben. Hozzon létre egy rendszerképet katalógus [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). A következő példában létrehozunk egy katalógusban nevű *myGallery* a a *myGalleryRG* erőforráscsoportot.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Kép definíció létrehozása 

Létrehozhatja a katalógus kép definíció [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). Ebben a példában a katalógus-lemezkép neve *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
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
### <a name="using-publisher-offer-and-sku"></a>Közzétevő, ajánlat és a Termékváltozat használatával 
Az ügyfelek végrehajtási megosztott lemezképek tervezése **egy soron következő kiadásban**, fogja tudni használni a személyes meghatározott **-közzétevő**, **-ajánlat** és **- Termékváltozat** értékek, keresse meg és adja meg a rendszerkép definíciójában, majd a rendszer a legfrissebb rendszerképverzió virtuális gép létrehozása lemezkép definíciója. Ha például az alábbiakban három rendszerkép-definíciók és azok értékeit:

|Rendszerkép-definíció|Közzétevő|Ajánlat|SKU|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|Tesztelés|standardOffer|testSku|

Az alábbi három egyedi értékek amelyekre. Lemezkép verziója, amelyek egy vagy két, de nem minden három érték lehet. **Egy soron következő kiadásban**, lesz úgy, hogy ezek az értékek annak érdekében, hogy a kérelem egy adott rendszerkép legújabb verzióját. **Ez nem működik a jelenlegi kiadásban**, de a jövőben lesz elérhető. Kiadásakor, a következő szintaxis használatával használandó be, mint a forrás lemezkép *myImage1* a fenti táblázatból.

```powershell
$vmConfig = Set-AzVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

Ez hasonlít hogyan jelenleg megadhat használható közzétevő, ajánlat és a Termékváltozat [Azure Marketplace-rendszerképek](../articles/virtual-machines/windows/cli-ps-findimage.md) Piactéri lemezképet legújabb verziójának beszerzéséhez. Ezt szem minden rendszerkép definíciójában rendelkeznie kell egy egyedi készletét ezeket az értékeket.  

##<a name="create-an-image-version"></a>Hozzon létre egy lemezkép verziója

Hozzon létre egy lemezkép verziója egy felügyelt rendszerképet az [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) . Ebben a példában a rendszerkép verziószáma *1.0.0-s* , és mindkét replikálás *USA nyugati középső Régiója* és *USA déli középső Régiójában* adatközpontokban.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
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

