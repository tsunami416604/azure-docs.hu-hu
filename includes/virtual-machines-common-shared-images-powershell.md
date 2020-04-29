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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "66241204"
---
## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell). A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="get-the-managed-image"></a>A felügyelt rendszerkép beolvasása

A [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage)használatával megtekintheti az erőforráscsoporthoz elérhető rendszerképek listáját. Ha ismeri a rendszerkép nevét és a benne található erőforráscsoportot, akkor ismét használhatja `Get-AzImage` a rendszerkép-objektum beolvasásához és egy változóban való tárolásához. Ez a példa egy *myImage* nevű rendszerképet kap a "myResourceGroup" erőforráscsoporthoz, és hozzárendeli azt a (z) *$managedImage*változóhoz. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Rendszerkép-gyűjtemény létrehozása 

A képgyűjtemény a képmegosztás engedélyezéséhez használt elsődleges erőforrás. A katalógus nevének megengedett karaktere nagybetűs vagy kisbetűk, számjegyek, pontok és időszakok. A gyűjtemény neve nem tartalmazhat kötőjeleket. A katalógus nevének egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy képtárat a [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery)használatával. A következő példában létrehozunk egy *MyGallery* nevű katalógust a *myGalleryRG* -erőforráscsoporthoz.

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
   
## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása 

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. Ezek az adatok a bennük létrehozott rendszerkép-verziókra vonatkozó információk kezelésére szolgálnak. A képdefiníciók nevei nagybetűket, kisbetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. További információ a képdefiníciók által megadható értékekről: [képdefiníciók](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Hozza létre a rendszerkép definícióját a [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)használatával. Ebben a példában a katalógus rendszerképének neve *myGalleryImage*.

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


## <a name="create-an-image-version"></a>Rendszerkép-verzió létrehozása

Hozzon létre egy rendszerkép-verziót egy felügyelt rendszerképből a [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)használatával. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és a rendszer replikálja az *USA nyugati középső* régiójában és az *USA déli középső* régiójában. A célcsoportok replikáláshoz való kiválasztásakor ne feledje, hogy a *forrás* régiót is meg kell adnia a replikálás céljának.


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

Eltarthat egy ideig, amíg replikálja a rendszerképet az összes megcélzott régióba, így létrehoztunk egy feladatot, hogy nyomon kövessük a folyamat előrehaladását. A feladatok előrehaladásának megtekintéséhez írja be a következőt `$job.State`:.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához. 
>
> A rendszerkép verzióját a [zóna redundáns tárolójában](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) `-StorageAccountType Standard_ZRS` is tárolhatja a rendszerkép verziójának létrehozásakor.
>


## <a name="share-the-gallery"></a>A katalógus megosztása

Javasoljuk, hogy a Képtár szintjén ossza meg a hozzáférést. Használjon e-mail-címet és a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) parancsmagot a felhasználó objektumazonosító beszerzéséhez, majd a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával adja meg nekik a katalógushoz való hozzáférést. Cserélje le a példában szereplő alinne_montes@contoso.com e-mailt a saját adataira.

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