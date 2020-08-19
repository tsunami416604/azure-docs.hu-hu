---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602369"
---
<!--Create a media services account -->

Az alábbi Azure CLI-parancs egy új Media Services-fiókot hoz létre. A következő értékeket lehet lecserélni: `amsaccount` `storageaccountforams` (meg kell egyeznie a Storage-fiókhoz megadott értékkel), és `amsResourceGroup` (meg kell egyeznie az erőforráscsoport számára megadott értékkel).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```