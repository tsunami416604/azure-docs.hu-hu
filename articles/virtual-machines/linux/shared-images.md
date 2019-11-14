---
title: Megosztott képgyűjtemények létrehozása az Azure CLI-vel
description: Ebből a cikkből megtudhatja, hogyan használhatja az Azure CLI-t egy virtuális gép megosztott rendszerképének létrehozásához az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: de1afa2367afcb78e8ca68e518acc93e33f61c43
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034966"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Megosztott képgyűjtemény létrehozása az Azure CLI-vel

A [megosztott képgyűjtemény](shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását a szervezeten belül vagy régiókban, egy HRE-bérlőn belül. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. Több gyűjteményt is létrehozhat, hogy logikailag csoportosítsa a megosztott rendszerképeket. 

A katalógus egy legfelső szintű erőforrás, amely teljes körű szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. A lemezképek telepíthetők, és eldöntheti, hogy az egyes lemezkép-verziókat egy másik Azure-régióba replikálja-e. A katalógus csak felügyelt lemezképekkel működik.

A megosztott képkatalógus funkció több erőforrástípust is tartalmaz. Ebben a cikkben a következő lépéseket fogjuk használni vagy felépíteni:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Ez egy alapszintű rendszerkép, amely önmagában vagy rendszerkép- **verzió** létrehozásához használható egy képgyűjteményben. A felügyelt lemezképek általánosított virtuális gépekről jönnek létre. A felügyelt rendszerkép olyan speciális VHD-típus, amellyel több virtuális gép hozható létre, és most már használható a megosztott rendszerkép-verziók létrehozásához is. |
| **Képtár** | Az Azure Marketplace-hez hasonlóan a képkatalógus **is a lemezképek** kezeléséhez és megosztásához használható tárház, de Ön szabályozhatja, hogy ki férhet hozzá. |
| **Rendszerkép definíciója** | A lemezképek a katalógusban vannak definiálva, és a rendszerképekkel és a belső használattal kapcsolatos követelményekkel rendelkeznek. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. |
| **Rendszerkép verziója** | A **rendszerkép verziója** az, amit a virtuális gép létrehozásához használ gyűjtemény létrehozásakor. A környezethez szükség lehet a rendszerkép több verziójára. A felügyelt rendszerképekhez hasonlóan, amikor **rendszerkép-verziót** használ egy virtuális gép létrehozásához, a rendszerkép verziója a virtuális gép új lemezének létrehozására szolgál. A rendszerkép verziója többször is használható. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a legújabb rendszerkép-verzióból az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal.

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

Egy adott verziót is használhat a `--image` paraméter rendszerkép-verziójának AZONOSÍTÓjának használatával. Ha például *a következőt* szeretné használni: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Következő lépések
Az [Azure rendszerkép-szerkesztő (előzetes verzió)](image-builder-overview.md) segítségével automatizálhatja a rendszerkép-verziók létrehozását, és [egy meglévő rendszerkép-verzióból](image-builder-gallery-update-image-version.md)is frissítheti és létrehozhatja az új rendszerkép verzióját. 

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Megosztott Képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása megosztott rendszerkép-gyűjteményben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerkép-verzió létrehozása megosztott rendszerkép-gyűjteményben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képtárakkal kapcsolatos további információkért tekintse meg az [áttekintést](shared-image-galleries.md). Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](troubleshooting-shared-images.md)című témakört.
