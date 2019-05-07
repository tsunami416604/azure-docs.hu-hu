---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/30/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 7e4ca54d8f97646192d19d5923bee24a906e8df7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149709"
---
## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="before-you-begin"></a>Előzetes teendők

A példában ez a cikk a végrehajtásához egy általánosított virtuális gép egy meglévő felügyelt rendszerkép kell rendelkeznie. További információkért lásd: [oktatóanyag: Hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek az Azure CLI 2.0 használatával](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 


## <a name="create-an-image-gallery"></a>Lemezkép-katalógus létrehozása 

Lemezkép-katalógus, hogy az elsődleges erőforrás használt lemezkép megosztása. Katalógus neveként engedélyezett karakterek:, kis-és nagybetűket, számokat, pontokat és időszakok. A katalógus neve nem tartalmazhat kötőjeleket.   Katalógus egyedieknek kell lenniük az előfizetésben. 

Hozzon létre egy rendszerképet katalógus [az sig létrehozása](/cli/azure/sig#az-sig-create). A következő példában létrehozunk egy katalógusban nevű *myGallery* a *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Kép definíció létrehozása

Lemezkép-definíciók létrehozása lemezképek logikai jellegű csoportosítását. A lemezkép-verziók azokon belül létrehozott adatainak kezeléséhez használhatók. Definíció rendszerképnevek a kis- és nagybetűket betűket, számokat, pontokat, kötőjeleket, és időszakokat is végezhető. Az értékeket is megadhat egy rendszerkép definíciójában kapcsolatos további információkért lásd: [definíciók kép](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

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

Hozzon létre a lemezkép verzió használatával szükség szerint [az image katalógus létrehozása-lemezkép-verzió](/cli/azure/sig/image-version#az-sig-image-version-create). Szüksége lesz a felügyelt lemezkép létrehozásához a rendszerkép verziószámát összegyűjtéséhez használt Azonosítóját adja át. Használhat [az képlista](/cli/azure/image?view#az-image-list) lemezképeket, amelyek egy erőforráscsoportba tartozó információt szeretne kapni. 

Lemezkép-verzió engedélyezett karakterek:, számokat és pontokat. Számok belül a 32 bites egész számnak kell lennie. Formátum: *Főverzió*. *MinorVersion*. *Javítás*.

Ebben a példában a lemezkép-verzió nem *1.0.0-s* és a 2 replika létrehozásához fogjuk a *USA nyugati középső Régiója* régió, 1, a replika a *USA déli középső Régiójában* régió és 1 a replika a *USA keleti RÉGIÓJA 2* régióban.


```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Várjon, amíg a rendszerkép verziószámát teljesen befejeződik, beépített és a replikált felügyelt ugyanazt a lemezképet létrehozni egy másik lemezkép-verzió használata előtt kell.