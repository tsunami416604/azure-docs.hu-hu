---
title: CLI – rendszerkép létrehozása pillanatképből vagy virtuális merevlemezből megosztott képtárban
description: Megtudhatja, hogyan hozhat létre képet pillanatképből vagy virtuális merevlemezből egy megosztott képtárban az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b5dcadd2381596509a3d2f512d0f4ebbbfbba893
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502877"
---
# <a name="create-an-image-from-a-vhd-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Rendszerkép létrehozása virtuális merevlemezről vagy pillanatképből egy megosztott képtárban az Azure CLI használatával

Ha van olyan meglévő pillanatképe vagy virtuális merevlemeze, amelyet át szeretne telepíteni egy megosztott képkatalógusba, közvetlenül a VHD-ből vagy a pillanatképből is létrehozhat egy megosztott rendszerkép-katalógust. Az új rendszerkép tesztelése után törölheti a forrás VHD-t vagy a pillanatképet. A [Azure PowerShell](image-version-snapshot-powershell.md)használatával létrehozhat egy rendszerképet egy virtuális merevlemezről vagy pillanatképből egy megosztott Képtárban.

A képkatalógusban található lemezképek két összetevővel rendelkeznek, amelyeket a következő példában hozunk létre:
- A **rendszerkép definíciója** információt nyújt a rendszerképekről és az azok használatára vonatkozó követelményekről. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, specializált vagy általánosított, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. 
- A **rendszerkép verziója** a virtuális gépek megosztott képtárat használó létrehozásához használatos. A környezethez szükség lehet a rendszerkép több verziójára. A virtuális gép létrehozásakor a rendszer a rendszerkép verzióját használja új lemezek létrehozásához a virtuális géphez. A rendszerkép verziója többször is használható.


## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez pillanatkép vagy virtuális merevlemez szükséges. 

Ha adatlemezt szeretne megadni, az adatlemez mérete nem lehet nagyobb 1 TB-nál.

A cikkben végzett munka során szükség esetén cserélje le az erőforrások nevét.

## <a name="find-the-snapshot-or-vhd"></a>A pillanatkép vagy a virtuális merevlemez megkeresése 

Az erőforráscsoporthoz elérhető Pillanatképek listáját az [az Snapshot List](/cli/azure/snapshot#az-snapshot-list)paranccsal tekintheti meg. 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Pillanatkép helyett virtuális merevlemezt is használhat. Virtuális merevlemez beszerzéséhez használja [az az Disk List](/cli/azure/disk#az-disk-list)lehetőséget. 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Miután megadta a pillanatkép vagy a virtuális merevlemez AZONOSÍTÓját, és hozzárendeli azt egy nevű változóhoz, amelyet `$source` később felhasználhat.

Ugyanezt a folyamatot használhatja a rendszerképbe felvenni kívánt adatlemezek lekéréséhez. Rendelje hozzá a változóhoz, majd ezeket a változókat később, amikor létrehozza a rendszerkép verzióját.


## <a name="find-the-gallery"></a>A gyűjtemény megkeresése

A rendszerkép-definíció létrehozásához a rendszerkép-katalógusra vonatkozó információkra lesz szüksége.

A rendelkezésre álló rendszerkép-gyűjteményekkel kapcsolatos információk listázása az [az SIG List](/cli/azure/sig#az-sig-list)paranccsal. Jegyezze fel a katalógus nevét, hogy a katalógus melyik erőforráscsoportot használja a későbbiekben.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. A rendszer a rendszerképpel kapcsolatos információk kezelésére szolgál. A képdefiníciók nevei nagybetűket, kisbetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. 

A rendszerkép meghatározásakor győződjön meg arról, hogy a megfelelő információval rendelkezik. Ebben a példában feltételezzük, hogy a pillanatkép vagy virtuális merevlemez egy használatban lévő virtuális gépről származik, és nem lett általánosítva. Ha a VHD-t vagy pillanatképet egy általánosított operációs rendszerből (a Sysprep futtatása után Windows-vagy [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` vagy Linux rendszeren) készítette, `-deprovision+user` akkor módosítsa a alkalmazást `-OsState` `generalized` . 

További információ a képdefiníciók által megadható értékekről: [képdefiníciók](./linux/shared-image-galleries.md#image-definitions).

Hozzon létre egy rendszerkép-definíciót a galériában az [az SIG rendszerkép-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create)paranccsal.

Ebben a példában a képdefiníció neve *myImageDefinition*, és egy [speciális](./linux/shared-image-galleries.md#generalized-and-specialized-images) Linux operációsrendszer-rendszerképhez van. Ha Windows operációs rendszert használó lemezképek definícióját szeretné létrehozni, használja a következőt: `--os-type Windows` . 

Ebben a példában a katalógus neve *MyGallery*, ez a *myGalleryRG* erőforráscsoport, a rendszerkép definíciójának neve pedig *mImageDefinition*lesz.

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
   --os-state specialized
```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre egy rendszerkép [-verziót az az rendszerkép-gyűjtemény létrehozása-rendszerkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create)paranccsal. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és 1 replikát hozunk létre az *USA déli középső* régiójában, az USA *2. keleti* régiójában pedig 1 replikát használunk a Zone-redundáns tárolás használatával. A célcsoportok replikáláshoz való kiválasztásakor ne feledje, hogy a VHD vagy a pillanatkép *forrás* -régióját is meg kell adnia a replikálás céljának.

Adja át a pillanatkép vagy virtuális merevlemez AZONOSÍTÓját a `--os-snapshot` paraméterben.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Ha az adatlemezeket is bele kívánja foglalni a lemezképbe, akkor azt is `--data-snapshot-luns` be kell állítania a LUN-számhoz beállított paraméterrel, valamint az `--data-snapshots` adatlemez vagy pillanatkép azonosítójának beállítását.

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> Az összes rendszerkép-verzió replikáját a [zóna redundáns tárolójában](../storage/common/storage-redundancy.md) is tárolhatja `--storage-account-type standard_zrs` a rendszerkép verziójának létrehozásakor.
>

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális gépet egy [speciális rendszerkép-verzióból](vm-specialized-image-version-cli.md).

A vásárlási tervre vonatkozó információk megadásával kapcsolatos információkért lásd: [Azure Marketplace vásárlási terv információinak megadása képek létrehozásakor](marketplace-images.md).
