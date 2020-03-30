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
ms.openlocfilehash: 9d8de8826fc3dfcc2360eb3b6c82b3ff8d65d845
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461231"
---
### <a name="access-the-media-services-api"></a>Hozzáférés a Media Services API-hoz

Az Azure Media Services API-khoz való csatlakozáshoz használja az Azure AD szolgáltatásnév hitelesítését. Az alábbi parancs létrehoz egy Azure AD-alkalmazást, majd egy szolgáltatásnevet csatol a fiókhoz. A visszaadott értékeket használja az alkalmazás konfigurálásához.

A parancsfájl futtatása előtt `amsaccount` cserélje `amsResourceGroup` le a és az okat az erőforrások létrehozásakor kiválasztott neveket. `amsaccount`: a szolgáltatásnév csatolásához használni kívánt Azure Media Services-fiók neve.

Ha több előfizetéshez is hozzáfér, először állítsa be az aktív előfizetést arra az előfizetésre, amelyhez a Media Services-fiókot létrehozták.

```azurecli
az account set --subscription subscriptionId
```

Az alábbi parancs egy `json` kimenetet ad vissza:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Ez a parancs a következőhöz hasonló választ ad:

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

Ha szeretné, hogy legyen egy `xml` a válaszban, használja az alábbi parancsot:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
