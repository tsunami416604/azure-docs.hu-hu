---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 68a208af1a9aa9e73f2af99021d195f264fb21f1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "67179735"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid erőforrás-szolgáltató engedélyezése

Ha még nem használta korábban az Event Gridet az Azure-előfizetésében, lehetséges, hogy regisztrálnia kell az Event Grid erőforrás-szolgáltatót. Futtassa a következő parancsot:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Lehetséges, hogy a regisztráció végrehajtása eltart néhány pillanatig. Az állapot ellenőrzéséhez futtassa a következőt:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Ha a `RegistrationStatus``Registered` értékű, készen áll a folytatásra.
