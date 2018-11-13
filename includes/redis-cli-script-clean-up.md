---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 23b92f7d1b1c3ef488e182a6443f275365a03d9a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572200"
---
## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után az alábbi parancs segítségével távolítsa el az erőforráscsoport az erőforráscsoport, az Azure Redis Cache-példányt és a kapcsolódó erőforrásokat.

```azurecli
az group delete --name contosoGroup
```