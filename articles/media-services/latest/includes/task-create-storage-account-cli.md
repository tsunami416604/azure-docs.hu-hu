---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 313c8ea9046deea953b4143f1a0264f81da38764
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602510"
---
<!-- ### Create a storage account -->

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. További információ a tárfiókok használatáról a Media Servicesben: [Storage-fiókok](../storage-account-concept.md).

Rendelkeznie kell egy **elsődleges** Storage-fiókkal, és tetszőleges számú **másodlagos** Storage-fiók társítható a Media Services fiókjához. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. A blobfiókok nem megengedettek **elsődleges** tárfiókként. Ha szeretne többet megtudni a Storage-fiókokkal kapcsolatban, tekintse meg [Az Azure Storage-fiók beállításai](../../../storage/common/storage-account-overview.md) című témakört. 

Ebben a példában egy általános célú v2 standard LRS-fiókot hozunk létre. Ha a Storage-fiókokkal szeretne kísérletezni, használja a következőt: `--sku Standard_LRS` . Ha azonban az üzemi célú SKU-t választotta, érdemes figyelembe vennie az `--sku Standard_RAGRS` üzleti folytonosságot, amely földrajzi replikációt tesz lehetővé. További információ: Storage- [fiókok](/cli/azure/storage/account?view=azure-cli-latest).

Az alábbi parancs egy Storage-fiókot hoz létre, amelyet a rendszer a Media Services-fiókhoz fog társítani. Az alábbi szkriptben a `storageaccountforams` helyére helyettesítheti be az Ön által megadott értéket. `amsResourceGroup` az előző lépésben az erőforráscsoport számára megadott értéknek kell megegyeznie. A Storage-fiók nevének 24-nél rövidebbnek kell lennie.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
