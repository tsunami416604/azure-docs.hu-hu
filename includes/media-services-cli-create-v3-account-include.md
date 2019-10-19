---
title: fájl belefoglalása
description: fájl belefoglalása
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: da27f818bf368108568287f1ed1bbdae4c3902d4
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72560413"
---
## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Először létre kell hoznia egy Media Services-fiókot. Ez a szakasz azt mutatja be, hogy mire van szüksége a fiók létrehozásához az Azure CLI használatával.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport létrehozásához használja az alábbi parancsot. Az Azure-erőforráscsoport egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az erőforrásokat (például az Azure Media Services-fiókokat és a kapcsolódó Storage-fiókokat).

Az értékkel helyettesítheti `amsResourceGroup`.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Create a storage account

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. További információ a tárfiókok használatáról a Media Servicesben: [Storage-fiókok](../articles/media-services/latest/storage-account-concept.md).

Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. A blobfiókok nem megengedettek **elsődleges** tárfiókként. Ha szeretne többet megtudni a Storage-fiókokkal kapcsolatban, tekintse meg [Az Azure Storage-fiók beállításai](../articles/storage/common/storage-account-options.md) című témakört. 

Ebben a példában egy általános célú v2 standard LRS-fiókot hozunk létre. Ha a Storage-fiókokkal szeretne kísérletezni, használja a `--sku Standard_LRS`. Ha azonban éles üzemben lévő SKU-t vesz fel, érdemes megfontolnia `--sku Standard_RAGRS`, amely földrajzi replikációt biztosít az üzletmenet folytonossága érdekében. További információ: Storage- [fiókok](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Az alábbi parancs egy Storage-fiókot hoz létre, amelyet a rendszer a Media Services-fiókhoz fog társítani. Az alábbi szkriptben a `storageaccountforams` helyére helyettesítheti be az Ön által megadott értéket. `amsResourceGroup`nak meg kell egyeznie az előző lépésben az erőforráscsoport számára megadott értékkel. A Storage-fiók nevének 24-nél rövidebbnek kell lennie.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Az alábbi Azure CLI-parancs egy új Media Services-fiókot hoz létre. A következő értékeket kell kicserélnie: `amsaccount`, `storageaccountforams` (meg kell egyeznie a tárfiók esetében megadott értékkel) és `amsResourceGroup` (meg kell egyeznie az erőforráscsoport esetében megadott értékkel).

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```
