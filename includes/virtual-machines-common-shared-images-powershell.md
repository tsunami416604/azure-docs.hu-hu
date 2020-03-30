---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241204"
---
## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/powershell)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="get-the-managed-image"></a>A felügyelt lemezkép beszereznie

A [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage)segítségével megtekintheti az erőforráscsoportban elérhető képek listáját. Miután tudja a lemezkép nevét, és milyen `Get-AzImage` erőforráscsoportban van, újra lekaphatja a képobjektumot, és tárolhatja azt egy változóban, amelyet később használhat. Ez a példa egy *myImage* nevű képet kap a "myResourceGroup" erőforráscsoportból, és hozzárendeli azt a *változóhoz $managedImage.* 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Képgaléria létrehozása 

A képgaléria a képmegosztás engedélyezéséhez használt elsődleges erőforrás. A Galéria nevének megengedett karakterei a nagy- vagy kisbetűk, számjegyek, pontszámok és pont. A gyűjtemény neve nem tartalmazhat kötőjeleket. A galérianeveknek egyedinek kell lenniük az előfizetésen belül. 

Hozzon létre egy képgalériát a [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery)segítségével. A következő példa létrehoz egy *myGallery* nevű galériát a *myGalleryRG* erőforráscsoportban.

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
   
## <a name="create-an-image-definition"></a>Képdefiníció létrehozása 

A képdefiníciók logikai csoportosítást hoznak létre a képekhez. A bennük létrehozott lemezkép-verziók adatainak kezelésére szolgálnak. A képdefiníciós nevek nagy- vagy kisbetűkből, számjegyekből, pontokból, kötőjelekből és pontokból állhatnak. A képdefinícióhoz megadható értékekről a [Képdefiníciók](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)című témakörben talál további információt.

Hozza létre a képdefiníciót a [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)segítségével. Ebben a példában a galéria kép neve *myGalleryImage*.

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


## <a name="create-an-image-version"></a>Képverzió létrehozása

Lemezkép-verzió létrehozása felügyelt lemezképből a [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)segítségével. 

A képverzió megengedett karakterei számok és időszakok. A számoknak a 32 bites egész szám tartományán belül kell lenniük. Formátum: *MajorVersion*. *MinorVersion*. *Patch*.

Ebben a példában a lemezkép verziója *1.0.0,* és replikálja mind *az USA nyugati középső régiója* és az USA déli középső *régiói* adatközpontok. A replikáció célterületeinek kiválasztásakor ne feledje, hogy a *forrásrégiót* is meg kell adnia a replikáció célként.


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

Eltarthat egy ideig, amíg a rendszerképet az összes célrégióra replikáljuk, ezért létrehoztunk egy feladatot, hogy nyomon követhessük a folyamatot. A feladat előrehaladásának megtekintéséhez `$job.State`írja be a következőt:

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Meg kell várnia, amíg a lemezkép-verzió teljesen befejeződik a létrehozás és a replikálás, mielőtt ugyanazt a felügyelt lemezképet használhatna egy másik lemezkép-verzió létrehozásához. 
>
> A lemezkép-verziót a [Zónaredundáns tárolásban](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) is tárolhatja, ha a lemezkép-verzió létrehozásakor hozzáadja. `-StorageAccountType Standard_ZRS`
>


## <a name="share-the-gallery"></a>A galéria megosztása

Azt javasoljuk, hogy ossza meg a hozzáférést a képgaléria szintjén. Használjon egy e-mail címet és a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) parancsmag a felhasználó objektumazonosítójának lekérni, majd a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) segítségével hozzáférést biztosít a katalógushoz. Cserélje le a alinne_montes@contoso.com példa e-mailt ebben a példában, a saját adatait.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```