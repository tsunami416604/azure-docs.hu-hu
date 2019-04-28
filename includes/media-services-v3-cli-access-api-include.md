---
title: fájl belefoglalása
description: fájl belefoglalása
services: media-services
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 05/29/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309083"
---
## <a name="access-the-media-services-api"></a>Hozzáférés a Media Services API-hoz

Az Azure Media Services API-khoz való csatlakozáshoz használja az Azure AD szolgáltatásnév hitelesítését. Az alábbi parancs létrehoz egy Azure AD-alkalmazást, majd egy szolgáltatásnevet csatol a fiókhoz. A visszaadott értékeket használja az alkalmazás konfigurálásához.

A szkript futtatása előtt kicserélheti az `amsaccount` és `amsResourceGroup` értékeket az erőforrások létrehozásakor választott nevekkel. `amsaccount`: a szolgáltatásnév csatolásához használni kívánt Azure Media Services-fiók neve.

Az alábbi parancs egy `json` kimenetet ad vissza:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Ez a parancs a következőhöz hasonló választ ad:

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.partner.microsoftonline.cn",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.chinacloudapi.cn/",
  "ArmEndpoint": "https://management.chinacloudapi.cn/",
  "Region": "chinaeast",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Ha szeretné, hogy legyen egy `xml` a válaszban, használja az alábbi parancsot:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
