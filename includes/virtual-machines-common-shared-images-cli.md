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
ms.openlocfilehash: 36c4757feb367fd39ae94640cb8e8a0f1714a0d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737115"
---
## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="before-you-begin"></a>Előkészületek

A példában ez a cikk a végrehajtásához egy általánosított virtuális gép egy meglévő felügyelt rendszerkép kell rendelkeznie. További információkért lásd: [oktatóanyag: Hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek az Azure CLI 2.0 használatával](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

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
 
Hozzon létre a lemezkép verzió használatával szükség szerint [az image katalógus létrehozása-lemezkép-verzió](/cli/azure/sig/image-version#az-sig-image-version-create). Szüksége lesz a felügyelt lemezkép létrehozásához a rendszerkép verziószámát összegyűjtéséhez használt Azonosítóját adja át. Használhat [az képlista](/cli/azure/image?view#az-image-list) lemezképeket, amelyek egy erőforráscsoportba tartozó információt szeretne kapni. Ebben a példában a lemezkép-verzió nem *1.0.0-s* fogjuk 5 replikák létrehozása, és a *USA nyugati középső Régiója* régió, 1, a replika a *USA déli középső Régiójában* régió és 1 a replika a *USA keleti RÉGIÓJA 2* régióban.

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

