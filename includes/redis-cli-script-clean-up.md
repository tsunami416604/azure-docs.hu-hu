---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/25/2018
ms.author: v-junlch
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607741"
---
## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után az alábbi parancs segítségével távolítsa el az erőforráscsoport az erőforráscsoport, a Redis-példányt az Azure Cache és a kapcsolódó erőforrásokat.

```azurecli
az group delete --name contosoGroup
```

<!-- ms.date: 12/25/2018 -->