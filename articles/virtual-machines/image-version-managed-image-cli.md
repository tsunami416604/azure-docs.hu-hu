---
title: Migrálás felügyelt rendszerképből egy rendszerkép-verzióra az Azure CLI-vel
description: Megtudhatja, hogyan telepíthet át felügyelt rendszerképekből egy rendszerkép-verzióra egy megosztott rendszerkép-katalógusban az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8631a411b26f91bc72e23ac7ff9fb2278f61168c
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502885"
---
# <a name="migrate-from-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Migrálás felügyelt rendszerképből rendszerkép-verzióra az Azure CLI használatával
Ha van egy meglévő felügyelt rendszerképe, amelyet át szeretne telepíteni egy megosztott képkatalógusba, akkor közvetlenül a felügyelt rendszerképből is létrehozhat egy közös rendszerkép-katalógust. Miután tesztelte az új rendszerképet, törölheti a forrás által felügyelt képet. A [PowerShell](image-version-managed-image-powershell.md)használatával is áttelepítheti a felügyelt rendszerképeket egy megosztott képkatalógusba.

A képkatalógusban található lemezképek két összetevővel rendelkeznek, amelyeket a következő példában hozunk létre:
- A **rendszerkép definíciója** információt nyújt a rendszerképekről és az azok használatára vonatkozó követelményekről. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, specializált vagy általánosított, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. 
- A **rendszerkép verziója** a virtuális gépek megosztott képtárat használó létrehozásához használatos. A környezethez szükség lehet a rendszerkép több verziójára. A virtuális gép létrehozásakor a rendszer a rendszerkép verzióját használja új lemezek létrehozásához a virtuális géphez. A rendszerkép verziója többször is használható.


## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához meg kell adnia egy meglévő [megosztott képtárat](shared-images-cli.md). 

A cikkben szereplő példa végrehajtásához rendelkeznie kell egy általánosított virtuális gép meglévő felügyelt képével. További információkért lásd: [felügyelt lemezkép rögzítése](./linux/capture-image.md). Ha a felügyelt lemezkép adatlemezt tartalmaz, az adatlemez mérete nem haladhatja meg az 1 TB-ot.

A cikkben végzett munka során szükség esetén cserélje le az erőforráscsoportot és a virtuális gépek nevét.



## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

Mivel a felügyelt lemezképek mindig általánosított lemezképek, egy általánosított lemezképhez tartozó rendszerkép-definíciót fog létrehozni `--os-state generalized` .

A képdefiníciók nevei kis-és nagybetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. 

További információ a képdefiníciók által megadható értékekről: [képdefiníciók](./linux/shared-image-galleries.md#image-definitions).

Hozzon létre egy rendszerkép-definíciót a galériában az [az SIG rendszerkép-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create)paranccsal.

Ebben a példában a képdefiníció neve *myImageDefinition*, és egy [általánosított](./linux/shared-image-galleries.md#generalized-and-specialized-images) Linux operációsrendszer-rendszerképhez van. Ha Windows operációs rendszert használó lemezképek definícióját szeretné létrehozni, használja a következőt: `--os-type Windows` . 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Verziók létrehozása [az az a rendszerkép-gyűjtemény létrehozása-rendszerkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create). A rendszerkép verziójának létrehozásához meg kell adnia a felügyelt rendszerkép AZONOSÍTÓját. A lemezképek azonosítóit az [az Image List](/cli/azure/image?view#az-image-list) paranccsal kérheti le. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és 1 replikát hozunk létre az *USA déli középső* régiójában, az USA *2. keleti* régiójában pedig 1 replikát használunk a Zone-redundáns tárolás használatával. A célcsoportok replikáláshoz való kiválasztásakor ne feledje, hogy a *forrás* régiót is meg kell adnia a replikálás céljának.

Adja át a felügyelt rendszerkép AZONOSÍTÓját a `--managed-image` paraméterben.


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> Az összes rendszerkép-verzió replikáját a [zóna redundáns tárolójában](../storage/common/storage-redundancy.md) is tárolhatja `--storage-account-type standard_zrs` a rendszerkép verziójának létrehozásakor.
>

## <a name="next-steps"></a>További lépések

Hozzon létre egy virtuális gépet egy [általánosított rendszerkép-verzióból](vm-generalized-image-version-cli.md).

A vásárlási tervre vonatkozó információk megadásával kapcsolatos információkért lásd: [Azure Marketplace vásárlási terv információinak megadása képek létrehozásakor](marketplace-images.md).
