---
title: fájl belefoglalása
description: fájl belefoglalása
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 9ecb07a2cb278f6cde4ffdc3b252cb9e816d08da
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2018
ms.locfileid: "35414637"
---
## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Először létre kell hoznia egy Media Services-fiókot. Ebben a szakaszban a fiók Azure CLI-vel történő létrehozásának előfeltételeit ismertetjük.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport létrehozásához használja az alábbi parancsot. Az Azure-erőforráscsoport egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az erőforrásokat (például az Azure Media Services-fiókokat és a kapcsolódó Storage-fiókokat).

```azurecli-interactive
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Create a storage account

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. 

Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. A blobfiókok nem megengedettek **elsődleges** tárfiókként. Ha szeretne többet megtudni a Storage-fiókokkal kapcsolatban, tekintse meg [Az Azure Storage-fiók beállításai](../articles/storage/common/storage-account-options.md) című témakört. 

További információ a tárfiókok használatáról a Media Servicesben: [Storage-fiókok](../articles/media-services/latest/storage-account-concept.md).

Az alábbi parancs egy Storage-fiókot hoz létre, amelyet a rendszer a Media Services-fiókhoz fog társítani. Az alábbi szkriptben a `storageaccountforams` helyére helyettesítheti be az Ön által megadott értéket. A fiók neve legfeljebb 24 karakter hosszú lehet.

```azurecli-interactive
az storage account create --name storageaccountforams \  
--kind StorageV2 \
--sku Standard_RAGRS \
--resource-group amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Az alábbi Azure CLI-parancs egy új Media Services-fiókot hoz létre. A következő értékeket kell kicserélnie: `amsaccount`, `storageaccountforams` (meg kell egyeznie a tárfiók esetében megadott értékkel) és `amsResourceGroup` (meg kell egyeznie az erőforráscsoport esetében megadott értékkel).

```azurecli-interactive
az ams account create --name amsaccount --resource-group amsResourceGroup --storage-account storageaccountforams
```
