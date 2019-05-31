---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 841027fe8d6b97e661faa038dc9381edbb3d4cd8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226040"
---
## <a name="before-you-begin"></a>Előkészületek

A példában ez a cikk a végrehajtásához egy általánosított virtuális gép egy meglévő felügyelt rendszerkép kell rendelkeznie. További információkért lásd: [oktatóanyag: Hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek az Azure CLI 2.0 használatával](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Ha a felügyelt rendszerkép egy adatlemezt tartalmaz, a az adatlemez mérete nem lehet több mint 1 TB.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha szeretné telepíteni, és a parancssori Felületet helyileg használja, lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Lemezkép-katalógus létrehozása 

Lemezkép-katalógus, hogy az elsődleges erőforrás használt lemezkép megosztása. Katalógus neveként engedélyezett karakterek:, kis-és nagybetűket, számokat, pontokat és időszakok. A katalógus neve nem tartalmazhat kötőjeleket.   Katalógus egyedieknek kell lenniük az előfizetésben. 

Hozzon létre egy rendszerképet katalógus [az sig létrehozása](/cli/azure/sig#az-sig-create). A következő példában létrehozunk egy katalógusban nevű *myGallery* a *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Kép definíció létrehozása

Lemezkép-definíciók létrehozása lemezképek logikai jellegű csoportosítását. A lemezkép-verziók azokon belül létrehozott adatainak kezeléséhez használhatók. Definíció rendszerképnevek a kis- és nagybetűket betűket, számokat, pontokat, kötőjeleket, és időszakokat is végezhető. Az értékeket is megadhat egy rendszerkép definíciójában kapcsolatos további információkért lásd: [definíciók kép](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Hozzon létre egy kezdeti rendszerkép definíciójában a katalógusban található [az sig lemezkép-definíció létrehozására](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
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

Ebben a példában a lemezkép-verzió nem *1.0.0-s* és a 2 replika létrehozásához fogjuk a *USA nyugati középső Régiója* régió, 1, a replika a *USA déli középső Régiójában* régió és 1 a replika a *USA keleti RÉGIÓJA 2* régióhoz zónaredundáns tárolás használatával.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Várjon, amíg a rendszerkép verziószámát teljesen befejeződik, beépített és a replikált felügyelt ugyanazt a lemezképet létrehozni egy másik lemezkép-verzió használata előtt kell.
>
> Is tárolhatja a rendszerkép verziója replikák mindegyike [Zónaredundáns tárolás](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) hozzáadásával `--storage-account-type standard_zrs` létrehozásakor, a rendszerkép verziószámát.
>

## <a name="share-the-gallery"></a>A katalógus megosztása

Azt javasoljuk, hogy a gyűjtemény szintjén más felhasználókkal megosztott. A katalógus objektum Azonosítójának lekéréséhez használja a [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Objektumazonosító hatókör, e-mail-címmel együtt használja, és [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) való felhasználói hozzáférés megadása a megosztott lemezkép-katalógusba.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


