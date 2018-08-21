---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 74d96952d6cafb6bd7bb16ccf2f4d69b4fbd6de3
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40233965"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid erőforrás-szolgáltató engedélyezése

Ha még nem használta korábban az Event Gridet az Azure-előfizetésében, lehetséges, hogy regisztrálnia kell az Event Grid erőforrás-szolgáltatót. A szolgáltató regisztrálásához futtassa az alábbi parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Lehetséges, hogy a regisztráció végrehajtása eltart néhány pillanatig. Az állapot ellenőrzéséhez futtassa a következőt:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Ha a `registrationState` `Registered` értékű, készen áll a folytatásra.