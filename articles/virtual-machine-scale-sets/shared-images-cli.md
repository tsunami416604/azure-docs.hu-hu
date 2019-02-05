---
title: A megosztott hozhat létre egy méretezési csoportot az Azure-beli Virtuálisgép-rendszerképek használata |} A Microsoft Docs
description: Ismerje meg, hogyan üzembe helyezése virtuálisgép-méretezési csoportok az Azure-ban használandó megosztott Virtuálisgép-rendszerképek létrehozása az Azure CLI használatával.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 2aa1f71d0e8e73ae793fc5396b8b654b9ca875bc
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733535"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Létrehozhat és használhat megosztott rendszerképek virtuálisgép-méretezési csoportokhoz az Azure CLI 2.0 használatával

Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. [Megosztott kép katalógusok](shared-image-galleries.md) jelentősen leegyszerűsíti az egyéni rendszerkép megosztása a szervezetben. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. A megosztott lemezkép-katalógus lehetővé teszi a VM-rendszerképeit megosztja másokkal a szervezetében, vagy régióban, az AAD-bérlőn belül is. Győződjön meg arról, azokat az elérhető, és kikre meg szeretné osztani azokat a kívánt mely régiókban, melyik képek meg szeretné osztani. Több katalógusok hozhat létre, így logikusan csoportosíthatja a megosztott-lemezképeket. A katalógus által biztosított teljes szerepköralapú hozzáférés-vezérlés (RBAC) legfelsőbb szintű erőforráshoz. Lemezképek lehetnek rendszerverzióval ellátott, és dönthet úgy, hogy minden lemezkép-verzió replikálása számára egy másik Azure-régióban. A katalógus csak felügyelt képekkel működik. Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy megosztott lemezkép-katalógusában
> * Megosztott kép definíciójának létrehozása
> * Hozzon létre egy megosztott lemezkép verziója
> * Virtuális gép létrehozása egy megosztott rendszerképből

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


>[!NOTE]
> Ez a cikk ismerteti a folyamatot egy általánosított felügyelt rendszerkép használatával. A méretezési csoportok speciális virtuálisgép-rendszerképekből való létrehozása nem támogatott.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk van szükség, hogy futnak-e az Azure CLI verziója 2.0.46 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Méretezési csoport létrehozása az egyéni virtuálisgép-rendszerképből
Hozzon létre egy méretezési csoportot az [az vmss create](/cli/azure/vmss#az-vmss-create) paranccsal. A platform rendszerképe helyett (pl. *UbuntuLTS* vagy *CentOS*) adja meg az egyéni virtuálisgép-rendszerkép nevét. Az alábbi példa egy *myScaleSet* nevű méretezési csoportot hoz létre, amely az előző lépésben szereplő, *myImage* nevű egyéni rendszerképet használja:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és további erőforrások eltávolításához törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást az [az group delete](/cli/azure/group) paranccsal. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>További lépések

Lemezkép-katalógusában a megosztott erőforrás-sablonok használatával is létrehozhat. Nincsenek elérhető számos Azure gyorsindítási sablonok: 

- [Hozzon létre egy megosztott lemezkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Kép definíció létrehozása egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Hozzon létre egy lemezkép verziója egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Lemezkép verziója egy virtuális gép létrehozása](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Ha problémákat tapasztal, [hibáinak elhárítása a közös kép katalógusok](troubleshooting-shared-images.md).
