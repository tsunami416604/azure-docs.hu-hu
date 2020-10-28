---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6c5ab187eb1307a95bbae4ff39d387221e094ea5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736843"
---
## <a name="enable-the-event-grid-resource-provider"></a>A Event Grid erőforrás-szolgáltató engedélyezése

Ha korábban még nem használta Event Grid az Azure-előfizetésében, előfordulhat, hogy regisztrálnia kell a Event Grid erőforrás-szolgáltatót. A szolgáltató regisztrálásához futtassa az alábbi parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Eltarthat egy kis ideig, amíg a regisztráció befejeződik. Az állapot ellenőrzéséhez futtassa a következőt:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Ha a `registrationState``Registered` értékű, készen áll a folytatásra.
