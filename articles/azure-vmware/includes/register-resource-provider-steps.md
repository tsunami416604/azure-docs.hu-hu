---
title: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálása
description: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálásához szükséges lépések.
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512366"
---
Az Azure VMware-megoldás használatához először regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Az erőforrás-szolgáltató regisztrálásának további módjaiért tekintse meg az [Azure erőforrás-szolgáltatók és-típusok](../../azure-resource-manager/management/resource-providers-and-types.md)című témakört.