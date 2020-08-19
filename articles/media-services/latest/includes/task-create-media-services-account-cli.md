---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: c902ea001d18504dabffc8431acafe33822792e1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539125"
---
<!--Create a media services account -->

Az alábbi Azure CLI-parancs egy új Media Services-fiókot hoz létre. A következő értékeket lehet lecserélni: `amsaccount` `storageaccountforams` (meg kell egyeznie a Storage-fiókhoz megadott értékkel), és `amsResourceGroup` (meg kell egyeznie az erőforráscsoport számára megadott értékkel).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```