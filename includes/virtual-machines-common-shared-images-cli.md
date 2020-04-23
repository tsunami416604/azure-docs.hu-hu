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

A cikkben szereplő példa végrehajtásához rendelkeznie kell egy általánosított virtuális gép meglévő felügyelt képével. További információ: [oktatóanyag: egyéni rendszerkép létrehozása Azure-beli virtuális gépről az Azure CLI-vel](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Ha a felügyelt lemezkép adatlemezt tartalmaz, az adatlemez mérete nem haladhatja meg az 1 TB-ot.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/bash](https://shell.azure.com/bash). A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a parancssori felület helyi telepítését és használatát szeretné használni, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

## <a name="create-an-image-gallery"></a>Rendszerkép-gyűjtemény létrehozása 

A képgyűjtemény a képmegosztás engedélyezéséhez használt elsődleges erőforrás. A katalógus nevének megengedett karaktere nagybetűs vagy kisbetűk, számjegyek, pontok és időszakok. A gyűjtemény neve nem tartalmazhat kötőjeleket.   A katalógus nevének egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy képtárat [az az SIG Create](/cli/azure/sig#az-sig-create)paranccsal. A következő példában létrehozunk egy *MyGallery* nevű katalógust a *myGalleryRG*-ben.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. Ezek az adatok a bennük létrehozott rendszerkép-verziókra vonatkozó információk kezelésére szolgálnak. A képdefiníciók nevei kis-és nagybetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. További információ a képdefiníciók által megadható értékekről: [képdefiníciók](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Hozzon létre egy kezdeti rendszerkép-definíciót a galériában az [az SIG rendszerkép-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create)paranccsal.

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


## <a name="create-an-image-version"></a>Rendszerkép-verzió létrehozása 

Szükség szerint hozza létre a rendszerkép verzióit az [az rendszerkép-gyűjtemény létrehozása-rendszerkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create)paranccsal. A rendszerkép verziójának létrehozásához meg kell adnia a felügyelt rendszerkép AZONOSÍTÓját. Az [az Image List](/cli/azure/image?view#az-image-list) paranccsal kérheti le az erőforráscsoporthoz tartozó rendszerképekkel kapcsolatos információkat. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és két replikát fogunk létrehozni az *USA nyugati középső* régiójában, 1 replika az *USA déli középső* régiójában és 1 replika az *USA 2. keleti* régiójában, a zóna redundáns tárolásával.


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
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> Az összes rendszerkép-verzió replikáját a [zóna redundáns tárolójában](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) `--storage-account-type standard_zrs` is tárolhatja a rendszerkép verziójának létrehozásakor.
>

## <a name="share-the-gallery"></a>A katalógus megosztása

Javasoljuk, hogy a katalógus szintjén ossza meg más felhasználókkal. A katalógus objektum-AZONOSÍTÓjának lekéréséhez használja az [az SIG show](/cli/azure/sig#az-sig-show)lehetőséget.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Használja az objektumazonosító hatókörként, valamint egy e-mail-cím és [az az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) lehetőséget, hogy a felhasználók hozzáférhessenek a megosztott képgyűjteményhez.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


