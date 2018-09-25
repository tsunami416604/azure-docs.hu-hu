---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 830deb7569772b610b7e6abde649830b7ad67a92
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047519"
---
## <a name="before-you-begin"></a>Előkészületek

A példában ez a cikk a végrehajtásához egy általánosított virtuális gép egy meglévő felügyelt rendszerkép kell rendelkeznie. További információkért lásd: [oktatóanyag: Azure virtuális gép egyéni rendszerképének létrehozása az Azure CLI 2.0 használatával](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

## <a name="preview-register-the-feature"></a>Előzetes verzió: A funkció regisztrálása

Megosztott kép katalógusok előzetes verzióban érhető el, de szeretne regisztrálni a szolgáltatást ahhoz is használhatja. A megosztott kép katalógusok funkció regisztrálása:

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

Eltarthat néhány percig, a funkció regisztrálása. A folyamat használatával ellenőrizheti:

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>Lemezkép-katalógus létrehozása 

Lemezkép-katalógus, hogy az elsődleges erőforrás használt lemezkép megosztása. Katalógus egyedieknek kell lenniük az előfizetésben. Hozzon létre egy rendszerképet katalógus [az sig létrehozása](/cli/azure/sig#az-sig-create). A következő példában létrehozunk egy katalógusban nevű *myGallery* a *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Kép definíció létrehozása

Hozzon létre egy kezdeti rendszerkép definíciójában a katalógusban található [az sig lemezkép-definíció létrehozására](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>Hozzon létre egy lemezkép verziója 
 
Hozzon létre a lemezkép verzió használatával szükség szerint [az image katalógus létrehozása-lemezkép-verzió](/cli/azure/sig/image-version#az-sig-image-version-create). Szüksége lesz a felügyelt lemezkép létrehozásához a rendszerkép verziószámát összegyűjtéséhez használt Azonosítóját adja át. Használhat [az képlista](/cli/azure/image?view#az-image-list) lemezképeket, amelyek egy erőforráscsoportba tartozó információt szeretne kapni. Ebben a példában a lemezkép-verzió nem *1.0.0-s* fogjuk 5 teljes replika létrehozása és a *USA nyugati középső Régiója*, *USA déli középső Régiójában* és az USA keleti RÉGIÓJA 2 * régió.

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

