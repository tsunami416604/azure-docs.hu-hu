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
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814876"
---
## <a name="before-you-begin"></a>Előkészületek

A jelen cikkben szereplő példa végrehajtásához rendelkeznie kell egy általánossé tett virtuális gép meglévő felügyelt rendszerképével. További információ: [Oktatóanyag: Hozzon létre egy egyéni lemezképet egy Azure virtuális gép az Azure CLI.](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images) Ha a felügyelt lemez adatlemezt tartalmaz, az adatlemez mérete nem lehet több 1 TB-nál.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/bash](https://shell.azure.com/bash)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a CLI-t helyileg szeretné telepíteni és használni, olvassa el [az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört.

## <a name="create-an-image-gallery"></a>Képgaléria létrehozása 

A képgaléria a képmegosztás engedélyezéséhez használt elsődleges erőforrás. A Galéria nevének megengedett karakterei a nagy- vagy kisbetűk, számjegyek, pontszámok és pont. A gyűjtemény neve nem tartalmazhat kötőjeleket.   A galérianeveknek egyedinek kell lenniük az előfizetésen belül. 

Hozzon létre egy képgalériát [az az sig create](/cli/azure/sig#az-sig-create)használatával. A következő példa létrehoz egy *myGallery* nevű galériát a *myGalleryRG-ben.*

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Képdefiníció létrehozása

A képdefiníciók logikai csoportosítást hoznak létre a képekhez. A bennük létrehozott lemezkép-verziók adatainak kezelésére szolgálnak. A képdefiníciós nevek nagy- vagy kisbetűkből, számjegyekből, pontokból, kötőjelekből és pontokból állnak. A képdefinícióhoz megadható értékekről a [Képdefiníciók](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)című témakörben talál további információt.

Hozzon létre egy kezdeti képdefiníciót a gyűjteményben az [sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)használatával.

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


## <a name="create-an-image-version"></a>Képverzió létrehozása 

Szükség szerint hozza létre a képverzióverzióit az [az képgaléria create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create)használatával. Át kell adnia a felügyelt lemezkép azonosítóját, hogy alapvonalként használhassa a lemezkép-verzió létrehozásához. Az [képlista](/cli/azure/image?view#az-image-list) segítségével információkat kaphat az erőforráscsoportban lévő képekről. 

A képverzió megengedett karakterei számok és időszakok. A számoknak a 32 bites egész szám tartományán belül kell lenniük. Formátum: *MajorVersion*. *MinorVersion*. *Patch*.

Ebben a példában a lemezkép verziója *1.0.0,* és 2 replikát fogunk létrehozni az *USA nyugati középső régiójában,* 1 replikát az USA déli középső régiójában és 1 replikát az USA keleti *régiójában* *2* régióban zónaredundáns tárolás használatával.


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
> Meg kell várnia, amíg a lemezkép-verzió teljesen befejeződik a létrehozás és a replikálás, mielőtt ugyanazt a felügyelt lemezképet használhatna egy másik lemezkép-verzió létrehozásához.
>
> Az összes lemezképverzió-replikát a [Zónaredundáns tárolásban](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) is tárolhatja, ha a lemezkép-verzió létrehozásakor hozzáadja. `--storage-account-type standard_zrs`
>

## <a name="share-the-gallery"></a>A galéria megosztása

Azt javasoljuk, hogy ossza meg más felhasználókkal a galéria szintjén. A galéria objektumazonosítójának leszámításához használja [az az sig show -t.](/cli/azure/sig#az-sig-show)

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Használja az objektumazonosítót hatókörként, valamint egy e-mail címet és [az az szerepkör-hozzárendelést létrehozva,](/cli/azure/role/assignment#az-role-assignment-create) hogy a felhasználó nak hozzáférést biztosítson a megosztott képtárhoz.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


