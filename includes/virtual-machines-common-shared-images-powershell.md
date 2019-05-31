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
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241204"
---
## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="get-the-managed-image"></a>A felügyelt lemezkép beolvasása

Láthatja, hogy egy erőforrás csoport az elérhető rendszerképek listájának [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Ha már tudja, hogy a rendszerkép nevének és milyen erőforráscsoport, akkor használhatja `Get-AzImage` újra, hogy a kép objektum, és a későbbi használat céljából változóban tárolja. Ebben a példában beolvassa-nevű rendszerképet *myImage* a "myResourceGroup" erőforráscsoportot, és hozzárendeli azt a változót *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Lemezkép-katalógus létrehozása 

Lemezkép-katalógus, hogy az elsődleges erőforrás használt lemezkép megosztása. Katalógus neveként engedélyezett karakterek:, kis-és nagybetűket, számokat, pontokat és időszakok. A katalógus neve nem tartalmazhat kötőjeleket. Katalógus egyedieknek kell lenniük az előfizetésben. 

Hozzon létre egy rendszerképet katalógus [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). A következő példában létrehozunk egy katalógusban nevű *myGallery* a a *myGalleryRG* erőforráscsoportot.

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

Lemezkép-definíciók létrehozása lemezképek logikai jellegű csoportosítását. A lemezkép-verziók azokon belül létrehozott adatainak kezeléséhez használhatók. Definíció rendszerképnevek a kis-és nagybetűket, számokat, pontokat, kötőjeleket és időszakok lehet tenni. Az értékeket is megadhat egy rendszerkép definíciójában kapcsolatos további információkért lásd: [definíciók kép](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Létrehozhatja a lemezkép definíció [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). Ebben a példában a katalógus-lemezkép neve *myGalleryImage*.

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


## <a name="create-an-image-version"></a>Hozzon létre egy lemezkép verziója

Hozzon létre egy lemezkép verziója egy felügyelt rendszerképet az [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Lemezkép-verzió engedélyezett karakterek:, számokat és pontokat. Számok belül a 32 bites egész számnak kell lennie. Formátum: *Főverzió*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziószáma *1.0.0-s* , és mindkét replikálás *USA nyugati középső Régiója* és *USA déli középső Régiójában* adatközpontokban. Replikáció célrégiók kiválasztásakor ne feledje, hogy is kell tartalmaznia a *forrás* régió a replikációs cél.


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

> [!NOTE]
> Várjon, amíg a rendszerkép verziószámát teljesen befejeződik, beépített és a replikált felügyelt ugyanazt a lemezképet létrehozni egy másik lemezkép-verzió használata előtt kell. 
>
> A kép verzióját is tárolhatja [Zónaredundáns tárolás](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) hozzáadásával `-StorageAccountType Standard_ZRS` létrehozásakor, a rendszerkép verziószámát.
>


## <a name="share-the-gallery"></a>A katalógus megosztása

Azt javasoljuk, hogy megosztott hozzáférés a lemezkép katalógus szintjén. E-mail-címet használ, és a [Get-AzADUser](/powershell/module/az.resources/get-azaduser) parancsmagot, hogy a felhasználó objektum Azonosítójának lekéréséhez, majd a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) biztosíthat hozzáférést a katalógusban. Cserélje le a például szolgáló e-mail, alinne_montes@contoso.com ebben a példában a saját adataira.

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