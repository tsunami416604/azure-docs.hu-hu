---
title: Virtuális gép létrehozása speciális rendszerkép-verzióból az Azure CLI használatával
description: Hozzon létre egy virtuális gépet egy speciális rendszerkép-verzió használatával egy megosztott rendszerkép-katalógusban az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 478413132a09334c6b5fe6e4d7c4c31c988cd38d
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501048"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Virtuális gép létrehozása speciális rendszerkép-verzióval az Azure CLI használatával

Hozzon létre egy virtuális gépet egy megosztott rendszerkép-gyűjteményben tárolt [speciális rendszerkép-verzióból](./linux/shared-image-galleries.md#generalized-and-specialized-images) . Ha általánosított rendszerkép-verzió használatával szeretne virtuális gépet létrehozni, tekintse meg [a virtuális gép létrehozása általánosított rendszerkép-verzióból](vm-generalized-image-version-cli.md)című témakört.

Szükség szerint cserélje le az erőforrás-neveket ebben a példában. 

A katalógusban szereplő képdefiníciók listázása az az [SIG rendszerkép-definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list) paranccsal, hogy megtekintse a definíciók nevét és azonosítóját.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Hozza létre a virtuális gépet az az [VM Create](/cli/azure/vm#az-vm-create) paranccsal a--Specialized paraméterrel, amely jelzi, hogy a rendszerkép egy speciális rendszerkép. 

A rendszerkép-definíció AZONOSÍTÓjának használatával `--image` hozza létre a virtuális gépet a rendelkezésre álló rendszerkép legújabb verziójából. A virtuális gépet egy adott verzióból is létrehozhatja, ha megadja a rendszerkép-verziójának AZONOSÍTÓját `--image` . 

Ebben a példában egy virtuális gépet hozunk létre a *myImageDefinition* -lemezkép legújabb verziójából.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>További lépések
Az [Azure rendszerkép-szerkesztő (előzetes verzió)](./linux/image-builder-overview.md) segítségével automatizálhatja a rendszerkép-verziók létrehozását, és [egy meglévő rendszerkép-verzióból](./linux/image-builder-gallery-update-image-version.md)is frissítheti és létrehozhatja az új rendszerkép verzióját. 

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerképverzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)
