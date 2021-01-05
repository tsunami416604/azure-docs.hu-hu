---
title: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálása
description: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálásához szükséges lépések.
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770823"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Az Azure VMware-megoldás használatához először regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében.  

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```


### <a name="azure-portal"></a>Azure Portal
 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.

1. A **minden szolgáltatás** mezőben adja meg az **előfizetés** elemet, majd válassza az **előfizetések** lehetőséget.

1. Válassza ki az előfizetést az előfizetés listából a megtekintéshez.

1. Válassza az **erőforrás-szolgáltatók** lehetőséget, és írja be a **Microsoft. AVS** kifejezést a keresésbe. 
 
1. Ha az erőforrás-szolgáltató nincs regisztrálva, válassza a **regisztráció** lehetőséget.