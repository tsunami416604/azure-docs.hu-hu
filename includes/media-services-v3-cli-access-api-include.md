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
ms.openlocfilehash: b0f93f950b55052ea8d8b31538c47226413dc82a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179210"
---
## <a name="access-the-media-services-api"></a>Hozzáférés a Media Services API-hoz

Az Azure Media Services API-khoz való csatlakozáshoz használja az Azure AD szolgáltatásnév hitelesítését. Az alábbi parancs létrehoz egy Azure AD-alkalmazást, majd egy szolgáltatásnevet csatol a fiókhoz. A visszaadott értékeket használja az alkalmazás konfigurálásához.

A szkript futtatása előtt kell cserélni a `amsaccount` és `amsResourceGroup` ezek az erőforrások létrehozásakor megadott nevét. `amsaccount`: a szolgáltatásnév csatolásához használni kívánt Azure Media Services-fiók neve.

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
