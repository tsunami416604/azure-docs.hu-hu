---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0946b703b45f69ede409167213f2629a796f18b5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869627"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid erőforrás-szolgáltató engedélyezése

Ha még nem használta korábban az Event Gridet az Azure-előfizetésében, lehetséges, hogy regisztrálnia kell az Event Grid erőforrás-szolgáltatót. Futtassa az alábbi parancsot:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Lehetséges, hogy a regisztráció végrehajtása eltart néhány pillanatig. Az állapot ellenőrzéséhez futtassa a következőt:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Ha a `RegistrationStatus` `Registered` értékű, készen áll a folytatásra.