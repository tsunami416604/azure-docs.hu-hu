---
title: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálása
description: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálásához szükséges lépések.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 380cb513ec389293db757e667ed4681778e29348
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254633"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Az Azure VMware-megoldás használatához először regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Azt is megteheti, hogy a grafikus felhasználói felület használatával regisztrálja a **Microsoft. AVS** erőforrás-szolgáltatót.  További információt az [erőforrás-szolgáltató és-típusok regisztrálása](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) című cikkben talál.  
