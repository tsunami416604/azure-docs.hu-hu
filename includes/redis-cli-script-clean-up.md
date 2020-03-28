---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67178923"
---
## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájlminta futtatása után a következő parancs az erőforráscsoport, az Azure Cache for Redis-példány és az erőforráscsoport kapcsolódó erőforrásainak eltávolítására használható.

```azurecli
az group delete --name contosoGroup
```